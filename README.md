
<h2 align="center">Implementation of the <a href="https://github.com/Thinklab-SJTU/Crossformer">Crossformer</a> model for Stock Return Prediction</h2>
<p align="center">
<img src="https://img.shields.io/badge/python-3670A0?style=for-the-badge&logo=python&logoColor=ffdd54">
  <img src="https://img.shields.io/badge/PyTorch-%23EE4C2C.svg?style=for-the-badge&logo=PyTorch&logoColor=white">
</p>

<p align="center">
  <a href="#summary">Summary</a> •
  <a href="#experiments">Experiments</a> •
  <a href="#results">Results</a>
  
  
</p>



## Summary

Crossformer was published as a conference paper at ISLR 2023. Unlike traditional transformer-based models, it utilizes cross-dimension dependency for multivariate time series forecasting. The original paper can be found <a href="https://openreview.net/pdf?id=vSVLM2j9eie">here</a>. Though listed in <a href="https://github.com/thuml/Time-Series-Library">Time-Series Library</a>, at time of this project, no comprehensive evaluation results were available.

The test data used in the paper have limited number of dimensions with relatively singificant seasonality (eg. Etth1 was a combination of of 7 hourly time-series data). This project aimed to experiment with the model and see how it would perform when the data is more complicated and the dimension is notably larger.

Stock return data from China A-shares from 2010 onwards was used. After several rounds of experiemnts, weak signals could be captured by the model, and the constructed portfolio resulted in a 13.5-16.2% Annual PnL in the test set.
<p align="center">
<img src="https://github.com/austinmyc/CrossFormer/assets/59735570/222a6c68-1dcd-449a-a4cb-1f923743ece7">
<img src="https://github.com/austinmyc/CrossFormer/assets/59735570/b09a9b6b-a1c6-4244-8dc8-ae65d33c3032">
</p>

This repo aims to share the experiment process and the configuration used to generate such results. Data used in training are not provided here due to size issues. But you are welcome to experiment with different markets as well.

## Experiments

Imputation

Cap size

Dimension reduction

Tuning





## Results

model results on test set


