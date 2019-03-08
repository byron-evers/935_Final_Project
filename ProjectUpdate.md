
# Project Update
### 2019-03-19

## Calculating thermal time indices and merging UAV data.

**Name**: Byron Evers<br/>
**Semester**: Spring 2019 <br/>
**Project area**: Agronomy

**Objective:** 

Write a python function to calculate biometeorological time (BMT), physiological days (Pdays) and growing degree days (GDD) for several UAV collection dates. The objective is to have the thermal time parameters calculated for the specific dates that UAV data was collected. Ultimitly, through a python function, I would like merge all of the UAV reflectance data, plot level phenotypic data and the calculated thermal values into one .csv file.

**Rational:** 
Current phenotyping methods are labor intensive, hard to replicate, and have limited temporal resolution. UAVs equipped with multispectral sensors present a possible solution to obtain valuable time sensitive data. To use these data points in spatio-temporal models, crop phenology needs to be estimate. Past research papers have used a range of thermal time equations to estimate crop phenology (Saiyed et al 2009). However, there is still a need to compare evaluate these thermal predictors in relationship to reflectance data.

Over the last two growing season I have collected UAV reflectance data across several breeding nursery locations at several dates. Currently this data is being merged with plot level data with excel. This process is inefficient and introduces potential errors. I would like to stream line this process with Python. Additionally, having the ability to calculate several phenological development thermal times, with a Python function, will be useful for modeling purposes.

Current UAV pipeline includes stitching photos and extracting plot level reflectance data through Agisoft software. Data recived from this process is formated as a .csv file and includes 5 indiviual reflectance bands (R,G,B,RE and NIR) and 3 vegitative indices (NDVI, NDRE and GNDVI). Each band and vegitative index can be be used to analyze biophysical traits of various crops. At this time I do not wish to calculate these indcies with python, though I would like to write a python script that can combine the Agisoft data with the calculated thermal times and plot level data such as yield and plant hieght.

**Step: 1 Download data from the KSU Mesonet**
- Go to http://mesonet.k-state.edu/weather/historical/
- Select location of intrest. For this projcet I have started with the Scandia weather station.
- Enter the range of dates you would like to download. However, maximum range is 366 days. For this project I have dowloaded days for the 2017-2018 winter wheat growing season. I used dates ranging from 2017-09-01 to 2018-07-15. This is beyond the scope of the growing season but the defined function will only capture the dates within the growing season. 
- Export the data as a CSV to your working directory 


**Step: 2 import needed modules**


```python
import pandas as pd
import numpy as np
import glob
import datetime as dt
from datetime import datetime
import matplotlib.pyplot as plt
```

**Step3: import data and review format**
- Review equation and evaluate minimum needed variables.    
**Growing Degree Days GDD**:
$$GDD = (\frac{Tmax+Tmin}{2})-Tbase$$
- Set directory name and file name


```python
dirname = '/Users/bevers/Desktop/Coding/Project_Update/'
filename = '2018_growing_season.txt'
```

- Import file and review the first 10 entries to check content and format


```python
df = pd.read_csv(dirname + filename)
df.head(10)
```




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
      <th>Timestamp</th>
      <th>Station</th>
      <th>AirTemperature</th>
      <th>AirTemperature.1</th>
      <th>RelativeHumidity</th>
      <th>Precipitation</th>
      <th>WindSpeed2m</th>
      <th>WindSpeed2m.1</th>
      <th>SoilTemperature5cm</th>
      <th>SoilTemperature5cm.1</th>
      <th>SoilTemperature10cm</th>
      <th>SoilTemperature10cm.1</th>
      <th>SolarRadiation</th>
      <th>ETo</th>
      <th>ETo.1</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>NaN</td>
      <td>NaN</td>
      <td>max</td>
      <td>min</td>
      <td>avg</td>
      <td>total</td>
      <td>avg</td>
      <td>max</td>
      <td>max</td>
      <td>min</td>
      <td>max</td>
      <td>min</td>
      <td>total</td>
      <td>grass</td>
      <td>alfalfa</td>
    </tr>
    <tr>
      <th>1</th>
      <td>NaN</td>
      <td>NaN</td>
      <td>°C</td>
      <td>°C</td>
      <td>%</td>
      <td>mm</td>
      <td>m/s</td>
      <td>m/s</td>
      <td>°C</td>
      <td>°C</td>
      <td>°C</td>
      <td>°C</td>
      <td>MJ/m²</td>
      <td>mm</td>
      <td>mm</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2017-09-01</td>
      <td>Scandia</td>
      <td>23.8</td>
      <td>15.4</td>
      <td>77.6</td>
      <td>0</td>
      <td>1.3</td>
      <td>5.7</td>
      <td>24.6</td>
      <td>20.4</td>
      <td>24.2</td>
      <td>20.9</td>
      <td>8.3</td>
      <td>2.17</td>
      <td>2.68</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2017-09-02</td>
      <td>Scandia</td>
      <td>30.4</td>
      <td>13.4</td>
      <td>77.2</td>
      <td>0</td>
      <td>1.1</td>
      <td>5.8</td>
      <td>31.3</td>
      <td>18.8</td>
      <td>29.8</td>
      <td>19.2</td>
      <td>21.6</td>
      <td>4.21</td>
      <td>4.99</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2017-09-03</td>
      <td>Scandia</td>
      <td>33.9</td>
      <td>13.3</td>
      <td>68.3</td>
      <td>0</td>
      <td>1.5</td>
      <td>5.3</td>
      <td>31.9</td>
      <td>19.6</td>
      <td>30.5</td>
      <td>20.3</td>
      <td>21.8</td>
      <td>5.02</td>
      <td>6.47</td>
    </tr>
    <tr>
      <th>5</th>
      <td>2017-09-04</td>
      <td>Scandia</td>
      <td>27.7</td>
      <td>14.5</td>
      <td>68.0</td>
      <td>0</td>
      <td>1.9</td>
      <td>7.6</td>
      <td>29.0</td>
      <td>20.9</td>
      <td>28.2</td>
      <td>21.7</td>
      <td>19.1</td>
      <td>4.37</td>
      <td>5.72</td>
    </tr>
    <tr>
      <th>6</th>
      <td>2017-09-05</td>
      <td>Scandia</td>
      <td>22.2</td>
      <td>6.4</td>
      <td>61.4</td>
      <td>0</td>
      <td>1.7</td>
      <td>7.9</td>
      <td>28.5</td>
      <td>17.2</td>
      <td>26.9</td>
      <td>18.2</td>
      <td>23.7</td>
      <td>4.06</td>
      <td>5.24</td>
    </tr>
    <tr>
      <th>7</th>
      <td>2017-09-06</td>
      <td>Scandia</td>
      <td>23.7</td>
      <td>4.7</td>
      <td>62.8</td>
      <td>0</td>
      <td>1.1</td>
      <td>6.1</td>
      <td>28.9</td>
      <td>15.5</td>
      <td>27.0</td>
      <td>16.1</td>
      <td>24.0</td>
      <td>3.82</td>
      <td>4.75</td>
    </tr>
    <tr>
      <th>8</th>
      <td>2017-09-07</td>
      <td>Scandia</td>
      <td>29.3</td>
      <td>5.4</td>
      <td>56.1</td>
      <td>0</td>
      <td>1.3</td>
      <td>4.9</td>
      <td>29.4</td>
      <td>16.0</td>
      <td>27.5</td>
      <td>16.4</td>
      <td>23.0</td>
      <td>4.41</td>
      <td>5.73</td>
    </tr>
    <tr>
      <th>9</th>
      <td>2017-09-08</td>
      <td>Scandia</td>
      <td>31.9</td>
      <td>14.5</td>
      <td>49.7</td>
      <td>0</td>
      <td>1.6</td>
      <td>6.6</td>
      <td>30.3</td>
      <td>19.1</td>
      <td>28.7</td>
      <td>19.4</td>
      <td>21.3</td>
      <td>4.97</td>
      <td>6.63</td>
    </tr>
  </tbody>
</table>
</div>



**Step4: Edit data frame to match format needed**


```python
df.rename(columns={'Timestamp':'Date', 'AirTemperature':'Tmax', 'AirTemperature.1':'Tmin'}, inplace=True)
df = df.drop((df.index[:2])) # Drop top lines of non numaric data
df = df.drop(['RelativeHumidity', 'Precipitation', 'WindSpeed2m','WindSpeed2m.1','SoilTemperature5cm','SoilTemperature5cm.1',
             'SoilTemperature10cm','SoilTemperature10cm.1','SolarRadiation','ETo','ETo.1'], axis=1)
df.info()
```

    <class 'pandas.core.frame.DataFrame'>
    Int64Index: 318 entries, 2 to 319
    Data columns (total 4 columns):
    Date       318 non-null object
    Station    318 non-null object
    Tmax       318 non-null object
    Tmin       318 non-null object
    dtypes: object(4)
    memory usage: 12.4+ KB


**Step5: Edit data types**
- The data should be in a 'pandas.core.frame.DataFrame'
- Date should be in datetime64 format
- Tmax and Tmin should be float64 format


```python
#Set T_max and T_min to float values
df.Tmin = df.Tmin.astype(float)
df.Tmax = df.Tmax.astype(float)

df.Date =  pd.to_datetime(df.Date,format='%Y-%m-%d')
df.head(5)
df.info()
```

    <class 'pandas.core.frame.DataFrame'>
    Int64Index: 318 entries, 2 to 319
    Data columns (total 4 columns):
    Date       318 non-null datetime64[ns]
    Station    318 non-null object
    Tmax       318 non-null float64
    Tmin       318 non-null float64
    dtypes: datetime64[ns](1), float64(2), object(1)
    memory usage: 12.4+ KB


**Step6: Set user inputs**
- Set planting date in 'YYYY-MM-DD' form. Make sure it is a string
- Set harvest date in 'YYYY-MM-DD' form. Make sure it is a string
- Set your Tbase value in degrees C


```python
# Define inputs 
plantDate=np.datetime64('2017-10-10') #set the date your crop was planted
harvestDate=np.datetime64('2018-04-10') #set the date your crop was planted
tbase=0 # set the base temperature for your given crop. 
```


```python

```
