
## Pyber Ride Sharing

### Analysis
<ul> 
<li> The average fare of rides in Rural cities is greater than the average fare of rides in Urban ones. </li>
<li> The driver count seems to be the greatest in Urban cities. </li>
<li> Suburban cities have more rides than Rural cities and fewer rides than Urban cities with only slightly cheaper fares on average than rural cities.  </li>



```python
#Import dependencies
import pandas as pd
import matplotlib.pyplot as plt
import numpy as np
import seaborn as sns
import plotly.plotly as py

```


```python
#Import data from csv 
city_details = 'city_data.csv'
city_df = pd.read_csv(city_details)

ride_details = 'ride_data.csv'
ride_df = pd.read_csv(ride_details)

#Merge the two dataframes
df_ride_city = ride_df.merge(city_df, how='outer', on="city")
df_ride_city.head()


```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>city</th>
      <th>date</th>
      <th>fare</th>
      <th>ride_id</th>
      <th>driver_count</th>
      <th>type</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Sarabury</td>
      <td>2016-01-16 13:49:27</td>
      <td>38.35</td>
      <td>5403689035038</td>
      <td>46</td>
      <td>Urban</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Sarabury</td>
      <td>2016-07-23 07:42:44</td>
      <td>21.76</td>
      <td>7546681945283</td>
      <td>46</td>
      <td>Urban</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Sarabury</td>
      <td>2016-04-02 04:32:25</td>
      <td>38.03</td>
      <td>4932495851866</td>
      <td>46</td>
      <td>Urban</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Sarabury</td>
      <td>2016-06-23 05:03:41</td>
      <td>26.82</td>
      <td>6711035373406</td>
      <td>46</td>
      <td>Urban</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Sarabury</td>
      <td>2016-09-30 12:48:34</td>
      <td>30.30</td>
      <td>6388737278232</td>
      <td>46</td>
      <td>Urban</td>
    </tr>
  </tbody>
</table>
</div>




```python
#Create DF for Avg Fare
avg_fare = pd.DataFrame(df_ride_city.groupby("city")["fare"].mean())
avg_fare.reset_index(inplace=True)
#Create DF for Total Rides
total_rides = pd.DataFrame(df_ride_city.groupby("city")["ride_id"].count())
total_rides.reset_index(inplace=True)
#Drop duplicates from city_df
new_city_df = city_df.drop_duplicates(subset=['city'])
new_city_df

#Merge all DFs 
city_analysis=avg_fare.merge(total_rides,how='outer',on='city')
city_analysis_final = city_analysis.merge(new_city_df,how='outer', on='city')
city_analysis_final.columns=["City","Avg Fare","Total Rides","Total Drivers","City Type"]
city_analysis_final.head()
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>City</th>
      <th>Avg Fare</th>
      <th>Total Rides</th>
      <th>Total Drivers</th>
      <th>City Type</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Alvarezhaven</td>
      <td>23.928710</td>
      <td>31</td>
      <td>21</td>
      <td>Urban</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Alyssaberg</td>
      <td>20.609615</td>
      <td>26</td>
      <td>67</td>
      <td>Urban</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Anitamouth</td>
      <td>37.315556</td>
      <td>9</td>
      <td>16</td>
      <td>Suburban</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Antoniomouth</td>
      <td>23.625000</td>
      <td>22</td>
      <td>21</td>
      <td>Urban</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Aprilchester</td>
      <td>21.981579</td>
      <td>19</td>
      <td>49</td>
      <td>Urban</td>
    </tr>
  </tbody>
</table>
</div>



### Bubble Plot of Ride Sharing Data


```python
#Create Bubble Plot 
x_axis = city_analysis_final["Total Rides"]
y_axis = city_analysis_final["Avg Fare"]
size = city_analysis_final["Total Drivers"]
colors ="lightcoral", "lightskyblue","gold"
colormap={"Urban":"lightcoral", "Suburban":"lightskyblue", "Rural":"gold"}
colors = [colormap[x] for x in city_analysis_final["City Type"]]
 
labels =["Urban","Suburban","Rural"]
    
city_analysis_final.plot(kind ="scatter", x="Total Rides", y="Avg Fare", 
                         s=size*10, c=colors, alpha=0.8, edgecolor="black")

plt.xlim(0,40)
plt.ylim(15,45)
plt.xlabel("Total Number of Rides (Per City)")
plt.ylabel("Average Fare ($)")
plt.title("Pyber Ride Sharing Data(2016)")
sns.set_style("darkgrid")

 
ax = sns.lmplot(x="Total Rides", y="Avg Fare", data=city_analysis_final,
          fit_reg=False, hue="City Type", legend = True,size = 6, scatter_kws={"s":(size*8)},
          palette=dict(Urban="lightcoral", Suburban="lightskyblue", Rural="gold"))
plt.xlim(0,40)
plt.ylim(15,45)
plt.xlabel("Total Number of Rides (Per City)")
plt.ylabel("Average Fare ($)")
plt.title("Pyber Ride Sharing Data(2016)")
sns.set_style("darkgrid")



plt.show()
#couldn't figure out how to include the legend 
#in the first scatterplot figure and couldn't 
#figure out how to give edgecolors in the second figure
```


![png](output_6_0.png)



![png](output_6_1.png)



```python
#Create DataFrame for Total Rides by City Type
city_group = pd.DataFrame(df_ride_city["type"].value_counts())
city_group.reset_index(inplace=True)
city_group.columns = ["City Type", "Total Rides"]


```


```python
#Create DateFrame for Driver Count by City Type
clean_ride_city = df_ride_city.drop_duplicates(subset=['city'])
clean_ride_city = pd.DataFrame(clean_ride_city.groupby("type")["driver_count"].sum())
clean_ride_city.reset_index(inplace=True)
clean_ride_city.columns=["City Type","Driver Count"]



```


```python
#Create DataFrame for Fare by City Type
type_fare = pd.DataFrame(df_ride_city.groupby("type")["fare"].sum())
type_fare.reset_index(inplace=True)
type_fare.columns=["City Type","Fare"]

```


```python
#Merge all 3 DataFrames by City Type into one 
by_city_type = city_group.merge(clean_ride_city, how='outer', on="City Type")
city_type_analysis = by_city_type.merge(type_fare, how='outer', on = "City Type")

```

### Total Fares by City Type


```python
#Pie Chart for Total Fares 
explode = (0.1,0,0)
colors = "lightcoral","lightskyblue","gold"
plt.pie(city_type_analysis["Fare"], labels = city_type_analysis["City Type"], explode=explode, 
        colors = colors, autopct="%1.1f%%", shadow = True, startangle = 180)
plt.title("% of Total Fares by City Type")
plt.axis('equal')
plt.show()
```


![png](output_12_0.png)


### Total Rides by City Type 


```python
#Pie Chart for Total Rides
explode = (0.1,0,0)
colors = "lightcoral","lightskyblue","gold"
plt.pie(city_type_analysis["Total Rides"], labels = city_type_analysis["City Type"], explode=explode, 
        colors = colors, autopct="%1.1f%%", shadow = True, startangle = 180)
plt.title("% of Total Rides by City Type")
plt.axis('equal')
plt.show()
```


![png](output_14_0.png)


### Total Drivers by City Type


```python
#Pie Chart for Total Drivers 
explode = (0.1,0,0)
colors = "lightcoral","lightskyblue","gold"
plt.pie(city_type_analysis["Driver Count"], labels = city_type_analysis["City Type"], explode=explode, 
        colors = colors, autopct="%1.1f%%", shadow = True, startangle = 180)
plt.title("% of Total Drivers by City Type")
plt.axis('equal')
plt.show()

```


![png](output_16_0.png)

