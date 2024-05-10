
<h2 align="center">Implementation of the <a href="https://github.com/Thinklab-SJTU/Crossformer">Crossformer</a> model for Stock Return Prediction</h2>
<p align="center">
<img src="https://img.shields.io/badge/python-3670A0?style=for-the-badge&logo=python&logoColor=ffdd54">
  <img src="https://img.shields.io/badge/PyTorch-%23EE4C2C.svg?style=for-the-badge&logo=PyTorch&logoColor=white">
</p>

<p align="center">
  <a href="#summary">Summary</a> •
  <a href="#experiments">Experiments</a> •
  <a href="#files">Files</a>
  
  
</p>



## Summary

Crossformer was published as a conference paper at ISLR 2023. Unlike traditional transformer-based models, it utilizes cross-dimension dependency for multivariate time series forecasting. The original paper can be found <a href="https://openreview.net/pdf?id=vSVLM2j9eie">here</a>. Though listed in <a href="https://github.com/thuml/Time-Series-Library">Time-Series Library</a>, at time of this project, no comprehensive evaluation results were available.

The test data used in the paper have limited number of dimensions with relatively singificant seasonality (eg. Etth1 was a combination of of 7 hourly time-series data). This project aimed to experiment with the model and see how it would perform when the data is more complicated and the dimension is notably larger.

Stock return data from China A-shares from 2010 onwards was used. After several rounds of experiemnts, weak signals could be captured by the model, and the constructed portfolio (with predictions of 674 small-cap stocks) resulted in a 13.5-16.2% Annual PnL in the test set.
<p align="center">
<img src="https://github.com/austinmyc/CrossFormer/assets/59735570/222a6c68-1dcd-449a-a4cb-1f923743ece7">
<img src="https://github.com/austinmyc/CrossFormer/assets/59735570/b09a9b6b-a1c6-4244-8dc8-ae65d33c3032">
</p>

This repo aims to share the experiment process and the configuration used to generate such results. Data used in training are not provided here due to size issues (daily return + 13 factors of 3000+ stocks for 10 years). But you are welcome to experiment with different markets.

## Experiments

### Return type
- The style-adjusted return gives a much more stable and better results then using the market-adjusted return.

### Size
- Separated the stocks into three groups based on their average market cap throughout the training period.
- Result showed that the predictions for small cap are more accurate. (better bet on SIZE)

### Industry
- Trained mdoel with randomly permutation parameters, and no correlation could be found between the industry of the stock and the prediction errors.

### Imputation
- Comparing with the naive way of zero-filling, the imputation using cross-sectional market-cap group mean return gave the best results. (which basically means most stocks follow their subgroup with similar size)
- MICE was considered but not tested due to the computation efforts and no-look-ahead issues.

### Dimension reduction
- PCA and TSNE were both tested to incorporate factors into the analysis while maintaining the computation cost within capacity, but each new feature explained too few information of the original data, leading to a poor result.
![68c3c68c-b2b8-4cc0-8218-94f185f5bc52](https://github.com/austinmyc/CrossFormer/assets/59735570/69948cdc-1fe6-477a-a257-edf962642b95)
- A simple notebook for using TSNE is available <a href="https://github.com/austinmyc/TSNE">here</a>.


### Parameters Tuning

- The segment length `seg_len` was discussed specifically in the paper. Recall the computation complexity of the cross-time stage is $O(DL^2)$, where $L$ and $D$ represent the number of segments and dimensions respectively. And in tuning the model, the `seg_len` parameter  $L_{seg}$ represents the length of each segment, there the training complexity  $O(N)~\propto \frac{1}{L^2_{seg}}$, which means the smaller the parameter is, the longer it takes. The authors indeed experimented with different numbers of $L_{seg}$ on the ETTh1 dataset and suggested two values: 6 and 24 with relatively better results.Taking note of their suggestions and based on previous analysis, after trying out both options, the `seg_len` = 6 performed notably better in terms of IC and PnL.

- A range of input lengths in {96, 128, 168, 192} were tested, and the input length of 168 gave the best results. For output length, 1 was selected naturally as we can always generate 1-day forecast once at a time, and keep using the newly gained information to generate the next day's forecast instead of generating forecast for a long period at one go.
  
- Tests was also conducted on increasing the number of heads in the multi-head self-attention stage (`n_heads`), and the number of encoder layers (`e_layers`) to 5 and 4 respectively to potentially increase the ability to capture complex non-linear relationship, but the result was not promising.

- During the course of tuning the above parameters, it was noticed the training always goes into early stopping. This might be due to the fact that the signal is weak in the dataset, and it was difficult for the model to locate another minima after the first local minima was found. In this sense, noting the instability of the results, after tested with different learning rates and patience (which represents the number of early-stopping counter), a learning rate of 8e-5 and patience of 5 provides a generally better result while still being able to converge in a reasonable time.

## Files
If you wishs to train/evaluate a trained model, make sure you clone from <a href="https://github.com/Thinklab-SJTU/Crossformer">Crossformer</a> first.
- `args.json` contains the final model's configuration.
- `runinfo.txt` contains the training log
- `scale_statistic.pkl` contains the mean and std of the selected 674 stocks
- `genforecast.ipynb` contains a custom class to generate forecast using the model's defined functions. 


