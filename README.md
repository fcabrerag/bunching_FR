## 2.- Data cleaning and processing of missing data:
### 2.1 Location Validation in GPS Bus Data:

How can I validate a location on a bus trip? Consider that I have a dataset with GPS bus data, and I would like to determine if certain conditions defined by my initial problem are met.
For example, determining if any trip occurs within the predefined geographical area stated in the problem statement or if a GPS coordinate point falls within the boundaries of a city or country.

There are different approaches to solving this problem.In particular, I would like to share a specific solution using the Geopandas library in Python.

One of the defined operations in Geopandas is "within" ,through which we can validate if a GPS point is located within a polygon (the polygon must be loaded beforehand). After performing this spatial join operation, we can exclude data that does not meet the specified condition.

On the following image, we can see the results obtained after performing the spatial join operation. As a result, the incorrect bus trips have been removed from the dataset.

![](https://github.com/fcabrerag/pro_investigacion/blob/main/imagenes/Rows_GPS_20180803.png)
