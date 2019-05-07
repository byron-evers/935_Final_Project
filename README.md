## Calculating thermal time indices and merging UAV data.

**Name**: Byron Evers<br/>
**Semester**: Spring 2019 <br/>
**Project area**: Agronomy


## Objective
Write a python function to calculate biometeorological time (BMT), physiological days (Pdays) and growing degree days (GDD) for several UAV collection dates. The objective is to have the thermal time parameters calculated for the specific dates that UAV data was collected. 
Ultimitly, through a python function, I would like merge all of the UAV reflectance data, plot level phenotypic data and the calculated thermal values into one .csv file. 


## Outcomes
The final outcome is to generate a .csv file that has UAV and plot level phenotype data merged by plot_id with additional columns for calculated date times. 


## Rationale

Current phenotyping methods are labor intensive, hard to replicate, and have limited temporal resolution. UAVs equipped with multispectral sensors present a possible solution to obtain valuable time sensitive data. To use these data points in spatio-temporal models, crop phenology needs to be estimate. Past research papers have used a range of thermal time equations to estimate crop phenology (Saiyed et al 2009). However, there is still a need to compare evaluate these thermal predictors in relationship to reflectance data.

Over the last two growing season I have collected UAV reflectance data across several breeding nursery locations at several dates. Currently this data is being merged with plot level data with excel. This process is inefficient and introduces potential errors. I would like to stream line this process with Python. Additionally, having the ability to calculate several phenological development thermal times, with a Python function, will be useful for modeling purposes.

Our labs current UAV pipeline includes stitching photos and extracting reflectance data through Agisoft software. Data recived from this process is formated as a .csv file and includes 5 indiviual reflectance bands (R,G,B,RE and NIR) and 3 vegitative indices (NDVI, NDRE and GNDVI). Each band and vegitative index can be be used to analyze biophysical traits of various crops. At this time I do not wish to calculate these indcies with python, though I would like to write a python script that can combine the Agisoft data with the calculated thermal times adn plot level data such as yield and plant hieght. 

## References
Kyratzis Angelos C., Skarlatos Dimitrios P., Menexes George C., Vamvakousis Vasileios F., Katsiotis Andreas, 2017. Assessment of Vegetation Indices Derived by UAV Imagery for Durum Wheat Phenotyping under a Water Limited and Heat Stressed Mediterranean Environment. Frontiers in Plant Science V.8 P 1114

Miller P, Lanier W, Brandt S (2001) Using Growing Degree Days to Predict Plant Stages, Ag/Extension Communications Coordinator, Communications Services, Montana State University-Bozeman, Bozeman, MO, pp 1-8. Available online: http://msuextension.org/publications/AgandNaturalResources/MT200103AG. pdf

Robertson, G. W. 1968. A biometeorological time scale for a cereal crop involving day and night temperatures and photoperiod. Int. J. Biometeor. 12: 191223.

Saiyed, I., Bullock, P.R., Sapirstein, H.D., Finlay, G.J., Jarvis, C.K., 2009. Thermal time models for estimating wheat phenological development and weather-based relationships to wheat quality. Can. J. Plant Sci. 89, 429–439.

