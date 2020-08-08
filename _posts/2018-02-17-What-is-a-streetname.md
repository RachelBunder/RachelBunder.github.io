---
layout: single
author_profile: true
title: What is a street *name*
published: true
date: 2018-02-14 12:00 f+1000
classes: wide
---

Now that we have streets sorted, we need to work out what part is the *name*. The most common full street name may be "Small Street", but I now want to separate the name, *Small*, from its desriptor *Street*.


```python
import pandas as pd
import geopandas as gpd
import matplotlib.pyplot as plt

import shapely
%matplotlib inline

streets = pd.read_csv("clean_streets_reduced.csv")
streets['name'].head()
```




    0                    1
    1          10th avenue
    2          12th avenue
    3    12th avenue north
    4          12th street
    Name: name, dtype: object



## Attempt 1
Lets keep it simple to start of with: the first part of the street name is the *name* an


```python
def get_name1(full_name):
    '''Gets the name of the street: full name minus the last word.'''

    split_name = full_name.split()
    return split_name[0]

streets["street_name"] = streets["name"].apply(get_name1)
streets.groupby("street_name")["id"].count().sort_values(ascending=False).head()
```




    street_name
    the        247
    st         108
    park        95
    william     73
    john        70
    Name: id, dtype: int64



And the most common name is "the". As in "*The* Avenue", "*The* Grand Parade" or "*The* Serpentine". In my mind "The" is an integral part of the street name. The whole name should be *The Avenue*, *The Grand Parade* or *The Serpentine*.

The second most common is "st" as in "St. Mark Close" or "St. Thomas Road". In these cases I think the names should be "St. Mark" or "St. Thomas".

Let's revise that funciton. If the name starts with "The" the full name is its name. Otherwise it will return everything other than the last word in the name:



```python
def get_name2(full_name):
    '''Gets the name of the street: full name minus the last word.
    Unless the full name starts with "the", then return the full_name
 '''

    split_name = full_name.split()
    if split_name[0]=='the':
        return full_name
    else:
        return ' '.join(split_name[:-1])

streets["street_name"] = streets["name"].apply(get_name2)
streets.groupby("street_name")["id"].count().sort_values(ascending=False).head()
```


    street_name
    park        92
                69
    victoria    60
    railway     56
    william     54
    Name: id, dtype: int64



"Park" is now the most common - which sounds about right. But <blank> is the second most commmon. These are streets with only a single word like "Broardway" or "Kingsway". Lets do another revision: now if the full name is only one word, return that word:


```python
def get_name3(full_name):
    '''Gets the name of the street: full name minus the last word.
    Unless the full name starts with "the" or is a single word,
    then return the full_name.
  '''

    split_name = full_name.split()
    if len(split_name)==1:
        return full_name
    if split_name[0]=='the':
        return full_name
    else:
        return ' '.join(split_name[:-1])

streets["street_name"] = streets["name"].apply(get_name3)
streets.groupby("street_name")["id"].count().sort_values(ascending=False).head()

```


    street_name
    park        92
    victoria    60
    railway     56
    william     54
    short       53
    Name: id, dtype: int64



This looks fine! Lets look at what the descriptor would look like. For this, I'm just taking what is left after the name:


```python
def get_description(full_name):
    '''Gets the description of the street: the last word of the
    full name, unless the full name starts with "the" or is a single word,
    then return None.
    '''

    split_name = full_name.split()
    if len(split_name)==1:
        return None
    if split_name[0]=='the':
        return None
    else:
        return split_name[-1]

streets["street_description"] = streets["name"].apply(get_description)
streets.groupby("street_description")["id"].count().sort_values(ascending=False).head(20)

```
    street_description
    street       12755
    place         6359
    avenue        5862
    road          4345
    lane          2228
    close         1732
    crescent      1626
    drive          969
    way            841
    court          672
    parade         461
    circuit        389
    grove          286
    terrace        103
    offramp         97
    onramp          80
    glen            57
    boulevard       55
    north           55
    south           50
    Name: id, dtype: int64


This mostly looks ok, but then we see descriptions like 'north' and 'south' which aren't what I orignally intended. Also "offramp" and "onramp". Those steret names are things like "Bathurt street offramp". I don't want the name to be "Bathurst street" and the descriptor "offramp". I rather the name be *Bathurst*, and the descriptor be *street offramp*.

At this point I thought that I could identify all the street descriptors, identify where in the full name the descriptor would be. Then everything before the descriptor would be the name, and everything other would be an optional suffix.

This did not work. For one there was more descriptors than I thought - 192 for the Sydney data test I've been testing with. Including typos like "roaw" and "avenur". Then I realised that there are streets whose name is also a descriptor: Park Lane is common, but there is also Grove Avenue, Terrace Lane and many many more.

I could have started to do something fancy like "identify the last occurace of the descriptor and everything before is the name and everything after is a suffix", but it just seemed too much. Maintaining a list of descriptors takes time, especially when I'm doing it over a larger area than Sydney.

Intead, I decided to focus on just the main suffixes: North, East, South, West, Offramp, Onramp and exit. If these appear at the end of the full street name I'll mark them as a suffix, otherwise I will stick to the same logic in `get_name2`


```python
def three_name_model(name):
   '''Gets the name of the street, descriptor and suffix

   Suffix: if word ends with north, east, south, west, offramp,
   onramp or exit, this is the suffix

   Descriptor: the last word of the full name once any suffix is
   removed. Unless what is left is a single word or starts with "the",
   there is no descriptor.

   Name: what remains of the beginning of the full name
  '''
    suffixes = ["north", "east", "south", "west", "offramp", "onramp", "exit"]

    split = name.split()

    suffix = None
    descriptor = None
    street_name = None

    if len(split) == 1:
        street_name = name
    else:
        # If the last word in the name is a recognised suffix
        if len(split) > 2 and split[-1] in suffixes:
            suffix = split[-1]
            split = split[:-1]

        if split[0] == 'the':
            street_name = name
            descriptor = split[-1]
        else:
            descriptor = split[-1]
            street_name = " ".join(split[:-1])

    return street_name, descriptor, suffix

streets["street_name"], streets["street_description"], streets["street_suffix"] = zip(*streets["name"].map(three_name_model))
streets.groupby("street_name")["id"].count().sort_values(ascending=False).head(20)

```

    street_name
    park         93
    victoria     66
    railway      57
    william      57
    short        53
    king         50
    albert       50
    station      49
    george       47
    church       47
    stanley      44
    james        44
    charles      43
    campbell     42
    edward       42
    wentworth    40
    john         40
    elizabeth    39
    gordon       39
    arthur       39
    Name: id, dtype: int64



The street names still appear fine. Checking the descriptors:


```python
streets.groupby("street_description")["id"].count().sort_values(ascending=False).head(40)
```




    street_description
    street        12860
    place          6362
    avenue         5924
    road           4485
    lane           2242
    close          1733
    crescent       1651
    drive           989
    way             848
    court           672
    parade          472
    circuit         392
    grove           293
    terrace         105
    boulevard        63
    glen             58
    motorway         53
    highway          32
    square           29
    row              27
    boulevarde       22
    walk             22
    esplanade        21
    gardens          21
    bridge           17
    parkway          17
    mews             16
    circle           15
    m7               15
    rise             14
    glade            14
    green            12
    loop             12
    promenade        11
    ridge            11
    bay              11
    link              9
    trail             8
    tunnel            8
    park              7
    Name: id, dtype: int64



The descriptors also mostly appear fine. There is an appearance of "M7" which is a major highway which has many onramps, offramps and exits. But as we want to fine the most common street *name*, not so much the descriptor, this isn't a problem.


```python
streets.groupby("street_suffix")["id"].count().sort_values(ascending=False)
```

    street_suffix
    offramp    99
    onramp     80
    north      55
    south      50
    west       46
    east       44
    exit       21
    Name: id, dtype: int64



And finally our suffixes. Pleasingly the cardinal directions have similar occurances. It also makes sense that there are more offramps than onramps as highways start somewhere (so don't need an on-ramp) and you don't want more input into your road than output.

# The most common street name in Sydney

Finally, the most comomn street name in Sydney is "Park", followed by "Vitoria", "Railway", "William" and "Short". Most suburbs in Sydney would have at least one Park, subsequently many also have a Park Road (most often). Even in Sydney CBD there is a Park Road that goes in between Hyde Park.

Many of the other street names are related to royalty: Victoria, King, Albert and Alfred (Victoria's second son who actually visited Sydney). William and George were also names of Kings, but they were also common first names.

A few names relate to early Australian coloinal history:
 * __[Wentworth](https://en.wikipedia.org/wiki/William_Wentworth)__, an explorer (also could be his __[father](https://en.wikipedia.org/wiki/D%27Arcy_Wentworth)__, the first paying passenger to come to the colony)
 * __[Hunter](https://en.wikipedia.org/wiki/John_Hunter_(Royal_Navy_officer))__ the second govenor of the colony
 * __[Cook](https://en.wikipedia.org/wiki/James_Cook): Captain Cook claimed the east coast of Australia for the British
 * __[Macquarie](https://en.wikipedia.org/wiki/Lachlan_Macquarie)__: the fifth govenor of the colony
 * __[Mitchell](https://en.wikipedia.org/wiki/Thomas_Mitchell_(explorer))__: an explorer



```python
streets.groupby("street_name")["id"].count().sort_values(ascending=False).head(50)

```

    street_name
    park          93
    victoria      66
    railway       57
    william       57
    short         53
    king          50
    albert        50
    station       49
    george        47
    church        47
    stanley       44
    james         44
    charles       43
    campbell      42
    edward        42
    wentworth     40
    john          40
    elizabeth     39
    gordon        39
    arthur        39
    hunter        38
    smith         37
    carrington    37
    cross         37
    boronia       36
    mary          36
    rose          36
    margaret      35
    cook          34
    first         34
    west          34
    waratah       34
    macquarie     34
    mitchell      34
    york          33
    rawson        33
    thomas        33
    second        32
    wattle        32
    western       32
    bridge        31
    alfred        31
    russell       31
    pine          31
    francis       31
    phillip       30
    young         30
    hill          30
    nelson        30
    bellevue      30
    Name: id, dtype: int64



My original motivation for this was the most common street names for many states in the USA are plant names like Cedar or Oak. I didn't think we did this in Australia - so I set out to find out. At least in Sydney, we see some plant names in the top 50:  __[Boronia](https://en.wikipedia.org/wiki/Boronia)__, https://en.wikipedia.org/wiki/Rose (although I think more likely to be a name), __[Waratah](https://en.wikipedia.org/wiki/Waratah)__ (NSW state emblem), __[Wattle](https://en.wikipedia.org/wiki/Acacia_sensu_lato)__ (National flower of Australia) and Pine. Pine is really interesting as I don't think there are native pine trees in Sydney. Perhaps named after Norfolk pines which were introduced to Sydney early on.

But, they are no where close to being the most common. Sure, this data is only for Sydney, but I doubt it will change when we look at the whole nation. Flora is diverse in Australia. Boronias and wattles are more or less nation wide, but Waratahs are only in the south east. Introduced pines are nation wide, but are mostly in state forests without many streets. And different states will then add their own native plants to the mix. A state may have a plant name as the most common street name, but I doubt there will be many in the top 10 street names nation wide.

