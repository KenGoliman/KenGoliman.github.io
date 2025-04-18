---
layout: page
title: Test import
subtitle: This is a test post
tags: [books, test]
author: Kennedy Goliman
---

```python
#Importing utilities
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
from mpl_toolkits.mplot3d import Axes3D
import seaborn as sns
import random
import statsmodels.api as sm
plt.rcParams['font.family'] = 'Serif'
plt.rcParams['font.sans-serif'] = ['Arial']
```


```python
#Load datasets
#Reading raw data from .xslx format
FY18_raw = pd.read_excel('/content/1_FY18AshMoisture.xlsx',header = 1)
FY19_raw_1 = pd.read_excel('/content/2_FY19SelfDegradation.xlsx',header = 1)
```

This notebook aims to answer some follow up comments from the Manuscript subjected under revision



```python
FY18 = FY18_raw.copy()
FY19_1 = FY19_raw_1.copy()
```


```python
#Checking dataset
#Uncomment as needed
#FY18.head()
#FY19_1.head()
```


```python
#Cleaning up and imputing data

#Standardizing Bale number

FY19_1['Bale No.'] = FY19_1['Bale No.'].str.replace('Bale - ','')
FY19_1['Bale No.'] = FY19_1['Bale No.'].str.replace('Bale-','') #Different format neccesitates extra code
FY19_1['Bale No.'] = FY19_1['Bale No.'].str.rstrip() #Different format neccesitates extra code
```


```python
#Coerce values to float
FY18['Bale No.'] = FY18['Bale No.'].apply(pd.to_numeric)
FY19_1['Bale No.'] = FY19_1['Bale No.'].apply(pd.to_numeric)
```


```python
#Checking dataset

#Uncomment as needed
#FY18
#FY19_1
```

----------
### Multivariate Non-Linear Analysis between corn stover properties and convertibility metrics

Question: 8.  Finally, one area that feels like a missed opportunity is the exploration of non-linear relationships between corn stover properties and convertibility metrics. A multivariate, non-linear analysis could provide deeper insight into potential interactions that a linear approach might overlook. Considering such an analysis before drawing conclusions could add an additional layer of robustness to the study.

We will be exploring any potential nonlinear relationships between corn stover properties (Moisture, Ash, Glucan, Lignin, CrI) compared to convertibility metrics (Glucose and Xylose, both treated and pre-treated)

We would also like to explore if a nonlinear relationship is more appropriate compared to a linear relationship. We will do so by fitting a linear and quadratic regression to the plot, then computing the residuals.


```python
# Plotting residual plots to check if a nonlinear relationship might be appropiate

#FY18

#R and P Glucose
col_x = ['Glucan (%)','Lignin (%)','CrI (%)']
col_y = ['R_Glucose yield (%)','P_Glucose yield (%)']
for i in col_x:
  for j in col_y:
    fig, ax = plt.subplots(2, 2,figsize=(10, 10))

    sns.regplot(data=FY18, x=i, y=j, ax = ax[0][0])
    sns.residplot(data=FY18, x=i, y=j, ax = ax[0][1]) #Get residual plot of linear fit
    model = sm.OLS(FY18[j], sm.add_constant(FY18[i])).fit()
    residuals = model.resid
    total_residual = np.sum(np.square(residuals)) #Retrieve total squared residual by accessing the same underlying model

    linear_corr = round(FY18[[i, j]].corr().iloc[0,1],4) #Get linear correlation coefficient
    quad_corr = round(FY18[[i, j]].corr(method = "kendall").iloc[0,1],4) #Get quadratic correlation coefficient using Kendall

    sns.regplot(data=FY18, x=i, y=j,order = 2, ax = ax[1][0])
    sns.residplot(data=FY18, x=i, y=j,order = 2, ax = ax[1][1]) #Get residual plot of quadratic fit
    coefficients, quad_residuals, _, _, _ = np.polyfit(FY18[i], FY18[j], 2, full=True) #Retrieve total residual from underlying polyfit model

    ax[0][0].set_title(f'Linear | Correlation: {linear_corr}')
    ax[0][1].set_title(f'Linear_Residuals: {total_residual}')
    ax[1][0].set_title(f'Quadratic | Correlation: {quad_corr}')
    ax[1][1].set_title(f'Quadratic_Residuals: {quad_residuals[0]}')

#R and P Xylose
col_x = ['Xylan (%)','Lignin (%)']
col_y = ['R_Xylose yield (%)','P_Xylose yield (%)']
for i in col_x:
  for j in col_y:
    fig, ax = plt.subplots(2, 2,figsize=(10, 10))

    sns.regplot(data=FY18, x=i, y=j, color = "red", ax = ax[0][0])
    sns.residplot(data=FY18, x=i, y=j, color = "red", ax = ax[0][1]) #Get residual plot of linear fit
    model = sm.OLS(FY18[j], sm.add_constant(FY18[i])).fit()
    residuals = model.resid
    total_residual = np.sum(np.square(residuals)) #Retrieve total squared residual by accessing the same underlying model

    linear_corr = round(FY18[[i, j]].corr().iloc[0,1],4) #Get linear correlation coefficient
    quad_corr = round(FY18[[i, j]].corr(method = "kendall").iloc[0,1],4) #Get quadratic correlation coefficient


    sns.regplot(data=FY18, x=i, y=j,order = 2, color = "red", ax = ax[1][0])
    sns.residplot(data=FY18, x=i, y=j,order = 2, color = "red", ax = ax[1][1]) #Get residual plot of quadratic fit
    coefficients, quad_residuals, _, _, _ = np.polyfit(FY18[i], FY18[j], 2, full=True) #Retrieve total residual from underlying polyfit model

    ax[0][0].set_title(f'Linear | Correlation: {linear_corr}')
    ax[0][1].set_title(f'Linear_Residuals: {total_residual}')
    ax[1][0].set_title(f'Quadratic | Correlation: {quad_corr}')
    ax[1][1].set_title(f'Quadratic_Residuals: {quad_residuals[0]}')

```




    '\ncol_x = [\'Ash\',\'Moisture\']\ncol_y = [\'R_Glucose yield (%)\',\'P_Glucose yield (%)\']\nfor i in col_x:\n  for j in col_y:\n    plt.figure()\n    fig, (ax1, ax2) = plt.subplots(1, 2, figsize=(10, 4))\n    sns.regplot(data=FY18, x=i, y=j,color = "red",ax = ax1)\n    sns.residplot(data=FY18, x=i, y=j,order = 2,ax = ax2)\n    ax1.set_title(\'Quadratic\')\n    ax2.set_title(\'Residuals\')\n    '




    
![png](/assets/project_images/Data_Discovery_Program_Follow_Up_files/Data_Discovery_Program_Follow_Up_12_1.png)
    



    
![png](Data_Discovery_Program_Follow_Up_files/Data_Discovery_Program_Follow_Up_12_2.png)
    



    
![png](Data_Discovery_Program_Follow_Up_files/Data_Discovery_Program_Follow_Up_12_3.png)
    



    
![png](Data_Discovery_Program_Follow_Up_files/Data_Discovery_Program_Follow_Up_12_4.png)
    



    
![png](Data_Discovery_Program_Follow_Up_files/Data_Discovery_Program_Follow_Up_12_5.png)
    



    
![png](Data_Discovery_Program_Follow_Up_files/Data_Discovery_Program_Follow_Up_12_6.png)
    



    
![png](Data_Discovery_Program_Follow_Up_files/Data_Discovery_Program_Follow_Up_12_7.png)
    



    
![png](Data_Discovery_Program_Follow_Up_files/Data_Discovery_Program_Follow_Up_12_8.png)
    



    
![png](Data_Discovery_Program_Follow_Up_files/Data_Discovery_Program_Follow_Up_12_9.png)
    



    
![png](Data_Discovery_Program_Follow_Up_files/Data_Discovery_Program_Follow_Up_12_10.png)
    



```python
# FY19_1

# Plotting residual plots to check if a nonlinear relationship might be appropiate

#R and P Glucose
col_x = ['Glucan (%)','Lignin (%)','CrI (%)']
col_y = ['R_Glucose yield (%)','P_Glucose yield (%)']
for i in col_x:
  for j in col_y:
    fig, ax = plt.subplots(2, 2,figsize=(10, 10))

    sns.regplot(data=FY19_1, x=i, y=j, ax = ax[0][0])
    sns.residplot(data=FY19_1, x=i, y=j, ax = ax[0][1]) #Get residual plot of linear fit
    model = sm.OLS(FY19_1[j], sm.add_constant(FY19_1[i])).fit()
    residuals = model.resid
    total_residual = np.sum(np.square(residuals)) #Retrieve total squared residual by accessing the same underlying model

    linear_corr = round(FY19_1[[i, j]].corr().iloc[0,1],4) #Get linear correlation coefficient
    quad_corr = round(FY19_1[[i, j]].corr(method = "kendall").iloc[0,1],4) #Get quadratic correlation coefficient


    sns.regplot(data=FY19_1, x=i, y=j,order = 2, ax = ax[1][0])
    sns.residplot(data=FY19_1, x=i, y=j,order = 2, ax = ax[1][1]) #Get residual plot of quadratic fit
    coefficients, quad_residuals, _, _, _ = np.polyfit(FY19_1[i], FY19_1[j], 2, full=True) #Retrieve total residual from underlying polyfit model

    ax[0][0].set_title(f'Linear | Correlation: {linear_corr}')
    ax[0][1].set_title(f'Linear_Residuals: {total_residual}')
    ax[1][0].set_title(f'Quadratic | Correlation: {quad_corr}')
    ax[1][1].set_title(f'Quadratic_Residuals: {quad_residuals[0]}')

#R and P Xylose
col_x = ['Xylan (%)','Lignin (%)']
col_y = ['R_Xylose yield (%)','P_Xylose yield (%)']
for i in col_x:
  for j in col_y:
    fig, ax = plt.subplots(2, 2,figsize=(10, 10))

    sns.regplot(data=FY19_1, x=i, y=j, color = "red", ax = ax[0][0])
    sns.residplot(data=FY19_1, x=i, y=j, color = "red", ax = ax[0][1]) #Get residual plot of linear fit
    model = sm.OLS(FY19_1[j], sm.add_constant(FY19_1[i])).fit()
    residuals = model.resid
    total_residual = np.sum(np.square(residuals)) #Retrieve total squared residual by accessing the same underlying model

    linear_corr = round(FY19_1[[i, j]].corr().iloc[0,1],4) #Get linear correlation coefficient
    quad_corr = round(FY19_1[[i, j]].corr(method = "kendall").iloc[0,1],4) #Get quadratic correlation coefficient


    sns.regplot(data=FY19_1, x=i, y=j,order = 2, color = "red", ax = ax[1][0])
    sns.residplot(data=FY19_1, x=i, y=j,order = 2, color = "red", ax = ax[1][1]) #Get residual plot of quadratic fit
    coefficients, quad_residuals, _, _, _ = np.polyfit(FY19_1[i], FY19_1[j], 2, full=True) #Retrieve total residual from underlying polyfit model

    ax[0][0].set_title(f'Linear | Correlation: {linear_corr}')
    ax[0][1].set_title(f'Linear_Residuals: {total_residual}')
    ax[1][0].set_title(f'Quadratic | Correlation: {quad_corr}')
    ax[1][1].set_title(f'Quadratic_Residuals: {quad_residuals[0]}')

```




    '\ncol_x = [\'Ash\',\'Moisture\']\ncol_y = [\'R_Glucose yield (%)\',\'P_Glucose yield (%)\']\nfor i in col_x:\n  for j in col_y:\n    plt.figure()\n    fig, (ax1, ax2) = plt.subplots(1, 2, figsize=(10, 4))\n    sns.regplot(data=FY19_1, x=i, y=j,color = "red",ax = ax1)\n    sns.residplot(data=FY19_1, x=i, y=j,order = 2,ax = ax2)\n    ax1.set_title(\'Quadratic\')\n    ax2.set_title(\'Residuals\')\n    '




    
![png](Data_Discovery_Program_Follow_Up_files/Data_Discovery_Program_Follow_Up_13_1.png)
    



    
![png](Data_Discovery_Program_Follow_Up_files/Data_Discovery_Program_Follow_Up_13_2.png)
    



    
![png](Data_Discovery_Program_Follow_Up_files/Data_Discovery_Program_Follow_Up_13_3.png)
    



    
![png](Data_Discovery_Program_Follow_Up_files/Data_Discovery_Program_Follow_Up_13_4.png)
    



    
![png](Data_Discovery_Program_Follow_Up_files/Data_Discovery_Program_Follow_Up_13_5.png)
    



    
![png](Data_Discovery_Program_Follow_Up_files/Data_Discovery_Program_Follow_Up_13_6.png)
    



    
![png](Data_Discovery_Program_Follow_Up_files/Data_Discovery_Program_Follow_Up_13_7.png)
    



    
![png](Data_Discovery_Program_Follow_Up_files/Data_Discovery_Program_Follow_Up_13_8.png)
    



    
![png](Data_Discovery_Program_Follow_Up_files/Data_Discovery_Program_Follow_Up_13_9.png)
    



    
![png](Data_Discovery_Program_Follow_Up_files/Data_Discovery_Program_Follow_Up_13_10.png)
    


For most of the relationships, a linear relationship is a better fit compared to a nonlinear relationship. In some cases, particularly the relationship between Glucan against R_Glucose and Lignin against R/P_Xylose, the resulting residual plot from the nonlinear relationship
Glucan R Glucose 18
Lignin R Xylose 18 mostly
Lignin P Xylose

Lignin R Glucose


```python
#Check again for the combined dataset of FY_18 and FY19_1

FY_18_cols = FY18[['Glucan (%)','Xylan (%)','Lignin (%)','CrI (%)','R_Glucose yield (%)','P_Glucose yield (%)','R_Xylose yield (%)','P_Xylose yield (%)']]
FY19_1_cols = FY19_1[['Glucan (%)','Xylan (%)','Lignin (%)','CrI (%)','R_Glucose yield (%)','P_Glucose yield (%)','R_Xylose yield (%)','P_Xylose yield (%)']]
FY_both = pd.concat([FY_18_cols,FY19_1_cols])
FY_both
```





  <div id="df-3f8f5f4b-e503-401f-b9fc-2c6912533ca5" class="colab-df-container">
    <div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Glucan (%)</th>
      <th>Xylan (%)</th>
      <th>Lignin (%)</th>
      <th>CrI (%)</th>
      <th>R_Glucose yield (%)</th>
      <th>P_Glucose yield (%)</th>
      <th>R_Xylose yield (%)</th>
      <th>P_Xylose yield (%)</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>36.3500</td>
      <td>21.3450</td>
      <td>17.8500</td>
      <td>54.952036</td>
      <td>22.517722</td>
      <td>40.803508</td>
      <td>26.039243</td>
      <td>29.992964</td>
    </tr>
    <tr>
      <th>1</th>
      <td>36.3500</td>
      <td>21.3450</td>
      <td>17.8500</td>
      <td>52.147885</td>
      <td>23.253550</td>
      <td>21.781925</td>
      <td>28.867274</td>
      <td>16.633955</td>
    </tr>
    <tr>
      <th>2</th>
      <td>36.3500</td>
      <td>21.3450</td>
      <td>17.8500</td>
      <td>53.549960</td>
      <td>21.659765</td>
      <td>27.585322</td>
      <td>27.354734</td>
      <td>20.697113</td>
    </tr>
    <tr>
      <th>3</th>
      <td>36.0950</td>
      <td>18.2600</td>
      <td>17.4750</td>
      <td>56.708654</td>
      <td>19.941080</td>
      <td>30.368772</td>
      <td>32.624562</td>
      <td>26.122013</td>
    </tr>
    <tr>
      <th>4</th>
      <td>36.0950</td>
      <td>18.2600</td>
      <td>17.4750</td>
      <td>55.790528</td>
      <td>19.694604</td>
      <td>24.890600</td>
      <td>31.091282</td>
      <td>20.128008</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>37</th>
      <td>36.1629</td>
      <td>19.0531</td>
      <td>16.0780</td>
      <td>56.518263</td>
      <td>24.922164</td>
      <td>47.109024</td>
      <td>26.172264</td>
      <td>30.788852</td>
    </tr>
    <tr>
      <th>38</th>
      <td>36.1629</td>
      <td>19.0531</td>
      <td>16.0780</td>
      <td>53.582305</td>
      <td>24.322366</td>
      <td>26.884609</td>
      <td>27.808838</td>
      <td>17.266754</td>
    </tr>
    <tr>
      <th>39</th>
      <td>31.9394</td>
      <td>11.6570</td>
      <td>22.3155</td>
      <td>56.997805</td>
      <td>35.153385</td>
      <td>37.668597</td>
      <td>75.922428</td>
      <td>71.463700</td>
    </tr>
    <tr>
      <th>40</th>
      <td>31.9394</td>
      <td>11.6570</td>
      <td>22.3155</td>
      <td>58.872508</td>
      <td>35.907197</td>
      <td>25.111934</td>
      <td>77.174968</td>
      <td>47.936470</td>
    </tr>
    <tr>
      <th>41</th>
      <td>31.9394</td>
      <td>11.6570</td>
      <td>22.3155</td>
      <td>57.935156</td>
      <td>36.930940</td>
      <td>22.564388</td>
      <td>79.277779</td>
      <td>41.967861</td>
    </tr>
  </tbody>
</table>
<p>72 rows × 8 columns</p>
</div>
    <div class="colab-df-buttons">

  <div class="colab-df-container">
    <button class="colab-df-convert" onclick="convertToInteractive('df-3f8f5f4b-e503-401f-b9fc-2c6912533ca5')"
            title="Convert this dataframe to an interactive table."
            style="display:none;">

  <svg xmlns="http://www.w3.org/2000/svg" height="24px" viewBox="0 -960 960 960">
    <path d="M120-120v-720h720v720H120Zm60-500h600v-160H180v160Zm220 220h160v-160H400v160Zm0 220h160v-160H400v160ZM180-400h160v-160H180v160Zm440 0h160v-160H620v160ZM180-180h160v-160H180v160Zm440 0h160v-160H620v160Z"/>
  </svg>
    </button>

  <style>
    .colab-df-container {
      display:flex;
      gap: 12px;
    }

    .colab-df-convert {
      background-color: #E8F0FE;
      border: none;
      border-radius: 50%;
      cursor: pointer;
      display: none;
      fill: #1967D2;
      height: 32px;
      padding: 0 0 0 0;
      width: 32px;
    }

    .colab-df-convert:hover {
      background-color: #E2EBFA;
      box-shadow: 0px 1px 2px rgba(60, 64, 67, 0.3), 0px 1px 3px 1px rgba(60, 64, 67, 0.15);
      fill: #174EA6;
    }

    .colab-df-buttons div {
      margin-bottom: 4px;
    }

    [theme=dark] .colab-df-convert {
      background-color: #3B4455;
      fill: #D2E3FC;
    }

    [theme=dark] .colab-df-convert:hover {
      background-color: #434B5C;
      box-shadow: 0px 1px 3px 1px rgba(0, 0, 0, 0.15);
      filter: drop-shadow(0px 1px 2px rgba(0, 0, 0, 0.3));
      fill: #FFFFFF;
    }
  </style>

    <script>
      const buttonEl =
        document.querySelector('#df-3f8f5f4b-e503-401f-b9fc-2c6912533ca5 button.colab-df-convert');
      buttonEl.style.display =
        google.colab.kernel.accessAllowed ? 'block' : 'none';

      async function convertToInteractive(key) {
        const element = document.querySelector('#df-3f8f5f4b-e503-401f-b9fc-2c6912533ca5');
        const dataTable =
          await google.colab.kernel.invokeFunction('convertToInteractive',
                                                    [key], {});
        if (!dataTable) return;

        const docLinkHtml = 'Like what you see? Visit the ' +
          '<a target="_blank" href=https://colab.research.google.com/notebooks/data_table.ipynb>data table notebook</a>'
          + ' to learn more about interactive tables.';
        element.innerHTML = '';
        dataTable['output_type'] = 'display_data';
        await google.colab.output.renderOutput(dataTable, element);
        const docLink = document.createElement('div');
        docLink.innerHTML = docLinkHtml;
        element.appendChild(docLink);
      }
    </script>
  </div>


<div id="df-1e95c9f7-1e15-4dcf-9c88-4beffb602269">
  <button class="colab-df-quickchart" onclick="quickchart('df-1e95c9f7-1e15-4dcf-9c88-4beffb602269')"
            title="Suggest charts"
            style="display:none;">

<svg xmlns="http://www.w3.org/2000/svg" height="24px"viewBox="0 0 24 24"
     width="24px">
    <g>
        <path d="M19 3H5c-1.1 0-2 .9-2 2v14c0 1.1.9 2 2 2h14c1.1 0 2-.9 2-2V5c0-1.1-.9-2-2-2zM9 17H7v-7h2v7zm4 0h-2V7h2v10zm4 0h-2v-4h2v4z"/>
    </g>
</svg>
  </button>

<style>
  .colab-df-quickchart {
      --bg-color: #E8F0FE;
      --fill-color: #1967D2;
      --hover-bg-color: #E2EBFA;
      --hover-fill-color: #174EA6;
      --disabled-fill-color: #AAA;
      --disabled-bg-color: #DDD;
  }

  [theme=dark] .colab-df-quickchart {
      --bg-color: #3B4455;
      --fill-color: #D2E3FC;
      --hover-bg-color: #434B5C;
      --hover-fill-color: #FFFFFF;
      --disabled-bg-color: #3B4455;
      --disabled-fill-color: #666;
  }

  .colab-df-quickchart {
    background-color: var(--bg-color);
    border: none;
    border-radius: 50%;
    cursor: pointer;
    display: none;
    fill: var(--fill-color);
    height: 32px;
    padding: 0;
    width: 32px;
  }

  .colab-df-quickchart:hover {
    background-color: var(--hover-bg-color);
    box-shadow: 0 1px 2px rgba(60, 64, 67, 0.3), 0 1px 3px 1px rgba(60, 64, 67, 0.15);
    fill: var(--button-hover-fill-color);
  }

  .colab-df-quickchart-complete:disabled,
  .colab-df-quickchart-complete:disabled:hover {
    background-color: var(--disabled-bg-color);
    fill: var(--disabled-fill-color);
    box-shadow: none;
  }

  .colab-df-spinner {
    border: 2px solid var(--fill-color);
    border-color: transparent;
    border-bottom-color: var(--fill-color);
    animation:
      spin 1s steps(1) infinite;
  }

  @keyframes spin {
    0% {
      border-color: transparent;
      border-bottom-color: var(--fill-color);
      border-left-color: var(--fill-color);
    }
    20% {
      border-color: transparent;
      border-left-color: var(--fill-color);
      border-top-color: var(--fill-color);
    }
    30% {
      border-color: transparent;
      border-left-color: var(--fill-color);
      border-top-color: var(--fill-color);
      border-right-color: var(--fill-color);
    }
    40% {
      border-color: transparent;
      border-right-color: var(--fill-color);
      border-top-color: var(--fill-color);
    }
    60% {
      border-color: transparent;
      border-right-color: var(--fill-color);
    }
    80% {
      border-color: transparent;
      border-right-color: var(--fill-color);
      border-bottom-color: var(--fill-color);
    }
    90% {
      border-color: transparent;
      border-bottom-color: var(--fill-color);
    }
  }
</style>

  <script>
    async function quickchart(key) {
      const quickchartButtonEl =
        document.querySelector('#' + key + ' button');
      quickchartButtonEl.disabled = true;  // To prevent multiple clicks.
      quickchartButtonEl.classList.add('colab-df-spinner');
      try {
        const charts = await google.colab.kernel.invokeFunction(
            'suggestCharts', [key], {});
      } catch (error) {
        console.error('Error during call to suggestCharts:', error);
      }
      quickchartButtonEl.classList.remove('colab-df-spinner');
      quickchartButtonEl.classList.add('colab-df-quickchart-complete');
    }
    (() => {
      let quickchartButtonEl =
        document.querySelector('#df-1e95c9f7-1e15-4dcf-9c88-4beffb602269 button');
      quickchartButtonEl.style.display =
        google.colab.kernel.accessAllowed ? 'block' : 'none';
    })();
  </script>
</div>

  <div id="id_a667f451-8d35-4cf6-9364-cbcefefb1c83">
    <style>
      .colab-df-generate {
        background-color: #E8F0FE;
        border: none;
        border-radius: 50%;
        cursor: pointer;
        display: none;
        fill: #1967D2;
        height: 32px;
        padding: 0 0 0 0;
        width: 32px;
      }

      .colab-df-generate:hover {
        background-color: #E2EBFA;
        box-shadow: 0px 1px 2px rgba(60, 64, 67, 0.3), 0px 1px 3px 1px rgba(60, 64, 67, 0.15);
        fill: #174EA6;
      }

      [theme=dark] .colab-df-generate {
        background-color: #3B4455;
        fill: #D2E3FC;
      }

      [theme=dark] .colab-df-generate:hover {
        background-color: #434B5C;
        box-shadow: 0px 1px 3px 1px rgba(0, 0, 0, 0.15);
        filter: drop-shadow(0px 1px 2px rgba(0, 0, 0, 0.3));
        fill: #FFFFFF;
      }
    </style>
    <button class="colab-df-generate" onclick="generateWithVariable('FY_both')"
            title="Generate code using this dataframe."
            style="display:none;">

  <svg xmlns="http://www.w3.org/2000/svg" height="24px"viewBox="0 0 24 24"
       width="24px">
    <path d="M7,19H8.4L18.45,9,17,7.55,7,17.6ZM5,21V16.75L18.45,3.32a2,2,0,0,1,2.83,0l1.4,1.43a1.91,1.91,0,0,1,.58,1.4,1.91,1.91,0,0,1-.58,1.4L9.25,21ZM18.45,9,17,7.55Zm-12,3A5.31,5.31,0,0,0,4.9,8.1,5.31,5.31,0,0,0,1,6.5,5.31,5.31,0,0,0,4.9,4.9,5.31,5.31,0,0,0,6.5,1,5.31,5.31,0,0,0,8.1,4.9,5.31,5.31,0,0,0,12,6.5,5.46,5.46,0,0,0,6.5,12Z"/>
  </svg>
    </button>
    <script>
      (() => {
      const buttonEl =
        document.querySelector('#id_a667f451-8d35-4cf6-9364-cbcefefb1c83 button.colab-df-generate');
      buttonEl.style.display =
        google.colab.kernel.accessAllowed ? 'block' : 'none';

      buttonEl.onclick = () => {
        google.colab.notebook.generateWithVariable('FY_both');
      }
      })();
    </script>
  </div>

    </div>
  </div>





```python
# FY_both

# Plotting residual plots to check if a nonlinear relationship might be appropiate

#R and P Glucose
col_x = ['Glucan (%)','Lignin (%)','CrI (%)']
col_y = ['R_Glucose yield (%)','P_Glucose yield (%)']
for i in col_x:
  for j in col_y:
    fig, ax = plt.subplots(2, 2,figsize=(10, 10))
    sns.regplot(data=FY_both, x=i, y=j, ax = ax[0][0])
    sns.residplot(data=FY_both, x=i, y=j, ax = ax[0][1]) #Get residual plot of linear fit
    model = sm.OLS(FY_both[j], sm.add_constant(FY_both[i])).fit()
    residuals = model.resid
    total_residual = np.sum(np.square(residuals)) #Retrieve total squared residual by accessing the same underlying model

    linear_corr = round(FY_both[[i, j]].corr().iloc[0,1],4) #Get linear correlation coefficient
    quad_corr = round(FY_both[[i, j]].corr(method = "kendall").iloc[0,1],4) #Get quadratic correlation coefficient


    sns.regplot(data=FY_both, x=i, y=j,order = 2, ax = ax[1][0])
    sns.residplot(data=FY_both, x=i, y=j,order = 2, ax = ax[1][1]) #Get residual plot of quadratic fit
    coefficients, quad_residuals, _, _, _ = np.polyfit(FY_both[i], FY_both[j], 2, full=True) #Retrieve total residual from underlying polyfit model

    ax[0][0].set_title(f'Linear | Correlation: {linear_corr}')
    ax[0][1].set_title(f'Linear_Residuals: {total_residual}')
    ax[1][0].set_title(f'Quadratic | Correlation: {quad_corr}')
    ax[1][1].set_title(f'Quadratic_Residuals: {quad_residuals[0]}')

#R and P Xylose
col_x = ['Xylan (%)','Lignin (%)']
col_y = ['R_Xylose yield (%)','P_Xylose yield (%)']
for i in col_x:
  for j in col_y:
    fig, ax = plt.subplots(2, 2,figsize=(10, 10))
    sns.regplot(data=FY_both, x=i, y=j, color = "red", ax = ax[0][0])
    sns.residplot(data=FY_both, x=i, y=j, color = "red", ax = ax[0][1]) #Get residual plot of linear fit
    model = sm.OLS(FY_both[j], sm.add_constant(FY_both[i])).fit()
    residuals = model.resid
    total_residual = np.sum(np.square(residuals)) #Retrieve total squared residual by accessing the same underlying model

    linear_corr = round(FY_both[[i, j]].corr().iloc[0,1],4) #Get linear correlation coefficient
    quad_corr = round(FY_both[[i, j]].corr(method = "kendall").iloc[0,1],4) #Get quadratic correlation coefficient


    sns.regplot(data=FY_both, x=i, y=j,order = 2, color = "red", ax = ax[1][0])
    sns.residplot(data=FY_both, x=i, y=j,order = 2, color = "red", ax = ax[1][1]) #Get residual plot of quadratic fit
    coefficients, quad_residuals, _, _, _ = np.polyfit(FY_both[i], FY_both[j], 2, full=True) #Retrieve total residual from underlying polyfit model

    ax[0][0].set_title(f'Linear | Correlation: {linear_corr}')
    ax[0][1].set_title(f'Linear_Residuals: {total_residual}')
    ax[1][0].set_title(f'Quadratic | Correlation: {quad_corr}')
    ax[1][1].set_title(f'Quadratic_Residuals: {quad_residuals[0]}')

```




    '\ncol_x = [\'Ash\',\'Moisture\']\ncol_y = [\'R_Glucose yield (%)\',\'P_Glucose yield (%)\']\nfor i in col_x:\n  for j in col_y:\n    plt.figure()\n    fig, (ax1, ax2) = plt.subplots(1, 2, figsize=(10, 4))\n    sns.regplot(data=FY19_1, x=i, y=j,color = "red",ax = ax1)\n    sns.residplot(data=FY19_1, x=i, y=j,order = 2,ax = ax2)\n    ax1.set_title(\'Quadratic\')\n    ax2.set_title(\'Residuals\')\n    '




    
![png](Data_Discovery_Program_Follow_Up_files/Data_Discovery_Program_Follow_Up_16_1.png)
    



    
![png](Data_Discovery_Program_Follow_Up_files/Data_Discovery_Program_Follow_Up_16_2.png)
    



    
![png](Data_Discovery_Program_Follow_Up_files/Data_Discovery_Program_Follow_Up_16_3.png)
    



    
![png](Data_Discovery_Program_Follow_Up_files/Data_Discovery_Program_Follow_Up_16_4.png)
    



    
![png](Data_Discovery_Program_Follow_Up_files/Data_Discovery_Program_Follow_Up_16_5.png)
    



    
![png](Data_Discovery_Program_Follow_Up_files/Data_Discovery_Program_Follow_Up_16_6.png)
    



    
![png](Data_Discovery_Program_Follow_Up_files/Data_Discovery_Program_Follow_Up_16_7.png)
    



    
![png](Data_Discovery_Program_Follow_Up_files/Data_Discovery_Program_Follow_Up_16_8.png)
    



    
![png](Data_Discovery_Program_Follow_Up_files/Data_Discovery_Program_Follow_Up_16_9.png)
    



    
![png](Data_Discovery_Program_Follow_Up_files/Data_Discovery_Program_Follow_Up_16_10.png)
    


Observations: Some graph doesn't seem to exhibit much of a difference if we're to take a nonlinear relationship, like Glucan vs R_Glucose. This indicates that a linear relationship is sufficient.

Some other graph exhibits a much better fit under a nonlinear relationship, like Lignin vs R, indicating that these relationships are better showcased through a nonlinear relationship compared to a linear one

-------------
### Checking for significance between Nitrogen Content

4.  A comparison of nitrogen (N) content across moisture content groups is mentioned, but it is unclear whether the differences were statistically significant. Clarifying this point with explicit statistical results would strengthen the argument.

We will be exploring wether or not any statistical significance exist between the Nitrogen content of different bales. We will be looking at how different ash and moisture content affects Nitrogen content.


```python
# First, extract the datas needed

nitrogen_data_check = {
    'Bale No.': [3,5,1,6,2,3,6,11,2,5],
    'Ash': ['Low','Low','Low','Low','High','High','High','High','High','High'],
    'Moisture': ['Low','Low','High','High','Low','Low','Low','Low','High','High'],
    'Nitrogen_mean': [0.57, 0.48, 0.59, 0.65, 0.60, 0.58, 0.71, 0.52, 0.75, 0.64]
}
nitrogen_check = pd.DataFrame(nitrogen_data_check)
nitrogen_check
```





  <div id="df-b1686c36-9079-4885-b4e6-948e7c320fde" class="colab-df-container">
    <div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Bale No.</th>
      <th>Ash</th>
      <th>Moisture</th>
      <th>Nitrogen_mean</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>3</td>
      <td>Low</td>
      <td>Low</td>
      <td>0.57</td>
    </tr>
    <tr>
      <th>1</th>
      <td>5</td>
      <td>Low</td>
      <td>Low</td>
      <td>0.48</td>
    </tr>
    <tr>
      <th>2</th>
      <td>1</td>
      <td>Low</td>
      <td>High</td>
      <td>0.59</td>
    </tr>
    <tr>
      <th>3</th>
      <td>6</td>
      <td>Low</td>
      <td>High</td>
      <td>0.65</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2</td>
      <td>High</td>
      <td>Low</td>
      <td>0.60</td>
    </tr>
    <tr>
      <th>5</th>
      <td>3</td>
      <td>High</td>
      <td>Low</td>
      <td>0.58</td>
    </tr>
    <tr>
      <th>6</th>
      <td>6</td>
      <td>High</td>
      <td>Low</td>
      <td>0.71</td>
    </tr>
    <tr>
      <th>7</th>
      <td>11</td>
      <td>High</td>
      <td>Low</td>
      <td>0.52</td>
    </tr>
    <tr>
      <th>8</th>
      <td>2</td>
      <td>High</td>
      <td>High</td>
      <td>0.75</td>
    </tr>
    <tr>
      <th>9</th>
      <td>5</td>
      <td>High</td>
      <td>High</td>
      <td>0.64</td>
    </tr>
  </tbody>
</table>
</div>
    <div class="colab-df-buttons">

  <div class="colab-df-container">
    <button class="colab-df-convert" onclick="convertToInteractive('df-b1686c36-9079-4885-b4e6-948e7c320fde')"
            title="Convert this dataframe to an interactive table."
            style="display:none;">

  <svg xmlns="http://www.w3.org/2000/svg" height="24px" viewBox="0 -960 960 960">
    <path d="M120-120v-720h720v720H120Zm60-500h600v-160H180v160Zm220 220h160v-160H400v160Zm0 220h160v-160H400v160ZM180-400h160v-160H180v160Zm440 0h160v-160H620v160ZM180-180h160v-160H180v160Zm440 0h160v-160H620v160Z"/>
  </svg>
    </button>

  <style>
    .colab-df-container {
      display:flex;
      gap: 12px;
    }

    .colab-df-convert {
      background-color: #E8F0FE;
      border: none;
      border-radius: 50%;
      cursor: pointer;
      display: none;
      fill: #1967D2;
      height: 32px;
      padding: 0 0 0 0;
      width: 32px;
    }

    .colab-df-convert:hover {
      background-color: #E2EBFA;
      box-shadow: 0px 1px 2px rgba(60, 64, 67, 0.3), 0px 1px 3px 1px rgba(60, 64, 67, 0.15);
      fill: #174EA6;
    }

    .colab-df-buttons div {
      margin-bottom: 4px;
    }

    [theme=dark] .colab-df-convert {
      background-color: #3B4455;
      fill: #D2E3FC;
    }

    [theme=dark] .colab-df-convert:hover {
      background-color: #434B5C;
      box-shadow: 0px 1px 3px 1px rgba(0, 0, 0, 0.15);
      filter: drop-shadow(0px 1px 2px rgba(0, 0, 0, 0.3));
      fill: #FFFFFF;
    }
  </style>

    <script>
      const buttonEl =
        document.querySelector('#df-b1686c36-9079-4885-b4e6-948e7c320fde button.colab-df-convert');
      buttonEl.style.display =
        google.colab.kernel.accessAllowed ? 'block' : 'none';

      async function convertToInteractive(key) {
        const element = document.querySelector('#df-b1686c36-9079-4885-b4e6-948e7c320fde');
        const dataTable =
          await google.colab.kernel.invokeFunction('convertToInteractive',
                                                    [key], {});
        if (!dataTable) return;

        const docLinkHtml = 'Like what you see? Visit the ' +
          '<a target="_blank" href=https://colab.research.google.com/notebooks/data_table.ipynb>data table notebook</a>'
          + ' to learn more about interactive tables.';
        element.innerHTML = '';
        dataTable['output_type'] = 'display_data';
        await google.colab.output.renderOutput(dataTable, element);
        const docLink = document.createElement('div');
        docLink.innerHTML = docLinkHtml;
        element.appendChild(docLink);
      }
    </script>
  </div>


<div id="df-ec5b7d43-d1da-4e7a-b9c0-392031afd6df">
  <button class="colab-df-quickchart" onclick="quickchart('df-ec5b7d43-d1da-4e7a-b9c0-392031afd6df')"
            title="Suggest charts"
            style="display:none;">

<svg xmlns="http://www.w3.org/2000/svg" height="24px"viewBox="0 0 24 24"
     width="24px">
    <g>
        <path d="M19 3H5c-1.1 0-2 .9-2 2v14c0 1.1.9 2 2 2h14c1.1 0 2-.9 2-2V5c0-1.1-.9-2-2-2zM9 17H7v-7h2v7zm4 0h-2V7h2v10zm4 0h-2v-4h2v4z"/>
    </g>
</svg>
  </button>

<style>
  .colab-df-quickchart {
      --bg-color: #E8F0FE;
      --fill-color: #1967D2;
      --hover-bg-color: #E2EBFA;
      --hover-fill-color: #174EA6;
      --disabled-fill-color: #AAA;
      --disabled-bg-color: #DDD;
  }

  [theme=dark] .colab-df-quickchart {
      --bg-color: #3B4455;
      --fill-color: #D2E3FC;
      --hover-bg-color: #434B5C;
      --hover-fill-color: #FFFFFF;
      --disabled-bg-color: #3B4455;
      --disabled-fill-color: #666;
  }

  .colab-df-quickchart {
    background-color: var(--bg-color);
    border: none;
    border-radius: 50%;
    cursor: pointer;
    display: none;
    fill: var(--fill-color);
    height: 32px;
    padding: 0;
    width: 32px;
  }

  .colab-df-quickchart:hover {
    background-color: var(--hover-bg-color);
    box-shadow: 0 1px 2px rgba(60, 64, 67, 0.3), 0 1px 3px 1px rgba(60, 64, 67, 0.15);
    fill: var(--button-hover-fill-color);
  }

  .colab-df-quickchart-complete:disabled,
  .colab-df-quickchart-complete:disabled:hover {
    background-color: var(--disabled-bg-color);
    fill: var(--disabled-fill-color);
    box-shadow: none;
  }

  .colab-df-spinner {
    border: 2px solid var(--fill-color);
    border-color: transparent;
    border-bottom-color: var(--fill-color);
    animation:
      spin 1s steps(1) infinite;
  }

  @keyframes spin {
    0% {
      border-color: transparent;
      border-bottom-color: var(--fill-color);
      border-left-color: var(--fill-color);
    }
    20% {
      border-color: transparent;
      border-left-color: var(--fill-color);
      border-top-color: var(--fill-color);
    }
    30% {
      border-color: transparent;
      border-left-color: var(--fill-color);
      border-top-color: var(--fill-color);
      border-right-color: var(--fill-color);
    }
    40% {
      border-color: transparent;
      border-right-color: var(--fill-color);
      border-top-color: var(--fill-color);
    }
    60% {
      border-color: transparent;
      border-right-color: var(--fill-color);
    }
    80% {
      border-color: transparent;
      border-right-color: var(--fill-color);
      border-bottom-color: var(--fill-color);
    }
    90% {
      border-color: transparent;
      border-bottom-color: var(--fill-color);
    }
  }
</style>

  <script>
    async function quickchart(key) {
      const quickchartButtonEl =
        document.querySelector('#' + key + ' button');
      quickchartButtonEl.disabled = true;  // To prevent multiple clicks.
      quickchartButtonEl.classList.add('colab-df-spinner');
      try {
        const charts = await google.colab.kernel.invokeFunction(
            'suggestCharts', [key], {});
      } catch (error) {
        console.error('Error during call to suggestCharts:', error);
      }
      quickchartButtonEl.classList.remove('colab-df-spinner');
      quickchartButtonEl.classList.add('colab-df-quickchart-complete');
    }
    (() => {
      let quickchartButtonEl =
        document.querySelector('#df-ec5b7d43-d1da-4e7a-b9c0-392031afd6df button');
      quickchartButtonEl.style.display =
        google.colab.kernel.accessAllowed ? 'block' : 'none';
    })();
  </script>
</div>

  <div id="id_ca29d582-05a2-4ac7-b62f-dfa9ac0a2ebc">
    <style>
      .colab-df-generate {
        background-color: #E8F0FE;
        border: none;
        border-radius: 50%;
        cursor: pointer;
        display: none;
        fill: #1967D2;
        height: 32px;
        padding: 0 0 0 0;
        width: 32px;
      }

      .colab-df-generate:hover {
        background-color: #E2EBFA;
        box-shadow: 0px 1px 2px rgba(60, 64, 67, 0.3), 0px 1px 3px 1px rgba(60, 64, 67, 0.15);
        fill: #174EA6;
      }

      [theme=dark] .colab-df-generate {
        background-color: #3B4455;
        fill: #D2E3FC;
      }

      [theme=dark] .colab-df-generate:hover {
        background-color: #434B5C;
        box-shadow: 0px 1px 3px 1px rgba(0, 0, 0, 0.15);
        filter: drop-shadow(0px 1px 2px rgba(0, 0, 0, 0.3));
        fill: #FFFFFF;
      }
    </style>
    <button class="colab-df-generate" onclick="generateWithVariable('nitrogen_check')"
            title="Generate code using this dataframe."
            style="display:none;">

  <svg xmlns="http://www.w3.org/2000/svg" height="24px"viewBox="0 0 24 24"
       width="24px">
    <path d="M7,19H8.4L18.45,9,17,7.55,7,17.6ZM5,21V16.75L18.45,3.32a2,2,0,0,1,2.83,0l1.4,1.43a1.91,1.91,0,0,1,.58,1.4,1.91,1.91,0,0,1-.58,1.4L9.25,21ZM18.45,9,17,7.55Zm-12,3A5.31,5.31,0,0,0,4.9,8.1,5.31,5.31,0,0,0,1,6.5,5.31,5.31,0,0,0,4.9,4.9,5.31,5.31,0,0,0,6.5,1,5.31,5.31,0,0,0,8.1,4.9,5.31,5.31,0,0,0,12,6.5,5.46,5.46,0,0,0,6.5,12Z"/>
  </svg>
    </button>
    <script>
      (() => {
      const buttonEl =
        document.querySelector('#id_ca29d582-05a2-4ac7-b62f-dfa9ac0a2ebc button.colab-df-generate');
      buttonEl.style.display =
        google.colab.kernel.accessAllowed ? 'block' : 'none';

      buttonEl.onclick = () => {
        google.colab.notebook.generateWithVariable('nitrogen_check');
      }
      })();
    </script>
  </div>

    </div>
  </div>





```python
#Check graphs for any disrepancies

sns.boxplot(data = nitrogen_check[nitrogen_check['Moisture'] == 'Low'], x = 'Moisture', y = 'Nitrogen_mean')
plt.show()
sns.boxplot(data = nitrogen_check[nitrogen_check['Moisture'] == 'High'], x = 'Moisture', y = 'Nitrogen_mean')
plt.show()
sns.boxplot(data = nitrogen_check[nitrogen_check['Ash'] == 'Low'], x = 'Ash', y = 'Nitrogen_mean')
plt.show()
sns.boxplot(data = nitrogen_check[nitrogen_check['Ash'] == 'High'], x = 'Ash', y = 'Nitrogen_mean')
plt.show()
```


    
![png](Data_Discovery_Program_Follow_Up_files/Data_Discovery_Program_Follow_Up_22_0.png)
    



    
![png](Data_Discovery_Program_Follow_Up_files/Data_Discovery_Program_Follow_Up_22_1.png)
    



    
![png](Data_Discovery_Program_Follow_Up_files/Data_Discovery_Program_Follow_Up_22_2.png)
    



    
![png](Data_Discovery_Program_Follow_Up_files/Data_Discovery_Program_Follow_Up_22_3.png)
    


The value of 0.71 here seems to be an outlier, judging by its placements relative to other data points. On the other hand, we cannot claim the same for the value of 0.75 as we don't have enough datapoints. As such, we will be conducting out test without the outlier.

Difference 0.6


```python
# First, extract the datas needed

nitrogen_data = {
    'Bale No.': [3,5,1,6,2,3,11,2,5],
    'Ash': ['Low','Low','Low','Low','High','High','High','High','High'],
    'Moisture': ['Low','Low','High','High','Low','Low','Low','High','High'],
    'Nitrogen_mean': [0.57, 0.48, 0.59, 0.65, 0.60, 0.58, 0.52, 0.75, 0.64]
}
nitrogen = pd.DataFrame(nitrogen_data)
nitrogen
```





  <div id="df-93cf9b6a-1de9-4d8b-99a9-dcb5616eecc8" class="colab-df-container">
    <div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Bale No.</th>
      <th>Ash</th>
      <th>Moisture</th>
      <th>Nitrogen_mean</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>3</td>
      <td>Low</td>
      <td>Low</td>
      <td>0.57</td>
    </tr>
    <tr>
      <th>1</th>
      <td>5</td>
      <td>Low</td>
      <td>Low</td>
      <td>0.48</td>
    </tr>
    <tr>
      <th>2</th>
      <td>1</td>
      <td>Low</td>
      <td>High</td>
      <td>0.59</td>
    </tr>
    <tr>
      <th>3</th>
      <td>6</td>
      <td>Low</td>
      <td>High</td>
      <td>0.65</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2</td>
      <td>High</td>
      <td>Low</td>
      <td>0.60</td>
    </tr>
    <tr>
      <th>5</th>
      <td>3</td>
      <td>High</td>
      <td>Low</td>
      <td>0.58</td>
    </tr>
    <tr>
      <th>6</th>
      <td>11</td>
      <td>High</td>
      <td>Low</td>
      <td>0.52</td>
    </tr>
    <tr>
      <th>7</th>
      <td>2</td>
      <td>High</td>
      <td>High</td>
      <td>0.75</td>
    </tr>
    <tr>
      <th>8</th>
      <td>5</td>
      <td>High</td>
      <td>High</td>
      <td>0.64</td>
    </tr>
  </tbody>
</table>
</div>
    <div class="colab-df-buttons">

  <div class="colab-df-container">
    <button class="colab-df-convert" onclick="convertToInteractive('df-93cf9b6a-1de9-4d8b-99a9-dcb5616eecc8')"
            title="Convert this dataframe to an interactive table."
            style="display:none;">

  <svg xmlns="http://www.w3.org/2000/svg" height="24px" viewBox="0 -960 960 960">
    <path d="M120-120v-720h720v720H120Zm60-500h600v-160H180v160Zm220 220h160v-160H400v160Zm0 220h160v-160H400v160ZM180-400h160v-160H180v160Zm440 0h160v-160H620v160ZM180-180h160v-160H180v160Zm440 0h160v-160H620v160Z"/>
  </svg>
    </button>

  <style>
    .colab-df-container {
      display:flex;
      gap: 12px;
    }

    .colab-df-convert {
      background-color: #E8F0FE;
      border: none;
      border-radius: 50%;
      cursor: pointer;
      display: none;
      fill: #1967D2;
      height: 32px;
      padding: 0 0 0 0;
      width: 32px;
    }

    .colab-df-convert:hover {
      background-color: #E2EBFA;
      box-shadow: 0px 1px 2px rgba(60, 64, 67, 0.3), 0px 1px 3px 1px rgba(60, 64, 67, 0.15);
      fill: #174EA6;
    }

    .colab-df-buttons div {
      margin-bottom: 4px;
    }

    [theme=dark] .colab-df-convert {
      background-color: #3B4455;
      fill: #D2E3FC;
    }

    [theme=dark] .colab-df-convert:hover {
      background-color: #434B5C;
      box-shadow: 0px 1px 3px 1px rgba(0, 0, 0, 0.15);
      filter: drop-shadow(0px 1px 2px rgba(0, 0, 0, 0.3));
      fill: #FFFFFF;
    }
  </style>

    <script>
      const buttonEl =
        document.querySelector('#df-93cf9b6a-1de9-4d8b-99a9-dcb5616eecc8 button.colab-df-convert');
      buttonEl.style.display =
        google.colab.kernel.accessAllowed ? 'block' : 'none';

      async function convertToInteractive(key) {
        const element = document.querySelector('#df-93cf9b6a-1de9-4d8b-99a9-dcb5616eecc8');
        const dataTable =
          await google.colab.kernel.invokeFunction('convertToInteractive',
                                                    [key], {});
        if (!dataTable) return;

        const docLinkHtml = 'Like what you see? Visit the ' +
          '<a target="_blank" href=https://colab.research.google.com/notebooks/data_table.ipynb>data table notebook</a>'
          + ' to learn more about interactive tables.';
        element.innerHTML = '';
        dataTable['output_type'] = 'display_data';
        await google.colab.output.renderOutput(dataTable, element);
        const docLink = document.createElement('div');
        docLink.innerHTML = docLinkHtml;
        element.appendChild(docLink);
      }
    </script>
  </div>


<div id="df-f337a8ec-4525-4b33-838e-0779f4978350">
  <button class="colab-df-quickchart" onclick="quickchart('df-f337a8ec-4525-4b33-838e-0779f4978350')"
            title="Suggest charts"
            style="display:none;">

<svg xmlns="http://www.w3.org/2000/svg" height="24px"viewBox="0 0 24 24"
     width="24px">
    <g>
        <path d="M19 3H5c-1.1 0-2 .9-2 2v14c0 1.1.9 2 2 2h14c1.1 0 2-.9 2-2V5c0-1.1-.9-2-2-2zM9 17H7v-7h2v7zm4 0h-2V7h2v10zm4 0h-2v-4h2v4z"/>
    </g>
</svg>
  </button>

<style>
  .colab-df-quickchart {
      --bg-color: #E8F0FE;
      --fill-color: #1967D2;
      --hover-bg-color: #E2EBFA;
      --hover-fill-color: #174EA6;
      --disabled-fill-color: #AAA;
      --disabled-bg-color: #DDD;
  }

  [theme=dark] .colab-df-quickchart {
      --bg-color: #3B4455;
      --fill-color: #D2E3FC;
      --hover-bg-color: #434B5C;
      --hover-fill-color: #FFFFFF;
      --disabled-bg-color: #3B4455;
      --disabled-fill-color: #666;
  }

  .colab-df-quickchart {
    background-color: var(--bg-color);
    border: none;
    border-radius: 50%;
    cursor: pointer;
    display: none;
    fill: var(--fill-color);
    height: 32px;
    padding: 0;
    width: 32px;
  }

  .colab-df-quickchart:hover {
    background-color: var(--hover-bg-color);
    box-shadow: 0 1px 2px rgba(60, 64, 67, 0.3), 0 1px 3px 1px rgba(60, 64, 67, 0.15);
    fill: var(--button-hover-fill-color);
  }

  .colab-df-quickchart-complete:disabled,
  .colab-df-quickchart-complete:disabled:hover {
    background-color: var(--disabled-bg-color);
    fill: var(--disabled-fill-color);
    box-shadow: none;
  }

  .colab-df-spinner {
    border: 2px solid var(--fill-color);
    border-color: transparent;
    border-bottom-color: var(--fill-color);
    animation:
      spin 1s steps(1) infinite;
  }

  @keyframes spin {
    0% {
      border-color: transparent;
      border-bottom-color: var(--fill-color);
      border-left-color: var(--fill-color);
    }
    20% {
      border-color: transparent;
      border-left-color: var(--fill-color);
      border-top-color: var(--fill-color);
    }
    30% {
      border-color: transparent;
      border-left-color: var(--fill-color);
      border-top-color: var(--fill-color);
      border-right-color: var(--fill-color);
    }
    40% {
      border-color: transparent;
      border-right-color: var(--fill-color);
      border-top-color: var(--fill-color);
    }
    60% {
      border-color: transparent;
      border-right-color: var(--fill-color);
    }
    80% {
      border-color: transparent;
      border-right-color: var(--fill-color);
      border-bottom-color: var(--fill-color);
    }
    90% {
      border-color: transparent;
      border-bottom-color: var(--fill-color);
    }
  }
</style>

  <script>
    async function quickchart(key) {
      const quickchartButtonEl =
        document.querySelector('#' + key + ' button');
      quickchartButtonEl.disabled = true;  // To prevent multiple clicks.
      quickchartButtonEl.classList.add('colab-df-spinner');
      try {
        const charts = await google.colab.kernel.invokeFunction(
            'suggestCharts', [key], {});
      } catch (error) {
        console.error('Error during call to suggestCharts:', error);
      }
      quickchartButtonEl.classList.remove('colab-df-spinner');
      quickchartButtonEl.classList.add('colab-df-quickchart-complete');
    }
    (() => {
      let quickchartButtonEl =
        document.querySelector('#df-f337a8ec-4525-4b33-838e-0779f4978350 button');
      quickchartButtonEl.style.display =
        google.colab.kernel.accessAllowed ? 'block' : 'none';
    })();
  </script>
</div>

  <div id="id_8c97c89c-7a2a-4414-9800-4b5966266c09">
    <style>
      .colab-df-generate {
        background-color: #E8F0FE;
        border: none;
        border-radius: 50%;
        cursor: pointer;
        display: none;
        fill: #1967D2;
        height: 32px;
        padding: 0 0 0 0;
        width: 32px;
      }

      .colab-df-generate:hover {
        background-color: #E2EBFA;
        box-shadow: 0px 1px 2px rgba(60, 64, 67, 0.3), 0px 1px 3px 1px rgba(60, 64, 67, 0.15);
        fill: #174EA6;
      }

      [theme=dark] .colab-df-generate {
        background-color: #3B4455;
        fill: #D2E3FC;
      }

      [theme=dark] .colab-df-generate:hover {
        background-color: #434B5C;
        box-shadow: 0px 1px 3px 1px rgba(0, 0, 0, 0.15);
        filter: drop-shadow(0px 1px 2px rgba(0, 0, 0, 0.3));
        fill: #FFFFFF;
      }
    </style>
    <button class="colab-df-generate" onclick="generateWithVariable('nitrogen')"
            title="Generate code using this dataframe."
            style="display:none;">

  <svg xmlns="http://www.w3.org/2000/svg" height="24px"viewBox="0 0 24 24"
       width="24px">
    <path d="M7,19H8.4L18.45,9,17,7.55,7,17.6ZM5,21V16.75L18.45,3.32a2,2,0,0,1,2.83,0l1.4,1.43a1.91,1.91,0,0,1,.58,1.4,1.91,1.91,0,0,1-.58,1.4L9.25,21ZM18.45,9,17,7.55Zm-12,3A5.31,5.31,0,0,0,4.9,8.1,5.31,5.31,0,0,0,1,6.5,5.31,5.31,0,0,0,4.9,4.9,5.31,5.31,0,0,0,6.5,1,5.31,5.31,0,0,0,8.1,4.9,5.31,5.31,0,0,0,12,6.5,5.46,5.46,0,0,0,6.5,12Z"/>
  </svg>
    </button>
    <script>
      (() => {
      const buttonEl =
        document.querySelector('#id_8c97c89c-7a2a-4414-9800-4b5966266c09 button.colab-df-generate');
      buttonEl.style.display =
        google.colab.kernel.accessAllowed ? 'block' : 'none';

      buttonEl.onclick = () => {
        google.colab.notebook.generateWithVariable('nitrogen');
      }
      })();
    </script>
  </div>

    </div>
  </div>





```python
# Creating a barplot grouped by ash and moisture level

plot = sns.barplot(data = nitrogen, x = 'Ash', y = 'Nitrogen_mean', hue = 'Moisture')
```


    
![png](Data_Discovery_Program_Follow_Up_files/Data_Discovery_Program_Follow_Up_26_0.png)
    



```python
#!pip install statannotations #//Install statannotations if you don't have it yet.
from statannotations.Annotator import Annotator

hue_order=["Low","High"]
order = ["Low","High"]

ax = sns.barplot(data = nitrogen, x = 'Ash', y = 'Nitrogen_mean', hue = 'Moisture', order = order,hue_order = hue_order)
pairs = [
    (('Low', 'Low'),('Low', 'High')),
    (('Low', 'Low'),('High', 'Low')),
      (('Low', 'High'),('High', 'High')),
     (('High', 'Low'),('High', 'High')),
    (('Low', 'Low'),('High', 'High'))
    ]
annot = Annotator(ax, pairs, data=nitrogen, x='Ash', y='Nitrogen_mean',hue = 'Moisture', order = order,hue_order = hue_order)
annot.configure(test='t-test_ind')
annot.apply_test()
annot.annotate()

plt.show()
```

    p-value annotation legend:
          ns: 5.00e-02 < p <= 1.00e+00
           *: 1.00e-02 < p <= 5.00e-02
          **: 1.00e-03 < p <= 1.00e-02
         ***: 1.00e-04 < p <= 1.00e-03
        ****: p <= 1.00e-04
    
    High_Low vs. High_High: t-test independent samples, P_val:8.801e-02 t=-2.496e+00
    Low_Low vs. Low_High: t-test independent samples, P_val:2.211e-01 t=-1.757e+00
    Low_Low vs. High_Low: t-test independent samples, P_val:4.291e-01 t=-9.119e-01
    Low_High vs. High_High: t-test independent samples, P_val:3.539e-01 t=-1.197e+00
    Low_Low vs. High_High: t-test independent samples, P_val:1.392e-01 t=-2.392e+00



    
![png](Data_Discovery_Program_Follow_Up_files/Data_Discovery_Program_Follow_Up_27_1.png)
    



```python
ax = sns.barplot(data = nitrogen, x = 'Ash', y = 'Nitrogen_mean')
pairs = [('Low','High')]
annot = Annotator(ax, pairs, data=nitrogen, x='Ash', y='Nitrogen_mean')
annot.configure(test='t-test_ind', verbose=2)
annot.apply_test()
annot.annotate()

plt.show()
```

    p-value annotation legend:
          ns: 5.00e-02 < p <= 1.00e+00
           *: 1.00e-02 < p <= 5.00e-02
          **: 1.00e-03 < p <= 1.00e-02
         ***: 1.00e-04 < p <= 1.00e-03
        ****: p <= 1.00e-04
    
    Low vs. High: t-test independent samples, P_val:4.214e-01 t=-8.540e-01



    
![png](Data_Discovery_Program_Follow_Up_files/Data_Discovery_Program_Follow_Up_28_1.png)
    



```python
ax = sns.barplot(data = nitrogen, x = 'Moisture', y = 'Nitrogen_mean')
pairs = [('Low','High')]
annot = Annotator(ax, pairs, data=nitrogen, x='Moisture', y='Nitrogen_mean')
annot.configure(test='t-test_ind', verbose=2)
annot.apply_test()
annot.annotate()

plt.show()
```

    p-value annotation legend:
          ns: 5.00e-02 < p <= 1.00e+00
           *: 1.00e-02 < p <= 5.00e-02
          **: 1.00e-03 < p <= 1.00e-02
         ***: 1.00e-04 < p <= 1.00e-03
        ****: p <= 1.00e-04
    
    Low vs. High: t-test independent samples, P_val:2.686e-02 t=-2.791e+00



    
![png](Data_Discovery_Program_Follow_Up_files/Data_Discovery_Program_Follow_Up_29_1.png)
    


There appears to be a statistical significance in nitrogen content between different moisture groups, and no statistical significance between all other groups (ash or ash-moisture).

------------
### Correlation between ash/moisture content and corn stover properties

6. While the study establishes a link between variability in ash and moisture content and the physicochemical variability of biomass, it does not go far enough in demonstrating the impact/effect of one on the other.



We will be exploring the correlation(effect) between ash and moisture content and corn stover properties, grouped by ash and moisture content.

Add Caption to categorize each pictures


```python
# We will be checking the relationship between 'Ash (%, 750C)', 'Moisture (%, 105C)' against 'Glucan (%)', 'Xylan (%)', 'Lignin (%)', 'CrI (%)'
# As those 2 columns only exist on FY18, we will only be using that dataset

column_x = ['Ash (%, 750C)', 'Moisture (%, 105C)']
column_y = ['Glucan (%)', 'Xylan (%)', 'Lignin (%)', 'CrI (%)']

for j in column_y:

  fig, axes = plt.subplots(1, 2, figsize=(10, 4),sharey = True)
  for index,i in enumerate(column_x):
    sns.regplot(data=FY18, x=i, y=j, x_jitter=0.2,y_jitter=0.2, scatter_kws={'alpha' : 0.5}, ax = axes[index])
    correlation = round(FY18[[i, j]].corr().iloc[0,1],4)
    axes[index].set_title(f'Correlation: {correlation}')
  plt.show()

```


    
![png](Data_Discovery_Program_Follow_Up_files/Data_Discovery_Program_Follow_Up_35_0.png)
    



    
![png](Data_Discovery_Program_Follow_Up_files/Data_Discovery_Program_Follow_Up_35_1.png)
    



    
![png](Data_Discovery_Program_Follow_Up_files/Data_Discovery_Program_Follow_Up_35_2.png)
    



    
![png](Data_Discovery_Program_Follow_Up_files/Data_Discovery_Program_Follow_Up_35_3.png)
    



```python

```
