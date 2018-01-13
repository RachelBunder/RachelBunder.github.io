---
layout: single
author_profile: true
except: 
header:
    image: /assets/header_images/cleaning_osm.JPG
    alt-text: "Traffic"
---

# Cleaning Open Street Maps
What is the most common street name in Australia? This is what I wondered after seeing a __[Washington Post article](https://www.washingtonpost.com/blogs/govbeat/wp/2015/03/06/these-are-the-most-popular-street-names-in-every-state/)__ on the most common street name in America. What really got me is all the plant names - Cedar, Oak, and Aspen etc., and I thought - we don't do that in Australia - do we?

So I went down a fairly windy road to find out. Sure, there are __[articles](https://www.realestate.com.au/news/weird-and-common-australian-street-names/)__ out there that list the common streets in Australia, but where is the fun in that? 

When I started this project the __[Geocoded National Address File (G-NAF)](https://data.gov.au/dataset/geocoded-national-address-file-g-naf)__ wasn't yet available. I can't exactly remember what other soures of data I was looked at, but Open Street MAP (OSM) was the easiest to get hold of, so I used that. It also had the benefit that if I ever wanted to do this analysis on any other country the data would be available.  



## Cleaning OSM data
To start off with, I am just considering Sydney - Australia is just too big. It is much quicker to test ideas on a small subset of Australia. And I live in Sydney and am familar with the streets. I should be able to notice any problems with my modelling. 


I got the data from __[Metro Extracts](https://mapzen.com/data/metro-extracts/)__ which is sadly shutting down at the end of January 2018. The geojson download gives you a folder of different files already divided up by map feature.

![File structue of Metro Maps geojson download](/assets/posts/cleaning_osm_files/metro_maps_files.png)

Conveniently, there are three files dedicated to the roads in Sydney. The two road_gen files are generalised versions of the roads_gen file. The roads_gen verions are generalised versions of the roads file - they incude simplified coordinates. 

### Reading
First I need to read in the data. To do this I'm using geopandas, as it can handle the geometry data - the longitude and latitude. As a first round of basic cleaning I'm removing all streets that do not have a defined name and then changing the street name to all lower case.


```python
import pandas as pd
import geopandas as gpd
import matplotlib.pyplot as plt
import seaborn as sns
import shapely
%matplotlib inline
```


```python
streets = gpd.read_file('Sydney/ex_skDAYAuv8avqtvLU5LSdM4Fc7xxkR_roads.geojson')

streets = streets[~streets["name"].isnull()]
streets["name"] = streets["name"].apply(lambda x: x.lower())
```

This is what the data we get from Metro Extracts looks like:


```python
streets.head()
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
      <th>access</th>
      <th>bridge</th>
      <th>class</th>
      <th>geometry</th>
      <th>id</th>
      <th>name</th>
      <th>oneway</th>
      <th>osm_id</th>
      <th>ref</th>
      <th>service</th>
      <th>tunnel</th>
      <th>type</th>
      <th>z_order</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>None</td>
      <td>0</td>
      <td>highway</td>
      <td>LINESTRING (151.2424485234584 -33.886582826295...</td>
      <td>1.0</td>
      <td>edgecliff road</td>
      <td>0</td>
      <td>1986039.0</td>
      <td>None</td>
      <td>None</td>
      <td>0</td>
      <td>residential</td>
      <td>3.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>None</td>
      <td>0</td>
      <td>highway</td>
      <td>LINESTRING (151.193104343295 -33.8876124592810...</td>
      <td>3.0</td>
      <td>cleveland street</td>
      <td>1</td>
      <td>1954876.0</td>
      <td>None</td>
      <td>None</td>
      <td>0</td>
      <td>primary</td>
      <td>6.0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>None</td>
      <td>0</td>
      <td>highway</td>
      <td>LINESTRING (151.2112821767074 -33.845685509054...</td>
      <td>4.0</td>
      <td>cliff street</td>
      <td>1</td>
      <td>1986113.0</td>
      <td>None</td>
      <td>None</td>
      <td>0</td>
      <td>unclassified</td>
      <td>3.0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>None</td>
      <td>0</td>
      <td>highway</td>
      <td>LINESTRING (151.0978939698709 -33.913194279223...</td>
      <td>5.0</td>
      <td>anglo road</td>
      <td>1</td>
      <td>1881386.0</td>
      <td>None</td>
      <td>None</td>
      <td>0</td>
      <td>residential</td>
      <td>3.0</td>
    </tr>
    <tr>
      <th>6</th>
      <td>None</td>
      <td>0</td>
      <td>highway</td>
      <td>LINESTRING (151.2108830304782 -33.847941079198...</td>
      <td>7.0</td>
      <td>northcliff street</td>
      <td>1</td>
      <td>1986116.0</td>
      <td>None</td>
      <td>None</td>
      <td>0</td>
      <td>residential</td>
      <td>3.0</td>
    </tr>
  </tbody>
</table>
</div>




```python
ax = streets['geometry'].plot()
ax.get_xaxis().get_major_formatter().set_useOffset(False)
ax.set_title("All Streets in Sydney")
ax.set_xlabel('Longitude')
ax.set_ylabel('Latitude')
```



![All streets Sydney](/assets/posts/cleaning_osm_files/cleaning_osm_6_1.png)


I like that you can identify the Royal National Park (the empty patch in the bottom middle), Botany Bay (to the right of the National Park) and even the Harbour Bridge (the first crossing of the harbour north of Botany Bay).

## Data Cleanup
In OSM, a lot more than just streets are included.

There are three classes *highway*, *railway* and *man-made*. The railway class includes rail lines (both goods and passenger) and light rail lines (used and disused). These are not streets.


```python
streets[streets["class"] == 'railway'].head()
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
      <th>access</th>
      <th>bridge</th>
      <th>class</th>
      <th>geometry</th>
      <th>id</th>
      <th>name</th>
      <th>oneway</th>
      <th>osm_id</th>
      <th>ref</th>
      <th>service</th>
      <th>tunnel</th>
      <th>type</th>
      <th>z_order</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>58</th>
      <td>None</td>
      <td>0</td>
      <td>railway</td>
      <td>LINESTRING (151.196358365745 -33.8687554402545...</td>
      <td>59.0</td>
      <td>inner west light rail</td>
      <td>1</td>
      <td>2654260.0</td>
      <td>None</td>
      <td>None</td>
      <td>0</td>
      <td>light_rail</td>
      <td>7.0</td>
    </tr>
    <tr>
      <th>104</th>
      <td>None</td>
      <td>0</td>
      <td>railway</td>
      <td>LINESTRING (151.2011414987909 -33.879140366829...</td>
      <td>105.0</td>
      <td>inner west light rail</td>
      <td>1</td>
      <td>2761538.0</td>
      <td>None</td>
      <td>None</td>
      <td>0</td>
      <td>light_rail</td>
      <td>7.0</td>
    </tr>
    <tr>
      <th>361</th>
      <td>None</td>
      <td>0</td>
      <td>railway</td>
      <td>LINESTRING (151.1997885757997 -33.877948376379...</td>
      <td>362.0</td>
      <td>the goodsline</td>
      <td>0</td>
      <td>3188010.0</td>
      <td>None</td>
      <td>None</td>
      <td>0</td>
      <td>disused</td>
      <td>7.0</td>
    </tr>
    <tr>
      <th>11627</th>
      <td>None</td>
      <td>0</td>
      <td>railway</td>
      <td>LINESTRING (150.6417417467128 -33.757431025617...</td>
      <td>11628.0</td>
      <td>main western line</td>
      <td>0</td>
      <td>22720526.0</td>
      <td>Up Main</td>
      <td>None</td>
      <td>0</td>
      <td>rail</td>
      <td>7.0</td>
    </tr>
    <tr>
      <th>32383</th>
      <td>None</td>
      <td>0</td>
      <td>railway</td>
      <td>LINESTRING (151.0229891759798 -33.888670171642...</td>
      <td>32384.0</td>
      <td>sefton - chullora goods line</td>
      <td>0</td>
      <td>49493757.0</td>
      <td>Sefton - Chullora Goods Line</td>
      <td>None</td>
      <td>0</td>
      <td>rail</td>
      <td>7.0</td>
    </tr>
  </tbody>
</table>
</div>




```python
ax = streets[streets["class"] == 'railway']["geometry"].plot()
ax.set_title("All railyways in Sdyney")
ax.get_xaxis().get_major_formatter().set_useOffset(False)
ax.set_xlabel('Longitude')
ax.set_ylabel('Latitude')
```


![All railways in Sydney](/assets/posts/cleaning_osm_files/cleaning_osm_10_1.png)


Let's look at the man-made class. This contains things such as the Fish Market, Taronga Zoo Ferry Wharf, piers and groynes (low walls built out to sea to stop errosion).  Also not what I would call streets.


```python
streets[streets["class"] == 'man_made'].head()
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
      <th>access</th>
      <th>bridge</th>
      <th>class</th>
      <th>geometry</th>
      <th>id</th>
      <th>name</th>
      <th>oneway</th>
      <th>osm_id</th>
      <th>ref</th>
      <th>service</th>
      <th>tunnel</th>
      <th>type</th>
      <th>z_order</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>406</th>
      <td>None</td>
      <td>0</td>
      <td>man_made</td>
      <td>LINESTRING (151.1486958468153 -34.059200662259...</td>
      <td>407.0</td>
      <td>gunnamatta baths</td>
      <td>0</td>
      <td>3619874.0</td>
      <td>None</td>
      <td>None</td>
      <td>0</td>
      <td>pier</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>984</th>
      <td>None</td>
      <td>0</td>
      <td>man_made</td>
      <td>LINESTRING (151.1909980548465 -33.873133140643...</td>
      <td>985.0</td>
      <td>fish market</td>
      <td>0</td>
      <td>4332267.0</td>
      <td>None</td>
      <td>None</td>
      <td>0</td>
      <td>pier</td>
      <td>20.0</td>
    </tr>
    <tr>
      <th>1175</th>
      <td>None</td>
      <td>0</td>
      <td>man_made</td>
      <td>LINESTRING (151.2452296389314 -33.872377009158...</td>
      <td>1176.0</td>
      <td>double bay marina</td>
      <td>0</td>
      <td>4354558.0</td>
      <td>None</td>
      <td>None</td>
      <td>0</td>
      <td>pier</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>1183</th>
      <td>None</td>
      <td>0</td>
      <td>man_made</td>
      <td>LINESTRING (151.2398218864609 -33.845772177933...</td>
      <td>1184.0</td>
      <td>taronga zoo ferry wharf</td>
      <td>0</td>
      <td>4354530.0</td>
      <td>None</td>
      <td>None</td>
      <td>0</td>
      <td>pier</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>5455</th>
      <td>None</td>
      <td>0</td>
      <td>man_made</td>
      <td>LINESTRING (151.2308674993006 -33.847500107272...</td>
      <td>5456.0</td>
      <td>cremorne ferry wharf</td>
      <td>0</td>
      <td>9984522.0</td>
      <td>None</td>
      <td>None</td>
      <td>0</td>
      <td>pier</td>
      <td>0.0</td>
    </tr>
  </tbody>
</table>
</div>




```python
ax = streets[streets["class"] == 'man_made']["geometry"].plot(figsize = (5,10))
ax.set_title("All 'Man-Made' class types in Sydney")
ax.get_xaxis().get_major_formatter().set_useOffset(False)
ax.set_xlabel('Longitude')
ax.set_ylabel('Latitude')

ax.set_xlim((150.6, 151.3))
```


![All Man Made class 'roads' in Sydney](/assets/posts/cleaning_osm_files/cleaning_osm_13_1.png)


This leaves us with the highway class which contains actual streets, but also includes other things like footways and cycleways which are not streets. All of this information is contained in the 'type' attribute. For a complete list of highway types and explanations on what they are, have a look at the  __[OSM wiki](http://www.wiki.openstreetmap.org/wiki/Key:highway)__


The highway types we see in Sydney include:


```python
streets[streets["class"] == 'highway']["type"].unique()
```




    array(['residential', 'primary', 'unclassified', 'tertiary', 'secondary',
           'tertiary_link', 'pedestrian', 'service', 'footway', 'cycleway',
           'path', 'trunk', 'steps', 'primary_link', 'living_street',
           'motorway_link', 'secondary_link', 'motorway', 'track', 'bridleway',
           'trunk_link', 'raceway', 'road'], dtype=object)



At this point I need to decide "What is a street?". Where do I draw the line between footway (pedestrains only), living_street (pedestrians have priority), cycleway (bikes allowed) or secondary_link (a road joining a motorway and another street)?

I want to be as incusive as possible with this definition. Motorways should be included as they have names, such as the Hume Highway (some have less interesting names like the M5 Motorway). I feel that link roads should be included as they are also mostly named. Although there are also ones named 'Westlink M7 onramp', there shouldn't be so many of them that Westlink M7 is a contender for the most common street name. 

I have decided a street is something I'm allowed to drive a car on. This means I will remove the pedestrian and non-car orientated streets. It also means I will remove private streets or those without public access, such as those at Holsworthy Barracks.  

There is also the __['access' key](https://wiki.openstreetmap.org/wiki/Key:access)__ which gives information on who can access this road.


```python
streets["access"].unique()
```




    array([None, 'yes', 'private', 'public', 'no', 'designated', 'permissive',
           'destination', 'psv', 'delivery', 'official', 'hgv', 'bus',
           'yes|bicycle|yes|yes', 'yes|yes|bicycle|yes|yes', 'military'], dtype=object)



I want to keep as many streets as possible, but also keep it to roads that are public, thus I'm not including no access roads, military roads and HGV (Heavy Goods Vehicle) roads are not real streets.

Note that 'None' just means that no access conditions have been recorded. 



```python
streets[streets["access"] == 'hgv']
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
      <th>access</th>
      <th>bridge</th>
      <th>class</th>
      <th>geometry</th>
      <th>id</th>
      <th>name</th>
      <th>oneway</th>
      <th>osm_id</th>
      <th>ref</th>
      <th>service</th>
      <th>tunnel</th>
      <th>type</th>
      <th>z_order</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>80102</th>
      <td>hgv</td>
      <td>0</td>
      <td>highway</td>
      <td>LINESTRING (150.8354220480671 -33.888192906075...</td>
      <td>80103.0</td>
      <td>truck stopping bay</td>
      <td>0</td>
      <td>225102002.0</td>
      <td>None</td>
      <td>None</td>
      <td>0</td>
      <td>service</td>
      <td>0.0</td>
    </tr>
  </tbody>
</table>
</div>



Finally, there is service. This tag provides additional context on what the road is used for. There are a few services I want to exclude. Namely, driveway, private_road, bicycle_training_track, go-kart, drive-through (bye KFC drive-through) as they are not public roads I can drive on. I've kept parking_aisle as I am driving on it before and after parking. 


```python
streets["service"].unique()
```




    array([None, 'alley', 'parking_aisle', 'driveway', 'yes', 'bus',
           'drive-through', 'Lane', 'emergency_access', 'parking', 'siding',
           'yard'], dtype=object)




```python
def get_streets(df):
    clean_streets = df.dropna(subset = ["name"])
#     clean_streets.loc[:,"name"] = clean_streets["name"].apply(lambda x: x.lower())
    
    clean_streets = clean_streets[clean_streets["class"] == 'highway']
    
    
    clean_streets = clean_streets[clean_streets["type"] != "footway"]
    clean_streets = clean_streets[clean_streets["type"] != "cycleway"]
    clean_streets = clean_streets[clean_streets["type"] != "bridleway"]
    clean_streets = clean_streets[clean_streets["type"] != "track"]
    clean_streets = clean_streets[clean_streets["type"] != "raceway"]
    clean_streets = clean_streets[clean_streets["type"] != "pedestrian"]
    clean_streets = clean_streets[clean_streets["type"] != "steps"]
    clean_streets = clean_streets[clean_streets["type"] != "path"]
    
    clean_streets = clean_streets[clean_streets["access"] != "private"]
    clean_streets = clean_streets[clean_streets["access"] != "no"]
    clean_streets = clean_streets[clean_streets["access"] != "military"]
    clean_streets = clean_streets[clean_streets["access"] != "HGV"]
    
    clean_streets = clean_streets[clean_streets["service"] != "private_road"]
    clean_streets = clean_streets[clean_streets["service"] != "bicycle_training_track"]
    clean_streets = clean_streets[clean_streets["service"] != "go-kart"]
    clean_streets = clean_streets[clean_streets["service"] != "drive-through"]
    
    return clean_streets
clean_streets = get_streets(streets)
```

All of this processing removes just 3,268 streets out of the original 72,798 streets. 

To save the file you need to use the geopandas function to_file. This has the unfortunate side effect of making the working file much bigger - 55MB compared to 9MB for the original download. 


```python
clean_streets.to_file("clean_streets_sydney.geojson")
```

This is all for cleaning up OSM street data. Next we will do some exploration on Sydney and other adjustments that need to be done. 
