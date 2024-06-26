# A Tool to Predict the Flood Water Level on a Road
## Using the water level data of all the roads in the city, as well as past data
Made as a part of Hackenza Hackathon, conducted by ASCII BITS Goa (Computer Science Department)
Watch this video to understand how it works: https://drive.google.com/file/d/1vonnWoVD0n78Syyzeq5TJBzUQssEeWao/view?usp=sharing

### ❓ Missing Data Imputation:
The problem of estimation of accurate water level values for missing data is divided into two parts : spatial and time-series. For estimating data using spatial-analysis, we have used the method of **inverse distance weighting**, and for predicting time series data, a **graph convolutional network** is implemented. Finally, an ensemble of the predictions by the 2 methods is taken to give the most accurate estimate possible for missing water levels.

### 📊 Dataset Preparation
After searching for datasets for road flood water levels on the internet, our efforts went **down the drain**. We knew that using completely randomised numbers as data would not make sense, as our deep learning algorithm would not be able to find any patterns in it. Hence, we followed a structured approach. Every road was given a score out of 10 for 2 important properties – Drainage and Elevation. We made 300 data points of time series data. Each point of time was given a random rainfall parameter (out of 10). Now, for each road at any given time, its water level was given by the formula `(rainfall)/(drainage*elevation)`. This gave us consistent data along every row and every  column. For example, roads with poor drainage and low elevation had consistently higher water levels. Also, all the roads at a given time of high rainfall, had high water levels.

### 📏 IDW interpolation algorithm for spatial missing value estimation :  
The algorithm assumes the distance between two roads as the distance between their midpoints and for every missing road segment, it calculates the distance to midpoints of all the other road segments. It assigns weights to these roads in inverse proportion to distance. The missing level is then estimated as a **weighted average** of nearby segment levels. Finally, the spatial estimated water level is assigned to the missing segment. 

### 🕸️ Graph Convolution Network for time series data prediction : 
We have used **TISER-GCN**, a Neural Network architecture that integrates **temporal sensitivity** and **graph convolutional layers**. It excels at predicting future values from time series data with irregular sampling intervals, making it suitable for applications such as road water level prediction.  The architecture contains a GCN layer and a fully connected layer implemented as linear transformations using ReLU activation function. The input dataset consists of columns represented as roads and rows representing points of time and each data point representing water level on a given road at a given time. The final output is a list of water levels of all the roads at the next point of time. The purpose of using this neural network architecture is to capture patterns in the data. For example, if we have data of the previous 3 monsoon seasons, and the water levels on a given road follow a low-high-low pattern every season, the GCN would be able to predict the future value accordingly.

### ➕ Ensemble of the above algorithms to predict the final water level:
From our observations, the spatial prediction (by the IDW algorithm) has a better correlation with the actual water level compared to the temporal prediction (by the GCN). Hence, our final water level prediction is given by the formula `0.8*(spatial prediction) + 0.2*(temporal prediction)`, i.e. giving 80% weightage to the output from the IDW algorithm and 20% weightage to the output from the GCN. 

### 🗺️ Map Plotting Using MatPlotLib :
The coordinates of each intersection point were found and stored as a tuple in the `nodes` list. These were then labelled according to index and plotted on the image map. Another list of tuples `edges` stored the pair of indices of the 2 nodes forming a road. The roads are coloured in a shade of blue. The intensity of the shade is directly proportional to height of water level. Road with missing data is initially coloured in red. Once final water levels are retrieved the red coloured road is coloured in **appropriate shade of blue** (lighter means less flooded, darker means more flooded)


That's all :) Have a good day!
