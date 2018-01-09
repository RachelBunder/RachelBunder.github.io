---
layout: single
author_profile: true
except: 
header:
    image: /assets/header_images/aquaduct.jpg
    caption: " "
---
# What is the most common street name in Australia?
## Cleaning OSM data

For this first step we are just considering Sydney. Firstly because I live in Sydney, familiar with the streets so I can easily sanity check the answers. Secondly because Australia is just too big to deal with initially. Once we complete this analysis on Australia, we will apply it to the entire of Australia.

I downloaded this data in geojson form from __[metro extracts](https://mapzen.com/data/metro-extracts/)__ that is sadly going away at the end of January 2018. This gives you a folder with lots of different types 

### Reading
First we need to read in the data. To do this I'm using geopandas as it can handle the geometry column. As a first round of basic cleaning I'm removing all streets that do not have a defined name and then changing the street name to all lower case


```python
import pandas as pd
import geopandas as gpd
import matplotlib.pyplot as plt
import seaborn as sns
import shapely
%matplotlib inline
```


```python
streets = gpd.read_file('Sydney/ex_skDAYAuv8avqtvLU5LSdM4Fc7xxkR_roads_gen0.geojson')
```


```python
# len(streets)
```




    22709




```python
clean_streets
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
    <tr>
      <th>7</th>
      <td>None</td>
      <td>0</td>
      <td>highway</td>
      <td>LINESTRING (151.214294884165 -33.8460758542856...</td>
      <td>8.0</td>
      <td>burton street</td>
      <td>0</td>
      <td>1986123.0</td>
      <td>None</td>
      <td>None</td>
      <td>0</td>
      <td>residential</td>
      <td>3.0</td>
    </tr>
    <tr>
      <th>9</th>
      <td>None</td>
      <td>0</td>
      <td>highway</td>
      <td>LINESTRING (151.2844036368792 -33.799186565923...</td>
      <td>10.0</td>
      <td>east esplanade</td>
      <td>0</td>
      <td>1999322.0</td>
      <td>None</td>
      <td>None</td>
      <td>0</td>
      <td>tertiary</td>
      <td>4.0</td>
    </tr>
    <tr>
      <th>10</th>
      <td>None</td>
      <td>0</td>
      <td>highway</td>
      <td>LINESTRING (151.2422065379138 -33.874905577889...</td>
      <td>11.0</td>
      <td>william street</td>
      <td>0</td>
      <td>1986065.0</td>
      <td>None</td>
      <td>None</td>
      <td>0</td>
      <td>secondary</td>
      <td>5.0</td>
    </tr>
    <tr>
      <th>11</th>
      <td>None</td>
      <td>0</td>
      <td>highway</td>
      <td>LINESTRING (151.2943003175943 -33.812494010678...</td>
      <td>12.0</td>
      <td>north head scenic drive</td>
      <td>0</td>
      <td>1999796.0</td>
      <td>None</td>
      <td>None</td>
      <td>0</td>
      <td>tertiary_link</td>
      <td>3.0</td>
    </tr>
    <tr>
      <th>14</th>
      <td>None</td>
      <td>0</td>
      <td>highway</td>
      <td>LINESTRING (151.2081345206086 -33.873118304675...</td>
      <td>15.0</td>
      <td>park street</td>
      <td>1</td>
      <td>1884057.0</td>
      <td>None</td>
      <td>None</td>
      <td>0</td>
      <td>secondary</td>
      <td>5.0</td>
    </tr>
    <tr>
      <th>16</th>
      <td>None</td>
      <td>0</td>
      <td>highway</td>
      <td>LINESTRING (151.2622370233846 -33.796498824852...</td>
      <td>17.0</td>
      <td>woodland street</td>
      <td>0</td>
      <td>2035257.0</td>
      <td>None</td>
      <td>None</td>
      <td>0</td>
      <td>tertiary</td>
      <td>4.0</td>
    </tr>
    <tr>
      <th>18</th>
      <td>None</td>
      <td>0</td>
      <td>highway</td>
      <td>LINESTRING (151.2188336847334 -33.834071041103...</td>
      <td>19.0</td>
      <td>lindsay street</td>
      <td>0</td>
      <td>2035314.0</td>
      <td>None</td>
      <td>None</td>
      <td>0</td>
      <td>residential</td>
      <td>3.0</td>
    </tr>
    <tr>
      <th>19</th>
      <td>None</td>
      <td>0</td>
      <td>highway</td>
      <td>LINESTRING (151.2222535850473 -33.832313356007...</td>
      <td>20.0</td>
      <td>yeo street</td>
      <td>0</td>
      <td>2035318.0</td>
      <td>None</td>
      <td>None</td>
      <td>0</td>
      <td>residential</td>
      <td>3.0</td>
    </tr>
    <tr>
      <th>20</th>
      <td>None</td>
      <td>0</td>
      <td>highway</td>
      <td>LINESTRING (151.0977030301166 -33.912845424413...</td>
      <td>21.0</td>
      <td>loch street</td>
      <td>0</td>
      <td>1881388.0</td>
      <td>None</td>
      <td>None</td>
      <td>0</td>
      <td>residential</td>
      <td>3.0</td>
    </tr>
    <tr>
      <th>21</th>
      <td>None</td>
      <td>0</td>
      <td>highway</td>
      <td>LINESTRING (151.2115387467635 -33.849897163942...</td>
      <td>22.0</td>
      <td>alfred street south</td>
      <td>0</td>
      <td>1986109.0</td>
      <td>None</td>
      <td>None</td>
      <td>0</td>
      <td>unclassified</td>
      <td>3.0</td>
    </tr>
    <tr>
      <th>22</th>
      <td>None</td>
      <td>0</td>
      <td>highway</td>
      <td>LINESTRING (151.2055012619076 -33.832485017384...</td>
      <td>23.0</td>
      <td>ridge street</td>
      <td>0</td>
      <td>2035327.0</td>
      <td>None</td>
      <td>None</td>
      <td>0</td>
      <td>residential</td>
      <td>3.0</td>
    </tr>
    <tr>
      <th>24</th>
      <td>None</td>
      <td>0</td>
      <td>highway</td>
      <td>LINESTRING (151.2322113698364 -33.892129215444...</td>
      <td>25.0</td>
      <td>lang road</td>
      <td>0</td>
      <td>1955001.0</td>
      <td>None</td>
      <td>None</td>
      <td>0</td>
      <td>tertiary</td>
      <td>4.0</td>
    </tr>
    <tr>
      <th>26</th>
      <td>None</td>
      <td>0</td>
      <td>highway</td>
      <td>LINESTRING (151.2082809524571 -33.841834024546...</td>
      <td>27.0</td>
      <td>middlemiss street</td>
      <td>1</td>
      <td>2035333.0</td>
      <td>None</td>
      <td>None</td>
      <td>0</td>
      <td>residential</td>
      <td>3.0</td>
    </tr>
    <tr>
      <th>27</th>
      <td>None</td>
      <td>0</td>
      <td>highway</td>
      <td>LINESTRING (151.0978234780652 -33.915575745553...</td>
      <td>28.0</td>
      <td>evaline street</td>
      <td>0</td>
      <td>1881389.0</td>
      <td>None</td>
      <td>None</td>
      <td>0</td>
      <td>tertiary</td>
      <td>4.0</td>
    </tr>
    <tr>
      <th>28</th>
      <td>None</td>
      <td>0</td>
      <td>highway</td>
      <td>LINESTRING (151.2098214624413 -33.837187013608...</td>
      <td>29.0</td>
      <td>arthur street</td>
      <td>1</td>
      <td>2035355.0</td>
      <td>None</td>
      <td>None</td>
      <td>0</td>
      <td>primary</td>
      <td>16.0</td>
    </tr>
    <tr>
      <th>29</th>
      <td>None</td>
      <td>0</td>
      <td>highway</td>
      <td>LINESTRING (151.080396160263 -34.0345762232977...</td>
      <td>30.0</td>
      <td>hotham road</td>
      <td>0</td>
      <td>1881523.0</td>
      <td>None</td>
      <td>None</td>
      <td>0</td>
      <td>tertiary</td>
      <td>4.0</td>
    </tr>
    <tr>
      <th>30</th>
      <td>None</td>
      <td>0</td>
      <td>highway</td>
      <td>LINESTRING (151.1993181833936 -33.831132848765...</td>
      <td>31.0</td>
      <td>newlands street</td>
      <td>0</td>
      <td>2063794.0</td>
      <td>None</td>
      <td>None</td>
      <td>0</td>
      <td>residential</td>
      <td>3.0</td>
    </tr>
    <tr>
      <th>31</th>
      <td>None</td>
      <td>0</td>
      <td>highway</td>
      <td>LINESTRING (151.1979622429172 -33.831258828769...</td>
      <td>32.0</td>
      <td>shirley road</td>
      <td>0</td>
      <td>2063796.0</td>
      <td>None</td>
      <td>None</td>
      <td>0</td>
      <td>residential</td>
      <td>3.0</td>
    </tr>
    <tr>
      <th>32</th>
      <td>None</td>
      <td>0</td>
      <td>highway</td>
      <td>LINESTRING (151.1872839497313 -33.830249982903...</td>
      <td>33.0</td>
      <td>chisholm street</td>
      <td>0</td>
      <td>2064284.0</td>
      <td>None</td>
      <td>None</td>
      <td>0</td>
      <td>residential</td>
      <td>3.0</td>
    </tr>
    <tr>
      <th>33</th>
      <td>None</td>
      <td>0</td>
      <td>highway</td>
      <td>LINESTRING (151.1875812558368 -33.829788475315...</td>
      <td>34.0</td>
      <td>oscar street</td>
      <td>0</td>
      <td>2064287.0</td>
      <td>None</td>
      <td>None</td>
      <td>0</td>
      <td>residential</td>
      <td>3.0</td>
    </tr>
    <tr>
      <th>34</th>
      <td>None</td>
      <td>0</td>
      <td>highway</td>
      <td>LINESTRING (151.185450156955 -33.8330372171660...</td>
      <td>35.0</td>
      <td>evelyn street</td>
      <td>0</td>
      <td>2064288.0</td>
      <td>None</td>
      <td>None</td>
      <td>0</td>
      <td>residential</td>
      <td>3.0</td>
    </tr>
    <tr>
      <th>35</th>
      <td>None</td>
      <td>0</td>
      <td>highway</td>
      <td>LINESTRING (151.2197773193927 -33.834185454081...</td>
      <td>36.0</td>
      <td>bydown street</td>
      <td>0</td>
      <td>2035308.0</td>
      <td>None</td>
      <td>None</td>
      <td>0</td>
      <td>residential</td>
      <td>3.0</td>
    </tr>
    <tr>
      <th>39</th>
      <td>None</td>
      <td>0</td>
      <td>highway</td>
      <td>LINESTRING (151.2100572453776 -33.839160867986...</td>
      <td>40.0</td>
      <td>mount street</td>
      <td>1</td>
      <td>2035357.0</td>
      <td>None</td>
      <td>None</td>
      <td>0</td>
      <td>tertiary</td>
      <td>4.0</td>
    </tr>
    <tr>
      <th>41</th>
      <td>None</td>
      <td>0</td>
      <td>highway</td>
      <td>LINESTRING (151.2130760716246 -33.861991160574...</td>
      <td>42.0</td>
      <td>albert street</td>
      <td>0</td>
      <td>2077105.0</td>
      <td>None</td>
      <td>None</td>
      <td>0</td>
      <td>unclassified</td>
      <td>3.0</td>
    </tr>
    <tr>
      <th>42</th>
      <td>None</td>
      <td>0</td>
      <td>highway</td>
      <td>LINESTRING (151.2105778453837 -33.861571646320...</td>
      <td>43.0</td>
      <td>alfred street</td>
      <td>1</td>
      <td>2077106.0</td>
      <td>None</td>
      <td>None</td>
      <td>0</td>
      <td>unclassified</td>
      <td>3.0</td>
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
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>118919</th>
      <td>None</td>
      <td>0</td>
      <td>highway</td>
      <td>LINESTRING (150.8439692423712 -33.717725531189...</td>
      <td>118920.0</td>
      <td>hollingsworth road</td>
      <td>1</td>
      <td>495579036.0</td>
      <td>None</td>
      <td>None</td>
      <td>0</td>
      <td>unclassified</td>
      <td>3.0</td>
    </tr>
    <tr>
      <th>118943</th>
      <td>None</td>
      <td>0</td>
      <td>highway</td>
      <td>LINESTRING (150.8208806195275 -33.780751072807...</td>
      <td>118944.0</td>
      <td>great western highway</td>
      <td>1</td>
      <td>495668705.0</td>
      <td>A44</td>
      <td>None</td>
      <td>0</td>
      <td>trunk</td>
      <td>8.0</td>
    </tr>
    <tr>
      <th>119014</th>
      <td>None</td>
      <td>0</td>
      <td>highway</td>
      <td>LINESTRING (151.0860440542593 -33.877870759955...</td>
      <td>119015.0</td>
      <td>woodward avenue</td>
      <td>0</td>
      <td>496099910.0</td>
      <td>None</td>
      <td>None</td>
      <td>0</td>
      <td>residential</td>
      <td>3.0</td>
    </tr>
    <tr>
      <th>119015</th>
      <td>None</td>
      <td>1</td>
      <td>highway</td>
      <td>LINESTRING (151.0774953512127 -34.013965877765...</td>
      <td>119016.0</td>
      <td>bates drive</td>
      <td>0</td>
      <td>496411056.0</td>
      <td>None</td>
      <td>None</td>
      <td>0</td>
      <td>tertiary</td>
      <td>24.0</td>
    </tr>
    <tr>
      <th>119016</th>
      <td>None</td>
      <td>0</td>
      <td>highway</td>
      <td>LINESTRING (151.0254028288176 -33.662424080983...</td>
      <td>119017.0</td>
      <td>dural downs way</td>
      <td>0</td>
      <td>496593070.0</td>
      <td>None</td>
      <td>None</td>
      <td>0</td>
      <td>residential</td>
      <td>3.0</td>
    </tr>
    <tr>
      <th>119021</th>
      <td>None</td>
      <td>0</td>
      <td>highway</td>
      <td>LINESTRING (151.1068764364067 -33.719979173495...</td>
      <td>119022.0</td>
      <td>pacific motorway</td>
      <td>1</td>
      <td>496782508.0</td>
      <td>M1</td>
      <td>None</td>
      <td>0</td>
      <td>motorway</td>
      <td>9.0</td>
    </tr>
    <tr>
      <th>119024</th>
      <td>None</td>
      <td>0</td>
      <td>highway</td>
      <td>LINESTRING (150.9091110469029 -33.646792334478...</td>
      <td>119025.0</td>
      <td>george street</td>
      <td>0</td>
      <td>497043118.0</td>
      <td>None</td>
      <td>None</td>
      <td>0</td>
      <td>residential</td>
      <td>3.0</td>
    </tr>
    <tr>
      <th>119050</th>
      <td>None</td>
      <td>0</td>
      <td>highway</td>
      <td>LINESTRING (151.0174866241845 -33.770862941633...</td>
      <td>119051.0</td>
      <td>andrew place</td>
      <td>0</td>
      <td>497143341.0</td>
      <td>None</td>
      <td>None</td>
      <td>0</td>
      <td>residential</td>
      <td>3.0</td>
    </tr>
    <tr>
      <th>119058</th>
      <td>None</td>
      <td>0</td>
      <td>highway</td>
      <td>LINESTRING (151.1065146734658 -33.720026615067...</td>
      <td>119059.0</td>
      <td>pacific motorway</td>
      <td>1</td>
      <td>496782509.0</td>
      <td>M1</td>
      <td>None</td>
      <td>0</td>
      <td>motorway</td>
      <td>9.0</td>
    </tr>
    <tr>
      <th>119060</th>
      <td>None</td>
      <td>0</td>
      <td>highway</td>
      <td>LINESTRING (151.1766106856872 -33.877506314805...</td>
      <td>119061.0</td>
      <td>the crescent</td>
      <td>0</td>
      <td>496325955.0</td>
      <td>None</td>
      <td>None</td>
      <td>0</td>
      <td>primary</td>
      <td>6.0</td>
    </tr>
    <tr>
      <th>119067</th>
      <td>None</td>
      <td>0</td>
      <td>highway</td>
      <td>LINESTRING (150.9017573517916 -33.703580482850...</td>
      <td>119068.0</td>
      <td>estuary crescent</td>
      <td>0</td>
      <td>497285233.0</td>
      <td>None</td>
      <td>None</td>
      <td>0</td>
      <td>service</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>119068</th>
      <td>None</td>
      <td>0</td>
      <td>highway</td>
      <td>LINESTRING (150.9023442526518 -33.704910607065...</td>
      <td>119069.0</td>
      <td>basin street</td>
      <td>0</td>
      <td>497285234.0</td>
      <td>None</td>
      <td>None</td>
      <td>0</td>
      <td>residential</td>
      <td>3.0</td>
    </tr>
    <tr>
      <th>119069</th>
      <td>None</td>
      <td>0</td>
      <td>highway</td>
      <td>LINESTRING (150.9022301749496 -33.698080948540...</td>
      <td>119070.0</td>
      <td>berambing street</td>
      <td>0</td>
      <td>497285237.0</td>
      <td>None</td>
      <td>None</td>
      <td>0</td>
      <td>residential</td>
      <td>3.0</td>
    </tr>
    <tr>
      <th>119070</th>
      <td>None</td>
      <td>0</td>
      <td>highway</td>
      <td>LINESTRING (150.8941159897635 -33.706245928059...</td>
      <td>119071.0</td>
      <td>brielle street</td>
      <td>0</td>
      <td>497285239.0</td>
      <td>None</td>
      <td>None</td>
      <td>0</td>
      <td>residential</td>
      <td>3.0</td>
    </tr>
    <tr>
      <th>119079</th>
      <td>None</td>
      <td>0</td>
      <td>highway</td>
      <td>LINESTRING (151.0180139297131 -33.941199726468...</td>
      <td>119080.0</td>
      <td>m5 motorway onramp</td>
      <td>1</td>
      <td>497290713.0</td>
      <td>None</td>
      <td>None</td>
      <td>0</td>
      <td>motorway_link</td>
      <td>3.0</td>
    </tr>
    <tr>
      <th>119081</th>
      <td>None</td>
      <td>0</td>
      <td>highway</td>
      <td>LINESTRING (150.8986455702384 -33.701640742818...</td>
      <td>119082.0</td>
      <td>wambool way</td>
      <td>0</td>
      <td>497285238.0</td>
      <td>None</td>
      <td>None</td>
      <td>0</td>
      <td>service</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>119089</th>
      <td>None</td>
      <td>0</td>
      <td>highway</td>
      <td>LINESTRING (150.6991221603629 -33.726654102087...</td>
      <td>119090.0</td>
      <td>wedgebill</td>
      <td>1</td>
      <td>497515071.0</td>
      <td>None</td>
      <td>None</td>
      <td>0</td>
      <td>residential</td>
      <td>3.0</td>
    </tr>
    <tr>
      <th>119090</th>
      <td>None</td>
      <td>0</td>
      <td>highway</td>
      <td>LINESTRING (150.8979379699725 -33.714879707423...</td>
      <td>119091.0</td>
      <td>admiral street</td>
      <td>0</td>
      <td>497565671.0</td>
      <td>None</td>
      <td>None</td>
      <td>0</td>
      <td>residential</td>
      <td>3.0</td>
    </tr>
    <tr>
      <th>119091</th>
      <td>None</td>
      <td>0</td>
      <td>highway</td>
      <td>LINESTRING (150.8979686477381 -33.714220806015...</td>
      <td>119092.0</td>
      <td>empress street</td>
      <td>0</td>
      <td>497565672.0</td>
      <td>None</td>
      <td>None</td>
      <td>0</td>
      <td>residential</td>
      <td>3.0</td>
    </tr>
    <tr>
      <th>119092</th>
      <td>None</td>
      <td>0</td>
      <td>highway</td>
      <td>LINESTRING (150.8967309759155 -33.713823839081...</td>
      <td>119093.0</td>
      <td>lodore street</td>
      <td>0</td>
      <td>497565675.0</td>
      <td>None</td>
      <td>None</td>
      <td>0</td>
      <td>residential</td>
      <td>3.0</td>
    </tr>
    <tr>
      <th>119093</th>
      <td>None</td>
      <td>0</td>
      <td>highway</td>
      <td>LINESTRING (150.8987651799967 -33.714226170433...</td>
      <td>119094.0</td>
      <td>lyrebird crescent</td>
      <td>0</td>
      <td>497565673.0</td>
      <td>None</td>
      <td>None</td>
      <td>0</td>
      <td>residential</td>
      <td>3.0</td>
    </tr>
    <tr>
      <th>119104</th>
      <td>None</td>
      <td>0</td>
      <td>highway</td>
      <td>LINESTRING (150.6954878509659 -33.715816971836...</td>
      <td>119105.0</td>
      <td>castlereagh road</td>
      <td>0</td>
      <td>497495053.0</td>
      <td>None</td>
      <td>None</td>
      <td>0</td>
      <td>secondary</td>
      <td>5.0</td>
    </tr>
    <tr>
      <th>119124</th>
      <td>None</td>
      <td>0</td>
      <td>highway</td>
      <td>LINESTRING (150.9009829477574 -33.706179124291...</td>
      <td>119125.0</td>
      <td>tomah crescent</td>
      <td>0</td>
      <td>497285232.0</td>
      <td>None</td>
      <td>None</td>
      <td>0</td>
      <td>residential</td>
      <td>3.0</td>
    </tr>
    <tr>
      <th>119125</th>
      <td>None</td>
      <td>0</td>
      <td>highway</td>
      <td>LINESTRING (150.8996328746132 -33.704272744233...</td>
      <td>119126.0</td>
      <td>springwood avenue</td>
      <td>0</td>
      <td>497285235.0</td>
      <td>None</td>
      <td>None</td>
      <td>0</td>
      <td>residential</td>
      <td>3.0</td>
    </tr>
    <tr>
      <th>119128</th>
      <td>None</td>
      <td>0</td>
      <td>highway</td>
      <td>LINESTRING (150.9022301749496 -33.698080948540...</td>
      <td>119129.0</td>
      <td>dunphy street</td>
      <td>0</td>
      <td>497285236.0</td>
      <td>None</td>
      <td>None</td>
      <td>0</td>
      <td>residential</td>
      <td>3.0</td>
    </tr>
    <tr>
      <th>119145</th>
      <td>None</td>
      <td>0</td>
      <td>highway</td>
      <td>LINESTRING (150.8979379699725 -33.714879707423...</td>
      <td>119146.0</td>
      <td>scribbly street</td>
      <td>0</td>
      <td>497565674.0</td>
      <td>None</td>
      <td>None</td>
      <td>0</td>
      <td>residential</td>
      <td>3.0</td>
    </tr>
    <tr>
      <th>119170</th>
      <td>None</td>
      <td>0</td>
      <td>highway</td>
      <td>LINESTRING (151.0067215783006 -34.074631159269...</td>
      <td>119171.0</td>
      <td>heathcote road</td>
      <td>0</td>
      <td>497980904.0</td>
      <td>A6</td>
      <td>None</td>
      <td>0</td>
      <td>trunk</td>
      <td>8.0</td>
    </tr>
    <tr>
      <th>119173</th>
      <td>None</td>
      <td>0</td>
      <td>highway</td>
      <td>LINESTRING (151.0065373440689 -34.074236623086...</td>
      <td>119174.0</td>
      <td>heathcote road</td>
      <td>0</td>
      <td>497980906.0</td>
      <td>A6</td>
      <td>None</td>
      <td>0</td>
      <td>trunk</td>
      <td>8.0</td>
    </tr>
    <tr>
      <th>119179</th>
      <td>None</td>
      <td>0</td>
      <td>highway</td>
      <td>LINESTRING (150.9382219833577 -34.114709564569...</td>
      <td>119180.0</td>
      <td>woronora dam road</td>
      <td>0</td>
      <td>497985253.0</td>
      <td>None</td>
      <td>None</td>
      <td>0</td>
      <td>unclassified</td>
      <td>3.0</td>
    </tr>
    <tr>
      <th>119191</th>
      <td>None</td>
      <td>0</td>
      <td>highway</td>
      <td>LINESTRING (151.0070811619468 -34.075601280742...</td>
      <td>119192.0</td>
      <td>heathcote road</td>
      <td>0</td>
      <td>497980903.0</td>
      <td>A6</td>
      <td>None</td>
      <td>0</td>
      <td>trunk</td>
      <td>8.0</td>
    </tr>
  </tbody>
</table>
<p>69530 rows × 13 columns</p>
</div>




```python

```


```python

```


```python

```


```python
streets = gpd.read_file('Sydney/ex_skDAYAuv8avqtvLU5LSdM4Fc7xxkR_roads.geojson')
```


```python
streets = streets[~streets["name"].isnull()]
streets["name"] = streets["name"].apply(lambda x: x.lower())
```

This is what the data we get from zen maps looks likes:


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
streets['geometry'].plot()
```




    <matplotlib.axes._subplots.AxesSubplot at 0x1033a8a20>




![png](cleaning_osm_files/cleaning_osm_13_1.png)


## Data Cleanup
In OSM there is a lot more than just streets that are included. First we are going to exclude these streets.

First we will consider the class column: here we have *highway*, *railway* and *man-made*. The railway class include rail-lines (both goods and passenger), light rail lines (used and disued). These are not streets


```python
streets[streets["class"] == 'railway']
```




<div>
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
      <td>Inner West Light Rail</td>
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
      <td>Inner West Light Rail</td>
      <td>1</td>
      <td>2761538.0</td>
      <td>None</td>
      <td>None</td>
      <td>0</td>
      <td>light_rail</td>
      <td>7.0</td>
    </tr>
    <tr>
      <th>179</th>
      <td>None</td>
      <td>0</td>
      <td>railway</td>
      <td>LINESTRING (151.2070169614585 -33.881036604784...</td>
      <td>180.0</td>
      <td>None</td>
      <td>1</td>
      <td>2949708.0</td>
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
      <td>The Goodsline</td>
      <td>0</td>
      <td>3188010.0</td>
      <td>None</td>
      <td>None</td>
      <td>0</td>
      <td>disused</td>
      <td>7.0</td>
    </tr>
    <tr>
      <th>565</th>
      <td>None</td>
      <td>0</td>
      <td>railway</td>
      <td>LINESTRING (151.1914580536927 -33.895390865426...</td>
      <td>566.0</td>
      <td>None</td>
      <td>0</td>
      <td>3726276.0</td>
      <td>Western Siding</td>
      <td>yard</td>
      <td>0</td>
      <td>rail</td>
      <td>7.0</td>
    </tr>
    <tr>
      <th>952</th>
      <td>None</td>
      <td>1</td>
      <td>railway</td>
      <td>LINESTRING (151.1959432938998 -33.937350253617...</td>
      <td>953.0</td>
      <td>None</td>
      <td>0</td>
      <td>4331725.0</td>
      <td>None</td>
      <td>None</td>
      <td>0</td>
      <td>rail</td>
      <td>37.0</td>
    </tr>
    <tr>
      <th>971</th>
      <td>None</td>
      <td>0</td>
      <td>railway</td>
      <td>LINESTRING (151.1688774580003 -33.920945360364...</td>
      <td>972.0</td>
      <td>None</td>
      <td>0</td>
      <td>4331845.0</td>
      <td>None</td>
      <td>None</td>
      <td>0</td>
      <td>rail</td>
      <td>7.0</td>
    </tr>
    <tr>
      <th>973</th>
      <td>None</td>
      <td>0</td>
      <td>railway</td>
      <td>LINESTRING (151.1910183390522 -33.932442565490...</td>
      <td>974.0</td>
      <td>None</td>
      <td>0</td>
      <td>4331765.0</td>
      <td>None</td>
      <td>None</td>
      <td>0</td>
      <td>rail</td>
      <td>7.0</td>
    </tr>
    <tr>
      <th>1134</th>
      <td>None</td>
      <td>0</td>
      <td>railway</td>
      <td>LINESTRING (151.0687451472137 -33.887053051063...</td>
      <td>1135.0</td>
      <td>None</td>
      <td>0</td>
      <td>4341342.0</td>
      <td>None</td>
      <td>spur</td>
      <td>0</td>
      <td>rail</td>
      <td>7.0</td>
    </tr>
    <tr>
      <th>1159</th>
      <td>None</td>
      <td>0</td>
      <td>railway</td>
      <td>LINESTRING (151.0685417184237 -33.880858321703...</td>
      <td>1160.0</td>
      <td>None</td>
      <td>0</td>
      <td>4341499.0</td>
      <td>None</td>
      <td>siding</td>
      <td>0</td>
      <td>rail</td>
      <td>7.0</td>
    </tr>
    <tr>
      <th>1253</th>
      <td>None</td>
      <td>0</td>
      <td>railway</td>
      <td>LINESTRING (151.1522834690116 -33.875266251182...</td>
      <td>1254.0</td>
      <td>None</td>
      <td>1</td>
      <td>4352775.0</td>
      <td>None</td>
      <td>None</td>
      <td>0</td>
      <td>light_rail</td>
      <td>7.0</td>
    </tr>
    <tr>
      <th>1259</th>
      <td>None</td>
      <td>0</td>
      <td>railway</td>
      <td>LINESTRING (151.1950446700606 -33.867383741800...</td>
      <td>1260.0</td>
      <td>None</td>
      <td>1</td>
      <td>4375371.0</td>
      <td>None</td>
      <td>None</td>
      <td>1</td>
      <td>light_rail</td>
      <td>-13.0</td>
    </tr>
    <tr>
      <th>1285</th>
      <td>None</td>
      <td>1</td>
      <td>railway</td>
      <td>LINESTRING (151.1790470534826 -33.875418466544...</td>
      <td>1286.0</td>
      <td>None</td>
      <td>1</td>
      <td>4376901.0</td>
      <td>None</td>
      <td>None</td>
      <td>0</td>
      <td>light_rail</td>
      <td>17.0</td>
    </tr>
    <tr>
      <th>1304</th>
      <td>None</td>
      <td>0</td>
      <td>railway</td>
      <td>LINESTRING (151.1982223333727 -33.870718230520...</td>
      <td>1305.0</td>
      <td>None</td>
      <td>1</td>
      <td>4375374.0</td>
      <td>None</td>
      <td>None</td>
      <td>1</td>
      <td>light_rail</td>
      <td>-3.0</td>
    </tr>
    <tr>
      <th>2859</th>
      <td>None</td>
      <td>0</td>
      <td>railway</td>
      <td>LINESTRING (151.068782949597 -33.8518282706146...</td>
      <td>2860.0</td>
      <td>None</td>
      <td>0</td>
      <td>5124339.0</td>
      <td>Down Homebush Bay Loop</td>
      <td>None</td>
      <td>1</td>
      <td>rail</td>
      <td>-13.0</td>
    </tr>
    <tr>
      <th>2861</th>
      <td>None</td>
      <td>0</td>
      <td>railway</td>
      <td>LINESTRING (151.0655819845941 -33.854180819378...</td>
      <td>2862.0</td>
      <td>None</td>
      <td>0</td>
      <td>5124340.0</td>
      <td>Down Homebush Bay Loop</td>
      <td>None</td>
      <td>0</td>
      <td>rail</td>
      <td>7.0</td>
    </tr>
    <tr>
      <th>3351</th>
      <td>None</td>
      <td>0</td>
      <td>railway</td>
      <td>LINESTRING (151.0735129413769 -33.896960041519...</td>
      <td>3352.0</td>
      <td>None</td>
      <td>0</td>
      <td>5666201.0</td>
      <td>None</td>
      <td>siding</td>
      <td>0</td>
      <td>rail</td>
      <td>7.0</td>
    </tr>
    <tr>
      <th>3389</th>
      <td>None</td>
      <td>0</td>
      <td>railway</td>
      <td>LINESTRING (151.0639920213811 -33.883211205743...</td>
      <td>3390.0</td>
      <td>None</td>
      <td>0</td>
      <td>5665521.0</td>
      <td>None</td>
      <td>yard</td>
      <td>0</td>
      <td>rail</td>
      <td>7.0</td>
    </tr>
    <tr>
      <th>8904</th>
      <td>None</td>
      <td>1</td>
      <td>railway</td>
      <td>LINESTRING (151.1935311498046 -33.874915552353...</td>
      <td>8905.0</td>
      <td>None</td>
      <td>1</td>
      <td>16175279.0</td>
      <td>None</td>
      <td>None</td>
      <td>0</td>
      <td>light_rail</td>
      <td>27.0</td>
    </tr>
    <tr>
      <th>11627</th>
      <td>None</td>
      <td>0</td>
      <td>railway</td>
      <td>LINESTRING (150.6417417467128 -33.757431025617...</td>
      <td>11628.0</td>
      <td>Main Western Line</td>
      <td>0</td>
      <td>22720526.0</td>
      <td>Up Main</td>
      <td>None</td>
      <td>0</td>
      <td>rail</td>
      <td>7.0</td>
    </tr>
    <tr>
      <th>16382</th>
      <td>None</td>
      <td>1</td>
      <td>railway</td>
      <td>LINESTRING (151.0420491208822 -33.861854535552...</td>
      <td>16383.0</td>
      <td>None</td>
      <td>0</td>
      <td>23462305.0</td>
      <td>Up Suburban</td>
      <td>None</td>
      <td>0</td>
      <td>rail</td>
      <td>27.0</td>
    </tr>
    <tr>
      <th>18161</th>
      <td>None</td>
      <td>0</td>
      <td>railway</td>
      <td>LINESTRING (151.1719162331768 -33.871902928715...</td>
      <td>18162.0</td>
      <td>None</td>
      <td>1</td>
      <td>24490654.0</td>
      <td>None</td>
      <td>None</td>
      <td>0</td>
      <td>light_rail</td>
      <td>7.0</td>
    </tr>
    <tr>
      <th>18282</th>
      <td>None</td>
      <td>0</td>
      <td>railway</td>
      <td>LINESTRING (151.166322653913 -33.8740697345045...</td>
      <td>18283.0</td>
      <td>None</td>
      <td>1</td>
      <td>24490655.0</td>
      <td>None</td>
      <td>None</td>
      <td>1</td>
      <td>light_rail</td>
      <td>-13.0</td>
    </tr>
    <tr>
      <th>18494</th>
      <td>None</td>
      <td>0</td>
      <td>railway</td>
      <td>LINESTRING (151.1863722501231 -33.931750974659...</td>
      <td>18495.0</td>
      <td>None</td>
      <td>0</td>
      <td>24781783.0</td>
      <td>None</td>
      <td>None</td>
      <td>0</td>
      <td>rail</td>
      <td>7.0</td>
    </tr>
    <tr>
      <th>18549</th>
      <td>None</td>
      <td>0</td>
      <td>railway</td>
      <td>LINESTRING (151.1946429254415 -33.936758155977...</td>
      <td>18550.0</td>
      <td>None</td>
      <td>0</td>
      <td>24781781.0</td>
      <td>None</td>
      <td>None</td>
      <td>0</td>
      <td>rail</td>
      <td>7.0</td>
    </tr>
    <tr>
      <th>18598</th>
      <td>None</td>
      <td>0</td>
      <td>railway</td>
      <td>LINESTRING (151.1745649983987 -33.925209234508...</td>
      <td>18599.0</td>
      <td>None</td>
      <td>0</td>
      <td>24781277.0</td>
      <td>None</td>
      <td>None</td>
      <td>0</td>
      <td>rail</td>
      <td>7.0</td>
    </tr>
    <tr>
      <th>27548</th>
      <td>None</td>
      <td>0</td>
      <td>railway</td>
      <td>LINESTRING (151.0077149176457 -34.181638726133...</td>
      <td>27549.0</td>
      <td>None</td>
      <td>0</td>
      <td>38810821.0</td>
      <td>None</td>
      <td>None</td>
      <td>1</td>
      <td>rail</td>
      <td>-13.0</td>
    </tr>
    <tr>
      <th>27553</th>
      <td>None</td>
      <td>0</td>
      <td>railway</td>
      <td>LINESTRING (151.0089942475271 -34.177326153132...</td>
      <td>27554.0</td>
      <td>None</td>
      <td>0</td>
      <td>38810877.0</td>
      <td>Down Illawarra</td>
      <td>None</td>
      <td>0</td>
      <td>rail</td>
      <td>7.0</td>
    </tr>
    <tr>
      <th>27558</th>
      <td>None</td>
      <td>0</td>
      <td>railway</td>
      <td>LINESTRING (151.0056677216147 -34.174414112331...</td>
      <td>27559.0</td>
      <td>None</td>
      <td>0</td>
      <td>38810717.0</td>
      <td>Down Illawarra</td>
      <td>None</td>
      <td>1</td>
      <td>rail</td>
      <td>-13.0</td>
    </tr>
    <tr>
      <th>27578</th>
      <td>None</td>
      <td>0</td>
      <td>railway</td>
      <td>LINESTRING (151.0042920836658 -34.190084583228...</td>
      <td>27579.0</td>
      <td>None</td>
      <td>0</td>
      <td>38810876.0</td>
      <td>None</td>
      <td>None</td>
      <td>1</td>
      <td>rail</td>
      <td>-13.0</td>
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
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>117577</th>
      <td>None</td>
      <td>0</td>
      <td>railway</td>
      <td>LINESTRING (151.0204410774151 -33.842953343896...</td>
      <td>117578.0</td>
      <td>8 Road</td>
      <td>0</td>
      <td>476918113.0</td>
      <td>None</td>
      <td>siding</td>
      <td>0</td>
      <td>rail</td>
      <td>7.0</td>
    </tr>
    <tr>
      <th>117578</th>
      <td>None</td>
      <td>0</td>
      <td>railway</td>
      <td>LINESTRING (151.0203149297723 -33.842935574261...</td>
      <td>117579.0</td>
      <td>10 Road</td>
      <td>0</td>
      <td>476918115.0</td>
      <td>None</td>
      <td>siding</td>
      <td>0</td>
      <td>rail</td>
      <td>7.0</td>
    </tr>
    <tr>
      <th>117579</th>
      <td>None</td>
      <td>0</td>
      <td>railway</td>
      <td>LINESTRING (151.0202559211739 -33.842928868739...</td>
      <td>117580.0</td>
      <td>11 Road</td>
      <td>0</td>
      <td>476918116.0</td>
      <td>None</td>
      <td>siding</td>
      <td>0</td>
      <td>rail</td>
      <td>7.0</td>
    </tr>
    <tr>
      <th>117580</th>
      <td>None</td>
      <td>0</td>
      <td>railway</td>
      <td>LINESTRING (151.0212047526132 -33.841214015168...</td>
      <td>117581.0</td>
      <td>None</td>
      <td>0</td>
      <td>476918117.0</td>
      <td>None</td>
      <td>yard</td>
      <td>0</td>
      <td>rail</td>
      <td>7.0</td>
    </tr>
    <tr>
      <th>117581</th>
      <td>None</td>
      <td>0</td>
      <td>railway</td>
      <td>LINESTRING (151.0211491805952 -33.841206722913...</td>
      <td>117582.0</td>
      <td>None</td>
      <td>0</td>
      <td>476918118.0</td>
      <td>None</td>
      <td>yard</td>
      <td>0</td>
      <td>rail</td>
      <td>7.0</td>
    </tr>
    <tr>
      <th>117582</th>
      <td>None</td>
      <td>0</td>
      <td>railway</td>
      <td>LINESTRING (151.0209130623827 -33.841180068461...</td>
      <td>117583.0</td>
      <td>None</td>
      <td>0</td>
      <td>476918122.0</td>
      <td>None</td>
      <td>yard</td>
      <td>0</td>
      <td>rail</td>
      <td>7.0</td>
    </tr>
    <tr>
      <th>117583</th>
      <td>None</td>
      <td>0</td>
      <td>railway</td>
      <td>LINESTRING (151.0208487731854 -33.841164478121...</td>
      <td>117584.0</td>
      <td>None</td>
      <td>0</td>
      <td>476918123.0</td>
      <td>None</td>
      <td>yard</td>
      <td>0</td>
      <td>rail</td>
      <td>7.0</td>
    </tr>
    <tr>
      <th>117586</th>
      <td>None</td>
      <td>0</td>
      <td>railway</td>
      <td>LINESTRING (151.0207253915707 -33.841148803962...</td>
      <td>117587.0</td>
      <td>None</td>
      <td>0</td>
      <td>476918125.0</td>
      <td>None</td>
      <td>yard</td>
      <td>0</td>
      <td>rail</td>
      <td>7.0</td>
    </tr>
    <tr>
      <th>117587</th>
      <td>None</td>
      <td>0</td>
      <td>railway</td>
      <td>LINESTRING (151.0206690651814 -33.841146624667...</td>
      <td>117588.0</td>
      <td>None</td>
      <td>0</td>
      <td>476918126.0</td>
      <td>None</td>
      <td>yard</td>
      <td>0</td>
      <td>rail</td>
      <td>7.0</td>
    </tr>
    <tr>
      <th>117588</th>
      <td>None</td>
      <td>0</td>
      <td>railway</td>
      <td>LINESTRING (151.020601926137 -33.8411399191448...</td>
      <td>117589.0</td>
      <td>None</td>
      <td>0</td>
      <td>476918127.0</td>
      <td>None</td>
      <td>yard</td>
      <td>0</td>
      <td>rail</td>
      <td>7.0</td>
    </tr>
    <tr>
      <th>117589</th>
      <td>None</td>
      <td>0</td>
      <td>railway</td>
      <td>LINESTRING (151.0108212509618 -33.832122416253...</td>
      <td>117590.0</td>
      <td>None</td>
      <td>0</td>
      <td>476918128.0</td>
      <td>None</td>
      <td>crossover</td>
      <td>0</td>
      <td>rail</td>
      <td>7.0</td>
    </tr>
    <tr>
      <th>117598</th>
      <td>None</td>
      <td>0</td>
      <td>railway</td>
      <td>LINESTRING (151.0193416231757 -33.837598816511...</td>
      <td>117599.0</td>
      <td>None</td>
      <td>0</td>
      <td>476918103.0</td>
      <td>None</td>
      <td>yard</td>
      <td>0</td>
      <td>rail</td>
      <td>7.0</td>
    </tr>
    <tr>
      <th>117599</th>
      <td>None</td>
      <td>0</td>
      <td>railway</td>
      <td>LINESTRING (150.7920899555929 -34.072947821654...</td>
      <td>117600.0</td>
      <td>Southern Line</td>
      <td>0</td>
      <td>476184048.0</td>
      <td>None</td>
      <td>None</td>
      <td>0</td>
      <td>rail</td>
      <td>7.0</td>
    </tr>
    <tr>
      <th>117606</th>
      <td>None</td>
      <td>0</td>
      <td>railway</td>
      <td>LINESTRING (151.020964024354 -33.8411934795061...</td>
      <td>117607.0</td>
      <td>None</td>
      <td>0</td>
      <td>476918121.0</td>
      <td>None</td>
      <td>yard</td>
      <td>0</td>
      <td>rail</td>
      <td>7.0</td>
    </tr>
    <tr>
      <th>117607</th>
      <td>None</td>
      <td>0</td>
      <td>railway</td>
      <td>LINESTRING (151.0207924467961 -33.841157772598...</td>
      <td>117608.0</td>
      <td>None</td>
      <td>0</td>
      <td>476918124.0</td>
      <td>None</td>
      <td>yard</td>
      <td>0</td>
      <td>rail</td>
      <td>7.0</td>
    </tr>
    <tr>
      <th>117616</th>
      <td>None</td>
      <td>0</td>
      <td>railway</td>
      <td>LINESTRING (151.0210257989804 -33.841195658800...</td>
      <td>117617.0</td>
      <td>None</td>
      <td>0</td>
      <td>476918120.0</td>
      <td>None</td>
      <td>yard</td>
      <td>0</td>
      <td>rail</td>
      <td>7.0</td>
    </tr>
    <tr>
      <th>117682</th>
      <td>None</td>
      <td>0</td>
      <td>railway</td>
      <td>LINESTRING (151.0210820415508 -33.841200017390...</td>
      <td>117683.0</td>
      <td>None</td>
      <td>0</td>
      <td>476918119.0</td>
      <td>None</td>
      <td>yard</td>
      <td>0</td>
      <td>rail</td>
      <td>7.0</td>
    </tr>
    <tr>
      <th>118687</th>
      <td>None</td>
      <td>0</td>
      <td>railway</td>
      <td>LINESTRING (150.8997021929525 -33.966556911406...</td>
      <td>118688.0</td>
      <td>Down Airport &amp; East Hills</td>
      <td>0</td>
      <td>491841672.0</td>
      <td>Down East Hills</td>
      <td>None</td>
      <td>0</td>
      <td>rail</td>
      <td>7.0</td>
    </tr>
    <tr>
      <th>118688</th>
      <td>None</td>
      <td>0</td>
      <td>railway</td>
      <td>LINESTRING (150.8959400595321 -33.969824344901...</td>
      <td>118689.0</td>
      <td>None</td>
      <td>0</td>
      <td>491841674.0</td>
      <td>None</td>
      <td>crossover</td>
      <td>0</td>
      <td>rail</td>
      <td>7.0</td>
    </tr>
    <tr>
      <th>118691</th>
      <td>None</td>
      <td>0</td>
      <td>railway</td>
      <td>LINESTRING (151.2067484890998 -33.885025720142...</td>
      <td>118692.0</td>
      <td>None</td>
      <td>0</td>
      <td>491842176.0</td>
      <td>Down Airport</td>
      <td>None</td>
      <td>0</td>
      <td>rail</td>
      <td>7.0</td>
    </tr>
    <tr>
      <th>118692</th>
      <td>None</td>
      <td>0</td>
      <td>railway</td>
      <td>LINESTRING (151.2065136281729 -33.885027564161...</td>
      <td>118693.0</td>
      <td>None</td>
      <td>0</td>
      <td>491842175.0</td>
      <td>Up Airport</td>
      <td>None</td>
      <td>0</td>
      <td>rail</td>
      <td>7.0</td>
    </tr>
    <tr>
      <th>118700</th>
      <td>None</td>
      <td>0</td>
      <td>railway</td>
      <td>LINESTRING (151.2060026673554 -33.885414724268...</td>
      <td>118701.0</td>
      <td>None</td>
      <td>0</td>
      <td>492102295.0</td>
      <td>Up Illawarra Local</td>
      <td>None</td>
      <td>0</td>
      <td>rail</td>
      <td>7.0</td>
    </tr>
    <tr>
      <th>118706</th>
      <td>None</td>
      <td>0</td>
      <td>railway</td>
      <td>LINESTRING (151.2057508749841 -33.885325708457...</td>
      <td>118707.0</td>
      <td>None</td>
      <td>0</td>
      <td>492102299.0</td>
      <td>Up Local</td>
      <td>None</td>
      <td>0</td>
      <td>rail</td>
      <td>7.0</td>
    </tr>
    <tr>
      <th>118709</th>
      <td>None</td>
      <td>0</td>
      <td>railway</td>
      <td>LINESTRING (150.8959400595321 -33.969824344901...</td>
      <td>118710.0</td>
      <td>None</td>
      <td>0</td>
      <td>491841673.0</td>
      <td>Down Leppington</td>
      <td>None</td>
      <td>0</td>
      <td>rail</td>
      <td>7.0</td>
    </tr>
    <tr>
      <th>118728</th>
      <td>None</td>
      <td>0</td>
      <td>railway</td>
      <td>LINESTRING (151.2061213551044 -33.885521677353...</td>
      <td>118729.0</td>
      <td>None</td>
      <td>0</td>
      <td>492102290.0</td>
      <td>Down Illawarra Local</td>
      <td>None</td>
      <td>0</td>
      <td>rail</td>
      <td>7.0</td>
    </tr>
    <tr>
      <th>118740</th>
      <td>None</td>
      <td>0</td>
      <td>railway</td>
      <td>LINESTRING (151.2056654633908 -33.885575740628...</td>
      <td>118741.0</td>
      <td>None</td>
      <td>0</td>
      <td>492102300.0</td>
      <td>Down Local</td>
      <td>None</td>
      <td>0</td>
      <td>rail</td>
      <td>7.0</td>
    </tr>
    <tr>
      <th>119011</th>
      <td>None</td>
      <td>1</td>
      <td>railway</td>
      <td>LINESTRING (151.0828508844264 -33.764859152028...</td>
      <td>119012.0</td>
      <td>None</td>
      <td>0</td>
      <td>496407140.0</td>
      <td>Down Relief</td>
      <td>siding</td>
      <td>0</td>
      <td>rail</td>
      <td>27.0</td>
    </tr>
    <tr>
      <th>119012</th>
      <td>None</td>
      <td>0</td>
      <td>railway</td>
      <td>LINESTRING (151.0767539718769 -33.733313356521...</td>
      <td>119013.0</td>
      <td>None</td>
      <td>0</td>
      <td>496407142.0</td>
      <td>None</td>
      <td>siding</td>
      <td>0</td>
      <td>rail</td>
      <td>7.0</td>
    </tr>
    <tr>
      <th>119013</th>
      <td>None</td>
      <td>0</td>
      <td>railway</td>
      <td>LINESTRING (151.0826675722039 -33.763634472156...</td>
      <td>119014.0</td>
      <td>None</td>
      <td>0</td>
      <td>496407141.0</td>
      <td>Down Relief</td>
      <td>siding</td>
      <td>0</td>
      <td>rail</td>
      <td>7.0</td>
    </tr>
    <tr>
      <th>119063</th>
      <td>None</td>
      <td>0</td>
      <td>railway</td>
      <td>LINESTRING (151.0814477538351 -33.770939133133...</td>
      <td>119064.0</td>
      <td>None</td>
      <td>0</td>
      <td>496407139.0</td>
      <td>Down Relief</td>
      <td>siding</td>
      <td>0</td>
      <td>rail</td>
      <td>7.0</td>
    </tr>
  </tbody>
</table>
<p>2873 rows × 13 columns</p>
</div>




```python
streets[streets["class"] == 'railway']["geometry"].plot()
```




    <matplotlib.axes._subplots.AxesSubplot at 0x110fb7358>




![png](cleaning_osm_files/cleaning_osm_16_1.png)


Lets look at the man-made class. This contains thing susch as Fish Market and Taronga Zoo Ferry Wharf. Also not we would call streets.

(In Sydney this class contains piers and groyne)


```python
streets[streets["class"] == 'man_made'].head()
```




<div>
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
      <td>Gunnamatta Baths</td>
      <td>0</td>
      <td>3619874.0</td>
      <td>None</td>
      <td>None</td>
      <td>0</td>
      <td>pier</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>491</th>
      <td>None</td>
      <td>0</td>
      <td>man_made</td>
      <td>LINESTRING (151.1501344328569 -34.056000870723...</td>
      <td>492.0</td>
      <td>None</td>
      <td>0</td>
      <td>3681494.0</td>
      <td>None</td>
      <td>None</td>
      <td>0</td>
      <td>pier</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>983</th>
      <td>None</td>
      <td>0</td>
      <td>man_made</td>
      <td>LINESTRING (151.1901614570908 -33.872610361343...</td>
      <td>984.0</td>
      <td>None</td>
      <td>0</td>
      <td>4332268.0</td>
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
      <td>Fish Market</td>
      <td>0</td>
      <td>4332267.0</td>
      <td>None</td>
      <td>None</td>
      <td>0</td>
      <td>pier</td>
      <td>20.0</td>
    </tr>
    <tr>
      <th>985</th>
      <td>None</td>
      <td>0</td>
      <td>man_made</td>
      <td>LINESTRING (151.1892821954478 -33.871995632564...</td>
      <td>986.0</td>
      <td>None</td>
      <td>0</td>
      <td>4332270.0</td>
      <td>None</td>
      <td>None</td>
      <td>0</td>
      <td>pier</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>1031</th>
      <td>None</td>
      <td>0</td>
      <td>man_made</td>
      <td>LINESTRING (151.228668423184 -33.8623220781117...</td>
      <td>1032.0</td>
      <td>None</td>
      <td>0</td>
      <td>4333820.0</td>
      <td>None</td>
      <td>None</td>
      <td>0</td>
      <td>pier</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>1070</th>
      <td>None</td>
      <td>0</td>
      <td>man_made</td>
      <td>LINESTRING (151.2802279403563 -33.645735041212...</td>
      <td>1071.0</td>
      <td>None</td>
      <td>0</td>
      <td>4335940.0</td>
      <td>None</td>
      <td>None</td>
      <td>0</td>
      <td>pier</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>1142</th>
      <td>None</td>
      <td>0</td>
      <td>man_made</td>
      <td>LINESTRING (151.1686243245244 -33.948267263405...</td>
      <td>1143.0</td>
      <td>None</td>
      <td>0</td>
      <td>4343325.0</td>
      <td>None</td>
      <td>None</td>
      <td>0</td>
      <td>pier</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>1174</th>
      <td>private</td>
      <td>0</td>
      <td>man_made</td>
      <td>LINESTRING (151.1930428201256 -33.837839460951...</td>
      <td>1175.0</td>
      <td>None</td>
      <td>0</td>
      <td>4354509.0</td>
      <td>None</td>
      <td>None</td>
      <td>0</td>
      <td>pier</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>1175</th>
      <td>None</td>
      <td>0</td>
      <td>man_made</td>
      <td>LINESTRING (151.2452296389314 -33.872377009158...</td>
      <td>1176.0</td>
      <td>Double Bay Marina</td>
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
      <td>Taronga Zoo Ferry Wharf</td>
      <td>0</td>
      <td>4354530.0</td>
      <td>None</td>
      <td>None</td>
      <td>0</td>
      <td>pier</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>1184</th>
      <td>None</td>
      <td>0</td>
      <td>man_made</td>
      <td>LINESTRING (151.2092934863604 -33.856524567143...</td>
      <td>1185.0</td>
      <td>None</td>
      <td>0</td>
      <td>4354619.0</td>
      <td>None</td>
      <td>None</td>
      <td>0</td>
      <td>pier</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>1186</th>
      <td>private</td>
      <td>0</td>
      <td>man_made</td>
      <td>LINESTRING (151.1930428201256 -33.837839460951...</td>
      <td>1187.0</td>
      <td>None</td>
      <td>0</td>
      <td>4354508.0</td>
      <td>None</td>
      <td>None</td>
      <td>0</td>
      <td>pier</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>1232</th>
      <td>None</td>
      <td>0</td>
      <td>man_made</td>
      <td>LINESTRING (151.1992120684994 -33.868798104141...</td>
      <td>1233.0</td>
      <td>None</td>
      <td>0</td>
      <td>4368330.0</td>
      <td>None</td>
      <td>None</td>
      <td>0</td>
      <td>pier</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>1233</th>
      <td>None</td>
      <td>0</td>
      <td>man_made</td>
      <td>LINESTRING (151.199300749035 -33.8694535689698...</td>
      <td>1234.0</td>
      <td>None</td>
      <td>0</td>
      <td>4368329.0</td>
      <td>None</td>
      <td>None</td>
      <td>0</td>
      <td>pier</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>1283</th>
      <td>None</td>
      <td>0</td>
      <td>man_made</td>
      <td>LINESTRING (151.1676292249797 -33.858244868951...</td>
      <td>1284.0</td>
      <td>None</td>
      <td>0</td>
      <td>4377145.0</td>
      <td>None</td>
      <td>None</td>
      <td>0</td>
      <td>pier</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>4585</th>
      <td>None</td>
      <td>0</td>
      <td>man_made</td>
      <td>LINESTRING (150.6729879730011 -33.722081103354...</td>
      <td>4586.0</td>
      <td>None</td>
      <td>0</td>
      <td>8083176.0</td>
      <td>None</td>
      <td>None</td>
      <td>0</td>
      <td>pier</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>4586</th>
      <td>None</td>
      <td>0</td>
      <td>man_made</td>
      <td>LINESTRING (150.6710360792091 -33.721620853051...</td>
      <td>4587.0</td>
      <td>None</td>
      <td>0</td>
      <td>8083177.0</td>
      <td>None</td>
      <td>None</td>
      <td>0</td>
      <td>pier</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>4587</th>
      <td>None</td>
      <td>0</td>
      <td>man_made</td>
      <td>LINESTRING (150.6730991170372 -33.722531379192...</td>
      <td>4588.0</td>
      <td>None</td>
      <td>0</td>
      <td>8083178.0</td>
      <td>None</td>
      <td>None</td>
      <td>0</td>
      <td>pier</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>4588</th>
      <td>None</td>
      <td>0</td>
      <td>man_made</td>
      <td>LINESTRING (150.6716048751584 -33.721433768972...</td>
      <td>4589.0</td>
      <td>None</td>
      <td>0</td>
      <td>8083179.0</td>
      <td>None</td>
      <td>None</td>
      <td>0</td>
      <td>pier</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>4589</th>
      <td>None</td>
      <td>0</td>
      <td>man_made</td>
      <td>LINESTRING (150.671297259312 -33.7215430689896...</td>
      <td>4590.0</td>
      <td>None</td>
      <td>0</td>
      <td>8083180.0</td>
      <td>None</td>
      <td>None</td>
      <td>0</td>
      <td>pier</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>4590</th>
      <td>None</td>
      <td>0</td>
      <td>man_made</td>
      <td>LINESTRING (150.672504421007 -33.7215531272734...</td>
      <td>4591.0</td>
      <td>None</td>
      <td>0</td>
      <td>8083181.0</td>
      <td>None</td>
      <td>None</td>
      <td>0</td>
      <td>pier</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>4592</th>
      <td>None</td>
      <td>0</td>
      <td>man_made</td>
      <td>LINESTRING (150.6729212530518 -33.721831071182...</td>
      <td>4593.0</td>
      <td>None</td>
      <td>0</td>
      <td>8083182.0</td>
      <td>None</td>
      <td>None</td>
      <td>0</td>
      <td>pier</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>4594</th>
      <td>None</td>
      <td>0</td>
      <td>man_made</td>
      <td>LINESTRING (150.6730268650318 -33.722347983151...</td>
      <td>4595.0</td>
      <td>None</td>
      <td>0</td>
      <td>8083183.0</td>
      <td>None</td>
      <td>None</td>
      <td>0</td>
      <td>pier</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>4598</th>
      <td>None</td>
      <td>0</td>
      <td>man_made</td>
      <td>LINESTRING (150.692699024142 -33.7258244613117...</td>
      <td>4599.0</td>
      <td>None</td>
      <td>0</td>
      <td>8083446.0</td>
      <td>None</td>
      <td>None</td>
      <td>0</td>
      <td>pier</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>4599</th>
      <td>None</td>
      <td>0</td>
      <td>man_made</td>
      <td>LINESTRING (150.6927071545881 -33.725701750249...</td>
      <td>4600.0</td>
      <td>None</td>
      <td>0</td>
      <td>8083447.0</td>
      <td>None</td>
      <td>None</td>
      <td>0</td>
      <td>pier</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>4600</th>
      <td>None</td>
      <td>0</td>
      <td>man_made</td>
      <td>LINESTRING (150.6927392572773 -33.725219874635...</td>
      <td>4601.0</td>
      <td>None</td>
      <td>0</td>
      <td>8083448.0</td>
      <td>None</td>
      <td>None</td>
      <td>0</td>
      <td>pier</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>4601</th>
      <td>None</td>
      <td>0</td>
      <td>man_made</td>
      <td>LINESTRING (150.6926829308879 -33.725947172374...</td>
      <td>4602.0</td>
      <td>None</td>
      <td>0</td>
      <td>8083449.0</td>
      <td>None</td>
      <td>None</td>
      <td>0</td>
      <td>pier</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>4602</th>
      <td>None</td>
      <td>0</td>
      <td>man_made</td>
      <td>LINESTRING (150.6927071545881 -33.725576859891...</td>
      <td>4603.0</td>
      <td>None</td>
      <td>0</td>
      <td>8083450.0</td>
      <td>None</td>
      <td>None</td>
      <td>0</td>
      <td>pier</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>4604</th>
      <td>None</td>
      <td>0</td>
      <td>man_made</td>
      <td>LINESTRING (150.6927500699323 -33.725097163573...</td>
      <td>4605.0</td>
      <td>None</td>
      <td>0</td>
      <td>8083451.0</td>
      <td>None</td>
      <td>None</td>
      <td>0</td>
      <td>pier</td>
      <td>0.0</td>
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
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>114053</th>
      <td>None</td>
      <td>0</td>
      <td>man_made</td>
      <td>LINESTRING (151.1813730316133 -33.864167018819...</td>
      <td>114054.0</td>
      <td>None</td>
      <td>0</td>
      <td>444481237.0</td>
      <td>None</td>
      <td>None</td>
      <td>0</td>
      <td>pier</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>114054</th>
      <td>None</td>
      <td>0</td>
      <td>man_made</td>
      <td>LINESTRING (151.1915676889861 -33.860596076610...</td>
      <td>114055.0</td>
      <td>None</td>
      <td>0</td>
      <td>444481239.0</td>
      <td>None</td>
      <td>None</td>
      <td>0</td>
      <td>pier</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>114055</th>
      <td>None</td>
      <td>0</td>
      <td>man_made</td>
      <td>LINESTRING (151.1916276195939 -33.859208620177...</td>
      <td>114056.0</td>
      <td>None</td>
      <td>0</td>
      <td>444481242.0</td>
      <td>None</td>
      <td>None</td>
      <td>0</td>
      <td>pier</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>114056</th>
      <td>None</td>
      <td>0</td>
      <td>man_made</td>
      <td>LINESTRING (151.1852395197282 -33.867753635187...</td>
      <td>114057.0</td>
      <td>None</td>
      <td>0</td>
      <td>444481233.0</td>
      <td>None</td>
      <td>None</td>
      <td>0</td>
      <td>pier</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>114057</th>
      <td>None</td>
      <td>0</td>
      <td>man_made</td>
      <td>LINESTRING (151.1913251167083 -33.859276178317...</td>
      <td>114058.0</td>
      <td>None</td>
      <td>0</td>
      <td>444481244.0</td>
      <td>None</td>
      <td>None</td>
      <td>0</td>
      <td>pier</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>114058</th>
      <td>None</td>
      <td>0</td>
      <td>man_made</td>
      <td>LINESTRING (151.1915054952646 -33.859471811937...</td>
      <td>114059.0</td>
      <td>None</td>
      <td>0</td>
      <td>444481245.0</td>
      <td>None</td>
      <td>None</td>
      <td>0</td>
      <td>pier</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>114063</th>
      <td>None</td>
      <td>0</td>
      <td>man_made</td>
      <td>LINESTRING (151.1912419682288 -33.859195544408...</td>
      <td>114064.0</td>
      <td>None</td>
      <td>0</td>
      <td>444481243.0</td>
      <td>None</td>
      <td>None</td>
      <td>0</td>
      <td>pier</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>114066</th>
      <td>None</td>
      <td>0</td>
      <td>man_made</td>
      <td>LINESTRING (151.1902352178387 -33.859979000898...</td>
      <td>114067.0</td>
      <td>None</td>
      <td>0</td>
      <td>444481240.0</td>
      <td>None</td>
      <td>None</td>
      <td>0</td>
      <td>pier</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>114068</th>
      <td>None</td>
      <td>0</td>
      <td>man_made</td>
      <td>LINESTRING (151.191630637079 -33.8596720556043...</td>
      <td>114069.0</td>
      <td>None</td>
      <td>0</td>
      <td>444481241.0</td>
      <td>None</td>
      <td>None</td>
      <td>0</td>
      <td>pier</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>114888</th>
      <td>None</td>
      <td>0</td>
      <td>man_made</td>
      <td>LINESTRING (151.019759461049 -33.9760344131439...</td>
      <td>114889.0</td>
      <td>None</td>
      <td>0</td>
      <td>450292619.0</td>
      <td>None</td>
      <td>None</td>
      <td>0</td>
      <td>pier</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>115584</th>
      <td>None</td>
      <td>1</td>
      <td>man_made</td>
      <td>LINESTRING (151.2226233946153 -33.806099875842...</td>
      <td>115585.0</td>
      <td>None</td>
      <td>0</td>
      <td>459326658.0</td>
      <td>None</td>
      <td>None</td>
      <td>0</td>
      <td>pier</td>
      <td>10.0</td>
    </tr>
    <tr>
      <th>115586</th>
      <td>None</td>
      <td>1</td>
      <td>man_made</td>
      <td>LINESTRING (151.222184266708 -33.8064445397006...</td>
      <td>115587.0</td>
      <td>None</td>
      <td>0</td>
      <td>459326659.0</td>
      <td>None</td>
      <td>None</td>
      <td>0</td>
      <td>pier</td>
      <td>10.0</td>
    </tr>
    <tr>
      <th>115587</th>
      <td>None</td>
      <td>0</td>
      <td>man_made</td>
      <td>LINESTRING (151.2216120341784 -33.806313446735...</td>
      <td>115588.0</td>
      <td>Northbridge Marina</td>
      <td>0</td>
      <td>459326660.0</td>
      <td>None</td>
      <td>None</td>
      <td>0</td>
      <td>pier</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>115681</th>
      <td>None</td>
      <td>0</td>
      <td>man_made</td>
      <td>LINESTRING (151.2561603950409 -33.869505872045...</td>
      <td>115682.0</td>
      <td>None</td>
      <td>0</td>
      <td>460602930.0</td>
      <td>None</td>
      <td>None</td>
      <td>0</td>
      <td>pier</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>115682</th>
      <td>None</td>
      <td>0</td>
      <td>man_made</td>
      <td>LINESTRING (151.2556741608378 -33.870640362640...</td>
      <td>115683.0</td>
      <td>None</td>
      <td>0</td>
      <td>460602931.0</td>
      <td>None</td>
      <td>None</td>
      <td>0</td>
      <td>pier</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>115683</th>
      <td>None</td>
      <td>0</td>
      <td>man_made</td>
      <td>LINESTRING (151.2552523834701 -33.870453949113...</td>
      <td>115684.0</td>
      <td>None</td>
      <td>0</td>
      <td>460602932.0</td>
      <td>None</td>
      <td>None</td>
      <td>0</td>
      <td>pier</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>115684</th>
      <td>None</td>
      <td>0</td>
      <td>man_made</td>
      <td>LINESTRING (151.2559548707751 -33.868721325908...</td>
      <td>115685.0</td>
      <td>None</td>
      <td>0</td>
      <td>460602935.0</td>
      <td>None</td>
      <td>None</td>
      <td>0</td>
      <td>pier</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>115685</th>
      <td>None</td>
      <td>0</td>
      <td>man_made</td>
      <td>LINESTRING (151.254922052666 -33.8709139479597...</td>
      <td>115686.0</td>
      <td>None</td>
      <td>0</td>
      <td>460602934.0</td>
      <td>None</td>
      <td>None</td>
      <td>0</td>
      <td>pier</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>115686</th>
      <td>None</td>
      <td>0</td>
      <td>man_made</td>
      <td>LINESTRING (151.2553613482114 -33.869520372738...</td>
      <td>115687.0</td>
      <td>None</td>
      <td>0</td>
      <td>460602933.0</td>
      <td>None</td>
      <td>None</td>
      <td>0</td>
      <td>pier</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>115687</th>
      <td>None</td>
      <td>0</td>
      <td>man_made</td>
      <td>LINESTRING (151.2546214776183 -33.868144231875...</td>
      <td>115688.0</td>
      <td>None</td>
      <td>0</td>
      <td>460602936.0</td>
      <td>None</td>
      <td>None</td>
      <td>0</td>
      <td>pier</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>115689</th>
      <td>None</td>
      <td>0</td>
      <td>man_made</td>
      <td>LINESTRING (151.2545223197037 -33.868762900148...</td>
      <td>115690.0</td>
      <td>None</td>
      <td>0</td>
      <td>460602937.0</td>
      <td>None</td>
      <td>None</td>
      <td>0</td>
      <td>pier</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>115887</th>
      <td>None</td>
      <td>0</td>
      <td>man_made</td>
      <td>LINESTRING (151.1807659303664 -33.842088834403...</td>
      <td>115888.0</td>
      <td>None</td>
      <td>0</td>
      <td>462836531.0</td>
      <td>None</td>
      <td>None</td>
      <td>0</td>
      <td>pier</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>116802</th>
      <td>None</td>
      <td>0</td>
      <td>man_made</td>
      <td>LINESTRING (151.2220385892309 -33.806138013501...</td>
      <td>116803.0</td>
      <td>None</td>
      <td>0</td>
      <td>468710441.0</td>
      <td>None</td>
      <td>None</td>
      <td>0</td>
      <td>pier</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>116803</th>
      <td>None</td>
      <td>0</td>
      <td>man_made</td>
      <td>LINESTRING (151.2225172797212 -33.806275728170...</td>
      <td>116804.0</td>
      <td>None</td>
      <td>0</td>
      <td>468710442.0</td>
      <td>None</td>
      <td>None</td>
      <td>0</td>
      <td>pier</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>117040</th>
      <td>None</td>
      <td>0</td>
      <td>man_made</td>
      <td>LINESTRING (151.2213643489396 -33.805668375466...</td>
      <td>117041.0</td>
      <td>None</td>
      <td>0</td>
      <td>472249548.0</td>
      <td>None</td>
      <td>None</td>
      <td>0</td>
      <td>pier</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>117041</th>
      <td>None</td>
      <td>0</td>
      <td>man_made</td>
      <td>LINESTRING (151.2216203322625 -33.806212528620...</td>
      <td>117042.0</td>
      <td>None</td>
      <td>0</td>
      <td>472249550.0</td>
      <td>None</td>
      <td>None</td>
      <td>0</td>
      <td>pier</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>117049</th>
      <td>None</td>
      <td>0</td>
      <td>man_made</td>
      <td>LINESTRING (151.2219026347614 -33.805698801775...</td>
      <td>117050.0</td>
      <td>None</td>
      <td>0</td>
      <td>472249549.0</td>
      <td>None</td>
      <td>None</td>
      <td>0</td>
      <td>pier</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>117327</th>
      <td>None</td>
      <td>0</td>
      <td>man_made</td>
      <td>LINESTRING (151.2110986968469 -33.973918066411...</td>
      <td>117328.0</td>
      <td>None</td>
      <td>0</td>
      <td>475229632.0</td>
      <td>None</td>
      <td>None</td>
      <td>0</td>
      <td>pier</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>117347</th>
      <td>None</td>
      <td>0</td>
      <td>man_made</td>
      <td>LINESTRING (151.2110754789751 -33.973914043098...</td>
      <td>117348.0</td>
      <td>Bulk Liquids Berth 1</td>
      <td>0</td>
      <td>475229629.0</td>
      <td>None</td>
      <td>None</td>
      <td>0</td>
      <td>pier</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>117523</th>
      <td>None</td>
      <td>0</td>
      <td>man_made</td>
      <td>LINESTRING (151.2105149811099 -33.976203224673...</td>
      <td>117524.0</td>
      <td>Bulk Liquids Berth 2</td>
      <td>0</td>
      <td>475229631.0</td>
      <td>None</td>
      <td>None</td>
      <td>0</td>
      <td>pier</td>
      <td>0.0</td>
    </tr>
  </tbody>
</table>
<p>977 rows × 13 columns</p>
</div>




```python
streets[streets["class"] == 'man_made']["geometry"].plot()
```




    <matplotlib.axes._subplots.AxesSubplot at 0x11686a588>




![png](cleaning_osm_files/cleaning_osm_19_1.png)


This leaves us with the highway class which contains actual roads, among other things. These other things are going to cause us problems as they include road type such as 'footway' and 'cycleway which are not roads. All of this information is contained in the 'type' attribute. For a complete list of highway types and explanation on what they are, have a look at the  __[OSM wiki](http://www.wiki.openstreetmap.org/wiki/Key:highway)__


The highway types we see in Sydney include:


## Eventually remove the below

To do here
* Introduce the access column: remove the private roads
* Make a plot showing the removed streets (coloured), under an opaque one of actual streets
* Check that my get streets function does it properly - might be able to do it simpler



```python
streets[streets["class"] == 'highway']["type"].unique()
```




    array(['residential', 'footway', 'primary', 'unclassified', 'tertiary',
           'secondary', 'tertiary_link', 'service', 'cycleway', 'pedestrian',
           'path', 'trunk', 'steps', 'primary_link', 'living_street',
           'motorway_link', 'secondary_link', 'motorway', 'track',
           'trunk_link', 'bridleway', 'raceway', 'road'], dtype=object)



At this point I need to decide what is a road. Where do I draw the line between footway (pedestrains only), living_street (pedestrians have priority), cycleway (bikes allowed) or secondary_link?

I decided I wanted to be as incusive as possible with this definition. Motorways should be included as they have name, such as the Hume Highway (some have less interesting names like the M5 Motorway). I felt that link roads should be included as they are also mostly named. Although there are also ones named 'Westlink M7 onramp', there shouldn't be so many of them that Westlink M7 is a contender for the most common street name. 

While I tried to be as inclusive as possible, a street is something a car can drive on. This means I will remove the pedestrian and on-car orientated streets. 

There is also the 'access' column which gives information on who can access this road. 


```python
streets["access"].unique()
```




    array([None, 'yes', 'private', 'public', 'no', 'designated', 'permissive',
           'destination', 'psv', 'delivery', 'official', 'hgv', 'bus',
           'yes|bicycle|yes|yes', 'yes|yes|bicycle|yes|yes', 'military'], dtype=object)



I wanted to keep as many streets as possible, but also keep it to roads that are public. Roads that I can see and drive down. This means I will remove any private roads, no access roads, military roads and HGV.

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



Lastly there is service. This tag provides additional context on what the road is used for. There are a few services I want to exclude. Namely, driveway, private_road, bicycle_training_track, go-kart, drive-through (bye KFC drive-through) as they are not public roads I can drive on. I've kept parking_aile as I could drive on it inthe process to parking.


```python
streets["service"].unique()
```




    array([None, 'alley', 'parking_aisle', 'yard', 'spur', 'siding',
           'driveway', 'bus', 'emergency_access', 'yes', 'private_road',
           'bicycle_training_track', 'go-kart', 'drive-through', 'slipway',
           'crossover', 'Lane', 'parking lane', 'access', 'depot', 'parking',
           'lane', 'laneway'], dtype=object)




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
```


```python
clean_streets = get_streets(streets)
len(clean_streets),  len(streets)
```




    -3268



All of this processing removes just 3268 from Sydney. One final word. To save the file you need to use the geopandas function to_file. This has the unfortunate side effect of making the working file much bigger - 55MB compared to 9MB. 


```python
clean_streets.to_file("clean_streets_sydney.geojson")
```

This is all for cleaning up OSM street data. Next we will do some exploration on Sydney and other adjustments that need to be done. 

### Initial exploring
The initial dataframe is looking good. Before separating out the street descriptor from the streetname, what is the most common complete street name?


```python
clean_streets.groupby("name").count()["id"].sort_values(ascending = False).head(10)
```




    name
    victoria road        197
    pacific highway      191
    princes highway      155
    westlink m7          145
    church street        137
    george street        131
    m2 hills motorway    130
    windsor road         124
    western motorway     119
    parramatta road      117
    Name: id, dtype: int64



Victoria Road sounds about right - but Pacific Highway as the second most common name? Something wrong is going here


```python
sns.set_style('white')
# plt.rcParams['figure.figsize']=(12,20)
victoria_road = clean_streets[clean_streets["name"] == "victoria road"]
ax = victoria_road["geometry"].plot(linewidth = 4, figsize = (12, 20))
ax.get_xaxis().get_major_formatter().set_useOffset(False)
# ax.set_xlabel("longitude")
# ax.set_ylabel("latiude")
# ax.set_title("Victoria Road")
# ax.figure.set_size_inches(12, 20)
ax.figure.savefig("victoria_road.png")
```


![png](cleaning_osm_files/cleaning_osm_37_0.png)



```python
sns.set_style('white')
pacific = clean_streets[clean_streets["name"] == "pacific highway"]
ax = pacific["geometry"].plot(linewidth = 2)
ax.get_xaxis().get_major_formatter().set_useOffset(False)
ax.set_xlabel("longitude")
ax.set_ylabel("latiude")
ax.set_title("Pacific Highway")
ax.figure.savefig("pacific.png")
```


![png](cleaning_osm_files/cleaning_osm_38_0.png)


Have a look at that big long multi-coloured Victoria Road. OSM defines streets in terms of street segments, so a single street can appear multiple times. The multiple joint Victoria roads we can see is really one 20km long road. So before we can do anything we need to join up these segments into the right streets. These line segments are often for each interesection. Roads with a sperator down the middle will also be recorded as two line segmnets - one for each direction of the traffic to indicate it is not possible to do a u-turn on these roads. 

The coordinates of the streets are recorded in the geometry column as a shapely lingstring type. Lets first try to join these together. Instead of victoria road, I'm going to work with anzac parade as there is only one of those in Sydney




```python
anzac_parade = clean_streets[clean_streets["name"] == "anzac parade"]
ax = anzac_parade["geometry"].plot()
ax.get_xaxis().get_major_formatter().set_useOffset(False)

ax.set_xlabel("longitude")
ax.set_ylabel("latiude")
ax.set_title("Anzac Parade")
```




    <matplotlib.text.Text at 0x115b1c4a8>




![png](cleaning_osm_files/cleaning_osm_40_1.png)



```python
len(streets)
```




    119197




```python
def reduce_street_distance(street):
    
    # Make everything a list so we can easily ad to it later
    street = street.apply(lambda x: x.apply(lambda y: [y]))
    
   
    whole_street = street.copy()
    current_street = []
    
     # Keep on repeating while we are still reducing the number of streets
    while len(whole_street) != len(current_street):

        used = [] # keeps track on the street id-s that have already been merged into another string
        current_street = pd.DataFrame(whole_street)
        whole_street = []

        count = 0

        for s, segment in current_street.iterrows():

            if segment["id"] in used:
                continue

            segment_points = [(coords[0], coords[1]) for seg in segment["geometry"] for coords in seg.coords[:]]
            to_join = [segment]


            for s2, segment2 in current_street.iloc[count+1:].iterrows():


                segment_points2 = [(coords[0], coords[1]) for seg in segment2["geometry"] for coords in seg.coords[:]]

                # Calculate the distance between the points
                
                # Limit things within 100km of the first point
                if distance(segment_points, segment_points2) < 1:
                    if 
                    
#                 common = list(set(segment_points).intersection(segment_points2))   
#                 if len(common) > 0:
#                     used.append(segment2["id"])
#                     to_join.append(segment2)


            # Once found all common points, join
            new_point = pd.Series(data={"access": [seg["access"] for seg in to_join],
                            "bridge": [seg["bridge"] for seg in to_join],
                            "class": [seg["class"] for seg in to_join],
                            "geometry": [coords  for seg in to_join for coords in seg["geometry"]],
                            "id": [seg["id"]  for seg in to_join],
                            "name": segment["name"],
                            "oneway": [seg["oneway"] for seg in to_join],
                            "osm_id": [seg["osm_id"]  for seg in to_join],
                            "ref": [seg["ref"] for seg in to_join],
                            "service":[seg["service"]  for seg in to_join],
                            "tunnel":[seg["tunnel"]  for seg in to_join],
                            "type":[seg["type"]  for seg in to_join],
                            "z_order":[seg["z_order"] for seg in to_join] })

            whole_street.append(new_point)
            count += 1


 
    return pd.DataFrame(whole_street)
```


      File "<ipython-input-9-735890f34370>", line 37
        if
           ^
    SyntaxError: invalid syntax




```python
def reduce_street_1(street):
    
    # Make everything a list so we can easily ad to it later
    street = street.apply(lambda x: x.apply(lambda y: [y]))
    
   
    whole_street = street.copy()
    current_street = []
    
     # Keep on repeating while we are still reducing the number of streets
    while len(whole_street) != len(current_street):

        used = [] # keeps track on the street id-s that have already been merged into another string
        current_street = pd.DataFrame(whole_street)
        whole_street = []

        count = 0

        for s, segment in current_street.iterrows():

            if segment["id"] in used:
                continue

            segment_points = [(coords[0], coords[1]) for seg in segment["geometry"] for coords in seg.coords[:]]
            to_join = [segment]


            for s2, segment2 in current_street.iloc[count+1:].iterrows():


                segment_points2 = [(coords[0], coords[1]) for seg in segment2["geometry"] for coords in seg.coords[:]]

                common = list(set(segment_points).intersection(segment_points2))   
                if len(common) > 0:
                    used.append(segment2["id"])
                    to_join.append(segment2)


            # Once found all common points, join
            new_point = pd.Series(data={"access": [seg["access"] for seg in to_join],
                            "bridge": [seg["bridge"] for seg in to_join],
                            "class": [seg["class"] for seg in to_join],
                            "geometry": [coords  for seg in to_join for coords in seg["geometry"]],
                            "id": [seg["id"]  for seg in to_join],
                            "name": segment["name"],
                            "oneway": [seg["oneway"] for seg in to_join],
                            "osm_id": [seg["osm_id"]  for seg in to_join],
                            "ref": [seg["ref"] for seg in to_join],
                            "service":[seg["service"]  for seg in to_join],
                            "tunnel":[seg["tunnel"]  for seg in to_join],
                            "type":[seg["type"]  for seg in to_join],
                            "z_order":[seg["z_order"] for seg in to_join] })

            whole_street.append(new_point)
            count += 1


 
    return pd.DataFrame(whole_street)
```


```python
anzac_parade_reduce_1 = anzac_parade.groupby("name").apply(lambda x: reduce_street_1(x))
```


```python
len(anzac_parade), len(anzac_parade_reduce_1)
```




    (105, 9)




```python
colours = ["red", "blue", "green", "yellow", "purple", "black", "orange", "navy", "cyan"]

ax = plt.gca()
for l, line in enumerate(anzac_parade_reduce_1["geometry"]):
    
    gpd.GeoDataFrame(geometry = anzac_parade_reduce_1["geometry"].iloc[l]).plot(color = colours[l], ax = ax)

```


![png](cleaning_osm_files/cleaning_osm_46_0.png)


We have reduced Anzac parade into 9 segments - down from over a 100. Put as we can see these segments are all part of  the same road but with slight gaps and the two way streets. To fix this up we need these streets ot be close to each other.

We will truncate the  coordinates to 2 decimal places (1 degree = 100km, .1 = 10km, .01= 1km). Roads that are less than 2km apart in either cardinal direction will be merged. It is 2km rather than 1km as we could have a street at (1, 1) and another at (1, 1.004)

(.5, .5) (1.49999, 1.49999) # the furtherest away two points can be
(.5, .5), (.4999, .499)
FIX UP THIS LOGIC FLOW


```python
def reduce_street_2(street):
    
    # Make everything a list so we can easily ad to it later
    street = street.apply(lambda x: x.apply(lambda y: [y]))
    
   
    whole_street = street.copy()
    current_street = []
    
     # Keep on repeating while we are still reducing the number of streets
    while len(whole_street) != len(current_street):

        used = [] # keeps track on the street id-s that have already been merged into another string
        current_street = pd.DataFrame(whole_street)
        whole_street = []

        count = 0

        for s, segment in current_street.iterrows():

            if segment["id"] in used:
                continue

            segment_points = [(round(coords[0], 2), round(coords[1], 2)) for seg in segment["geometry"] for coords in seg.coords[:]]
            to_join = [segment]


            for s2, segment2 in current_street.iloc[count+1:].iterrows():


                segment_points2 = [(round(coords[0], 2), round(coords[1], 2)) for seg in segment2["geometry"] for coords in seg.coords[:]]

                common = list(set(segment_points).intersection(segment_points2))   
                if len(common) > 0:
                    used.append(segment2["id"])
                    to_join.append(segment2)


            # Once found all common points, join
            new_point = pd.Series(data={"access": [seg["access"] for seg in to_join],
                            "bridge": [seg["bridge"] for seg in to_join],
                            "class": [seg["class"] for seg in to_join],
                            "geometry": [coords  for seg in to_join for coords in seg["geometry"]],
                            "id": [seg["id"]  for seg in to_join],
                            "name": segment["name"],
                            "oneway": [seg["oneway"] for seg in to_join],
                            "osm_id": [seg["osm_id"]  for seg in to_join],
                            "ref": [seg["ref"] for seg in to_join],
                            "service":[seg["service"]  for seg in to_join],
                            "tunnel":[seg["tunnel"]  for seg in to_join],
                            "type":[seg["type"]  for seg in to_join],
                            "z_order":[seg["z_order"] for seg in to_join] })

            whole_street.append(new_point)
            count += 1


 
    return pd.DataFrame(whole_street)
```


```python
anzac_parade_reduce_2 = anzac_parade.groupby("name").apply(lambda x: reduce_street_2(x))
```


```python
len(anzac_parade), len(anzac_parade_reduce_2)
```




    (105, 1)




```python
colours = ["red", "blue", "green", "yellow", "purple", "black", "orange", "navy", "cyan"]

ax = plt.gca()
for l, line in enumerate(anzac_parade_reduce_2["geometry"]):
    
    gpd.GeoDataFrame(geometry = anzac_parade_reduce_2["geometry"].iloc[l]).plot(color = colours[l], ax = ax)

```


![png](cleaning_osm_files/cleaning_osm_51_0.png)


Need to test this on victoria road. But it looks all good


```python
anzac_parade_reduce["geometry"].apply(lambda x: shapely.ops.linemerge([y for y in x]) )
```


    ---------------------------------------------------------------------------

    NameError                                 Traceback (most recent call last)

    <ipython-input-18-50dcecb4fcff> in <module>()
    ----> 1 anzac_parade_reduce["geometry"].apply(lambda x: shapely.ops.linemerge([y for y in x]) )
    

    NameError: name 'anzac_parade_reduce' is not defined



```python
anzac_parade
```


```python
# First step: use line merge to get all the obvious ones together, then run the other function over everything
# Get each point for line, put buffer around

def merge_lines(streets):
    
    # do a line merge - maybe if there is say less than 2000 streets with a single name ignore it
#     streets_merged = streets["geometry"].apply(lambda x: shapely.ops.linemerge(x))
    
#     street
    street_name = streets["name"].iloc[0]
    streets_merged = streets.copy()
    
    distance = .001 # 1 = 100km, .1 = 10km, .01 = 1km
    
    # create a distance matrix
    dist = streets_merged["geometry"].apply(lambda x: streets_merged["geometry"].apply(lambda y: x.distance(y)))
    
    new_df = []
    used = []
    for street in streets.index:
        
        # Check if street has been added
        if street in used:
            continue
        
        # Put all streets < 100m away from this street on a stack
        curr_street = dist.loc[street]
        close_seg = curr_street[curr_street < distance]

        one_street = [val for val in close_seg.index.values]

        i = 0
        while True:
            try:
                # pop item from stack
                next_street = one_street[i]
                i = i + 1
                
                # push all streets that are within 10m and haven't already been added
                curr_street = dist.loc[next_street]
                close_seg = curr_street[curr_street < distance]
                
                new_streets = [st for st in close_seg.index if st not in one_street]
  
                one_street.extend(new_streets)
            except IndexError:
                break
            
        used.extend(one_street)
        
        # Make a new row for a DF
        new_point = pd.Series(data={"access": [streets.loc[id]["access"] for id in one_street],
                                    "bridge": [streets.loc[id]["bridge"] for id in one_street],
                                     "class": [streets.loc[id]["class"] for id in one_street],
                                     "geometry": [streets.loc[id]["geometry"] for id in one_street],
                                     "id": [streets.loc[id]["id"]  for id in one_street],
                                     "name": street_name,
                                     "oneway": [streets.loc[id]["oneway"] for id in one_street],
                                     "osm_id": [streets.loc[id]["osm_id"]  for id in one_street],
                                     "ref": [streets.loc[id]["ref"] for id in one_street],
                                     "service":[streets.loc[id]["service"] for id in one_street],
                                     "tunnel":[streets.loc[id]["tunnel"]  for id in one_street],
                                     "type":[streets.loc[id]["type"] for id in one_street],
                                     "z_order":[streets.loc[id]["z_order"] for id in one_street] })
        new_df.append(new_point)
    
    return pd.DataFrame(new_df)
    
    # also want to return the type of street etc.Counting

```


```python
sydney_reduced = clean_streets.groupby('name').apply(lambda x: merge_lines(x))
```


```python
sydney_reduced.groupby('name')['id'].count().sort_values(ascending = False)
```




    name
    short street               46
    george street              44
    william street             44
    john street                38
    church street              37
    king street                36
    albert street              34
    arthur street              34
    victoria street            34
    charles street             33
    james street               32
    station street             31
    edward street              31
    mary street                29
    high street                29
    stanley street             28
    smith street               26
    cross street               26
    the avenue                 26
    margaret street            26
    first avenue               26
    park street                26
    second avenue              26
    queen street               25
    hunter street              25
    campbell street            25
    park road                  24
    the crescent               24
    francis street             24
    western motorway onramp    24
                               ..
    monte bello close           1
    monteclair avenue           1
    montefiore avenue           1
    monteith place              1
    monmouth avenue             1
    monk avenue                 1
    monckton place              1
    monitor road                1
    moncrief close              1
    moncrief place              1
    moncrieff drive             1
    moncrieff road              1
    moncur avenue               1
    moncur lane                 1
    mondial place               1
    mondovi street              1
    monet court                 1
    monet place                 1
    money close                 1
    monfarville street          1
    monford place               1
    monga place                 1
    mongon place                1
    moni way                    1
    monica avenue               1
    monica close                1
    monica place                1
    monie avenue                1
    monier square               1
    1                           1
    Name: id, dtype: int64




```python
merge_lines(anzac_parade)
```


```python
vic_linemerge = shapely.ops.linemerge(victoria_road["geometry"].values)

print(vic_linemerge)
```

    MULTILINESTRING ((150.8298032392747 -34.15843451684535, 150.8293112215584 -34.15804274669102, 150.827125891763 -34.15651707267536, 150.824228351655 -34.15616637384659, 150.8230264705589 -34.15601181155208, 150.8204622787401 -34.15575431948659, 150.8162348658745 -34.15525400368617), (150.8334433321849 -33.76640008110822, 150.8336361997769 -33.76539827604094, 150.8336591661916 -33.76527841482556, 150.8337450806991 -33.76483216230059, 150.8339722302752 -33.76365081686731, 150.8341453165757 -33.76280432846581, 150.8342266210365 -33.76240685861734, 150.8342985377656 -33.76203771960158, 150.8346123562205 -33.76042780745905, 150.8348861929972 -33.75908267963774), (150.8813690428743 -33.98322281712458, 150.8815617428282 -33.98325190232859, 150.8819514175068 -33.98332440579102, 150.8833294862076 -33.98349765972964), (150.8833294862076 -33.98349765972964, 150.8835212641521 -33.98349556425384, 150.8835983776614 -33.98348131501845, 150.8836636726871 -33.98348030919007), (150.8836599846497 -33.98357670107654, 150.8835703821048 -33.98356127837471, 150.8834196754857 -33.98353412100843, 150.8833294862076 -33.98349765972964), (150.8844034594612 -33.98363696696038, 150.8841691852674 -33.98362414264852, 150.8840491564141 -33.98360327170963, 150.8839609787926 -33.98358793282682, 150.8838844520167 -33.98358885483617, 150.8838157204107 -33.98359061503584), (150.8838387706444 -33.9834994199293, 150.8839444664434 -33.98353705467454, 150.8840579574124 -33.98356597224048, 150.884181255208 -33.98359170468325, 150.8842899684922 -33.98360385844285, 150.8844034594612 -33.98363696696038, 150.8847137575166 -33.98366890201145, 150.8858015609105 -33.98380016261515, 150.8867752866022 -33.98392765136241, 150.8879066758926 -33.98407575959151, 150.8884864521351 -33.98415161581522, 150.8889813196984 -33.98421640792677, 150.8894496166287 -33.98427776345798, 150.8901744836152 -33.9843800226767, 150.8902726357013 -33.98438882367503, 150.8903134555698 -33.98440256999623, 150.8903498330296 -33.98442075872612, 150.8903929998309 -33.98443115228608, 150.8904209953875 -33.98443551087573, 150.8904602226943 -33.98443794162764, 150.8905057364286 -33.9844301464577, 150.8905527589054 -33.98442880535318, 150.890604559067 -33.98443031409576, 150.8910255820634 -33.98448873596087, 150.8914184418651 -33.9845390273799, 150.8925971889084 -33.98468990163704, 150.8927019626981 -33.98470364795824, 150.8928432815856 -33.98472586000165, 150.8932002668418 -33.98476961353619, 150.8932944794335 -33.98478126438161, 150.8937752653995 -33.98484412865542), (150.8940186758676 -33.98487891355359, 150.8942020719091 -33.98490447835826, 150.8943646808306 -33.9849367486855, 150.8945554529469 -33.98497631126847, 150.8946725481342 -33.98500330099668, 150.8947266952287 -33.98501587385144, 150.8949157909643 -33.98504730598836, 150.8949763921242 -33.98505820246248, 150.895158027966 -33.98507697792559, 150.8953683299167 -33.98508955078034, 150.8956501295013 -33.98508955078034, 150.8958923665031 -33.98511478030889, 150.8963195921078 -33.98515845002444, 150.8973050524639 -33.98527076752696, 150.897860018273 -33.98534846776937, 150.8991175552061 -33.98550143750228, 150.9012317226436 -33.98578851768599, 150.9021620300769 -33.98591164784361, 150.9025109687059 -33.98595774831104, 150.9035514981659 -33.98610334196917, 150.9044957195584 -33.98623745241997, 150.904684899113 -33.98626192757723, 150.9047705621635 -33.98624893562732, 150.9048239548867 -33.9862183416807, 150.9048586559658 -33.98613745631511, 150.9048369468366 -33.98602061258487, 150.9048239548867 -33.98592170612741, 150.9048607514416 -33.9858157588713, 150.9049323328947 -33.98568458208663, 150.9049800259237 -33.98555684188227, 150.9049951971685 -33.98542021686055, 150.9050732326871 -33.98491872759368, 150.9050882362938 -33.98486122773789), (151.065872249901 -33.80922682864008, 151.0652593651409 -33.80977978279245, 151.0637162567667 -33.81117000525279, 151.0627485660453 -33.81187727024258, 151.0621223540592 -33.8121828744323, 151.061482647209 -33.81244153996423, 151.0604134516402 -33.81288653520369, 151.0593554878216 -33.81328123902415, 151.0583720391223 -33.81366353762789, 151.0573477705545 -33.81406972465567, 151.0568585188662 -33.81426577737091, 151.0566944850211 -33.81432193612216, 151.0565326304709 -33.81436066051484, 151.0563920659546 -33.81438044180632, 151.0562420298878 -33.81439267938495, 151.0560539399806 -33.81439251174689, 151.0557737329575 -33.81437365246475, 151.0555186716439 -33.81435563137293, 151.0553389636399 -33.81433911902365, 151.055214995292 -33.81432461833117, 151.0548969858856 -33.8142779311305, 151.0546456964285 -33.81423350704369, 151.0543970891803 -33.8141863169288, 151.0537474916844 -33.81408682373814, 151.0536437237231 -33.81407173631243, 151.0532909294185 -33.81401633193246, 151.0523817443813 -33.81387627033044, 151.0522972547973 -33.81386327838052, 151.0519392637128 -33.81380854455278, 151.0513296478949 -33.81372413878781, 151.0507202835342 -33.81363411714776, 151.0502692533245 -33.81358332281451, 151.0492163186478 -33.81344015990831, 151.0482541599825 -33.81328903419409, 151.0480735299691 -33.8132594460759, 151.0480202210649 -33.81325056125851, 151.047090668003 -33.81311737281708, 151.0469579824757 -33.81309893263011, 151.0468123888176 -33.8130788160625, 151.0458950733343 -33.81294495706882, 151.0458680836061 -33.81294261013593, 151.0452581325122 -33.81286273059867, 151.0437701770608 -33.81265427266674, 151.0427312401624 -33.8125014705719, 151.0419342888087 -33.81238831487906, 151.0417387390076 -33.81235981640828, 151.0406552942035 -33.8122206768156, 151.0396255773986 -33.81209746283894, 151.038712788143 -33.81196653751138, 151.0375048720767 -33.81180744898913, 151.0364267916905 -33.81167132688159, 151.0354428400769 -33.81153746788792, 151.0352858470305 -33.81151642931096, 151.035080993317 -33.81148072240345, 151.0349362378491 -33.81145155338041, 151.0347662528527 -33.81140587200809, 151.0344778315646 -33.81131132414029, 151.0339063534062 -33.81108945516331, 151.033414419509 -33.81089474355258, 151.033293049551 -33.81084034500097, 151.0328743734875 -33.81067706552716, 151.0323426255501 -33.81046760176685, 151.0319214349157 -33.8102833675351, 151.0314173472588 -33.81004272309499, 151.0312157624875 -33.80993610528662, 151.0309233178858 -33.80982437451731, 151.0307286900941 -33.8097580736632, 151.030073476723 -33.80954240729457, 151.0298034956218 -33.80944618304613, 151.0293449216992 -33.80929514115095, 151.0291502939075 -33.80922842120168, 151.0288879403381 -33.80915675592954, 151.0287838371008 -33.80913236459131, 151.0286745370834 -33.80911677425141, 151.0285158676563 -33.80910571013922, 151.0280805954245 -33.80909623858864, 151.0278560442385 -33.80909892079765, 151.0277953592595 -33.80910160300667, 151.0277351771947 -33.80910361466343, 151.027656722581 -33.80910495576794, 151.0275945288595 -33.80910604541535, 151.0274662857409 -33.80910822471017, 151.0274009907152 -33.80910931435758, 151.0270322707946 -33.80911585224206, 151.0269096435512 -33.80911803153688, 151.0267542430663 -33.80912674871618, 151.0265600343698 -33.80913395715291, 151.0263240837954 -33.80914611091251, 151.0260304657272 -33.80915885140536, 151.0250644513865 -33.80920503569183, 151.0245012713123 -33.80923370180068, 151.0240384226191 -33.80925105234024, 151.023779337992 -33.80925180671153, 151.0233707202123 -33.80923965295193, 151.0230447479979 -33.80922381115494, 151.0228266508773 -33.80920226966379, 151.0226280835911 -33.80918181782005, 151.0224057955189 -33.80915365462536, 151.0221556795282 -33.80912171957428, 151.0209384595494 -33.80893128273419, 151.0202064679452 -33.80881334935654, 151.0196086706109 -33.80869633798824, 151.019297366727 -33.80863741320891, 151.01903308532 -33.80859751534982, 151.0189534572398 -33.80857890752478, 151.0186249704544 -33.80852484424929, 151.0182414983843 -33.80848930497984, 151.0180443560216 -33.80849483703594, 151.0171688662351 -33.80857337546868, 151.0167595779032 -33.80861117785199, 151.016275690633 -33.80864931551142, 151.0152481531229 -33.80873137434349, 151.0147451551134 -33.80878325832413, 151.0144632717098 -33.80883044843901, 151.0141188593084 -33.80883774069477, 151.0139810608202 -33.8088497268163, 151.0138866805905 -33.80885802490045, 151.0134473850451 -33.80889172015119, 151.0124002338817 -33.80897771847776, 151.0122165863831 -33.80899951142601, 151.0111089178787 -33.80908483920032, 151.0109659226106 -33.80909565185542, 151.01034096791 -33.80914435071284, 151.0097062902017 -33.80918835570451, 151.009516943009 -33.80918885861871, 151.009271353246 -33.80917142426011, 151.008610859276 -33.80909556803638, 151.008220178769 -33.80905441289181, 151.0072758735575 -33.80893622805706, 151.0072253306814 -33.80892977399162, 151.0069460456676 -33.80888015312482, 151.0053271648887 -33.80868711789475, 151.0037728247642 -33.80846843404095, 151.0018154827351 -33.80821463001285, 151.0016784386182 -33.80819468108331), (151.0017050930703 -33.80810532999547, 151.0037906782179 -33.8084036419294, 151.005267150462 -33.80859600660724, 151.0053401568386 -33.80861101021392, 151.0057470982377 -33.80867454503996, 151.0059546341603 -33.8086965056263, 151.0063305625176 -33.80873631966637, 151.0069653240449 -33.80881385227073, 151.0072908771642 -33.80885743816722, 151.0082323325286 -33.80898350199095, 151.0092721914364 -33.80908257608647, 151.009540747614 -33.80910202210183, 151.0099074558779 -33.8090959033125, 151.0102556401357 -33.80909020361835, 151.0110812575983 -33.80902717170648, 151.0122218669821 -33.80893480313352, 151.0124367789795 -33.80891803932718, 151.0128464864067 -33.80888132659126, 151.0131764819346 -33.80883581285704, 151.0135216487072 -33.80879490916954, 151.0147251223649 -33.80869575125502, 151.0162142512827 -33.80859340821726, 151.0174868756416 -33.80848402438085, 151.018053324658 -33.8084329785905, 151.0181654745225 -33.80842283648767, 151.0185231303309 -33.80843725336114, 151.0186767706161 -33.80845753756682, 151.0188686323797 -33.80849592668335, 151.0191671957707 -33.80855711457653, 151.0209183429818 -33.8088449491315, 151.0213048325371 -33.80890873541463, 151.0219873709125 -33.80902650115422, 151.0223315318568 -33.80907712784941, 151.0225410794362 -33.80909942371184, 151.0228436661407 -33.8091363040858, 151.0231136472419 -33.80915717502469, 151.0233638470517 -33.80917301682172, 151.0237774939733 -33.80918181782005, 151.0239962616461 -33.80917972234425, 151.0243046318639 -33.80917050225077, 151.0249903553625 -33.8091413332277, 151.0269703285301 -33.80904251058931, 151.027036042651 -33.80903831963772, 151.0273931955452 -33.809015353223, 151.0275152360554 -33.80900923443369, 151.0276458261069 -33.8089981703215, 151.0277984605637 -33.80899523665539, 151.0281353292522 -33.80898132269613, 151.0285591182767 -33.80899691303603, 151.0288229805886 -33.80905432907278, 151.0293586680204 -33.8092237273359, 151.029685226968 -33.80932363962174, 151.0301075910689 -33.80945464876833, 151.0309620422785 -33.80974022020945, 151.0311426722918 -33.8098045094068, 151.0323734709538 -33.81038696785834, 151.0332337894956 -33.81072920096491, 151.033316937975 -33.81076323149179, 151.0334652976612 -33.81082391647075, 151.034241294257 -33.81111895946248, 151.034605739407 -33.81125248318003, 151.0348769777937 -33.81133890060175, 151.0351057199313 -33.811395059353, 151.0353964881524 -33.81143898052562, 151.0374841687758 -33.81172128302453, 151.039666229629 -33.81202554610971, 151.0405685415056 -33.81213551667935, 151.0417543293476 -33.81229217444965, 151.0428828687909 -33.81244036649778, 151.0447826271451 -33.81271990296861, 151.0452737228521 -33.81278762874626, 151.0459060536275 -33.81287681219601, 151.0470268817198 -33.81303497870891, 151.0492140555339 -33.8133479589734, 151.0505523940137 -33.81352867280583, 151.0522104182804 -33.81377434638785, 151.0523155273462 -33.81378993672774, 151.0534596571294 -33.81395631750573, 151.053659397882 -33.81398540270976, 151.0538738907842 -33.81402035524599, 151.0543496476084 -33.81409118232779, 151.0543877852678 -33.81409696584097, 151.0548731812806 -33.81417081040794, 151.0551686433674 -33.81421540213282, 151.0553144884827 -33.81423493196723, 151.0554987227144 -33.8142522825068, 151.0556989663812 -33.81426996832249, 151.0558743996146 -33.81427801494954, 151.0562186443779 -33.81429276709912, 151.0564077401136 -33.81427960751114, 151.0565525794004 -33.81426150260029, 151.0566919704501 -33.81423635689077, 151.0568092332756 -33.81419981179292, 151.0569577605998 -33.8141522025829, 151.0574685537792 -33.81395103690673, 151.0583221667984 -33.81361970027427, 151.0591247340273 -33.81330294815336, 151.0593396460246 -33.81321770419808, 151.0604071652128 -33.81280430873358, 151.0615717468398 -33.81232226548207, 151.0620167420792 -33.81213853416449, 151.062491576894 -33.81190895383656, 151.0625410301228 -33.81188020390869, 151.0626803373535 -33.81179604960082, 151.0633390711239 -33.81132674684213, 151.0637240519366 -33.81102172938566, 151.0637761873744 -33.81098627393524, 151.06452536188 -33.81029376109504, 151.0652074811603 -33.80970451330194, 151.0655285918709 -33.80940762629155, 151.0658337769654 -33.80913228077227, 151.0659690608827 -33.80901677814654, 151.0662117169795 -33.80884000380863, 151.0663470847157 -33.80876230356619, 151.0664634255318 -33.80869365577922, 151.0666191612927 -33.80862173904998, 151.0667203308641 -33.80857463275416), (151.0415670776307 -33.74115035216931, 151.0416409221976 -33.74090685788212, 151.0429833678099 -33.74067895393483, 151.0439810657446 -33.7405069572817, 151.0456561890939 -33.74021610524159, 151.0468227823775 -33.74004586878814, 151.0476529260679 -33.73990823793801, 151.0477093362762 -33.73989910166355), (151.0478674189701 -33.73987211193534, 151.0498515830893 -33.73953021410489, 151.0508541425279 -33.73936081584176, 151.0511727386675 -33.73931044060367, 151.0519217455351 -33.73918094019964, 151.0531436593798 -33.73900114837659, 151.0537176521091 -33.73891305457422, 151.0545541660458 -33.7387695563919, 151.0557113715979 -33.738590267483), (151.0521199775451 -33.92683943085697, 151.052460198995 -33.92732013300397, 151.0527170205082 -33.92781307272961, 151.0527347901429 -33.92784710325648, 151.0532666218993 -33.92881806292012, 151.0532926896182 -33.92886550449208, 151.0534035821972 -33.92908242814622, 151.0538505890934 -33.92984828263917, 151.0536360961912 -33.93064590454517, 151.0533719824222 -33.93199203819484, 151.0531139874425 -33.93395164333776, 151.0523892880941 -33.93871792875836, 151.0524026991392 -33.93891515494002), (151.0558996291432 -33.73856134991706, 151.0569936351454 -33.73835792112706, 151.0579658520945 -33.73824434633905, 151.0581144632377 -33.73821853007726, 151.059189945234 -33.73806623089661, 151.0599422210438 -33.73794276546286, 151.0607766395047 -33.73780580516501, 151.0615676397072 -33.73768644686382, 151.0616201942401 -33.73767831641774, 151.0626660881181 -33.73751604277231, 151.0629682557275 -33.73746910411452, 151.0631118377289 -33.73743783961569, 151.0631978360554 -33.73736667725774), (151.0869013553159 -33.80739404169216, 151.0867195518361 -33.80738976692155, 151.0863402707175 -33.80731927511584, 151.0859196668162 -33.80724710692954, 151.0857305710806 -33.80722942111385, 151.0855717340155 -33.80722196121999, 151.0850022675139 -33.80719497149178, 151.0848289297563 -33.80718675722667, 151.0847439372581 -33.80718265009412, 151.0840970219712 -33.80712246802935, 151.0836868954489 -33.8070648005355, 151.0835135576912 -33.80703127292281, 151.083032268811 -33.80691610557321, 151.0824498941785 -33.80678937119723, 151.0816868895326 -33.8066392513114, 151.0808813886377 -33.80649105926326, 151.0784290952262 -33.80596006569723, 151.0776999534691 -33.8059102771924, 151.0763686557881 -33.80581673515297, 151.075858533161 -33.80588035379805, 151.0746447497624 -33.8061770731704, 151.071540428103 -33.80693286937955, 151.0707091947653 -33.80713520852217, 151.0705300734945 -33.80720301811883, 151.0674787254632 -33.80843918119888, 151.0665548720954 -33.80883078371514, 151.065872249901 -33.80922682864008), (151.0667203308641 -33.80857463275416, 151.0666815226524 -33.80866641459388, 151.0663939395545 -33.80884796661664, 151.0660389659551 -33.80906421971852, 151.065872249901 -33.80922682864008), (151.0667203308641 -33.80857463275416, 151.0667633300274 -33.80856013206167, 151.0669123602658 -33.80850740989069, 151.0671119333803 -33.80844865274946, 151.0673538351059 -33.80838067551471, 151.06753354311 -33.80832652842022, 151.0676507221164 -33.80828755257044, 151.0677654703708 -33.80824312848364, 151.0678790451588 -33.80819602218781, 151.0691834369306 -33.80763443467518, 151.0697618720686 -33.80739437696828, 151.0700965614624 -33.80724526291084, 151.070264534802 -33.80717912969479, 151.0704954562344 -33.80708433036988, 151.0711598896989 -33.80691526738289, 151.071516623498 -33.80683790241658, 151.0716972535114 -33.80678635371208, 151.0735274420693 -33.80635510479379, 151.0758439486494 -33.80580206682242, 151.0763608606181 -33.80573853199635, 151.0767695622169 -33.80575202686046, 151.0774912440801 -33.80582561997034, 151.0777053178871 -33.80583836046316, 151.0778614727433 -33.80584766437568, 151.0783490480509 -33.80588337128318, 151.0787218751041 -33.80593450089256, 151.0791350191114 -33.80603030604584, 151.0795680282294 -33.80612870958907, 151.0799610556692 -33.80621093605922, 151.0802756284953 -33.80627966766525, 151.0803380736739 -33.80629240815807, 151.0808947996828 -33.80641754997245, 151.0817006358538 -33.80657362100953, 151.0824683343655 -33.80671812502024, 151.083304261569 -33.80689607282462, 151.0836511885413 -33.80697913748506, 151.0839575471023 -33.80702448358124, 151.0847497207713 -33.80709405337757, 151.0858538688763 -33.80715146941432, 151.0863606387422 -33.80724341889214, 151.0869013553159 -33.80739404169216), (151.0869013553159 -33.80739404169216, 151.0879777593214 -33.80761322846016, 151.0881892347385 -33.80764013436934, 151.088393920814 -33.80766561535498, 151.088557787021 -33.80768983905514, 151.0887677536955 -33.80772445631527, 151.0890339629403 -33.80778011215232, 151.0902199184204 -33.80806342047958, 151.0905635764505 -33.80816324894639, 151.090847387692 -33.8082673521838, 151.0910689213928 -33.80836022367097, 151.0913136729655 -33.80846600328903, 151.0916621924994 -33.808613860061, 151.0920894181042 -33.80880706292916, 151.0924730578125 -33.80902541150681, 151.0928622295768 -33.80924686138866), (151.1792924756076 -33.86813903509511, 151.178485298332 -33.8684273725643, 151.177883058589 -33.86862946024982, 151.1774136720113 -33.86880548021644, 151.1768866179397 -33.86900362840748, 151.1766970192899 -33.86904143079079, 151.1765568738688 -33.86905693731165, 151.1764576321353 -33.8690592004255, 151.1763649282861 -33.86903413853503, 151.1763189954568 -33.86900983101582, 151.176266692381 -33.8689754652128, 151.1762334162254 -33.86893816574369, 151.1761810293306 -33.86878645329625, 151.1761027423549 -33.86852116606082, 151.1760253773886 -33.86829410030384, 151.1759596632677 -33.8680893304093, 151.1758724914747 -33.8678986421121, 151.1757889239001 -33.86772262214546, 151.1756231298553 -33.86749647839784, 151.1753859219955 -33.86719070657008, 151.1752914579467 -33.86706967188825, 151.1751740274833 -33.86695685147154, 151.1750426830606 -33.866836571161, 151.1748387513563 -33.86667404605845, 151.1747335584715 -33.86659927948215, 151.1745625676468 -33.86648478268481, 151.1743801774337 -33.86638227200899, 151.1742245254917 -33.86631320512682, 151.1740555463238 -33.86625084376723, 151.1738034186764 -33.86617951377121, 151.173641480307 -33.86615118293849, 151.1735620198649 -33.86613726897923, 151.1732964811724 -33.86609946659593, 151.1730038689326 -33.86603081880892, 151.172875374357 -33.86598580798889, 151.1727332172791 -33.86590567699454, 151.1724770663182 -33.86571683271603, 151.1722948437431 -33.8655772740282, 151.1720992939422 -33.86543930790197, 151.1719428876289 -33.86532472728558, 151.1716661171861 -33.86512180140974, 151.1714293284215 -33.86494821219503, 151.1711308488495 -33.86472936070317, 151.1704572791105 -33.8642310565595, 151.1700419558082 -33.86393877959586, 151.1686684971542 -33.86305574609651, 151.1686317844183 -33.86303210912956, 151.1684701813251 -33.86293211302469, 151.1684241646767 -33.8629043689252, 151.1682002840429 -33.86276615134187, 151.1680705321818 -33.86268585270946, 151.167500562766 -33.86232903509136, 151.1672137340394 -33.8621735507875, 151.1668978201088 -33.86203483028998, 151.1664884479578 -33.86187163463521, 151.166119392761 -33.86173023192865, 151.1653044203155 -33.86135841070387, 151.1646233906827 -33.86104886701969, 151.1642796488335 -33.86090260280929, 151.1640819197376 -33.86080185233317, 151.163847897001 -33.86067327393849, 151.1635648401308 -33.86051502360655, 151.1634131276834 -33.86042190066229, 151.1631651909876 -33.86025912410266, 151.1625785415844 -33.85983147940277, 151.1620947381332 -33.8594269687556, 151.1614962702467 -33.85884417502794, 151.1611231917364 -33.8584254151454, 151.1607921903801 -33.85800347013964, 151.1603585945289 -33.85741941912653, 151.1599934788267 -33.85697366951575, 151.1597816681335 -33.8567263195531, 151.1593397741982 -33.85624804815802, 151.1592677736499 -33.85617018027753, 151.159003995157 -33.85588662049318, 151.1582647951162 -33.85509251898651, 151.1559157667518 -33.85259965716369, 151.1554504873067 -33.85211426115092, 151.1544956208971 -33.85110030232399, 151.1540839856323 -33.85067282526217, 151.1539203708823 -33.85048138259367, 151.1537781299855 -33.85030536262704, 151.1536601966078 -33.85012263713784, 151.1534643953497 -33.84981745204331, 151.1530499940568 -33.84913952371463, 151.1525926774196 -33.84839856347409, 151.1524156516246 -33.84813344387672, 151.152150029113 -33.8477636343087, 151.1518791260025 -33.84733372649492, 151.1514617910435 -33.8466438120447, 151.1513507308264 -33.84646041600327, 151.15109592097 -33.84601047544092, 151.1509431188751 -33.84576765170598, 151.1508036440063 -33.84554712383348, 151.1506480758834 -33.8453221535523, 151.1504495924162 -33.84506373947747, 151.1502537911581 -33.84481421021999, 151.1499915214078 -33.8445207597899, 151.1497509607867 -33.84426016642024, 151.1497476918445 -33.84425656220185, 151.1489282769903 -33.84340772686747, 151.1467087490299 -33.84099172709673, 151.1465183960088 -33.84078670574513, 151.1458370310999 -33.84014968110392, 151.1451130861228 -33.83952372057493, 151.1448933964406 -33.83936890682332, 151.1446722818349 -33.83923965787639, 151.1444239260438 -33.83911786882328, 151.1441694514635 -33.83902147693678, 151.1437691317679 -33.83890907561522, 151.1435058561892 -33.83885165957849, 151.1431958934099 -33.83881955688931, 151.1429253255754 -33.83880270926394, 151.1425123492061 -33.83881402483323, 151.1417207622704 -33.83893908282857, 151.1410042771871 -33.83909037618088, 151.1402849422568 -33.8392996723031, 151.1396082712135 -33.83953344358261, 151.1390428280255 -33.83971390595795, 151.1389602662792 -33.83974030895294, 151.1381659971345 -33.83994214518137, 151.1377284617888 -33.84002990370757, 151.1370906827764 -33.84015781155003, 151.1366906983569 -33.84018555564953, 151.1363646423235 -33.84015722481678, 151.1363180389418 -33.84015311768422, 151.1360399273945 -33.84007751291761, 151.1357594689143 -33.83993954679138, 151.135201317982 -33.83960837779699, 151.13448298888 -33.83911040892945, 151.1324811389451 -33.83772823309613, 151.1313376797142 -33.83732707521025, 151.130840632856 -33.83714686429204, 151.1303687317073 -33.83697570582922, 151.1298407556264 -33.83676925955405, 151.1293330637512 -33.83656700423049, 151.1289196682867 -33.83642828373296, 151.1287338414933 -33.83636164760273, 151.1285923549677 -33.8362892279593, 151.1284732481237 -33.83621253354528, 151.1283852381403 -33.83613114526545, 151.1282951326812 -33.83604405729147, 151.1282214557523 -33.83596090881201, 151.1281635368014 -33.83586325964004, 151.12812556678 -33.83579260019627, 151.1280685698384 -33.83567466681862, 151.1279889417583 -33.8354965513762, 151.1278103234017 -33.8350966507758, 151.1275425215953 -33.83453581763445, 151.127459792211 -33.83434890119366, 151.1272176390283 -33.83380147909742, 151.127119151666 -33.8335764249972, 151.1270515935264 -33.83340660763892, 151.1269573809348 -33.83311265429461, 151.1269123701147 -33.83288592381376, 151.1268903257093 -33.83269045783177, 151.1268410401187 -33.8320825183946, 151.1267814447871 -33.83126444464486, 151.126775828912 -33.83120057454266, 151.1267012299737 -33.83016784025267, 151.126656973525 -33.82976475452904, 151.1266242841026 -33.82941615117604, 151.126596288546 -33.8291463377129, 151.1265818716726 -33.8290082039486, 151.1264581547817 -33.82815274691072, 151.1264181731036 -33.82789181826493, 151.1263565661152 -33.82738320438037, 151.1262427398702 -33.82665540372781, 151.1261486949165 -33.82619154920619, 151.1260696535696 -33.82580137161344, 151.1260199488838 -33.82559132111993, 151.1259753571589 -33.82543340606412, 151.1259098944951 -33.8253086833449, 151.1258174421031 -33.82517432143703, 151.1256884446133 -33.82503350546372, 151.1255421804029 -33.8249103753061, 151.1253842653471 -33.82479043027168, 151.1251185590165 -33.82461600286666, 151.1247726378725 -33.8244275776833, 151.1245312390611 -33.82428014000649, 151.1240537220374 -33.82400823106754, 151.123632866679 -33.82379776147886, 151.12359037043 -33.82377605234964, 151.1234877759351 -33.82372358163576, 151.1233125941587 -33.82361310815193, 151.1231411842389 -33.82349157055592, 151.1224037443977 -33.82306292002764, 151.1220725754033 -33.82286611294111, 151.1217826453725 -33.82268271689969, 151.1216277478018 -33.8225686391975, 151.121271516917 -33.82230796200881, 151.1210328841336 -33.8221207102919, 151.1207420320935 -33.82188727428851, 151.1204417923219 -33.82166800370149, 151.1204285489148 -33.82165836451284, 151.1196351179604 -33.82107582224228, 151.1187454627576 -33.82039160748624, 151.1185683531435 -33.82026202326318, 151.1184123659255 -33.82016605047184, 151.1181990464897 -33.82005616372123, 151.1179656943053 -33.81994216983807, 151.1177326773971 -33.81984133554289, 151.1173687351613 -33.81969414932317, 151.1170741950838 -33.81957747323099, 151.116726094645 -33.81944151876151, 151.1165061535057 -33.81935501752076, 151.115772653159 -33.8190239323454, 151.1155300808812 -33.81889116299914, 151.1143317201845 -33.81823326741906, 151.113263362806 -33.81759096217885, 151.1127281782884 -33.81726658252606, 151.1120385991143 -33.81685243269023, 151.1118243576691 -33.81675687899406, 151.1115797737345 -33.81666962338204, 151.1113724892691 -33.81661497337333, 151.1111522966727 -33.8165905820351, 151.1106559203668 -33.81658857037834, 151.1093995569001 -33.81660835166983, 151.1089353671023 -33.81661564392559, 151.1087124922969 -33.81661740412525, 151.108517193953 -33.81663123426549, 151.1082217318661 -33.81665210520441, 151.1079806683308 -33.8166650133353, 151.1077535187549 -33.81666015183146, 151.1074212601131 -33.81661773940138, 151.1072050908302 -33.81657901500873, 151.1070150730853 -33.8165546236705, 151.1068080400769 -33.81654741523377, 151.1066144181136 -33.816561245374, 151.1064406612608 -33.81659200695864, 151.1062910442892 -33.81662947406582, 151.1059084942283 -33.81676710491595, 151.1055687756927 -33.81688076352299, 151.1054078431517 -33.8169114412886, 151.1052716372252 -33.8169146264118, 151.1051423882782 -33.81689677295805, 151.1050060985327 -33.81684731972931, 151.1048575712084 -33.81676710491595, 151.1046397255449 -33.81659862866215, 151.1043167708157 -33.81633216796027, 151.1041121685592 -33.81619671640499, 151.1039223184523 -33.816080543227, 151.103823747271 -33.81603821461599, 151.1037106753972 -33.81598976721563, 151.103512275749 -33.81592380163767, 151.1032529396649 -33.81587334258055, 151.1031209246899 -33.81584911888038, 151.1029871495152 -33.81583428291177, 151.1027374526197 -33.81580083911811, 151.1026199383372 -33.81578306948336, 151.1024713271939 -33.81576060598286, 151.1016586178623 -33.81565440726965, 151.1004653701265 -33.81545835455444, 151.1000639607834 -33.81538124104523, 151.0995966696815 -33.8152948236235, 151.0994056461082 -33.81526188274403, 151.0992904787586 -33.81523782668194, 151.099191069387 -33.81520664600214, 151.0990304721222 -33.8151381658532, 151.0988184937909 -33.81503255387321, 151.0984810383692 -33.81485125330758, 151.0981384699865 -33.81466785726613, 151.0976207198274 -33.81438815315724, 151.0969957651268 -33.81404944044999, 151.096858972467 -33.81396914181758, 151.096708433486 -33.81388020982493, 151.0963322536716 -33.81361391676108, 151.0961020866105 -33.81341182907559, 151.0960409825363 -33.81335332339142, 151.0956844163753 -33.8128791591289, 151.095522394187 -33.81266877335926, 151.0953216476059 -33.81238865015519, 151.0952008643812 -33.81221640204498, 151.0950229165769 -33.81196561550203, 151.0947033984279 -33.81156630163483, 151.0945590620552 -33.81136932691027, 151.0944219341193 -33.81125424337969, 151.0941805353079 -33.81102851872722, 151.093897981352 -33.81074101944839, 151.0938027629319 -33.8105802545455, 151.0936300957265 -33.81025411469304, 151.0934799758407 -33.80989997928395, 151.093409651673 -33.80978657213402, 151.0933147685291 -33.80962907617337, 151.0932433547141 -33.80956352969056, 151.0931614635201 -33.80949865375999, 151.0929237527461 -33.80933897850454, 151.0928622295768 -33.80924686138866), (151.0928622295768 -33.80924686138866, 151.0929601302059 -33.8092620326334, 151.093245785466 -33.809456408968, 151.0933222284229 -33.80950963405314, 151.0933949833424 -33.80957216305083, 151.0934344621065 -33.80962530431694, 151.0934762878033 -33.80969160517105, 151.0935265792223 -33.80981783663285, 151.0936950554761 -33.81016652380487, 151.0939870809827 -33.81070992258759, 151.0941066907409 -33.81085660589315, 151.0943727323477 -33.81111745071991, 151.0946345830029 -33.81136555505383, 151.0948182305013 -33.81158457418377, 151.095263644836 -33.81215496269471, 151.0954276786811 -33.81238806342196, 151.0956127511032 -33.81263767649847, 151.0957378929175 -33.81278117468082, 151.0961108876088 -33.81325936225687, 151.0962925234505 -33.81345315185823, 151.0963896697083 -33.81355935057144, 151.0965842975 -33.81370326784892, 151.0968250257592 -33.81385020261155, 151.0972176341038 -33.81407156867436, 151.0975870245767 -33.81427021977959, 151.0981964727564 -33.81459971239335, 151.098657645069 -33.81484706235599, 151.0991128662303 -33.81507781615036, 151.099243875377 -33.8151310412355, 151.0993749683426 -33.81516775397139, 151.0994961706625 -33.81519063656708, 151.0999776271808 -33.81527973619779, 151.1002779507715 -33.81533447002553, 151.1007298191716 -33.81541443338179, 151.101024023973 -33.8154585221925, 151.1015772295824 -33.81552666706528, 151.1020215542696 -33.81558123325496, 151.1022578401201 -33.81561023463993, 151.1024459300273 -33.81563437452107, 151.10263527722 -33.81565097068935, 151.1027934437329 -33.81565591601222, 151.1028239538605 -33.81565683802157, 151.1030097806538 -33.81567217690439, 151.1030849663253 -33.81567921770306, 151.1032520176555 -33.81568801870139, 151.1033729685183 -33.81570763235482, 151.1035031394746 -33.81573814248236, 151.1035529279795 -33.81574954187067, 151.1036208213952 -33.81577116718086, 151.103705143341 -33.81580544916486, 151.1038247530994 -33.81585708168839, 151.1039234919187 -33.815916760839, 151.104069337034 -33.81602698286571, 151.1042770405946 -33.81619378273887, 151.1048189306348 -33.81661916432492, 151.1049428989827 -33.81669896004314, 151.1050855589747 -33.81676584763048, 151.1052115389794 -33.81680457202313, 151.1053643410743 -33.81681127754566, 151.1055039835812 -33.81679702831028, 151.10563306489 -33.81676576381145, 151.1058529222103 -33.8166922545206, 151.106081999624 -33.81660675910823, 151.1063542438391 -33.81652428118102, 151.1065259890351 -33.81648773608315, 151.1066949682031 -33.81646770333458, 151.1069147417043 -33.81646585931588, 151.107085816348 -33.8164770072471, 151.1072540411448 -33.81650181768049, 151.1076039856022 -33.816561245374, 151.1078388465291 -33.81658530143611, 151.108044538433 -33.81658932474962, 151.1083713488378 -33.81656392758302, 151.1086920404532 -33.81653844659738, 151.1090551444987 -33.81651581545881, 151.1094230262289 -33.81650642772723, 151.1099152954023 -33.81650148240436, 151.1107954790546 -33.81650416461338, 151.1111318448289 -33.8165113730501, 151.1113288195535 -33.81652612519972, 151.1115212680504 -33.81656560396365, 151.1118816898868 -33.81668580045516, 151.1120565363871 -33.81676207577405, 151.1127927189428 -33.81720363443321, 151.1141896469257 -33.81802145672586, 151.1144058162085 -33.81815472898631, 151.1155781930054 -33.81882243139313, 151.1158592382188 -33.81897112635542, 151.1165658326563 -33.81930061896918, 151.1167638970283 -33.81936826092777, 151.1173822300254 -33.81961267722431, 151.1175358703106 -33.81967554149813, 151.1176864931106 -33.8197373161245, 151.1180041672409 -33.81987653953621, 151.1182968632997 -33.82002054063275, 151.1185047344985 -33.82013084647849, 151.1186247633518 -33.82021332440573, 151.1190340516839 -33.82050124277976, 151.1197128182028 -33.82100600098885, 151.1218353675434 -33.8226222833778, 151.1223356833439 -33.82294004132712, 151.1228638270629 -33.8232596432951, 151.1237075494363 -33.82374973317366, 151.1247690336542 -33.82432414499814, 151.1251584568756 -33.82457224933208, 151.1254266777772 -33.82474240196652, 151.1255844251949 -33.82486553212414, 151.1257367243755 -33.82499955875588, 151.1258694937218 -33.82514557150915, 151.1259668914367 -33.82527993341702, 151.1260433343937 -33.82541840245744, 151.1260988225926 -33.82558126283612, 151.1263152433326 -33.8266662163829, 151.1264104617526 -33.82729527821606, 151.126477265521 -33.82776055766124, 151.1265261320165 -33.82813807858017, 151.126606849744 -33.82865951677658, 151.12667817974 -33.82916016785313, 151.1268038244686 -33.83017177974716, 151.1268821952632 -33.83111616877772, 151.1268889846048 -33.83119772469558, 151.126929133921 -33.83209014592648, 151.126957967668 -33.832471857797, 151.1269900703571 -33.83273002041474, 151.1270149646095 -33.83287435678739, 151.1270555330209 -33.83309153189861, 151.1271748913221 -33.8335009040496, 151.1272994464032 -33.83379083408039, 151.127792721405 -33.83488425334937, 151.1279706692094 -33.8352860817875, 151.1281121557349 -33.83561155108774, 151.1281926220054 -33.83576477227774, 151.1282447574432 -33.83584297543436, 151.1282623594398 -33.83586946224838, 151.1283508723373 -33.83597423603806, 151.1284153291728 -33.83603517247411, 151.1284497787948 -33.83606752662038, 151.1285424826439 -33.83614614887213, 151.1286653613444 -33.83622535785713, 151.1288332508649 -33.83630557267051, 151.1290046607849 -33.83637313081007, 151.1292485741672 -33.83646516410693, 151.1295222433058 -33.83656935116338, 151.1298462876825 -33.83670270724286, 151.1303949670643 -33.83689532337777, 151.1309149803371 -33.83705625591872, 151.1314153799566 -33.83722221760155, 151.1325632815962 -33.8376449169786, 151.1330188380337 -33.83794146871286, 151.1331326642788 -33.83803551366648, 151.133315389768 -33.83814808262611, 151.1337044777133 -33.83840155137807, 151.1342866847077 -33.8387960037414, 151.1350882461082 -33.83936706280463, 151.135708255486 -33.83979554569484, 151.1360000295354 -33.83994072025782, 151.1363748682453 -33.84003342410691, 151.1366226373032 -33.84005756398807, 151.1370510363743 -33.84004834389458, 151.1379093432593 -33.83985975107316, 151.1384409235586 -33.83973997367681, 151.1389171832969 -33.8396131554818, 151.1395638471267 -33.83943193873517, 151.1398910766266 -33.83933856433384, 151.1402835173332 -33.83921711055686, 151.1405002733493 -33.83915382718789, 151.1407183704698 -33.83909280693279, 151.1409369705046 -33.83903446888669, 151.1411568278248 -33.83897881304964, 151.1413775233354 -33.83892650997382, 151.1415899207618 -33.83887940367799, 151.1418030725595 -33.83883497959118, 151.1420172301856 -33.83879323771336, 151.1421717924802 -33.83876557743289, 151.1422526778458 -33.83875191493073, 151.1424478923707 -33.83871930932739, 151.1426418496101 -33.83869047558045, 151.142823988366 -33.83866650333738, 151.1429345456689 -33.83865191882586, 151.143830068204 -33.83849693743619, 151.1439824512037 -33.8384723784599, 151.1441322358134 -33.83845251334935, 151.1442906537833 -33.83843574954301, 151.1444490717533 -33.83843164241046, 151.1446020414863 -33.83844077868491, 151.1446582840566 -33.83844832239777, 151.1448640597795 -33.83848076036307, 151.1450625432466 -33.83855225799712, 151.1452472803925 -33.83865158354973, 151.1454134935325 -33.8388015357975, 151.1455661279893 -33.83895660100623, 151.1456572392768 -33.83910345194982, 151.1457149905896 -33.83923152743031, 151.1458799464441 -33.83961927427112, 151.1459523660875 -33.83977744078399, 151.1460126319714 -33.83989545798067, 151.1460797710158 -33.8400059314645, 151.1460819503106 -33.84000894894965, 151.1462111992575 -33.84018647765888, 151.1466269416549 -33.84072887061322, 151.1468079907635 -33.84092710262328, 151.1490302847519 -33.84334310239402, 151.149844251369 -33.84419302737581, 151.1501974647688 -33.84457582889374, 151.1504308169531 -33.8448520126033, 151.1506587209004 -33.84513272254059, 151.1508465593506 -33.84539566284315, 151.1510396783996 -33.8456718465527, 151.1516092287203 -33.8465954484634, 151.1517536489119 -33.84682963883807, 151.1519754340699 -33.84718930630325, 151.1530137842351 -33.84889083264747, 151.1536816542799 -33.84999791441862, 151.1538264935668 -33.85021173676859, 151.1539954727348 -33.85042337982372, 151.1541644519027 -33.85060602149386, 151.1545855587182 -33.851044814125, 151.1560226360173 -33.85257643929187, 151.158361941374 -33.85504314957683, 151.1588793562569 -33.85560607819397, 151.1593478208252 -33.85611561408788, 151.1594147922316 -33.85619993603379, 151.1601040361296 -33.85691826513575, 151.1604943813604 -33.85732361397325, 151.160572752155 -33.85739972165405, 151.1639141978551 -33.860484513479, 151.1641861906131 -33.86069657562931, 151.1642923893263 -33.86077410823363, 151.1644850892803 -33.86087267941497, 151.1648533901057 -33.86104065275458, 151.1664033716406 -33.86169594994467, 151.1668892705675 -33.86191312505591, 151.167294451767 -33.86208344532838, 151.1673335952548 -33.86210372953406, 151.1674709746478 -33.86217480807298, 151.1680320592462 -33.86253606809976, 151.1681498249858 -33.86260915829544, 151.1684684211254 -33.86280680357225, 151.1685101630032 -33.86283270365305, 151.1686214746774 -33.86290185435425, 151.1686971632631 -33.86294862537395, 151.1701421195511 -33.86385504438314, 151.1703487334643 -33.86399024448133, 151.1706840934103 -33.86423742680591, 151.1711484508461 -33.86458301267376, 151.1714997364081 -33.8648427678531, 151.1721682770053 -33.86533847360678, 151.1727123463402 -33.86575002505262, 151.1729058844845 -33.86586712023995, 151.1730504723143 -33.86593065506599, 151.1732482852291 -33.86598631090308, 151.173572748701 -33.86602595730508, 151.1737910972787 -33.86605504250909, 151.1739863956226 -33.86609946659593, 151.1741610744847 -33.86615302695719, 151.1744398565843 -33.86627733058125, 151.1747261823967 -33.86644237025473, 151.1750077305243 -33.86664286537865, 151.1752357182907 -33.86682760252461, 151.1753698287414 -33.86696137769925, 151.1755227984743 -33.86714837795904, 151.1757399735856 -33.86745574234843, 151.1758580746013 -33.86761600433709, 151.1759465874988 -33.86778758189507, 151.1760216893512 -33.86795463322531, 151.1761932669092 -33.86850465371157, 151.1762700451422 -33.86876893511862, 151.1762989627082 -33.86883121265919), (151.1564563995065 -33.91497275143908, 151.1564619315626 -33.91491826906844, 151.1564727442177 -33.91486487634523, 151.1566095368775 -33.91450646616553, 151.1566470878037 -33.91442180894347, 151.1567087786111 -33.91433958247333, 151.1567892448815 -33.91428836904495, 151.1568804399881 -33.9142705155912, 151.1569581402305 -33.91427940040856, 151.1570279614839 -33.91429725386232, 151.1574973480616 -33.91455541648006, 151.1576260940944 -33.91461107231714, 151.1577655689632 -33.91464895851948, 151.1578781379228 -33.91464677922465, 151.1579720990574 -33.91462448336222, 151.1580874340451 -33.9145732699338, 151.158261777631 -33.91444863103362, 151.1584629433072 -33.91427277870505, 151.1587827967323 -33.91395250618478, 151.1590003909386 -33.91370658114568, 151.159518895469 -33.91304600335658, 151.1597772257248 -33.91273134671144, 151.1599951552073 -33.91246781967567, 151.1601044552247 -33.91233773253842, 151.1602369731139 -33.91216263458111, 151.1604053655486 -33.91195551775371, 151.1604191956889 -33.91193867012834, 151.1608523724449 -33.91139996521136, 151.161866834186 -33.91012683793835, 151.1622009368465 -33.9097160408638, 151.1622803972885 -33.90962157681503, 151.1626813875364 -33.90911455549207, 151.1633673624921 -33.90825113564617, 151.1640658264835 -33.90739693589376, 151.1645232269397 -33.90682411663087, 151.1646075488857 -33.90672470725923, 151.1650528794012 -33.90619664735931, 151.1652007361732 -33.90604501873091, 151.1654203420364 -33.9059161050601, 151.1657218390935 -33.90583287276158, 151.1658845318341 -33.90580026715823, 151.1660392617667 -33.9057640573365, 151.1670771928367 -33.90562122970644, 151.1672873271493 -33.90558535516087, 151.1673767620561 -33.90556951336387, 151.1674541270224 -33.90555442593816, 151.1675103695927 -33.90553405791343, 151.1675525305657 -33.90551234878421, 151.1675995530425 -33.90548636488438, 151.1677152233062 -33.9055305375141, 151.1678112799166 -33.90554436765436, 151.1678727192669 -33.90554352946404), (151.1677565460889 -33.90526265188868, 151.1677360942452 -33.90539357721624, 151.1677456496148 -33.90543456472278, 151.1677639221637 -33.90546264409841, 151.167817650163 -33.90551486335517, 151.1678727192669 -33.90554352946404), (151.1678727192669 -33.90554352946404, 151.1683614680409 -33.90553757831279, 151.1693894246461 -33.9055131031555, 151.1701565364246 -33.90558753445569, 151.1702065763865 -33.90559197686437, 151.1703314667438 -33.90560656137589, 151.1704134417569 -33.90561770930711, 151.1704858614003 -33.90562726467672, 151.1707491369789 -33.905654422043, 151.1712335271633 -33.9057423482073), (151.1714564857878 -33.90579582474956, 151.1719446478286 -33.90590931571853, 151.1726259289185 -33.90607854634359, 151.1729263363283 -33.90614970870155, 151.1730148492258 -33.9061675621553, 151.1730658950161 -33.90615641422409, 151.1731085589032 -33.90614442810255, 151.1731602752458 -33.906129843591, 151.173147450934 -33.90608122855261, 151.1731619516265 -33.90602641090587, 151.17322137932 -33.90594762101603, 151.1732743529481 -33.90588391855189), (151.1732743529481 -33.90588391855189, 151.1732659710448 -33.90596840813589, 151.1732336168986 -33.90607888161972, 151.1732563318562 -33.90615951552826), (151.173548944096 -33.90547957554281, 151.1734669690829 -33.90550907984198, 151.1734260653954 -33.90557311758224, 151.1733387259644 -33.90572323746807, 151.1732743529481 -33.90588391855189), (151.1762989627082 -33.86883121265919, 151.176343470614 -33.86892970002148, 151.1763649282861 -33.86903413853503, 151.176359144773 -33.86911225787262, 151.1763401178528 -33.86916254929164, 151.1763331608731 -33.86918115711668, 151.1762785946835 -33.86926573051971, 151.1761975416798 -33.8693164410339), (151.1762989627082 -33.86883121265919, 151.1764095200111 -33.86891645661446, 151.176493758138 -33.86894353016172, 151.1765912396718 -33.86895702502582, 151.1766948399951 -33.86895015186522, 151.1768985202422 -33.86891218184385, 151.1770757974943 -33.86884990430326, 151.1774242332093 -33.86872501394598, 151.1788374220843 -33.86821321493821, 151.1792504822727 -33.86804884581699), (151.178704652738 -33.87578903048353, 151.1787493282819 -33.87576556115465, 151.1795164400604 -33.875330708018, 151.1797831522194 -33.8751664227158, 151.1802425643323 -33.87488236001724, 151.1805846298008 -33.87466954349568, 151.1806676944612 -33.87461900061953, 151.180997187075 -33.87441842167661, 151.1811843549729 -33.87430451161248, 151.1813558487118 -33.87420015691796), (151.2594758568412 -33.8856940092827, 151.2595098873681 -33.88545361629969, 151.2595698179758 -33.88500225081378, 151.2595818879163 -33.8848810484939, 151.2595794571644 -33.88477510123778, 151.2595774455077 -33.88468717507351, 151.2595245556986 -33.88443471214993, 151.2594214582896 -33.88411385289643, 151.2593733461654 -33.88401821538122, 151.2592695782041 -33.88382258176114, 151.2591866811817 -33.88371303028668, 151.2590912951236 -33.88361026815377, 151.2590339629059 -33.8835485773464, 151.2588432746087 -33.88337574250298, 151.2587598746721 -33.88331262677207, 151.2585489859883 -33.88322855628326, 151.2584813440297 -33.88320081218373, 151.2584299629632 -33.88315831593465, 151.2583194894794 -33.88303317412027, 151.2582487462166 -33.88289001121407, 151.2582299707535 -33.88280200123074, 151.258247069836 -33.88267736233055, 151.2582689466033 -33.88252372204539, 151.2582663482133 -33.88239673621231, 151.2582341617051 -33.8822921300607, 151.2582040706727 -33.88223387583363, 151.2580794317725 -33.88206950671241, 151.2579835428002 -33.88196381091339, 151.2576550560148 -33.88160137742017, 151.2571988290251 -33.88109586483978, 151.2570475356728 -33.88092562838631, 151.2568903749883 -33.88077735251917, 151.2566945737302 -33.88062371223401, 151.2565808313041 -33.88054441942998, 151.2564585393368 -33.88048406972715, 151.2561529351471 -33.88033747024065, 151.2558792660085 -33.88022330871942, 151.2555930240151 -33.88013957350671, 151.2555051816698 -33.88011828347265, 151.2554187642481 -33.88008090018448, 151.2553272338655 -33.8800249090713, 151.2552408164437 -33.87995793766493, 151.2549680693145 -33.87970648056972, 151.2547831645304 -33.87952836512729, 151.2546026183362 -33.87936014033058, 151.254552159279 -33.87931160911123, 151.2544844335014 -33.87925033739904, 151.2543256802553 -33.87909275761936, 151.2541384285384 -33.87888966410549, 151.2540339900248 -33.87879167965738, 151.2539204990559 -33.87870417258824, 151.2538611551814 -33.87865052840795, 151.2538077624581 -33.8786010751792, 151.2537263741784 -33.87852387785099, 151.2536309881203 -33.87843494585832, 151.2533853983573 -33.87820444352104, 151.2532436603746 -33.87805457509231, 151.252983234643 -33.87771250962379, 151.2529252318731 -33.87741025819534, 151.2529089709809 -33.8772865413045, 151.2528922909936 -33.8771592201953, 151.2528889382323 -33.87705813444302, 151.2528976554116 -33.87694816387341, 151.2529526406964 -33.87675470954814, 151.2530164269795 -33.87651456802223, 151.253080967634 -33.87627501322953, 151.2531354500047 -33.87605825721346, 151.2531146628847 -33.87595557889958, 151.2531067838958 -33.87592674515267, 151.25305707921 -33.87581392473595, 151.2528717553308 -33.8753719469816, 151.2528019340774 -33.87520238108041, 151.2527625391324 -33.875127363047, 151.2527606951137 -33.87502275689542, 151.2527657242557 -33.8748185737341, 151.2527456915071 -33.87427031344751, 151.2527418358316 -33.87393554023478, 151.2527351303091 -33.87369581780402, 151.252721719264 -33.87345416753552, 151.2527289277007 -33.87343287750144, 151.2527199590643 -33.8733043829258, 151.2527079729428 -33.87317647508337, 151.2526730204066 -33.8731213221605, 151.2526326196332 -33.87307740098785, 151.2525744492252 -33.873030210873, 151.2525398319651 -33.87301688364695, 151.2524952402402 -33.87299726999353, 151.2524341361661 -33.8729884689952, 151.252346629097 -33.87298704407166, 151.2520440423924 -33.87299131884228, 151.2517323194134 -33.87299592888903, 151.2516529427903 -33.87299425250839, 151.251538529812 -33.87299550979387, 151.2514476699816 -33.87300095803093, 151.2514068501131 -33.87300104184996, 151.2513538764851 -33.87300640626799, 151.2512488512384 -33.87301713510404, 151.2508983200476 -33.87308125666331, 151.2507150916443 -33.87312065160825, 151.2506191188529 -33.87314177400424, 151.2505933864102 -33.8731474736984, 151.2504876906112 -33.87315660997285, 151.2503001874372 -33.87318435407235, 151.2501346448495 -33.87318753919555, 151.2499783223553 -33.87316365077152, 151.2498373387439 -33.8731229147221, 151.2494469935131 -33.87302861831139, 151.2492505217027 -33.87300825028669, 151.2490390462857 -33.87297991945397, 151.2488846516292 -33.872958964696, 151.2487311789821 -33.87293985395678, 151.2486922869514 -33.87293624973842, 151.2486254831831 -33.8729285383875, 151.248572341917 -33.87292770019718, 151.248416522337 -33.87290884091504, 151.2483020255397 -33.87289107128031, 151.248202699987 -33.87280733606761), (151.2571188656688 -33.88893068119316, 151.2572746852488 -33.88881551384355, 151.2578318303528 -33.88846213280576, 151.2581564614626 -33.8882322172017, 151.2585552724157 -33.88791412397626, 151.2587110919957 -33.88774472571313, 151.2587967550461 -33.88760508320627, 151.2588257564311 -33.88731867357481, 151.2588484713887 -33.8871847307621, 151.2588769698594 -33.88705615236742, 151.2589097431009 -33.88697845212502, 151.2589385768478 -33.88691122926156, 151.2592045346355 -33.88641904390721, 151.2593145890241 -33.88622936143838, 151.2593159301287 -33.88619994095824, 151.2594076281494 -33.8860714463826, 151.2594465201801 -33.8860352365609), (151.2595250586128 -33.88606566286941, 151.2595317641353 -33.88611075750848, 151.2594139983958 -33.88620564065241, 151.259277289555 -33.88642700671522, 151.2592425884759 -33.88649657651158, 151.2591684924519 -33.88664711549256, 151.2590352201914 -33.88693746461848, 151.2589798996304 -33.88714910767362, 151.25888644141 -33.88761086671946, 151.2588396703903 -33.88772720753551, 151.2585156260137 -33.88804798296996, 151.2580571359101 -33.88841921746152, 151.2575662916602 -33.88874871007525, 151.2574998231681 -33.88882004007127, 151.25743905437 -33.88887410334672), (151.2594465201801 -33.8860352365609, 151.2594121543771 -33.88594806476788, 151.2594183569855 -33.88588385938959, 151.2594274932599 -33.88583247832312, 151.2594434188759 -33.88574455215883, 151.2594758568412 -33.8856940092827), (151.2595250586128 -33.88606566286941, 151.2594465201801 -33.8860352365609), (151.2594758568412 -33.8856940092827, 151.2595046905881 -33.8857114436413, 151.2594980688846 -33.88577950469508, 151.2594854960299 -33.88588226682799, 151.2594829814589 -33.88593674919861, 151.259491028086 -33.88598645388441, 151.2595261482602 -33.88603582329412, 151.2595250586128 -33.88606566286941))



```python
colours = ["red", "blue", "green", "yellow", "purple", "black", "orange", "navy", "cyan"]
sns.set_context("talk")
# colours = sns.color_palette(None,9)
# vic_road_merge = merge_lines(victoria_road)
# plt.rcParams['figure.figsize']=(20,12)
ax = plt.gca()
for l, line in enumerate(vic_road_merge["geometry"]):
#     for s, seg in enumerate(line):
        gpd.GeoDataFrame(geometry = vic_road_merge["geometry"].iloc[l]).plot(color = colours[l], ax = ax, figsize = (12, 20),
                                                                            linewidth = 4)
    
ax.get_xaxis().get_major_formatter().set_useOffset(False)
# ax.set_xlabel("longitude")
# ax.set_ylabel("latiude")
# ax.set_title("Victoria Road")

ax.figure.savefig("victoria_clean.png")
```


![png](cleaning_osm_files/cleaning_osm_60_0.png)



```python
streets_reduced_linemerge = streets.groupby("name")['geometry'].apply(lambda x: shapely.ops.linemerge(x.values) ).(rename("merged_line").reset_index())

s = streets_reduced_linemerge.apply(lambda x: pd.Series(x['merged_line']),axis=1).stack().reset_index(level=1)
```


```python
len(streets), len(streets_reduced_linemerge)
```




    (72798, 29520)




```python
s = streets_reduced_linemerge.apply(lambda x: pd.Series(x['merged_line']),axis=1).stack().reset_index(level=1, drop = True).rename("lines")
streets_reduced_linemerge.drop('merged_line', axis=1).join(s)
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>name</th>
      <th>lines</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>(steep, rough track)</td>
      <td>LINESTRING (150.8365482405775 -34.054023160669...</td>
    </tr>
    <tr>
      <th>1</th>
      <td>(walking track, steep sections, rough surface)</td>
      <td>LINESTRING (150.8497772307182 -34.079535913730...</td>
    </tr>
    <tr>
      <th>1</th>
      <td>(walking track, steep sections, rough surface)</td>
      <td>LINESTRING (150.8522587769717 -34.080268408248...</td>
    </tr>
    <tr>
      <th>2</th>
      <td>0+4+4 steps</td>
      <td>LINESTRING (151.2313620315879 -33.918242615686...</td>
    </tr>
    <tr>
      <th>3</th>
      <td>1</td>
      <td>LINESTRING (151.1917438765909 -33.872509024133...</td>
    </tr>
    <tr>
      <th>4</th>
      <td>1 road</td>
      <td>LINESTRING (151.0208514553944 -33.843009083552...</td>
    </tr>
    <tr>
      <th>5</th>
      <td>10 road</td>
      <td>LINESTRING (151.0203149297723 -33.842935574261...</td>
    </tr>
    <tr>
      <th>6</th>
      <td>100 metre hill</td>
      <td>LINESTRING (151.2491582369488 -33.730503658758...</td>
    </tr>
    <tr>
      <th>7</th>
      <td>104</td>
      <td>LINESTRING (150.9689275768912 -34.183576538328...</td>
    </tr>
    <tr>
      <th>8</th>
      <td>105</td>
      <td>LINESTRING (150.9694203489789 -34.180166612479...</td>
    </tr>
    <tr>
      <th>9</th>
      <td>108 management trail</td>
      <td>LINESTRING (150.982058834041 -34.1561159147895...</td>
    </tr>
    <tr>
      <th>10</th>
      <td>10b management trail</td>
      <td>LINESTRING (150.8414140191887 -34.191103152102...</td>
    </tr>
    <tr>
      <th>11</th>
      <td>10d management trail</td>
      <td>LINESTRING (150.8345442951667 -34.178122266295...</td>
    </tr>
    <tr>
      <th>11</th>
      <td>10d management trail</td>
      <td>LINESTRING (150.8400830567838 -34.175923357816...</td>
    </tr>
    <tr>
      <th>12</th>
      <td>10t management trail</td>
      <td>LINESTRING (150.8101135619869 -34.168689440102...</td>
    </tr>
    <tr>
      <th>13</th>
      <td>10th avenue</td>
      <td>LINESTRING (151.2234513590108 -33.950876382225...</td>
    </tr>
    <tr>
      <th>14</th>
      <td>11 road</td>
      <td>LINESTRING (151.0202559211739 -33.842928868739...</td>
    </tr>
    <tr>
      <th>15</th>
      <td>11th street</td>
      <td>LINESTRING (151.2182208837925 -33.947695449971...</td>
    </tr>
    <tr>
      <th>16</th>
      <td>12</td>
      <td>LINESTRING (151.1586143204785 -33.818815139137...</td>
    </tr>
    <tr>
      <th>16</th>
      <td>12</td>
      <td>LINESTRING (151.1559236457408 -33.818434768371...</td>
    </tr>
    <tr>
      <th>16</th>
      <td>12</td>
      <td>LINESTRING (151.1595987750063 -33.818234943799...</td>
    </tr>
    <tr>
      <th>17</th>
      <td>126 greville street</td>
      <td>LINESTRING (151.1690203694494 -33.794901317926...</td>
    </tr>
    <tr>
      <th>18</th>
      <td>12km loop</td>
      <td>LINESTRING (150.824928324389 -33.8794401036868...</td>
    </tr>
    <tr>
      <th>18</th>
      <td>12km loop</td>
      <td>LINESTRING (150.8259448816059 -33.883014231019...</td>
    </tr>
    <tr>
      <th>18</th>
      <td>12km loop</td>
      <td>LINESTRING (150.8313715771775 -33.877534142724...</td>
    </tr>
    <tr>
      <th>18</th>
      <td>12km loop</td>
      <td>LINESTRING (150.8299928379245 -33.879641017905...</td>
    </tr>
    <tr>
      <th>19</th>
      <td>12th avenue</td>
      <td>LINESTRING (151.2192529475302 -33.951601165393...</td>
    </tr>
    <tr>
      <th>20</th>
      <td>12th avenue north</td>
      <td>LINESTRING (151.219416227004 -33.9510610355526...</td>
    </tr>
    <tr>
      <th>21</th>
      <td>12th street</td>
      <td>LINESTRING (151.219944286904 -33.9476576475880...</td>
    </tr>
    <tr>
      <th>22</th>
      <td>12th street west</td>
      <td>LINESTRING (151.2152610661439 -33.948080346965...</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>29493</th>
      <td>zermatt avenue</td>
      <td>LINESTRING (150.9251229965352 -33.793858106257...</td>
    </tr>
    <tr>
      <th>29494</th>
      <td>zeta road</td>
      <td>LINESTRING (151.1708062177395 -33.821145811133...</td>
    </tr>
    <tr>
      <th>29495</th>
      <td>zeya close</td>
      <td>LINESTRING (150.7742965162601 -33.803524703730...</td>
    </tr>
    <tr>
      <th>29496</th>
      <td>zieria place</td>
      <td>LINESTRING (151.2056610209821 -33.736458162772...</td>
    </tr>
    <tr>
      <th>29497</th>
      <td>zig zag lane</td>
      <td>LINESTRING (151.2013368809539 -33.826731930503...</td>
    </tr>
    <tr>
      <th>29497</th>
      <td>zig zag lane</td>
      <td>LINESTRING (151.2014983164091 -33.823920472541...</td>
    </tr>
    <tr>
      <th>29497</th>
      <td>zig zag lane</td>
      <td>LINESTRING (151.2014231307376 -33.823907983505...</td>
    </tr>
    <tr>
      <th>29497</th>
      <td>zig zag lane</td>
      <td>LINESTRING (151.2014985678662 -33.824811049753...</td>
    </tr>
    <tr>
      <th>29498</th>
      <td>zig-zag home</td>
      <td>LINESTRING (150.6594612576597 -33.662157201186...</td>
    </tr>
    <tr>
      <th>29499</th>
      <td>zillah street</td>
      <td>LINESTRING (151.0040368547142 -33.848824531793...</td>
    </tr>
    <tr>
      <th>29500</th>
      <td>zinnia lane</td>
      <td>LINESTRING (151.1814061401308 -33.792338215754...</td>
    </tr>
    <tr>
      <th>29501</th>
      <td>zinnia way</td>
      <td>LINESTRING (150.9022465196608 -33.795322005646...</td>
    </tr>
    <tr>
      <th>29502</th>
      <td>zions avenue</td>
      <td>LINESTRING (151.2531271519205 -33.970775858549...</td>
    </tr>
    <tr>
      <th>29503</th>
      <td>zircon place</td>
      <td>LINESTRING (150.8181332831243 -34.031892673177...</td>
    </tr>
    <tr>
      <th>29504</th>
      <td>zircon street</td>
      <td>LINESTRING (150.888753080475 -33.8605629680927...</td>
    </tr>
    <tr>
      <th>29505</th>
      <td>zissie street</td>
      <td>LINESTRING (150.8823811576825 -33.693321871556...</td>
    </tr>
    <tr>
      <th>29506</th>
      <td>zodiac place</td>
      <td>LINESTRING (150.8066919852924 -33.801661406654...</td>
    </tr>
    <tr>
      <th>29507</th>
      <td>zoe close</td>
      <td>LINESTRING (150.9671391302111 -33.867801579673...</td>
    </tr>
    <tr>
      <th>29508</th>
      <td>zoe place</td>
      <td>LINESTRING (150.8261268527238 -33.765094012955...</td>
    </tr>
    <tr>
      <th>29509</th>
      <td>zoeller street</td>
      <td>LINESTRING (151.1083197163142 -33.858245455684...</td>
    </tr>
    <tr>
      <th>29510</th>
      <td>zola avenue</td>
      <td>LINESTRING (151.1095550412039 -33.794898970993...</td>
    </tr>
    <tr>
      <th>29511</th>
      <td>zolyomi lane</td>
      <td>LINESTRING (150.9118843672059 -33.768877268772...</td>
    </tr>
    <tr>
      <th>29512</th>
      <td>zonnebeke crescent</td>
      <td>LINESTRING (150.985975865032 -33.9406683138078...</td>
    </tr>
    <tr>
      <th>29513</th>
      <td>zoo car park entrance</td>
      <td>LINESTRING (151.2436483928977 -33.841612323207...</td>
    </tr>
    <tr>
      <th>29514</th>
      <td>zoric close</td>
      <td>LINESTRING (150.8615990669541 -33.953605613718...</td>
    </tr>
    <tr>
      <th>29515</th>
      <td>zouch road</td>
      <td>LINESTRING (150.8441339467685 -33.969134262813...</td>
    </tr>
    <tr>
      <th>29516</th>
      <td>zulio court</td>
      <td>LINESTRING (150.9779727400629 -33.723732841193...</td>
    </tr>
    <tr>
      <th>29517</th>
      <td>zuni close</td>
      <td>LINESTRING (150.8857073483189 -33.866866829831...</td>
    </tr>
    <tr>
      <th>29518</th>
      <td>zuttion lane</td>
      <td>LINESTRING (151.162019971557 -33.9230818236642...</td>
    </tr>
    <tr>
      <th>29519</th>
      <td>zutton avenue</td>
      <td>LINESTRING (151.0776386817569 -33.944679138295...</td>
    </tr>
  </tbody>
</table>
<p>53922 rows × 2 columns</p>
</div>




```python
streets_reduced_linemerge
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>name</th>
      <th>merged_line</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>(steep, rough track)</td>
      <td>LINESTRING (150.8365482405775 -34.054023160669...</td>
    </tr>
    <tr>
      <th>1</th>
      <td>(walking track, steep sections, rough surface)</td>
      <td>(LINESTRING (150.8497772307182 -34.07953591373...</td>
    </tr>
    <tr>
      <th>2</th>
      <td>0+4+4 steps</td>
      <td>LINESTRING (151.2313620315879 -33.918242615686...</td>
    </tr>
    <tr>
      <th>3</th>
      <td>1</td>
      <td>LINESTRING (151.1917438765909 -33.872509024133...</td>
    </tr>
    <tr>
      <th>4</th>
      <td>1 road</td>
      <td>LINESTRING (151.0208514553944 -33.843009083552...</td>
    </tr>
    <tr>
      <th>5</th>
      <td>10 road</td>
      <td>LINESTRING (151.0203149297723 -33.842935574261...</td>
    </tr>
    <tr>
      <th>6</th>
      <td>100 metre hill</td>
      <td>LINESTRING (151.2491582369488 -33.730503658758...</td>
    </tr>
    <tr>
      <th>7</th>
      <td>104</td>
      <td>LINESTRING (150.9689275768912 -34.183576538328...</td>
    </tr>
    <tr>
      <th>8</th>
      <td>105</td>
      <td>LINESTRING (150.9694203489789 -34.180166612479...</td>
    </tr>
    <tr>
      <th>9</th>
      <td>108 management trail</td>
      <td>LINESTRING (150.982058834041 -34.1561159147895...</td>
    </tr>
    <tr>
      <th>10</th>
      <td>10b management trail</td>
      <td>LINESTRING (150.8414140191887 -34.191103152102...</td>
    </tr>
    <tr>
      <th>11</th>
      <td>10d management trail</td>
      <td>(LINESTRING (150.8345442951667 -34.17812226629...</td>
    </tr>
    <tr>
      <th>12</th>
      <td>10t management trail</td>
      <td>LINESTRING (150.8101135619869 -34.168689440102...</td>
    </tr>
    <tr>
      <th>13</th>
      <td>10th avenue</td>
      <td>LINESTRING (151.2234513590108 -33.950876382225...</td>
    </tr>
    <tr>
      <th>14</th>
      <td>11 road</td>
      <td>LINESTRING (151.0202559211739 -33.842928868739...</td>
    </tr>
    <tr>
      <th>15</th>
      <td>11th street</td>
      <td>LINESTRING (151.2182208837925 -33.947695449971...</td>
    </tr>
    <tr>
      <th>16</th>
      <td>12</td>
      <td>(LINESTRING (151.1586143204785 -33.81881513913...</td>
    </tr>
    <tr>
      <th>17</th>
      <td>126 greville street</td>
      <td>LINESTRING (151.1690203694494 -33.794901317926...</td>
    </tr>
    <tr>
      <th>18</th>
      <td>12km loop</td>
      <td>(LINESTRING (150.824928324389 -33.879440103686...</td>
    </tr>
    <tr>
      <th>19</th>
      <td>12th avenue</td>
      <td>LINESTRING (151.2192529475302 -33.951601165393...</td>
    </tr>
    <tr>
      <th>20</th>
      <td>12th avenue north</td>
      <td>LINESTRING (151.219416227004 -33.9510610355526...</td>
    </tr>
    <tr>
      <th>21</th>
      <td>12th street</td>
      <td>LINESTRING (151.219944286904 -33.9476576475880...</td>
    </tr>
    <tr>
      <th>22</th>
      <td>12th street west</td>
      <td>LINESTRING (151.2152610661439 -33.948080346965...</td>
    </tr>
    <tr>
      <th>23</th>
      <td>14th avenue</td>
      <td>(LINESTRING (151.2168469222242 -33.95154282734...</td>
    </tr>
    <tr>
      <th>24</th>
      <td>17th avenue</td>
      <td>LINESTRING (151.2211462518191 -33.949697802820...</td>
    </tr>
    <tr>
      <th>25</th>
      <td>19th avenue</td>
      <td>LINESTRING (151.21898028422 -33.94974281364054...</td>
    </tr>
    <tr>
      <th>26</th>
      <td>1st avenue</td>
      <td>LINESTRING (151.2233322521667 -33.957186027477...</td>
    </tr>
    <tr>
      <th>27</th>
      <td>1st street</td>
      <td>LINESTRING (151.213661044647 -33.9481423730485...</td>
    </tr>
    <tr>
      <th>28</th>
      <td>2</td>
      <td>LINESTRING (151.1919964233335 -33.872514640008...</td>
    </tr>
    <tr>
      <th>29</th>
      <td>2 road</td>
      <td>LINESTRING (151.020797727395 -33.8430001149161...</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>29490</th>
      <td>zep place</td>
      <td>LINESTRING (150.9164002851787 -33.708994521748...</td>
    </tr>
    <tr>
      <th>29491</th>
      <td>zeppelin street</td>
      <td>LINESTRING (150.8143909309954 -34.014994002008...</td>
    </tr>
    <tr>
      <th>29492</th>
      <td>zerafa place</td>
      <td>LINESTRING (150.8919509441737 -33.722398358389...</td>
    </tr>
    <tr>
      <th>29493</th>
      <td>zermatt avenue</td>
      <td>LINESTRING (150.9251229965352 -33.793858106257...</td>
    </tr>
    <tr>
      <th>29494</th>
      <td>zeta road</td>
      <td>LINESTRING (151.1708062177395 -33.821145811133...</td>
    </tr>
    <tr>
      <th>29495</th>
      <td>zeya close</td>
      <td>LINESTRING (150.7742965162601 -33.803524703730...</td>
    </tr>
    <tr>
      <th>29496</th>
      <td>zieria place</td>
      <td>LINESTRING (151.2056610209821 -33.736458162772...</td>
    </tr>
    <tr>
      <th>29497</th>
      <td>zig zag lane</td>
      <td>(LINESTRING (151.2013368809539 -33.82673193050...</td>
    </tr>
    <tr>
      <th>29498</th>
      <td>zig-zag home</td>
      <td>LINESTRING (150.6594612576597 -33.662157201186...</td>
    </tr>
    <tr>
      <th>29499</th>
      <td>zillah street</td>
      <td>LINESTRING (151.0040368547142 -33.848824531793...</td>
    </tr>
    <tr>
      <th>29500</th>
      <td>zinnia lane</td>
      <td>LINESTRING (151.1814061401308 -33.792338215754...</td>
    </tr>
    <tr>
      <th>29501</th>
      <td>zinnia way</td>
      <td>LINESTRING (150.9022465196608 -33.795322005646...</td>
    </tr>
    <tr>
      <th>29502</th>
      <td>zions avenue</td>
      <td>LINESTRING (151.2531271519205 -33.970775858549...</td>
    </tr>
    <tr>
      <th>29503</th>
      <td>zircon place</td>
      <td>LINESTRING (150.8181332831243 -34.031892673177...</td>
    </tr>
    <tr>
      <th>29504</th>
      <td>zircon street</td>
      <td>LINESTRING (150.888753080475 -33.8605629680927...</td>
    </tr>
    <tr>
      <th>29505</th>
      <td>zissie street</td>
      <td>LINESTRING (150.8823811576825 -33.693321871556...</td>
    </tr>
    <tr>
      <th>29506</th>
      <td>zodiac place</td>
      <td>LINESTRING (150.8066919852924 -33.801661406654...</td>
    </tr>
    <tr>
      <th>29507</th>
      <td>zoe close</td>
      <td>LINESTRING (150.9671391302111 -33.867801579673...</td>
    </tr>
    <tr>
      <th>29508</th>
      <td>zoe place</td>
      <td>LINESTRING (150.8261268527238 -33.765094012955...</td>
    </tr>
    <tr>
      <th>29509</th>
      <td>zoeller street</td>
      <td>LINESTRING (151.1083197163142 -33.858245455684...</td>
    </tr>
    <tr>
      <th>29510</th>
      <td>zola avenue</td>
      <td>LINESTRING (151.1095550412039 -33.794898970993...</td>
    </tr>
    <tr>
      <th>29511</th>
      <td>zolyomi lane</td>
      <td>LINESTRING (150.9118843672059 -33.768877268772...</td>
    </tr>
    <tr>
      <th>29512</th>
      <td>zonnebeke crescent</td>
      <td>LINESTRING (150.985975865032 -33.9406683138078...</td>
    </tr>
    <tr>
      <th>29513</th>
      <td>zoo car park entrance</td>
      <td>LINESTRING (151.2436483928977 -33.841612323207...</td>
    </tr>
    <tr>
      <th>29514</th>
      <td>zoric close</td>
      <td>LINESTRING (150.8615990669541 -33.953605613718...</td>
    </tr>
    <tr>
      <th>29515</th>
      <td>zouch road</td>
      <td>LINESTRING (150.8441339467685 -33.969134262813...</td>
    </tr>
    <tr>
      <th>29516</th>
      <td>zulio court</td>
      <td>LINESTRING (150.9779727400629 -33.723732841193...</td>
    </tr>
    <tr>
      <th>29517</th>
      <td>zuni close</td>
      <td>LINESTRING (150.8857073483189 -33.866866829831...</td>
    </tr>
    <tr>
      <th>29518</th>
      <td>zuttion lane</td>
      <td>LINESTRING (151.162019971557 -33.9230818236642...</td>
    </tr>
    <tr>
      <th>29519</th>
      <td>zutton avenue</td>
      <td>LINESTRING (151.0776386817569 -33.944679138295...</td>
    </tr>
  </tbody>
</table>
<p>29520 rows × 2 columns</p>
</div>




```python
val = victoria_road.groupby("name")['geometry'].apply(lambda x: shapely.ops.linemerge(x.values) ).iloc[0]
```


```python
print(val)
```

    MULTILINESTRING ((150.8298032392747 -34.15843451684535, 150.8293112215584 -34.15804274669102, 150.827125891763 -34.15651707267536, 150.824228351655 -34.15616637384659, 150.8230264705589 -34.15601181155208, 150.8204622787401 -34.15575431948659, 150.8162348658745 -34.15525400368617), (150.8334433321849 -33.76640008110822, 150.8336361997769 -33.76539827604094, 150.8336591661916 -33.76527841482556, 150.8337450806991 -33.76483216230059, 150.8339722302752 -33.76365081686731, 150.8341453165757 -33.76280432846581, 150.8342266210365 -33.76240685861734, 150.8342985377656 -33.76203771960158, 150.8346123562205 -33.76042780745905, 150.8348861929972 -33.75908267963774), (150.8813690428743 -33.98322281712458, 150.8815617428282 -33.98325190232859, 150.8819514175068 -33.98332440579102, 150.8833294862076 -33.98349765972964), (150.8833294862076 -33.98349765972964, 150.8835212641521 -33.98349556425384, 150.8835983776614 -33.98348131501845, 150.8836636726871 -33.98348030919007), (150.8836599846497 -33.98357670107654, 150.8835703821048 -33.98356127837471, 150.8834196754857 -33.98353412100843, 150.8833294862076 -33.98349765972964), (150.8844034594612 -33.98363696696038, 150.8841691852674 -33.98362414264852, 150.8840491564141 -33.98360327170963, 150.8839609787926 -33.98358793282682, 150.8838844520167 -33.98358885483617, 150.8838157204107 -33.98359061503584), (150.8838387706444 -33.9834994199293, 150.8839444664434 -33.98353705467454, 150.8840579574124 -33.98356597224048, 150.884181255208 -33.98359170468325, 150.8842899684922 -33.98360385844285, 150.8844034594612 -33.98363696696038, 150.8847137575166 -33.98366890201145, 150.8858015609105 -33.98380016261515, 150.8867752866022 -33.98392765136241, 150.8879066758926 -33.98407575959151, 150.8884864521351 -33.98415161581522, 150.8889813196984 -33.98421640792677, 150.8894496166287 -33.98427776345798, 150.8901744836152 -33.9843800226767, 150.8902726357013 -33.98438882367503, 150.8903134555698 -33.98440256999623, 150.8903498330296 -33.98442075872612, 150.8903929998309 -33.98443115228608, 150.8904209953875 -33.98443551087573, 150.8904602226943 -33.98443794162764, 150.8905057364286 -33.9844301464577, 150.8905527589054 -33.98442880535318, 150.890604559067 -33.98443031409576, 150.8910255820634 -33.98448873596087, 150.8914184418651 -33.9845390273799, 150.8925971889084 -33.98468990163704, 150.8927019626981 -33.98470364795824, 150.8928432815856 -33.98472586000165, 150.8932002668418 -33.98476961353619, 150.8932944794335 -33.98478126438161, 150.8937752653995 -33.98484412865542), (150.8940186758676 -33.98487891355359, 150.8942020719091 -33.98490447835826, 150.8943646808306 -33.9849367486855, 150.8945554529469 -33.98497631126847, 150.8946725481342 -33.98500330099668, 150.8947266952287 -33.98501587385144, 150.8949157909643 -33.98504730598836, 150.8949763921242 -33.98505820246248, 150.895158027966 -33.98507697792559, 150.8953683299167 -33.98508955078034, 150.8956501295013 -33.98508955078034, 150.8958923665031 -33.98511478030889, 150.8963195921078 -33.98515845002444, 150.8973050524639 -33.98527076752696, 150.897860018273 -33.98534846776937, 150.8991175552061 -33.98550143750228, 150.9012317226436 -33.98578851768599, 150.9021620300769 -33.98591164784361, 150.9025109687059 -33.98595774831104, 150.9035514981659 -33.98610334196917, 150.9044957195584 -33.98623745241997, 150.904684899113 -33.98626192757723, 150.9047705621635 -33.98624893562732, 150.9048239548867 -33.9862183416807, 150.9048586559658 -33.98613745631511, 150.9048369468366 -33.98602061258487, 150.9048239548867 -33.98592170612741, 150.9048607514416 -33.9858157588713, 150.9049323328947 -33.98568458208663, 150.9049800259237 -33.98555684188227, 150.9049951971685 -33.98542021686055, 150.9050732326871 -33.98491872759368, 150.9050882362938 -33.98486122773789), (151.065872249901 -33.80922682864008, 151.0652593651409 -33.80977978279245, 151.0637162567667 -33.81117000525279, 151.0627485660453 -33.81187727024258, 151.0621223540592 -33.8121828744323, 151.061482647209 -33.81244153996423, 151.0604134516402 -33.81288653520369, 151.0593554878216 -33.81328123902415, 151.0583720391223 -33.81366353762789, 151.0573477705545 -33.81406972465567, 151.0568585188662 -33.81426577737091, 151.0566944850211 -33.81432193612216, 151.0565326304709 -33.81436066051484, 151.0563920659546 -33.81438044180632, 151.0562420298878 -33.81439267938495, 151.0560539399806 -33.81439251174689, 151.0557737329575 -33.81437365246475, 151.0555186716439 -33.81435563137293, 151.0553389636399 -33.81433911902365, 151.055214995292 -33.81432461833117, 151.0548969858856 -33.8142779311305, 151.0546456964285 -33.81423350704369, 151.0543970891803 -33.8141863169288, 151.0537474916844 -33.81408682373814, 151.0536437237231 -33.81407173631243, 151.0532909294185 -33.81401633193246, 151.0523817443813 -33.81387627033044, 151.0522972547973 -33.81386327838052, 151.0519392637128 -33.81380854455278, 151.0513296478949 -33.81372413878781, 151.0507202835342 -33.81363411714776, 151.0502692533245 -33.81358332281451, 151.0492163186478 -33.81344015990831, 151.0482541599825 -33.81328903419409, 151.0480735299691 -33.8132594460759, 151.0480202210649 -33.81325056125851, 151.047090668003 -33.81311737281708, 151.0469579824757 -33.81309893263011, 151.0468123888176 -33.8130788160625, 151.0458950733343 -33.81294495706882, 151.0458680836061 -33.81294261013593, 151.0452581325122 -33.81286273059867, 151.0437701770608 -33.81265427266674, 151.0427312401624 -33.8125014705719, 151.0419342888087 -33.81238831487906, 151.0417387390076 -33.81235981640828, 151.0406552942035 -33.8122206768156, 151.0396255773986 -33.81209746283894, 151.038712788143 -33.81196653751138, 151.0375048720767 -33.81180744898913, 151.0364267916905 -33.81167132688159, 151.0354428400769 -33.81153746788792, 151.0352858470305 -33.81151642931096, 151.035080993317 -33.81148072240345, 151.0349362378491 -33.81145155338041, 151.0347662528527 -33.81140587200809, 151.0344778315646 -33.81131132414029, 151.0339063534062 -33.81108945516331, 151.033414419509 -33.81089474355258, 151.033293049551 -33.81084034500097, 151.0328743734875 -33.81067706552716, 151.0323426255501 -33.81046760176685, 151.0319214349157 -33.8102833675351, 151.0314173472588 -33.81004272309499, 151.0312157624875 -33.80993610528662, 151.0309233178858 -33.80982437451731, 151.0307286900941 -33.8097580736632, 151.030073476723 -33.80954240729457, 151.0298034956218 -33.80944618304613, 151.0293449216992 -33.80929514115095, 151.0291502939075 -33.80922842120168, 151.0288879403381 -33.80915675592954, 151.0287838371008 -33.80913236459131, 151.0286745370834 -33.80911677425141, 151.0285158676563 -33.80910571013922, 151.0280805954245 -33.80909623858864, 151.0278560442385 -33.80909892079765, 151.0277953592595 -33.80910160300667, 151.0277351771947 -33.80910361466343, 151.027656722581 -33.80910495576794, 151.0275945288595 -33.80910604541535, 151.0274662857409 -33.80910822471017, 151.0274009907152 -33.80910931435758, 151.0270322707946 -33.80911585224206, 151.0269096435512 -33.80911803153688, 151.0267542430663 -33.80912674871618, 151.0265600343698 -33.80913395715291, 151.0263240837954 -33.80914611091251, 151.0260304657272 -33.80915885140536, 151.0250644513865 -33.80920503569183, 151.0245012713123 -33.80923370180068, 151.0240384226191 -33.80925105234024, 151.023779337992 -33.80925180671153, 151.0233707202123 -33.80923965295193, 151.0230447479979 -33.80922381115494, 151.0228266508773 -33.80920226966379, 151.0226280835911 -33.80918181782005, 151.0224057955189 -33.80915365462536, 151.0221556795282 -33.80912171957428, 151.0209384595494 -33.80893128273419, 151.0202064679452 -33.80881334935654, 151.0196086706109 -33.80869633798824, 151.019297366727 -33.80863741320891, 151.01903308532 -33.80859751534982, 151.0189534572398 -33.80857890752478, 151.0186249704544 -33.80852484424929, 151.0182414983843 -33.80848930497984, 151.0180443560216 -33.80849483703594, 151.0171688662351 -33.80857337546868, 151.0167595779032 -33.80861117785199, 151.016275690633 -33.80864931551142, 151.0152481531229 -33.80873137434349, 151.0147451551134 -33.80878325832413, 151.0144632717098 -33.80883044843901, 151.0141188593084 -33.80883774069477, 151.0139810608202 -33.8088497268163, 151.0138866805905 -33.80885802490045, 151.0134473850451 -33.80889172015119, 151.0124002338817 -33.80897771847776, 151.0122165863831 -33.80899951142601, 151.0111089178787 -33.80908483920032, 151.0109659226106 -33.80909565185542, 151.01034096791 -33.80914435071284, 151.0097062902017 -33.80918835570451, 151.009516943009 -33.80918885861871, 151.009271353246 -33.80917142426011, 151.008610859276 -33.80909556803638, 151.008220178769 -33.80905441289181, 151.0072758735575 -33.80893622805706, 151.0072253306814 -33.80892977399162, 151.0069460456676 -33.80888015312482, 151.0053271648887 -33.80868711789475, 151.0037728247642 -33.80846843404095, 151.0018154827351 -33.80821463001285, 151.0016784386182 -33.80819468108331), (151.0017050930703 -33.80810532999547, 151.0037906782179 -33.8084036419294, 151.005267150462 -33.80859600660724, 151.0053401568386 -33.80861101021392, 151.0057470982377 -33.80867454503996, 151.0059546341603 -33.8086965056263, 151.0063305625176 -33.80873631966637, 151.0069653240449 -33.80881385227073, 151.0072908771642 -33.80885743816722, 151.0082323325286 -33.80898350199095, 151.0092721914364 -33.80908257608647, 151.009540747614 -33.80910202210183, 151.0099074558779 -33.8090959033125, 151.0102556401357 -33.80909020361835, 151.0110812575983 -33.80902717170648, 151.0122218669821 -33.80893480313352, 151.0124367789795 -33.80891803932718, 151.0128464864067 -33.80888132659126, 151.0131764819346 -33.80883581285704, 151.0135216487072 -33.80879490916954, 151.0147251223649 -33.80869575125502, 151.0162142512827 -33.80859340821726, 151.0174868756416 -33.80848402438085, 151.018053324658 -33.8084329785905, 151.0181654745225 -33.80842283648767, 151.0185231303309 -33.80843725336114, 151.0186767706161 -33.80845753756682, 151.0188686323797 -33.80849592668335, 151.0191671957707 -33.80855711457653, 151.0209183429818 -33.8088449491315, 151.0213048325371 -33.80890873541463, 151.0219873709125 -33.80902650115422, 151.0223315318568 -33.80907712784941, 151.0225410794362 -33.80909942371184, 151.0228436661407 -33.8091363040858, 151.0231136472419 -33.80915717502469, 151.0233638470517 -33.80917301682172, 151.0237774939733 -33.80918181782005, 151.0239962616461 -33.80917972234425, 151.0243046318639 -33.80917050225077, 151.0249903553625 -33.8091413332277, 151.0269703285301 -33.80904251058931, 151.027036042651 -33.80903831963772, 151.0273931955452 -33.809015353223, 151.0275152360554 -33.80900923443369, 151.0276458261069 -33.8089981703215, 151.0277984605637 -33.80899523665539, 151.0281353292522 -33.80898132269613, 151.0285591182767 -33.80899691303603, 151.0288229805886 -33.80905432907278, 151.0293586680204 -33.8092237273359, 151.029685226968 -33.80932363962174, 151.0301075910689 -33.80945464876833, 151.0309620422785 -33.80974022020945, 151.0311426722918 -33.8098045094068, 151.0323734709538 -33.81038696785834, 151.0332337894956 -33.81072920096491, 151.033316937975 -33.81076323149179, 151.0334652976612 -33.81082391647075, 151.034241294257 -33.81111895946248, 151.034605739407 -33.81125248318003, 151.0348769777937 -33.81133890060175, 151.0351057199313 -33.811395059353, 151.0353964881524 -33.81143898052562, 151.0374841687758 -33.81172128302453, 151.039666229629 -33.81202554610971, 151.0405685415056 -33.81213551667935, 151.0417543293476 -33.81229217444965, 151.0428828687909 -33.81244036649778, 151.0447826271451 -33.81271990296861, 151.0452737228521 -33.81278762874626, 151.0459060536275 -33.81287681219601, 151.0470268817198 -33.81303497870891, 151.0492140555339 -33.8133479589734, 151.0505523940137 -33.81352867280583, 151.0522104182804 -33.81377434638785, 151.0523155273462 -33.81378993672774, 151.0534596571294 -33.81395631750573, 151.053659397882 -33.81398540270976, 151.0538738907842 -33.81402035524599, 151.0543496476084 -33.81409118232779, 151.0543877852678 -33.81409696584097, 151.0548731812806 -33.81417081040794, 151.0551686433674 -33.81421540213282, 151.0553144884827 -33.81423493196723, 151.0554987227144 -33.8142522825068, 151.0556989663812 -33.81426996832249, 151.0558743996146 -33.81427801494954, 151.0562186443779 -33.81429276709912, 151.0564077401136 -33.81427960751114, 151.0565525794004 -33.81426150260029, 151.0566919704501 -33.81423635689077, 151.0568092332756 -33.81419981179292, 151.0569577605998 -33.8141522025829, 151.0574685537792 -33.81395103690673, 151.0583221667984 -33.81361970027427, 151.0591247340273 -33.81330294815336, 151.0593396460246 -33.81321770419808, 151.0604071652128 -33.81280430873358, 151.0615717468398 -33.81232226548207, 151.0620167420792 -33.81213853416449, 151.062491576894 -33.81190895383656, 151.0625410301228 -33.81188020390869, 151.0626803373535 -33.81179604960082, 151.0633390711239 -33.81132674684213, 151.0637240519366 -33.81102172938566, 151.0637761873744 -33.81098627393524, 151.06452536188 -33.81029376109504, 151.0652074811603 -33.80970451330194, 151.0655285918709 -33.80940762629155, 151.0658337769654 -33.80913228077227, 151.0659690608827 -33.80901677814654, 151.0662117169795 -33.80884000380863, 151.0663470847157 -33.80876230356619, 151.0664634255318 -33.80869365577922, 151.0666191612927 -33.80862173904998, 151.0667203308641 -33.80857463275416), (151.0415670776307 -33.74115035216931, 151.0416409221976 -33.74090685788212, 151.0429833678099 -33.74067895393483, 151.0439810657446 -33.7405069572817, 151.0456561890939 -33.74021610524159, 151.0468227823775 -33.74004586878814, 151.0476529260679 -33.73990823793801, 151.0477093362762 -33.73989910166355), (151.0478674189701 -33.73987211193534, 151.0498515830893 -33.73953021410489, 151.0508541425279 -33.73936081584176, 151.0511727386675 -33.73931044060367, 151.0519217455351 -33.73918094019964, 151.0531436593798 -33.73900114837659, 151.0537176521091 -33.73891305457422, 151.0545541660458 -33.7387695563919, 151.0557113715979 -33.738590267483), (151.0521199775451 -33.92683943085697, 151.052460198995 -33.92732013300397, 151.0527170205082 -33.92781307272961, 151.0527347901429 -33.92784710325648, 151.0532666218993 -33.92881806292012, 151.0532926896182 -33.92886550449208, 151.0534035821972 -33.92908242814622, 151.0538505890934 -33.92984828263917, 151.0536360961912 -33.93064590454517, 151.0533719824222 -33.93199203819484, 151.0531139874425 -33.93395164333776, 151.0523892880941 -33.93871792875836, 151.0524026991392 -33.93891515494002), (151.0558996291432 -33.73856134991706, 151.0569936351454 -33.73835792112706, 151.0579658520945 -33.73824434633905, 151.0581144632377 -33.73821853007726, 151.059189945234 -33.73806623089661, 151.0599422210438 -33.73794276546286, 151.0607766395047 -33.73780580516501, 151.0615676397072 -33.73768644686382, 151.0616201942401 -33.73767831641774, 151.0626660881181 -33.73751604277231, 151.0629682557275 -33.73746910411452, 151.0631118377289 -33.73743783961569, 151.0631978360554 -33.73736667725774), (151.0869013553159 -33.80739404169216, 151.0867195518361 -33.80738976692155, 151.0863402707175 -33.80731927511584, 151.0859196668162 -33.80724710692954, 151.0857305710806 -33.80722942111385, 151.0855717340155 -33.80722196121999, 151.0850022675139 -33.80719497149178, 151.0848289297563 -33.80718675722667, 151.0847439372581 -33.80718265009412, 151.0840970219712 -33.80712246802935, 151.0836868954489 -33.8070648005355, 151.0835135576912 -33.80703127292281, 151.083032268811 -33.80691610557321, 151.0824498941785 -33.80678937119723, 151.0816868895326 -33.8066392513114, 151.0808813886377 -33.80649105926326, 151.0784290952262 -33.80596006569723, 151.0776999534691 -33.8059102771924, 151.0763686557881 -33.80581673515297, 151.075858533161 -33.80588035379805, 151.0746447497624 -33.8061770731704, 151.071540428103 -33.80693286937955, 151.0707091947653 -33.80713520852217, 151.0705300734945 -33.80720301811883, 151.0674787254632 -33.80843918119888, 151.0665548720954 -33.80883078371514, 151.065872249901 -33.80922682864008), (151.0667203308641 -33.80857463275416, 151.0666815226524 -33.80866641459388, 151.0663939395545 -33.80884796661664, 151.0660389659551 -33.80906421971852, 151.065872249901 -33.80922682864008), (151.0667203308641 -33.80857463275416, 151.0667633300274 -33.80856013206167, 151.0669123602658 -33.80850740989069, 151.0671119333803 -33.80844865274946, 151.0673538351059 -33.80838067551471, 151.06753354311 -33.80832652842022, 151.0676507221164 -33.80828755257044, 151.0677654703708 -33.80824312848364, 151.0678790451588 -33.80819602218781, 151.0691834369306 -33.80763443467518, 151.0697618720686 -33.80739437696828, 151.0700965614624 -33.80724526291084, 151.070264534802 -33.80717912969479, 151.0704954562344 -33.80708433036988, 151.0711598896989 -33.80691526738289, 151.071516623498 -33.80683790241658, 151.0716972535114 -33.80678635371208, 151.0735274420693 -33.80635510479379, 151.0758439486494 -33.80580206682242, 151.0763608606181 -33.80573853199635, 151.0767695622169 -33.80575202686046, 151.0774912440801 -33.80582561997034, 151.0777053178871 -33.80583836046316, 151.0778614727433 -33.80584766437568, 151.0783490480509 -33.80588337128318, 151.0787218751041 -33.80593450089256, 151.0791350191114 -33.80603030604584, 151.0795680282294 -33.80612870958907, 151.0799610556692 -33.80621093605922, 151.0802756284953 -33.80627966766525, 151.0803380736739 -33.80629240815807, 151.0808947996828 -33.80641754997245, 151.0817006358538 -33.80657362100953, 151.0824683343655 -33.80671812502024, 151.083304261569 -33.80689607282462, 151.0836511885413 -33.80697913748506, 151.0839575471023 -33.80702448358124, 151.0847497207713 -33.80709405337757, 151.0858538688763 -33.80715146941432, 151.0863606387422 -33.80724341889214, 151.0869013553159 -33.80739404169216), (151.0869013553159 -33.80739404169216, 151.0879777593214 -33.80761322846016, 151.0881892347385 -33.80764013436934, 151.088393920814 -33.80766561535498, 151.088557787021 -33.80768983905514, 151.0887677536955 -33.80772445631527, 151.0890339629403 -33.80778011215232, 151.0902199184204 -33.80806342047958, 151.0905635764505 -33.80816324894639, 151.090847387692 -33.8082673521838, 151.0910689213928 -33.80836022367097, 151.0913136729655 -33.80846600328903, 151.0916621924994 -33.808613860061, 151.0920894181042 -33.80880706292916, 151.0924730578125 -33.80902541150681, 151.0928622295768 -33.80924686138866), (151.1792924756076 -33.86813903509511, 151.178485298332 -33.8684273725643, 151.177883058589 -33.86862946024982, 151.1774136720113 -33.86880548021644, 151.1768866179397 -33.86900362840748, 151.1766970192899 -33.86904143079079, 151.1765568738688 -33.86905693731165, 151.1764576321353 -33.8690592004255, 151.1763649282861 -33.86903413853503, 151.1763189954568 -33.86900983101582, 151.176266692381 -33.8689754652128, 151.1762334162254 -33.86893816574369, 151.1761810293306 -33.86878645329625, 151.1761027423549 -33.86852116606082, 151.1760253773886 -33.86829410030384, 151.1759596632677 -33.8680893304093, 151.1758724914747 -33.8678986421121, 151.1757889239001 -33.86772262214546, 151.1756231298553 -33.86749647839784, 151.1753859219955 -33.86719070657008, 151.1752914579467 -33.86706967188825, 151.1751740274833 -33.86695685147154, 151.1750426830606 -33.866836571161, 151.1748387513563 -33.86667404605845, 151.1747335584715 -33.86659927948215, 151.1745625676468 -33.86648478268481, 151.1743801774337 -33.86638227200899, 151.1742245254917 -33.86631320512682, 151.1740555463238 -33.86625084376723, 151.1738034186764 -33.86617951377121, 151.173641480307 -33.86615118293849, 151.1735620198649 -33.86613726897923, 151.1732964811724 -33.86609946659593, 151.1730038689326 -33.86603081880892, 151.172875374357 -33.86598580798889, 151.1727332172791 -33.86590567699454, 151.1724770663182 -33.86571683271603, 151.1722948437431 -33.8655772740282, 151.1720992939422 -33.86543930790197, 151.1719428876289 -33.86532472728558, 151.1716661171861 -33.86512180140974, 151.1714293284215 -33.86494821219503, 151.1711308488495 -33.86472936070317, 151.1704572791105 -33.8642310565595, 151.1700419558082 -33.86393877959586, 151.1686684971542 -33.86305574609651, 151.1686317844183 -33.86303210912956, 151.1684701813251 -33.86293211302469, 151.1684241646767 -33.8629043689252, 151.1682002840429 -33.86276615134187, 151.1680705321818 -33.86268585270946, 151.167500562766 -33.86232903509136, 151.1672137340394 -33.8621735507875, 151.1668978201088 -33.86203483028998, 151.1664884479578 -33.86187163463521, 151.166119392761 -33.86173023192865, 151.1653044203155 -33.86135841070387, 151.1646233906827 -33.86104886701969, 151.1642796488335 -33.86090260280929, 151.1640819197376 -33.86080185233317, 151.163847897001 -33.86067327393849, 151.1635648401308 -33.86051502360655, 151.1634131276834 -33.86042190066229, 151.1631651909876 -33.86025912410266, 151.1625785415844 -33.85983147940277, 151.1620947381332 -33.8594269687556, 151.1614962702467 -33.85884417502794, 151.1611231917364 -33.8584254151454, 151.1607921903801 -33.85800347013964, 151.1603585945289 -33.85741941912653, 151.1599934788267 -33.85697366951575, 151.1597816681335 -33.8567263195531, 151.1593397741982 -33.85624804815802, 151.1592677736499 -33.85617018027753, 151.159003995157 -33.85588662049318, 151.1582647951162 -33.85509251898651, 151.1559157667518 -33.85259965716369, 151.1554504873067 -33.85211426115092, 151.1544956208971 -33.85110030232399, 151.1540839856323 -33.85067282526217, 151.1539203708823 -33.85048138259367, 151.1537781299855 -33.85030536262704, 151.1536601966078 -33.85012263713784, 151.1534643953497 -33.84981745204331, 151.1530499940568 -33.84913952371463, 151.1525926774196 -33.84839856347409, 151.1524156516246 -33.84813344387672, 151.152150029113 -33.8477636343087, 151.1518791260025 -33.84733372649492, 151.1514617910435 -33.8466438120447, 151.1513507308264 -33.84646041600327, 151.15109592097 -33.84601047544092, 151.1509431188751 -33.84576765170598, 151.1508036440063 -33.84554712383348, 151.1506480758834 -33.8453221535523, 151.1504495924162 -33.84506373947747, 151.1502537911581 -33.84481421021999, 151.1499915214078 -33.8445207597899, 151.1497509607867 -33.84426016642024, 151.1497476918445 -33.84425656220185, 151.1489282769903 -33.84340772686747, 151.1467087490299 -33.84099172709673, 151.1465183960088 -33.84078670574513, 151.1458370310999 -33.84014968110392, 151.1451130861228 -33.83952372057493, 151.1448933964406 -33.83936890682332, 151.1446722818349 -33.83923965787639, 151.1444239260438 -33.83911786882328, 151.1441694514635 -33.83902147693678, 151.1437691317679 -33.83890907561522, 151.1435058561892 -33.83885165957849, 151.1431958934099 -33.83881955688931, 151.1429253255754 -33.83880270926394, 151.1425123492061 -33.83881402483323, 151.1417207622704 -33.83893908282857, 151.1410042771871 -33.83909037618088, 151.1402849422568 -33.8392996723031, 151.1396082712135 -33.83953344358261, 151.1390428280255 -33.83971390595795, 151.1389602662792 -33.83974030895294, 151.1381659971345 -33.83994214518137, 151.1377284617888 -33.84002990370757, 151.1370906827764 -33.84015781155003, 151.1366906983569 -33.84018555564953, 151.1363646423235 -33.84015722481678, 151.1363180389418 -33.84015311768422, 151.1360399273945 -33.84007751291761, 151.1357594689143 -33.83993954679138, 151.135201317982 -33.83960837779699, 151.13448298888 -33.83911040892945, 151.1324811389451 -33.83772823309613, 151.1313376797142 -33.83732707521025, 151.130840632856 -33.83714686429204, 151.1303687317073 -33.83697570582922, 151.1298407556264 -33.83676925955405, 151.1293330637512 -33.83656700423049, 151.1289196682867 -33.83642828373296, 151.1287338414933 -33.83636164760273, 151.1285923549677 -33.8362892279593, 151.1284732481237 -33.83621253354528, 151.1283852381403 -33.83613114526545, 151.1282951326812 -33.83604405729147, 151.1282214557523 -33.83596090881201, 151.1281635368014 -33.83586325964004, 151.12812556678 -33.83579260019627, 151.1280685698384 -33.83567466681862, 151.1279889417583 -33.8354965513762, 151.1278103234017 -33.8350966507758, 151.1275425215953 -33.83453581763445, 151.127459792211 -33.83434890119366, 151.1272176390283 -33.83380147909742, 151.127119151666 -33.8335764249972, 151.1270515935264 -33.83340660763892, 151.1269573809348 -33.83311265429461, 151.1269123701147 -33.83288592381376, 151.1268903257093 -33.83269045783177, 151.1268410401187 -33.8320825183946, 151.1267814447871 -33.83126444464486, 151.126775828912 -33.83120057454266, 151.1267012299737 -33.83016784025267, 151.126656973525 -33.82976475452904, 151.1266242841026 -33.82941615117604, 151.126596288546 -33.8291463377129, 151.1265818716726 -33.8290082039486, 151.1264581547817 -33.82815274691072, 151.1264181731036 -33.82789181826493, 151.1263565661152 -33.82738320438037, 151.1262427398702 -33.82665540372781, 151.1261486949165 -33.82619154920619, 151.1260696535696 -33.82580137161344, 151.1260199488838 -33.82559132111993, 151.1259753571589 -33.82543340606412, 151.1259098944951 -33.8253086833449, 151.1258174421031 -33.82517432143703, 151.1256884446133 -33.82503350546372, 151.1255421804029 -33.8249103753061, 151.1253842653471 -33.82479043027168, 151.1251185590165 -33.82461600286666, 151.1247726378725 -33.8244275776833, 151.1245312390611 -33.82428014000649, 151.1240537220374 -33.82400823106754, 151.123632866679 -33.82379776147886, 151.12359037043 -33.82377605234964, 151.1234877759351 -33.82372358163576, 151.1233125941587 -33.82361310815193, 151.1231411842389 -33.82349157055592, 151.1224037443977 -33.82306292002764, 151.1220725754033 -33.82286611294111, 151.1217826453725 -33.82268271689969, 151.1216277478018 -33.8225686391975, 151.121271516917 -33.82230796200881, 151.1210328841336 -33.8221207102919, 151.1207420320935 -33.82188727428851, 151.1204417923219 -33.82166800370149, 151.1204285489148 -33.82165836451284, 151.1196351179604 -33.82107582224228, 151.1187454627576 -33.82039160748624, 151.1185683531435 -33.82026202326318, 151.1184123659255 -33.82016605047184, 151.1181990464897 -33.82005616372123, 151.1179656943053 -33.81994216983807, 151.1177326773971 -33.81984133554289, 151.1173687351613 -33.81969414932317, 151.1170741950838 -33.81957747323099, 151.116726094645 -33.81944151876151, 151.1165061535057 -33.81935501752076, 151.115772653159 -33.8190239323454, 151.1155300808812 -33.81889116299914, 151.1143317201845 -33.81823326741906, 151.113263362806 -33.81759096217885, 151.1127281782884 -33.81726658252606, 151.1120385991143 -33.81685243269023, 151.1118243576691 -33.81675687899406, 151.1115797737345 -33.81666962338204, 151.1113724892691 -33.81661497337333, 151.1111522966727 -33.8165905820351, 151.1106559203668 -33.81658857037834, 151.1093995569001 -33.81660835166983, 151.1089353671023 -33.81661564392559, 151.1087124922969 -33.81661740412525, 151.108517193953 -33.81663123426549, 151.1082217318661 -33.81665210520441, 151.1079806683308 -33.8166650133353, 151.1077535187549 -33.81666015183146, 151.1074212601131 -33.81661773940138, 151.1072050908302 -33.81657901500873, 151.1070150730853 -33.8165546236705, 151.1068080400769 -33.81654741523377, 151.1066144181136 -33.816561245374, 151.1064406612608 -33.81659200695864, 151.1062910442892 -33.81662947406582, 151.1059084942283 -33.81676710491595, 151.1055687756927 -33.81688076352299, 151.1054078431517 -33.8169114412886, 151.1052716372252 -33.8169146264118, 151.1051423882782 -33.81689677295805, 151.1050060985327 -33.81684731972931, 151.1048575712084 -33.81676710491595, 151.1046397255449 -33.81659862866215, 151.1043167708157 -33.81633216796027, 151.1041121685592 -33.81619671640499, 151.1039223184523 -33.816080543227, 151.103823747271 -33.81603821461599, 151.1037106753972 -33.81598976721563, 151.103512275749 -33.81592380163767, 151.1032529396649 -33.81587334258055, 151.1031209246899 -33.81584911888038, 151.1029871495152 -33.81583428291177, 151.1027374526197 -33.81580083911811, 151.1026199383372 -33.81578306948336, 151.1024713271939 -33.81576060598286, 151.1016586178623 -33.81565440726965, 151.1004653701265 -33.81545835455444, 151.1000639607834 -33.81538124104523, 151.0995966696815 -33.8152948236235, 151.0994056461082 -33.81526188274403, 151.0992904787586 -33.81523782668194, 151.099191069387 -33.81520664600214, 151.0990304721222 -33.8151381658532, 151.0988184937909 -33.81503255387321, 151.0984810383692 -33.81485125330758, 151.0981384699865 -33.81466785726613, 151.0976207198274 -33.81438815315724, 151.0969957651268 -33.81404944044999, 151.096858972467 -33.81396914181758, 151.096708433486 -33.81388020982493, 151.0963322536716 -33.81361391676108, 151.0961020866105 -33.81341182907559, 151.0960409825363 -33.81335332339142, 151.0956844163753 -33.8128791591289, 151.095522394187 -33.81266877335926, 151.0953216476059 -33.81238865015519, 151.0952008643812 -33.81221640204498, 151.0950229165769 -33.81196561550203, 151.0947033984279 -33.81156630163483, 151.0945590620552 -33.81136932691027, 151.0944219341193 -33.81125424337969, 151.0941805353079 -33.81102851872722, 151.093897981352 -33.81074101944839, 151.0938027629319 -33.8105802545455, 151.0936300957265 -33.81025411469304, 151.0934799758407 -33.80989997928395, 151.093409651673 -33.80978657213402, 151.0933147685291 -33.80962907617337, 151.0932433547141 -33.80956352969056, 151.0931614635201 -33.80949865375999, 151.0929237527461 -33.80933897850454, 151.0928622295768 -33.80924686138866), (151.0928622295768 -33.80924686138866, 151.0929601302059 -33.8092620326334, 151.093245785466 -33.809456408968, 151.0933222284229 -33.80950963405314, 151.0933949833424 -33.80957216305083, 151.0934344621065 -33.80962530431694, 151.0934762878033 -33.80969160517105, 151.0935265792223 -33.80981783663285, 151.0936950554761 -33.81016652380487, 151.0939870809827 -33.81070992258759, 151.0941066907409 -33.81085660589315, 151.0943727323477 -33.81111745071991, 151.0946345830029 -33.81136555505383, 151.0948182305013 -33.81158457418377, 151.095263644836 -33.81215496269471, 151.0954276786811 -33.81238806342196, 151.0956127511032 -33.81263767649847, 151.0957378929175 -33.81278117468082, 151.0961108876088 -33.81325936225687, 151.0962925234505 -33.81345315185823, 151.0963896697083 -33.81355935057144, 151.0965842975 -33.81370326784892, 151.0968250257592 -33.81385020261155, 151.0972176341038 -33.81407156867436, 151.0975870245767 -33.81427021977959, 151.0981964727564 -33.81459971239335, 151.098657645069 -33.81484706235599, 151.0991128662303 -33.81507781615036, 151.099243875377 -33.8151310412355, 151.0993749683426 -33.81516775397139, 151.0994961706625 -33.81519063656708, 151.0999776271808 -33.81527973619779, 151.1002779507715 -33.81533447002553, 151.1007298191716 -33.81541443338179, 151.101024023973 -33.8154585221925, 151.1015772295824 -33.81552666706528, 151.1020215542696 -33.81558123325496, 151.1022578401201 -33.81561023463993, 151.1024459300273 -33.81563437452107, 151.10263527722 -33.81565097068935, 151.1027934437329 -33.81565591601222, 151.1028239538605 -33.81565683802157, 151.1030097806538 -33.81567217690439, 151.1030849663253 -33.81567921770306, 151.1032520176555 -33.81568801870139, 151.1033729685183 -33.81570763235482, 151.1035031394746 -33.81573814248236, 151.1035529279795 -33.81574954187067, 151.1036208213952 -33.81577116718086, 151.103705143341 -33.81580544916486, 151.1038247530994 -33.81585708168839, 151.1039234919187 -33.815916760839, 151.104069337034 -33.81602698286571, 151.1042770405946 -33.81619378273887, 151.1048189306348 -33.81661916432492, 151.1049428989827 -33.81669896004314, 151.1050855589747 -33.81676584763048, 151.1052115389794 -33.81680457202313, 151.1053643410743 -33.81681127754566, 151.1055039835812 -33.81679702831028, 151.10563306489 -33.81676576381145, 151.1058529222103 -33.8166922545206, 151.106081999624 -33.81660675910823, 151.1063542438391 -33.81652428118102, 151.1065259890351 -33.81648773608315, 151.1066949682031 -33.81646770333458, 151.1069147417043 -33.81646585931588, 151.107085816348 -33.8164770072471, 151.1072540411448 -33.81650181768049, 151.1076039856022 -33.816561245374, 151.1078388465291 -33.81658530143611, 151.108044538433 -33.81658932474962, 151.1083713488378 -33.81656392758302, 151.1086920404532 -33.81653844659738, 151.1090551444987 -33.81651581545881, 151.1094230262289 -33.81650642772723, 151.1099152954023 -33.81650148240436, 151.1107954790546 -33.81650416461338, 151.1111318448289 -33.8165113730501, 151.1113288195535 -33.81652612519972, 151.1115212680504 -33.81656560396365, 151.1118816898868 -33.81668580045516, 151.1120565363871 -33.81676207577405, 151.1127927189428 -33.81720363443321, 151.1141896469257 -33.81802145672586, 151.1144058162085 -33.81815472898631, 151.1155781930054 -33.81882243139313, 151.1158592382188 -33.81897112635542, 151.1165658326563 -33.81930061896918, 151.1167638970283 -33.81936826092777, 151.1173822300254 -33.81961267722431, 151.1175358703106 -33.81967554149813, 151.1176864931106 -33.8197373161245, 151.1180041672409 -33.81987653953621, 151.1182968632997 -33.82002054063275, 151.1185047344985 -33.82013084647849, 151.1186247633518 -33.82021332440573, 151.1190340516839 -33.82050124277976, 151.1197128182028 -33.82100600098885, 151.1218353675434 -33.8226222833778, 151.1223356833439 -33.82294004132712, 151.1228638270629 -33.8232596432951, 151.1237075494363 -33.82374973317366, 151.1247690336542 -33.82432414499814, 151.1251584568756 -33.82457224933208, 151.1254266777772 -33.82474240196652, 151.1255844251949 -33.82486553212414, 151.1257367243755 -33.82499955875588, 151.1258694937218 -33.82514557150915, 151.1259668914367 -33.82527993341702, 151.1260433343937 -33.82541840245744, 151.1260988225926 -33.82558126283612, 151.1263152433326 -33.8266662163829, 151.1264104617526 -33.82729527821606, 151.126477265521 -33.82776055766124, 151.1265261320165 -33.82813807858017, 151.126606849744 -33.82865951677658, 151.12667817974 -33.82916016785313, 151.1268038244686 -33.83017177974716, 151.1268821952632 -33.83111616877772, 151.1268889846048 -33.83119772469558, 151.126929133921 -33.83209014592648, 151.126957967668 -33.832471857797, 151.1269900703571 -33.83273002041474, 151.1270149646095 -33.83287435678739, 151.1270555330209 -33.83309153189861, 151.1271748913221 -33.8335009040496, 151.1272994464032 -33.83379083408039, 151.127792721405 -33.83488425334937, 151.1279706692094 -33.8352860817875, 151.1281121557349 -33.83561155108774, 151.1281926220054 -33.83576477227774, 151.1282447574432 -33.83584297543436, 151.1282623594398 -33.83586946224838, 151.1283508723373 -33.83597423603806, 151.1284153291728 -33.83603517247411, 151.1284497787948 -33.83606752662038, 151.1285424826439 -33.83614614887213, 151.1286653613444 -33.83622535785713, 151.1288332508649 -33.83630557267051, 151.1290046607849 -33.83637313081007, 151.1292485741672 -33.83646516410693, 151.1295222433058 -33.83656935116338, 151.1298462876825 -33.83670270724286, 151.1303949670643 -33.83689532337777, 151.1309149803371 -33.83705625591872, 151.1314153799566 -33.83722221760155, 151.1325632815962 -33.8376449169786, 151.1330188380337 -33.83794146871286, 151.1331326642788 -33.83803551366648, 151.133315389768 -33.83814808262611, 151.1337044777133 -33.83840155137807, 151.1342866847077 -33.8387960037414, 151.1350882461082 -33.83936706280463, 151.135708255486 -33.83979554569484, 151.1360000295354 -33.83994072025782, 151.1363748682453 -33.84003342410691, 151.1366226373032 -33.84005756398807, 151.1370510363743 -33.84004834389458, 151.1379093432593 -33.83985975107316, 151.1384409235586 -33.83973997367681, 151.1389171832969 -33.8396131554818, 151.1395638471267 -33.83943193873517, 151.1398910766266 -33.83933856433384, 151.1402835173332 -33.83921711055686, 151.1405002733493 -33.83915382718789, 151.1407183704698 -33.83909280693279, 151.1409369705046 -33.83903446888669, 151.1411568278248 -33.83897881304964, 151.1413775233354 -33.83892650997382, 151.1415899207618 -33.83887940367799, 151.1418030725595 -33.83883497959118, 151.1420172301856 -33.83879323771336, 151.1421717924802 -33.83876557743289, 151.1422526778458 -33.83875191493073, 151.1424478923707 -33.83871930932739, 151.1426418496101 -33.83869047558045, 151.142823988366 -33.83866650333738, 151.1429345456689 -33.83865191882586, 151.143830068204 -33.83849693743619, 151.1439824512037 -33.8384723784599, 151.1441322358134 -33.83845251334935, 151.1442906537833 -33.83843574954301, 151.1444490717533 -33.83843164241046, 151.1446020414863 -33.83844077868491, 151.1446582840566 -33.83844832239777, 151.1448640597795 -33.83848076036307, 151.1450625432466 -33.83855225799712, 151.1452472803925 -33.83865158354973, 151.1454134935325 -33.8388015357975, 151.1455661279893 -33.83895660100623, 151.1456572392768 -33.83910345194982, 151.1457149905896 -33.83923152743031, 151.1458799464441 -33.83961927427112, 151.1459523660875 -33.83977744078399, 151.1460126319714 -33.83989545798067, 151.1460797710158 -33.8400059314645, 151.1460819503106 -33.84000894894965, 151.1462111992575 -33.84018647765888, 151.1466269416549 -33.84072887061322, 151.1468079907635 -33.84092710262328, 151.1490302847519 -33.84334310239402, 151.149844251369 -33.84419302737581, 151.1501974647688 -33.84457582889374, 151.1504308169531 -33.8448520126033, 151.1506587209004 -33.84513272254059, 151.1508465593506 -33.84539566284315, 151.1510396783996 -33.8456718465527, 151.1516092287203 -33.8465954484634, 151.1517536489119 -33.84682963883807, 151.1519754340699 -33.84718930630325, 151.1530137842351 -33.84889083264747, 151.1536816542799 -33.84999791441862, 151.1538264935668 -33.85021173676859, 151.1539954727348 -33.85042337982372, 151.1541644519027 -33.85060602149386, 151.1545855587182 -33.851044814125, 151.1560226360173 -33.85257643929187, 151.158361941374 -33.85504314957683, 151.1588793562569 -33.85560607819397, 151.1593478208252 -33.85611561408788, 151.1594147922316 -33.85619993603379, 151.1601040361296 -33.85691826513575, 151.1604943813604 -33.85732361397325, 151.160572752155 -33.85739972165405, 151.1639141978551 -33.860484513479, 151.1641861906131 -33.86069657562931, 151.1642923893263 -33.86077410823363, 151.1644850892803 -33.86087267941497, 151.1648533901057 -33.86104065275458, 151.1664033716406 -33.86169594994467, 151.1668892705675 -33.86191312505591, 151.167294451767 -33.86208344532838, 151.1673335952548 -33.86210372953406, 151.1674709746478 -33.86217480807298, 151.1680320592462 -33.86253606809976, 151.1681498249858 -33.86260915829544, 151.1684684211254 -33.86280680357225, 151.1685101630032 -33.86283270365305, 151.1686214746774 -33.86290185435425, 151.1686971632631 -33.86294862537395, 151.1701421195511 -33.86385504438314, 151.1703487334643 -33.86399024448133, 151.1706840934103 -33.86423742680591, 151.1711484508461 -33.86458301267376, 151.1714997364081 -33.8648427678531, 151.1721682770053 -33.86533847360678, 151.1727123463402 -33.86575002505262, 151.1729058844845 -33.86586712023995, 151.1730504723143 -33.86593065506599, 151.1732482852291 -33.86598631090308, 151.173572748701 -33.86602595730508, 151.1737910972787 -33.86605504250909, 151.1739863956226 -33.86609946659593, 151.1741610744847 -33.86615302695719, 151.1744398565843 -33.86627733058125, 151.1747261823967 -33.86644237025473, 151.1750077305243 -33.86664286537865, 151.1752357182907 -33.86682760252461, 151.1753698287414 -33.86696137769925, 151.1755227984743 -33.86714837795904, 151.1757399735856 -33.86745574234843, 151.1758580746013 -33.86761600433709, 151.1759465874988 -33.86778758189507, 151.1760216893512 -33.86795463322531, 151.1761932669092 -33.86850465371157, 151.1762700451422 -33.86876893511862, 151.1762989627082 -33.86883121265919), (151.1564563995065 -33.91497275143908, 151.1564619315626 -33.91491826906844, 151.1564727442177 -33.91486487634523, 151.1566095368775 -33.91450646616553, 151.1566470878037 -33.91442180894347, 151.1567087786111 -33.91433958247333, 151.1567892448815 -33.91428836904495, 151.1568804399881 -33.9142705155912, 151.1569581402305 -33.91427940040856, 151.1570279614839 -33.91429725386232, 151.1574973480616 -33.91455541648006, 151.1576260940944 -33.91461107231714, 151.1577655689632 -33.91464895851948, 151.1578781379228 -33.91464677922465, 151.1579720990574 -33.91462448336222, 151.1580874340451 -33.9145732699338, 151.158261777631 -33.91444863103362, 151.1584629433072 -33.91427277870505, 151.1587827967323 -33.91395250618478, 151.1590003909386 -33.91370658114568, 151.159518895469 -33.91304600335658, 151.1597772257248 -33.91273134671144, 151.1599951552073 -33.91246781967567, 151.1601044552247 -33.91233773253842, 151.1602369731139 -33.91216263458111, 151.1604053655486 -33.91195551775371, 151.1604191956889 -33.91193867012834, 151.1608523724449 -33.91139996521136, 151.161866834186 -33.91012683793835, 151.1622009368465 -33.9097160408638, 151.1622803972885 -33.90962157681503, 151.1626813875364 -33.90911455549207, 151.1633673624921 -33.90825113564617, 151.1640658264835 -33.90739693589376, 151.1645232269397 -33.90682411663087, 151.1646075488857 -33.90672470725923, 151.1650528794012 -33.90619664735931, 151.1652007361732 -33.90604501873091, 151.1654203420364 -33.9059161050601, 151.1657218390935 -33.90583287276158, 151.1658845318341 -33.90580026715823, 151.1660392617667 -33.9057640573365, 151.1670771928367 -33.90562122970644, 151.1672873271493 -33.90558535516087, 151.1673767620561 -33.90556951336387, 151.1674541270224 -33.90555442593816, 151.1675103695927 -33.90553405791343, 151.1675525305657 -33.90551234878421, 151.1675995530425 -33.90548636488438, 151.1677152233062 -33.9055305375141, 151.1678112799166 -33.90554436765436, 151.1678727192669 -33.90554352946404), (151.1677565460889 -33.90526265188868, 151.1677360942452 -33.90539357721624, 151.1677456496148 -33.90543456472278, 151.1677639221637 -33.90546264409841, 151.167817650163 -33.90551486335517, 151.1678727192669 -33.90554352946404), (151.1678727192669 -33.90554352946404, 151.1683614680409 -33.90553757831279, 151.1693894246461 -33.9055131031555, 151.1701565364246 -33.90558753445569, 151.1702065763865 -33.90559197686437, 151.1703314667438 -33.90560656137589, 151.1704134417569 -33.90561770930711, 151.1704858614003 -33.90562726467672, 151.1707491369789 -33.905654422043, 151.1712335271633 -33.9057423482073), (151.1714564857878 -33.90579582474956, 151.1719446478286 -33.90590931571853, 151.1726259289185 -33.90607854634359, 151.1729263363283 -33.90614970870155, 151.1730148492258 -33.9061675621553, 151.1730658950161 -33.90615641422409, 151.1731085589032 -33.90614442810255, 151.1731602752458 -33.906129843591, 151.173147450934 -33.90608122855261, 151.1731619516265 -33.90602641090587, 151.17322137932 -33.90594762101603, 151.1732743529481 -33.90588391855189), (151.1732743529481 -33.90588391855189, 151.1732659710448 -33.90596840813589, 151.1732336168986 -33.90607888161972, 151.1732563318562 -33.90615951552826), (151.173548944096 -33.90547957554281, 151.1734669690829 -33.90550907984198, 151.1734260653954 -33.90557311758224, 151.1733387259644 -33.90572323746807, 151.1732743529481 -33.90588391855189), (151.1762989627082 -33.86883121265919, 151.176343470614 -33.86892970002148, 151.1763649282861 -33.86903413853503, 151.176359144773 -33.86911225787262, 151.1763401178528 -33.86916254929164, 151.1763331608731 -33.86918115711668, 151.1762785946835 -33.86926573051971, 151.1761975416798 -33.8693164410339), (151.1762989627082 -33.86883121265919, 151.1764095200111 -33.86891645661446, 151.176493758138 -33.86894353016172, 151.1765912396718 -33.86895702502582, 151.1766948399951 -33.86895015186522, 151.1768985202422 -33.86891218184385, 151.1770757974943 -33.86884990430326, 151.1774242332093 -33.86872501394598, 151.1788374220843 -33.86821321493821, 151.1792504822727 -33.86804884581699), (151.178704652738 -33.87578903048353, 151.1787493282819 -33.87576556115465, 151.1795164400604 -33.875330708018, 151.1797831522194 -33.8751664227158, 151.1802425643323 -33.87488236001724, 151.1805846298008 -33.87466954349568, 151.1806676944612 -33.87461900061953, 151.180997187075 -33.87441842167661, 151.1811843549729 -33.87430451161248, 151.1813558487118 -33.87420015691796), (151.2594758568412 -33.8856940092827, 151.2595098873681 -33.88545361629969, 151.2595698179758 -33.88500225081378, 151.2595818879163 -33.8848810484939, 151.2595794571644 -33.88477510123778, 151.2595774455077 -33.88468717507351, 151.2595245556986 -33.88443471214993, 151.2594214582896 -33.88411385289643, 151.2593733461654 -33.88401821538122, 151.2592695782041 -33.88382258176114, 151.2591866811817 -33.88371303028668, 151.2590912951236 -33.88361026815377, 151.2590339629059 -33.8835485773464, 151.2588432746087 -33.88337574250298, 151.2587598746721 -33.88331262677207, 151.2585489859883 -33.88322855628326, 151.2584813440297 -33.88320081218373, 151.2584299629632 -33.88315831593465, 151.2583194894794 -33.88303317412027, 151.2582487462166 -33.88289001121407, 151.2582299707535 -33.88280200123074, 151.258247069836 -33.88267736233055, 151.2582689466033 -33.88252372204539, 151.2582663482133 -33.88239673621231, 151.2582341617051 -33.8822921300607, 151.2582040706727 -33.88223387583363, 151.2580794317725 -33.88206950671241, 151.2579835428002 -33.88196381091339, 151.2576550560148 -33.88160137742017, 151.2571988290251 -33.88109586483978, 151.2570475356728 -33.88092562838631, 151.2568903749883 -33.88077735251917, 151.2566945737302 -33.88062371223401, 151.2565808313041 -33.88054441942998, 151.2564585393368 -33.88048406972715, 151.2561529351471 -33.88033747024065, 151.2558792660085 -33.88022330871942, 151.2555930240151 -33.88013957350671, 151.2555051816698 -33.88011828347265, 151.2554187642481 -33.88008090018448, 151.2553272338655 -33.8800249090713, 151.2552408164437 -33.87995793766493, 151.2549680693145 -33.87970648056972, 151.2547831645304 -33.87952836512729, 151.2546026183362 -33.87936014033058, 151.254552159279 -33.87931160911123, 151.2544844335014 -33.87925033739904, 151.2543256802553 -33.87909275761936, 151.2541384285384 -33.87888966410549, 151.2540339900248 -33.87879167965738, 151.2539204990559 -33.87870417258824, 151.2538611551814 -33.87865052840795, 151.2538077624581 -33.8786010751792, 151.2537263741784 -33.87852387785099, 151.2536309881203 -33.87843494585832, 151.2533853983573 -33.87820444352104, 151.2532436603746 -33.87805457509231, 151.252983234643 -33.87771250962379, 151.2529252318731 -33.87741025819534, 151.2529089709809 -33.8772865413045, 151.2528922909936 -33.8771592201953, 151.2528889382323 -33.87705813444302, 151.2528976554116 -33.87694816387341, 151.2529526406964 -33.87675470954814, 151.2530164269795 -33.87651456802223, 151.253080967634 -33.87627501322953, 151.2531354500047 -33.87605825721346, 151.2531146628847 -33.87595557889958, 151.2531067838958 -33.87592674515267, 151.25305707921 -33.87581392473595, 151.2528717553308 -33.8753719469816, 151.2528019340774 -33.87520238108041, 151.2527625391324 -33.875127363047, 151.2527606951137 -33.87502275689542, 151.2527657242557 -33.8748185737341, 151.2527456915071 -33.87427031344751, 151.2527418358316 -33.87393554023478, 151.2527351303091 -33.87369581780402, 151.252721719264 -33.87345416753552, 151.2527289277007 -33.87343287750144, 151.2527199590643 -33.8733043829258, 151.2527079729428 -33.87317647508337, 151.2526730204066 -33.8731213221605, 151.2526326196332 -33.87307740098785, 151.2525744492252 -33.873030210873, 151.2525398319651 -33.87301688364695, 151.2524952402402 -33.87299726999353, 151.2524341361661 -33.8729884689952, 151.252346629097 -33.87298704407166, 151.2520440423924 -33.87299131884228, 151.2517323194134 -33.87299592888903, 151.2516529427903 -33.87299425250839, 151.251538529812 -33.87299550979387, 151.2514476699816 -33.87300095803093, 151.2514068501131 -33.87300104184996, 151.2513538764851 -33.87300640626799, 151.2512488512384 -33.87301713510404, 151.2508983200476 -33.87308125666331, 151.2507150916443 -33.87312065160825, 151.2506191188529 -33.87314177400424, 151.2505933864102 -33.8731474736984, 151.2504876906112 -33.87315660997285, 151.2503001874372 -33.87318435407235, 151.2501346448495 -33.87318753919555, 151.2499783223553 -33.87316365077152, 151.2498373387439 -33.8731229147221, 151.2494469935131 -33.87302861831139, 151.2492505217027 -33.87300825028669, 151.2490390462857 -33.87297991945397, 151.2488846516292 -33.872958964696, 151.2487311789821 -33.87293985395678, 151.2486922869514 -33.87293624973842, 151.2486254831831 -33.8729285383875, 151.248572341917 -33.87292770019718, 151.248416522337 -33.87290884091504, 151.2483020255397 -33.87289107128031, 151.248202699987 -33.87280733606761), (151.2571188656688 -33.88893068119316, 151.2572746852488 -33.88881551384355, 151.2578318303528 -33.88846213280576, 151.2581564614626 -33.8882322172017, 151.2585552724157 -33.88791412397626, 151.2587110919957 -33.88774472571313, 151.2587967550461 -33.88760508320627, 151.2588257564311 -33.88731867357481, 151.2588484713887 -33.8871847307621, 151.2588769698594 -33.88705615236742, 151.2589097431009 -33.88697845212502, 151.2589385768478 -33.88691122926156, 151.2592045346355 -33.88641904390721, 151.2593145890241 -33.88622936143838, 151.2593159301287 -33.88619994095824, 151.2594076281494 -33.8860714463826, 151.2594465201801 -33.8860352365609), (151.2595250586128 -33.88606566286941, 151.2595317641353 -33.88611075750848, 151.2594139983958 -33.88620564065241, 151.259277289555 -33.88642700671522, 151.2592425884759 -33.88649657651158, 151.2591684924519 -33.88664711549256, 151.2590352201914 -33.88693746461848, 151.2589798996304 -33.88714910767362, 151.25888644141 -33.88761086671946, 151.2588396703903 -33.88772720753551, 151.2585156260137 -33.88804798296996, 151.2580571359101 -33.88841921746152, 151.2575662916602 -33.88874871007525, 151.2574998231681 -33.88882004007127, 151.25743905437 -33.88887410334672), (151.2594465201801 -33.8860352365609, 151.2594121543771 -33.88594806476788, 151.2594183569855 -33.88588385938959, 151.2594274932599 -33.88583247832312, 151.2594434188759 -33.88574455215883, 151.2594758568412 -33.8856940092827), (151.2595250586128 -33.88606566286941, 151.2594465201801 -33.8860352365609), (151.2594758568412 -33.8856940092827, 151.2595046905881 -33.8857114436413, 151.2594980688846 -33.88577950469508, 151.2594854960299 -33.88588226682799, 151.2594829814589 -33.88593674919861, 151.259491028086 -33.88598645388441, 151.2595261482602 -33.88603582329412, 151.2595250586128 -33.88606566286941))



```python
colours = ["red", "blue", "green", "yellow", "purple", "black", "orange", "navy", "cyan"]

ax = plt.gca()
for l, line in enumerate(val):
    gpd.GeoDataFrame(geometry = [val[l]]).plot(color = colours[l%9], ax = ax, linewidth = 4)

ax.get_xaxis().get_major_formatter().set_useOffset(False)
# ax.set_xlabel("longitude")
# ax.set_ylabel("latiude")
# ax.set_title("Victoria Road")
ax.figure.savefig("victoria_linemerge.png")
```


![png](cleaning_osm_files/cleaning_osm_67_0.png)



```python
vic_parade_reduce = victoria_road.groupby("name").apply(lambda x: reduce_street_2(x))
```


```python
vic_parade_reduce
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
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
    <tr>
      <th>name</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th rowspan="8" valign="top">victoria road</th>
      <th>0</th>
      <td>[[[[[[[None], [None], [None], [None]], [[None]...</td>
      <td>[[[[[[[0], [0], [0], [0]], [[1], [1], [1], [0]...</td>
      <td>[[[[[[['highway'], ['highway'], ['highway'], [...</td>
      <td>[LINESTRING (151.0016784386182 -33.80819468108...</td>
      <td>[[[[[[[885.0], [45130.0], [80376.0], [86655.0]...</td>
      <td>[victoria road]</td>
      <td>[[[[[[[0], [1], [1], [1]], [[1], [1], [1], [1]...</td>
      <td>[[[[[[[4085524.0], [164972893.0], [225871455.0...</td>
      <td>[[[[[[[None], [None], [None], [None]], [['A40'...</td>
      <td>[[[[[[[None], [None], [None], [None]], [[None]...</td>
      <td>[[[[[[[0], [0], [0], [0]], [[0], [0], [0], [0]...</td>
      <td>[[[[[[['secondary'], ['secondary'], ['secondar...</td>
      <td>[[[[[[[5.0], [5.0], [5.0], [5.0]], [[28.0], [2...</td>
    </tr>
    <tr>
      <th>1</th>
      <td>[[[[[[[None], [None], [None], [None], [None], ...</td>
      <td>[[[[[[[0], [0], [0], [0], [0], [0], [0], [0], ...</td>
      <td>[[[[[[['highway'], ['highway'], ['highway'], [...</td>
      <td>[LINESTRING (151.173548944096 -33.905479575542...</td>
      <td>[[[[[[[1439.0], [7053.0], [21624.0], [47988.0]...</td>
      <td>[victoria road]</td>
      <td>[[[[[[[0], [1], [1], [0], [0], [0], [0], [1], ...</td>
      <td>[[[[[[[4574898.0], [13858769.0], [26920752.0],...</td>
      <td>[[[[[[[None], [None], [None], [None], [None], ...</td>
      <td>[[[[[[[None], [None], [None], [None], [None], ...</td>
      <td>[[[[[[[0], [0], [0], [0], [0], [0], [0], [0], ...</td>
      <td>[[[[[[['residential'], ['residential'], ['resi...</td>
      <td>[[[[[[[3.0], [3.0], [3.0], [5.0], [3.0], [3.0]...</td>
    </tr>
    <tr>
      <th>2</th>
      <td>[[[[[[[None], [None], [None]], [[None], [None]...</td>
      <td>[[[[[[[0], [0], [1]], [[0], [0], [1]]], [[[0],...</td>
      <td>[[[[[[['highway'], ['highway'], ['highway']], ...</td>
      <td>[LINESTRING (151.0521199775451 -33.92683943085...</td>
      <td>[[[[[[[15208.0], [15208.0], [15247.0]], [[1520...</td>
      <td>[victoria road]</td>
      <td>[[[[[[[0], [0], [0]], [[0], [0], [0]]], [[[0],...</td>
      <td>[[[[[[[23329964.0], [23329964.0], [23330283.0]...</td>
      <td>[[[[[[[None], [None], [None]], [[None], [None]...</td>
      <td>[[[[[[[None], [None], [None]], [[None], [None]...</td>
      <td>[[[[[[[0], [0], [0]], [[0], [0], [0]]], [[[0],...</td>
      <td>[[[[[[['residential'], ['residential'], ['resi...</td>
      <td>[[[[[[[3.0], [3.0], [23.0]], [[3.0], [3.0], [2...</td>
    </tr>
    <tr>
      <th>3</th>
      <td>[[[[[[[None], [None]], [[None], [None]]], [[[N...</td>
      <td>[[[[[[[0], [0]], [[0], [0]]], [[[0], [0]], [[0...</td>
      <td>[[[[[[['highway'], ['highway']], [['highway'],...</td>
      <td>[LINESTRING (150.8334433321849 -33.76640008110...</td>
      <td>[[[[[[[17192.0], [17192.0]], [[17192.0], [1719...</td>
      <td>[victoria road]</td>
      <td>[[[[[[[0], [0]], [[0], [0]]], [[[0], [0]], [[0...</td>
      <td>[[[[[[[23692472.0], [23692472.0]], [[23692472....</td>
      <td>[[[[[[[None], [None]], [[None], [None]]], [[[N...</td>
      <td>[[[[[[[None], [None]], [[None], [None]]], [[[N...</td>
      <td>[[[[[[[0], [0]], [[0], [0]]], [[[0], [0]], [[0...</td>
      <td>[[[[[[['residential'], ['residential']], [['re...</td>
      <td>[[[[[[[3.0], [3.0]], [[3.0], [3.0]]], [[[3.0],...</td>
    </tr>
    <tr>
      <th>4</th>
      <td>[[[[[[[None], [None], [None]], [[None], [None]...</td>
      <td>[[[[[[[0], [0], [0]], [[0], [0], [0]], [[0], [...</td>
      <td>[[[[[[['highway'], ['highway'], ['highway']], ...</td>
      <td>[LINESTRING (150.8940186758676 -33.98487891355...</td>
      <td>[[[[[[[53344.0], [53344.0], [115185.0]], [[533...</td>
      <td>[victoria road]</td>
      <td>[[[[[[[0], [0], [1]], [[0], [0], [1]], [[1], [...</td>
      <td>[[[[[[[173099158.0], [173099158.0], [454991441...</td>
      <td>[[[[[[[None], [None], [None]], [[None], [None]...</td>
      <td>[[[[[[[None], [None], [None]], [[None], [None]...</td>
      <td>[[[[[[[0], [0], [0]], [[0], [0], [0]], [[0], [...</td>
      <td>[[[[[[['residential'], ['residential'], ['resi...</td>
      <td>[[[[[[[3.0], [3.0], [3.0]], [[3.0], [3.0], [3....</td>
    </tr>
    <tr>
      <th>5</th>
      <td>[[[[[[[None], [None], [None]], [[None], [None]...</td>
      <td>[[[[[[[0], [0], [0]], [[0], [0], [0]], [[0], [...</td>
      <td>[[[[[[['highway'], ['highway'], ['highway']], ...</td>
      <td>[LINESTRING (151.0416409221976 -33.74090685788...</td>
      <td>[[[[[[[53357.0], [53357.0], [73057.0]], [[5335...</td>
      <td>[victoria road]</td>
      <td>[[[[[[[1], [1], [0]], [[1], [1], [0]], [[0], [...</td>
      <td>[[[[[[[173122378.0], [173122378.0], [183946663...</td>
      <td>[[[[[[[None], [None], [None]], [[None], [None]...</td>
      <td>[[[[[[[None], [None], [None]], [[None], [None]...</td>
      <td>[[[[[[[0], [0], [0]], [[0], [0], [0]], [[0], [...</td>
      <td>[[[[[[['residential'], ['residential'], ['resi...</td>
      <td>[[[[[[[3.0], [3.0], [3.0]], [[3.0], [3.0], [3....</td>
    </tr>
    <tr>
      <th>6</th>
      <td>[[[[[[[None], [None], [None], [None], [None], ...</td>
      <td>[[[[[[[0], [0], [0], [0], [0], [0], [0], [0], ...</td>
      <td>[[[[[[['highway'], ['highway'], ['highway'], [...</td>
      <td>[LINESTRING (151.2590352201914 -33.88693746461...</td>
      <td>[[[[[[[83156.0], [83156.0], [83160.0], [83162....</td>
      <td>[victoria road]</td>
      <td>[[[[[[[1], [1], [0], [1], [1], [1], [1], [0], ...</td>
      <td>[[[[[[[241840062.0], [241840062.0], [241840067...</td>
      <td>[[[[[[[None], [None], [None], [None], [None], ...</td>
      <td>[[[[[[[None], [None], [None], [None], [None], ...</td>
      <td>[[[[[[[0], [0], [0], [0], [0], [0], [0], [0], ...</td>
      <td>[[[[[[['tertiary'], ['tertiary'], ['tertiary']...</td>
      <td>[[[[[[[4.0], [4.0], [4.0], [4.0], [4.0], [4.0]...</td>
    </tr>
    <tr>
      <th>7</th>
      <td>[[[[[[[None], [None]], [[None], [None]]], [[[N...</td>
      <td>[[[[[[[0], [0]], [[0], [0]]], [[[0], [0]], [[0...</td>
      <td>[[[[[[['highway'], ['highway']], [['highway'],...</td>
      <td>[LINESTRING (150.8298032392747 -34.15843451684...</td>
      <td>[[[[[[[91621.0], [91621.0]], [[91621.0], [9162...</td>
      <td>[victoria road]</td>
      <td>[[[[[[[0], [0]], [[0], [0]]], [[[0], [0]], [[0...</td>
      <td>[[[[[[[289038195.0], [289038195.0]], [[2890381...</td>
      <td>[[[[[[[None], [None]], [[None], [None]]], [[[N...</td>
      <td>[[[[[[[None], [None]], [[None], [None]]], [[[N...</td>
      <td>[[[[[[[0], [0]], [[0], [0]]], [[[0], [0]], [[0...</td>
      <td>[[[[[[['residential'], ['residential']], [['re...</td>
      <td>[[[[[[[3.0], [3.0]], [[3.0], [3.0]]], [[[3.0],...</td>
    </tr>
  </tbody>
</table>
</div>




```python

```


```python
victoria_road
```




<div>
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
      <th>buffer_geometry</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>884</th>
      <td>None</td>
      <td>0</td>
      <td>highway</td>
      <td>LINESTRING (151.0016784386182 -33.808194681083...</td>
      <td>885.0</td>
      <td>victoria road</td>
      <td>0</td>
      <td>4085524.0</td>
      <td>None</td>
      <td>None</td>
      <td>0</td>
      <td>secondary</td>
      <td>5.0</td>
      <td>POLYGON ((151.0032559588228 -33.79831892242618...</td>
    </tr>
    <tr>
      <th>1209</th>
      <td>None</td>
      <td>0</td>
      <td>highway</td>
      <td>LINESTRING (151.178704652738 -33.8757890304835...</td>
      <td>1210.0</td>
      <td>victoria road</td>
      <td>0</td>
      <td>4338718.0</td>
      <td>None</td>
      <td>None</td>
      <td>0</td>
      <td>residential</td>
      <td>3.0</td>
      <td>POLYGON ((151.1738218591528 -33.8670731560912,...</td>
    </tr>
    <tr>
      <th>1438</th>
      <td>None</td>
      <td>0</td>
      <td>highway</td>
      <td>LINESTRING (151.173548944096 -33.9054795755428...</td>
      <td>1439.0</td>
      <td>victoria road</td>
      <td>0</td>
      <td>4574898.0</td>
      <td>None</td>
      <td>None</td>
      <td>0</td>
      <td>residential</td>
      <td>3.0</td>
      <td>POLYGON ((151.1817130184552 -33.91123990455313...</td>
    </tr>
    <tr>
      <th>2458</th>
      <td>None</td>
      <td>0</td>
      <td>highway</td>
      <td>LINESTRING (151.1458370310999 -33.840149681103...</td>
      <td>2459.0</td>
      <td>victoria road</td>
      <td>1</td>
      <td>4987368.0</td>
      <td>A40</td>
      <td>None</td>
      <td>0</td>
      <td>trunk</td>
      <td>8.0</td>
      <td>POLYGON ((151.1279013600059 -33.84671816574105...</td>
    </tr>
    <tr>
      <th>2463</th>
      <td>None</td>
      <td>0</td>
      <td>highway</td>
      <td>LINESTRING (151.1007298191716 -33.815414433381...</td>
      <td>2464.0</td>
      <td>victoria road</td>
      <td>1</td>
      <td>4987302.0</td>
      <td>A40</td>
      <td>None</td>
      <td>0</td>
      <td>trunk</td>
      <td>8.0</td>
      <td>POLYGON ((151.1025196512228 -33.82566250137184...</td>
    </tr>
    <tr>
      <th>2732</th>
      <td>None</td>
      <td>1</td>
      <td>highway</td>
      <td>LINESTRING (151.1465183960088 -33.840786705745...</td>
      <td>2733.0</td>
      <td>victoria road</td>
      <td>1</td>
      <td>5066693.0</td>
      <td>A40</td>
      <td>None</td>
      <td>0</td>
      <td>trunk_link</td>
      <td>23.0</td>
      <td>POLYGON ((151.1390076398048 -33.84745443397716...</td>
    </tr>
    <tr>
      <th>5119</th>
      <td>None</td>
      <td>1</td>
      <td>highway</td>
      <td>LINESTRING (151.160572752155 -33.8573997216540...</td>
      <td>5120.0</td>
      <td>victoria road</td>
      <td>1</td>
      <td>9283459.0</td>
      <td>A40</td>
      <td>None</td>
      <td>0</td>
      <td>trunk</td>
      <td>28.0</td>
      <td>POLYGON ((151.1704669952158 -33.85292414174517...</td>
    </tr>
    <tr>
      <th>5123</th>
      <td>None</td>
      <td>1</td>
      <td>highway</td>
      <td>LINESTRING (151.1497476918445 -33.844256562201...</td>
      <td>5124.0</td>
      <td>victoria road</td>
      <td>1</td>
      <td>9283781.0</td>
      <td>A40</td>
      <td>None</td>
      <td>0</td>
      <td>trunk</td>
      <td>28.0</td>
      <td>POLYGON ((151.1417336258577 -33.85035301268456...</td>
    </tr>
    <tr>
      <th>7052</th>
      <td>None</td>
      <td>0</td>
      <td>highway</td>
      <td>LINESTRING (151.1678727192669 -33.905543529464...</td>
      <td>7053.0</td>
      <td>victoria road</td>
      <td>1</td>
      <td>13858769.0</td>
      <td>None</td>
      <td>None</td>
      <td>0</td>
      <td>residential</td>
      <td>3.0</td>
      <td>POLYGON ((151.1679476928706 -33.91554343718636...</td>
    </tr>
    <tr>
      <th>11483</th>
      <td>None</td>
      <td>0</td>
      <td>highway</td>
      <td>LINESTRING (151.0285158676563 -33.809105710139...</td>
      <td>11484.0</td>
      <td>victoria road</td>
      <td>1</td>
      <td>22672963.0</td>
      <td>A40</td>
      <td>None</td>
      <td>0</td>
      <td>trunk</td>
      <td>8.0</td>
      <td>POLYGON ((151.0273471725095 -33.79911249401314...</td>
    </tr>
    <tr>
      <th>13480</th>
      <td>None</td>
      <td>0</td>
      <td>highway</td>
      <td>LINESTRING (151.126775828912 -33.8312005745426...</td>
      <td>13481.0</td>
      <td>victoria road</td>
      <td>1</td>
      <td>23061042.0</td>
      <td>A40</td>
      <td>None</td>
      <td>0</td>
      <td>trunk</td>
      <td>8.0</td>
      <td>POLYGON ((151.1088108933871 -33.82658222559643...</td>
    </tr>
    <tr>
      <th>15207</th>
      <td>None</td>
      <td>0</td>
      <td>highway</td>
      <td>LINESTRING (151.0521199775451 -33.926839430856...</td>
      <td>15208.0</td>
      <td>victoria road</td>
      <td>0</td>
      <td>23329964.0</td>
      <td>None</td>
      <td>None</td>
      <td>0</td>
      <td>residential</td>
      <td>3.0</td>
      <td>POLYGON ((151.0433368457928 -33.93160346409475...</td>
    </tr>
    <tr>
      <th>15246</th>
      <td>None</td>
      <td>1</td>
      <td>highway</td>
      <td>LINESTRING (151.0523892880941 -33.938717928758...</td>
      <td>15247.0</td>
      <td>victoria road</td>
      <td>0</td>
      <td>23330283.0</td>
      <td>None</td>
      <td>None</td>
      <td>0</td>
      <td>residential</td>
      <td>23.0</td>
      <td>POLYGON ((151.0623796601595 -33.9382367385483,...</td>
    </tr>
    <tr>
      <th>17191</th>
      <td>None</td>
      <td>0</td>
      <td>highway</td>
      <td>LINESTRING (150.8334433321849 -33.766400081108...</td>
      <td>17192.0</td>
      <td>victoria road</td>
      <td>0</td>
      <td>23692472.0</td>
      <td>None</td>
      <td>None</td>
      <td>0</td>
      <td>residential</td>
      <td>3.0</td>
      <td>POLYGON ((150.8238156911752 -33.76351210793332...</td>
    </tr>
    <tr>
      <th>18754</th>
      <td>None</td>
      <td>0</td>
      <td>highway</td>
      <td>LINESTRING (151.1597816681335 -33.856726319553...</td>
      <td>18755.0</td>
      <td>victoria road</td>
      <td>1</td>
      <td>25153980.0</td>
      <td>A40</td>
      <td>None</td>
      <td>0</td>
      <td>trunk</td>
      <td>8.0</td>
      <td>POLYGON ((151.1519961989935 -33.86303567711688...</td>
    </tr>
    <tr>
      <th>21623</th>
      <td>None</td>
      <td>0</td>
      <td>highway</td>
      <td>LINESTRING (151.1732743529481 -33.905883918551...</td>
      <td>21624.0</td>
      <td>victoria road</td>
      <td>1</td>
      <td>26920752.0</td>
      <td>None</td>
      <td>None</td>
      <td>0</td>
      <td>residential</td>
      <td>3.0</td>
      <td>POLYGON ((151.1634534898019 -33.90402263757625...</td>
    </tr>
    <tr>
      <th>21974</th>
      <td>None</td>
      <td>0</td>
      <td>highway</td>
      <td>LINESTRING (151.1700419558082 -33.863938779595...</td>
      <td>21975.0</td>
      <td>victoria road</td>
      <td>1</td>
      <td>27555905.0</td>
      <td>A40</td>
      <td>None</td>
      <td>0</td>
      <td>trunk</td>
      <td>8.0</td>
      <td>POLYGON ((151.162700346782 -33.87112189421854,...</td>
    </tr>
    <tr>
      <th>23441</th>
      <td>None</td>
      <td>0</td>
      <td>highway</td>
      <td>LINESTRING (151.1761027423549 -33.868521166060...</td>
      <td>23442.0</td>
      <td>victoria road</td>
      <td>1</td>
      <td>29232923.0</td>
      <td>A40</td>
      <td>None</td>
      <td>0</td>
      <td>trunk</td>
      <td>8.0</td>
      <td>POLYGON ((151.1684476024157 -33.87494184911029...</td>
    </tr>
    <tr>
      <th>23536</th>
      <td>None</td>
      <td>0</td>
      <td>highway</td>
      <td>LINESTRING (151.0961020866105 -33.813411829075...</td>
      <td>23537.0</td>
      <td>victoria road</td>
      <td>1</td>
      <td>30129618.0</td>
      <td>A40</td>
      <td>None</td>
      <td>0</td>
      <td>trunk</td>
      <td>8.0</td>
      <td>POLYGON ((151.0870339745069 -33.81798729587803...</td>
    </tr>
    <tr>
      <th>23537</th>
      <td>None</td>
      <td>0</td>
      <td>highway</td>
      <td>LINESTRING (151.088557787021 -33.8076898390551...</td>
      <td>23538.0</td>
      <td>victoria road</td>
      <td>0</td>
      <td>30129709.0</td>
      <td>A40</td>
      <td>None</td>
      <td>0</td>
      <td>trunk</td>
      <td>8.0</td>
      <td>POLYGON ((151.0903944953173 -33.79785765786308...</td>
    </tr>
    <tr>
      <th>23538</th>
      <td>None</td>
      <td>0</td>
      <td>highway</td>
      <td>LINESTRING (151.0799610556692 -33.806210936059...</td>
      <td>23539.0</td>
      <td>victoria road</td>
      <td>1</td>
      <td>30129777.0</td>
      <td>A40</td>
      <td>None</td>
      <td>0</td>
      <td>trunk</td>
      <td>8.0</td>
      <td>POLYGON ((151.0823561389098 -33.79649833148489...</td>
    </tr>
    <tr>
      <th>23643</th>
      <td>None</td>
      <td>0</td>
      <td>highway</td>
      <td>LINESTRING (151.1519754340699 -33.847189306303...</td>
      <td>23644.0</td>
      <td>victoria road</td>
      <td>1</td>
      <td>30269875.0</td>
      <td>A40</td>
      <td>None</td>
      <td>0</td>
      <td>trunk</td>
      <td>8.0</td>
      <td>POLYGON ((151.1615498854847 -33.84368170839701...</td>
    </tr>
    <tr>
      <th>29182</th>
      <td>None</td>
      <td>0</td>
      <td>highway</td>
      <td>LINESTRING (151.1714997364081 -33.864842767853...</td>
      <td>29183.0</td>
      <td>victoria road</td>
      <td>1</td>
      <td>44983957.0</td>
      <td>A40</td>
      <td>None</td>
      <td>0</td>
      <td>trunk</td>
      <td>8.0</td>
      <td>POLYGON ((151.178124357295 -33.85730572142697,...</td>
    </tr>
    <tr>
      <th>29184</th>
      <td>None</td>
      <td>0</td>
      <td>highway</td>
      <td>LINESTRING (151.1701421195511 -33.863855044383...</td>
      <td>29185.0</td>
      <td>victoria road</td>
      <td>1</td>
      <td>44983961.0</td>
      <td>A40</td>
      <td>None</td>
      <td>0</td>
      <td>trunk</td>
      <td>8.0</td>
      <td>POLYGON ((151.164450842822 -33.8720659367339, ...</td>
    </tr>
    <tr>
      <th>30070</th>
      <td>None</td>
      <td>0</td>
      <td>highway</td>
      <td>LINESTRING (151.0269703285301 -33.809042510589...</td>
      <td>30071.0</td>
      <td>victoria road</td>
      <td>1</td>
      <td>45663610.0</td>
      <td>A40</td>
      <td>None</td>
      <td>0</td>
      <td>trunk</td>
      <td>8.0</td>
      <td>POLYGON ((151.0263969534869 -33.79905876193359...</td>
    </tr>
    <tr>
      <th>30074</th>
      <td>None</td>
      <td>1</td>
      <td>highway</td>
      <td>LINESTRING (151.1761810293306 -33.868786453296...</td>
      <td>30075.0</td>
      <td>victoria road</td>
      <td>1</td>
      <td>45664701.0</td>
      <td>A40</td>
      <td>None</td>
      <td>0</td>
      <td>trunk</td>
      <td>28.0</td>
      <td>POLYGON ((151.1665116487507 -33.87135152354292...</td>
    </tr>
    <tr>
      <th>30075</th>
      <td>None</td>
      <td>0</td>
      <td>highway</td>
      <td>LINESTRING (151.1298462876825 -33.836702707242...</td>
      <td>30076.0</td>
      <td>victoria road</td>
      <td>1</td>
      <td>45664149.0</td>
      <td>A40</td>
      <td>None</td>
      <td>0</td>
      <td>trunk</td>
      <td>8.0</td>
      <td>POLYGON ((151.1280781626148 -33.84665104237018...</td>
    </tr>
    <tr>
      <th>30104</th>
      <td>None</td>
      <td>0</td>
      <td>highway</td>
      <td>LINESTRING (151.1298407556264 -33.836769259554...</td>
      <td>30105.0</td>
      <td>victoria road</td>
      <td>1</td>
      <td>45664209.0</td>
      <td>A40</td>
      <td>None</td>
      <td>0</td>
      <td>trunk</td>
      <td>8.0</td>
      <td>POLYGON ((151.1258893029098 -33.8459594029046,...</td>
    </tr>
    <tr>
      <th>33332</th>
      <td>None</td>
      <td>0</td>
      <td>highway</td>
      <td>LINESTRING (151.1272176390283 -33.833801479097...</td>
      <td>33333.0</td>
      <td>victoria road</td>
      <td>1</td>
      <td>52153015.0</td>
      <td>A40</td>
      <td>None</td>
      <td>0</td>
      <td>trunk</td>
      <td>8.0</td>
      <td>POLYGON ((151.1168109514456 -33.83199077873955...</td>
    </tr>
    <tr>
      <th>34569</th>
      <td>None</td>
      <td>0</td>
      <td>highway</td>
      <td>LINESTRING (151.1582647951162 -33.855092518986...</td>
      <td>34570.0</td>
      <td>victoria road</td>
      <td>1</td>
      <td>61265623.0</td>
      <td>A40</td>
      <td>None</td>
      <td>0</td>
      <td>trunk</td>
      <td>8.0</td>
      <td>POLYGON ((151.1486671403388 -33.85948872375631...</td>
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
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>108196</th>
      <td>None</td>
      <td>0</td>
      <td>highway</td>
      <td>LINESTRING (151.2527456915071 -33.874270313447...</td>
      <td>108197.0</td>
      <td>victoria road</td>
      <td>0</td>
      <td>409047692.0</td>
      <td>None</td>
      <td>None</td>
      <td>0</td>
      <td>tertiary</td>
      <td>4.0</td>
      <td>POLYGON ((151.2427434461414 -33.87413294160203...</td>
    </tr>
    <tr>
      <th>108197</th>
      <td>None</td>
      <td>0</td>
      <td>highway</td>
      <td>LINESTRING (151.2508983200476 -33.873081256663...</td>
      <td>108198.0</td>
      <td>victoria road</td>
      <td>0</td>
      <td>409047683.0</td>
      <td>None</td>
      <td>None</td>
      <td>0</td>
      <td>tertiary</td>
      <td>4.0</td>
      <td>POLYGON ((151.248769518707 -33.86331043500812,...</td>
    </tr>
    <tr>
      <th>108198</th>
      <td>None</td>
      <td>0</td>
      <td>highway</td>
      <td>LINESTRING (151.2552408164437 -33.879957937664...</td>
      <td>108199.0</td>
      <td>victoria road</td>
      <td>0</td>
      <td>409047696.0</td>
      <td>None</td>
      <td>None</td>
      <td>0</td>
      <td>tertiary</td>
      <td>4.0</td>
      <td>POLYGON ((151.2481897739878 -33.88705867156727...</td>
    </tr>
    <tr>
      <th>108199</th>
      <td>None</td>
      <td>0</td>
      <td>highway</td>
      <td>LINESTRING (151.2543256802553 -33.879092757619...</td>
      <td>108200.0</td>
      <td>victoria road</td>
      <td>0</td>
      <td>409047701.0</td>
      <td>None</td>
      <td>None</td>
      <td>0</td>
      <td>tertiary</td>
      <td>4.0</td>
      <td>POLYGON ((151.2473117269433 -33.88620359247862...</td>
    </tr>
    <tr>
      <th>108203</th>
      <td>None</td>
      <td>0</td>
      <td>highway</td>
      <td>LINESTRING (151.2595698179758 -33.885002250813...</td>
      <td>108204.0</td>
      <td>victoria road</td>
      <td>0</td>
      <td>409047704.0</td>
      <td>None</td>
      <td>None</td>
      <td>0</td>
      <td>tertiary</td>
      <td>4.0</td>
      <td>POLYGON ((151.249696253224 -33.88651051878189,...</td>
    </tr>
    <tr>
      <th>108205</th>
      <td>None</td>
      <td>0</td>
      <td>highway</td>
      <td>LINESTRING (151.2528717553308 -33.875371946981...</td>
      <td>108206.0</td>
      <td>victoria road</td>
      <td>0</td>
      <td>409047657.0</td>
      <td>None</td>
      <td>None</td>
      <td>0</td>
      <td>tertiary</td>
      <td>4.0</td>
      <td>POLYGON ((151.2435701759522 -33.87904636322057...</td>
    </tr>
    <tr>
      <th>108213</th>
      <td>None</td>
      <td>0</td>
      <td>highway</td>
      <td>LINESTRING (151.2526730204066 -33.873121322160...</td>
      <td>108214.0</td>
      <td>victoria road</td>
      <td>0</td>
      <td>409047623.0</td>
      <td>None</td>
      <td>None</td>
      <td>0</td>
      <td>tertiary</td>
      <td>4.0</td>
      <td>POLYGON ((151.2511187415473 -33.88299304943229...</td>
    </tr>
    <tr>
      <th>108223</th>
      <td>None</td>
      <td>0</td>
      <td>highway</td>
      <td>LINESTRING (151.253080967634 -33.8762750132295...</td>
      <td>108224.0</td>
      <td>victoria road</td>
      <td>0</td>
      <td>409047658.0</td>
      <td>None</td>
      <td>None</td>
      <td>0</td>
      <td>tertiary</td>
      <td>4.0</td>
      <td>POLYGON ((151.2431509722084 -33.87519666401851...</td>
    </tr>
    <tr>
      <th>108225</th>
      <td>None</td>
      <td>0</td>
      <td>highway</td>
      <td>LINESTRING (151.2531146628847 -33.875955578899...</td>
      <td>108226.0</td>
      <td>victoria road</td>
      <td>0</td>
      <td>409047667.0</td>
      <td>None</td>
      <td>None</td>
      <td>0</td>
      <td>tertiary</td>
      <td>4.0</td>
      <td>POLYGON ((151.2439625444604 -33.87995537366961...</td>
    </tr>
    <tr>
      <th>108231</th>
      <td>None</td>
      <td>0</td>
      <td>highway</td>
      <td>LINESTRING (151.2504876906112 -33.873156609972...</td>
      <td>108232.0</td>
      <td>victoria road</td>
      <td>0</td>
      <td>409047675.0</td>
      <td>None</td>
      <td>None</td>
      <td>0</td>
      <td>tertiary</td>
      <td>4.0</td>
      <td>POLYGON ((151.2499301976425 -33.86317538964057...</td>
    </tr>
    <tr>
      <th>108236</th>
      <td>None</td>
      <td>0</td>
      <td>highway</td>
      <td>LINESTRING (151.2538077624581 -33.878601075179...</td>
      <td>108237.0</td>
      <td>victoria road</td>
      <td>0</td>
      <td>409047655.0</td>
      <td>None</td>
      <td>None</td>
      <td>0</td>
      <td>tertiary</td>
      <td>4.0</td>
      <td>POLYGON ((151.2468757049973 -33.88580880561352...</td>
    </tr>
    <tr>
      <th>108249</th>
      <td>None</td>
      <td>0</td>
      <td>highway</td>
      <td>LINESTRING (151.2594758568412 -33.885694009282...</td>
      <td>108250.0</td>
      <td>victoria road</td>
      <td>0</td>
      <td>409047652.0</td>
      <td>None</td>
      <td>None</td>
      <td>0</td>
      <td>tertiary</td>
      <td>4.0</td>
      <td>POLYGON ((151.2496086051959 -33.88405197035792...</td>
    </tr>
    <tr>
      <th>108251</th>
      <td>None</td>
      <td>0</td>
      <td>highway</td>
      <td>LINESTRING (151.2514476699816 -33.873000958030...</td>
      <td>108252.0</td>
      <td>victoria road</td>
      <td>0</td>
      <td>409047688.0</td>
      <td>None</td>
      <td>None</td>
      <td>0</td>
      <td>tertiary</td>
      <td>4.0</td>
      <td>POLYGON ((151.2528450291611 -33.88289108575053...</td>
    </tr>
    <tr>
      <th>108252</th>
      <td>None</td>
      <td>0</td>
      <td>highway</td>
      <td>LINESTRING (151.2592045346355 -33.886419043907...</td>
      <td>108253.0</td>
      <td>victoria road</td>
      <td>1</td>
      <td>409186130.0</td>
      <td>None</td>
      <td>None</td>
      <td>0</td>
      <td>tertiary</td>
      <td>4.0</td>
      <td>POLYGON ((151.2506650402896 -33.88121086498141...</td>
    </tr>
    <tr>
      <th>108253</th>
      <td>None</td>
      <td>0</td>
      <td>highway</td>
      <td>LINESTRING (151.259277289555 -33.8864270067152...</td>
      <td>108254.0</td>
      <td>victoria road</td>
      <td>1</td>
      <td>409186131.0</td>
      <td>None</td>
      <td>None</td>
      <td>0</td>
      <td>tertiary</td>
      <td>4.0</td>
      <td>POLYGON ((151.2681911687968 -33.89096008524811...</td>
    </tr>
    <tr>
      <th>108254</th>
      <td>None</td>
      <td>0</td>
      <td>highway</td>
      <td>LINESTRING (151.2595317641353 -33.886110757508...</td>
      <td>108255.0</td>
      <td>victoria road</td>
      <td>1</td>
      <td>409186132.0</td>
      <td>None</td>
      <td>None</td>
      <td>0</td>
      <td>tertiary</td>
      <td>4.0</td>
      <td>POLYGON ((151.2671642058138 -33.89256763552065...</td>
    </tr>
    <tr>
      <th>108255</th>
      <td>None</td>
      <td>0</td>
      <td>highway</td>
      <td>LINESTRING (151.2593145890241 -33.886229361438...</td>
      <td>108256.0</td>
      <td>victoria road</td>
      <td>1</td>
      <td>409186134.0</td>
      <td>None</td>
      <td>None</td>
      <td>0</td>
      <td>tertiary</td>
      <td>4.0</td>
      <td>POLYGON ((151.2493263034918 -33.88574457335272...</td>
    </tr>
    <tr>
      <th>108256</th>
      <td>None</td>
      <td>0</td>
      <td>highway</td>
      <td>LINESTRING (151.2592425884759 -33.886496576511...</td>
      <td>108257.0</td>
      <td>victoria road</td>
      <td>1</td>
      <td>409186133.0</td>
      <td>None</td>
      <td>None</td>
      <td>0</td>
      <td>tertiary</td>
      <td>4.0</td>
      <td>POLYGON ((151.2682003474881 -33.89094175412075...</td>
    </tr>
    <tr>
      <th>108272</th>
      <td>None</td>
      <td>0</td>
      <td>highway</td>
      <td>LINESTRING (151.2527657242557 -33.874818573734...</td>
      <td>108273.0</td>
      <td>victoria road</td>
      <td>0</td>
      <td>409047632.0</td>
      <td>None</td>
      <td>None</td>
      <td>0</td>
      <td>tertiary</td>
      <td>4.0</td>
      <td>POLYGON ((151.2427523602336 -33.87463545733586...</td>
    </tr>
    <tr>
      <th>108291</th>
      <td>None</td>
      <td>0</td>
      <td>highway</td>
      <td>LINESTRING (151.254552159279 -33.8793116091112...</td>
      <td>108292.0</td>
      <td>victoria road</td>
      <td>0</td>
      <td>409047656.0</td>
      <td>None</td>
      <td>None</td>
      <td>0</td>
      <td>tertiary</td>
      <td>4.0</td>
      <td>POLYGON ((151.2477755405788 -33.886665912613, ...</td>
    </tr>
    <tr>
      <th>108324</th>
      <td>None</td>
      <td>0</td>
      <td>highway</td>
      <td>LINESTRING (151.2594465201801 -33.886035236560...</td>
      <td>108325.0</td>
      <td>victoria road</td>
      <td>1</td>
      <td>409047654.0</td>
      <td>None</td>
      <td>None</td>
      <td>0</td>
      <td>tertiary</td>
      <td>4.0</td>
      <td>POLYGON ((151.2501089951441 -33.88961565638709...</td>
    </tr>
    <tr>
      <th>110294</th>
      <td>None</td>
      <td>0</td>
      <td>highway</td>
      <td>LINESTRING (151.071540428103 -33.8069328693795...</td>
      <td>110295.0</td>
      <td>victoria road</td>
      <td>1</td>
      <td>418273631.0</td>
      <td>A40</td>
      <td>None</td>
      <td>0</td>
      <td>trunk</td>
      <td>8.0</td>
      <td>POLYGON ((151.0737561383287 -33.81668552444346...</td>
    </tr>
    <tr>
      <th>110730</th>
      <td>None</td>
      <td>0</td>
      <td>highway</td>
      <td>LINESTRING (151.1508465593506 -33.845395662843...</td>
      <td>110731.0</td>
      <td>victoria road</td>
      <td>1</td>
      <td>422816585.0</td>
      <td>A40</td>
      <td>None</td>
      <td>0</td>
      <td>trunk</td>
      <td>8.0</td>
      <td>POLYGON ((151.1592349177204 -33.83994139696512...</td>
    </tr>
    <tr>
      <th>110741</th>
      <td>None</td>
      <td>0</td>
      <td>highway</td>
      <td>LINESTRING (151.1508036440063 -33.845547123833...</td>
      <td>110742.0</td>
      <td>victoria road</td>
      <td>1</td>
      <td>422816586.0</td>
      <td>A40</td>
      <td>None</td>
      <td>0</td>
      <td>trunk</td>
      <td>8.0</td>
      <td>POLYGON ((151.1425746288608 -33.85122896854545...</td>
    </tr>
    <tr>
      <th>114648</th>
      <td>None</td>
      <td>0</td>
      <td>highway</td>
      <td>LINESTRING (151.1677565460889 -33.905262651888...</td>
      <td>114649.0</td>
      <td>victoria road</td>
      <td>1</td>
      <td>448741554.0</td>
      <td>None</td>
      <td>None</td>
      <td>0</td>
      <td>residential</td>
      <td>3.0</td>
      <td>POLYGON ((151.1619069430339 -33.91356682208487...</td>
    </tr>
    <tr>
      <th>114649</th>
      <td>None</td>
      <td>0</td>
      <td>highway</td>
      <td>LINESTRING (151.1678727192669 -33.905543529464...</td>
      <td>114650.0</td>
      <td>victoria road</td>
      <td>0</td>
      <td>448741555.0</td>
      <td>None</td>
      <td>None</td>
      <td>0</td>
      <td>residential</td>
      <td>3.0</td>
      <td>POLYGON ((151.1681815710862 -33.89553902750809...</td>
    </tr>
    <tr>
      <th>114676</th>
      <td>None</td>
      <td>0</td>
      <td>highway</td>
      <td>LINESTRING (151.1670771928367 -33.905621229706...</td>
      <td>114677.0</td>
      <td>victoria road</td>
      <td>0</td>
      <td>448741559.0</td>
      <td>None</td>
      <td>None</td>
      <td>0</td>
      <td>secondary</td>
      <td>5.0</td>
      <td>POLYGON ((151.1656044557056 -33.89572797499653...</td>
    </tr>
    <tr>
      <th>115154</th>
      <td>None</td>
      <td>0</td>
      <td>highway</td>
      <td>LINESTRING (150.8836599846497 -33.983576701076...</td>
      <td>115155.0</td>
      <td>victoria road</td>
      <td>1</td>
      <td>454991440.0</td>
      <td>None</td>
      <td>None</td>
      <td>0</td>
      <td>residential</td>
      <td>3.0</td>
      <td>POLYGON ((150.881874091473 -33.99341635818629,...</td>
    </tr>
    <tr>
      <th>115180</th>
      <td>None</td>
      <td>0</td>
      <td>highway</td>
      <td>LINESTRING (150.8833294862076 -33.983497659729...</td>
      <td>115181.0</td>
      <td>victoria road</td>
      <td>1</td>
      <td>454991439.0</td>
      <td>None</td>
      <td>None</td>
      <td>0</td>
      <td>residential</td>
      <td>3.0</td>
      <td>POLYGON ((150.8825528752174 -33.97353835490925...</td>
    </tr>
    <tr>
      <th>115184</th>
      <td>None</td>
      <td>0</td>
      <td>highway</td>
      <td>LINESTRING (150.8838387706444 -33.983499419929...</td>
      <td>115185.0</td>
      <td>victoria road</td>
      <td>1</td>
      <td>454991441.0</td>
      <td>None</td>
      <td>None</td>
      <td>0</td>
      <td>residential</td>
      <td>3.0</td>
      <td>POLYGON ((150.8823040783002 -33.99340730038009...</td>
    </tr>
  </tbody>
</table>
<p>197 rows × 14 columns</p>
</div>




```python
victoria_road["buffer_geometry"] = victoria_road["geometry"].apply(lambda x: x.buffer(.001))
```


```python
# victoria_road["buffer_geometry"].apply(lambda x: shapely.ops.linemerge([y for y in x]) )
from shapely.ops import cascaded_union

# lines = [
#     LineString([(845, 555), (365, -5), (130, -650)]),
#     LineString([(740, 605), (640, 60), (315, -375)]),
#     LineString([(0, -500), (655, -150), (900, 300)]),
# ]

# # Two example unions
# unioned_lines = cascaded_union(lines)
vic_road_unioned_buffered_poly = cascaded_intersection([l.buffer(50) for l in victoria_road["buffer_geometry"].values])
```


    ---------------------------------------------------------------------------

    NameError                                 Traceback (most recent call last)

    <ipython-input-208-4cff09e2a3f8> in <module>()
         10 # # Two example unions
         11 # unioned_lines = cascaded_union(lines)
    ---> 12 vic_road_unioned_buffered_poly = cascaded_intersection([l.buffer(50) for l in victoria_road["buffer_geometry"].values])
    

    NameError: name 'cascaded_intersection' is not defined



```python

```
