---
layout: single
author_profile: true
published: true
date: 2019-12-06 12:00 f+1000
classes: wide
---

__[Sizth day of Advent of Code](https://adventofcode.com/2019/day/4)__, with a problem that (initially) defeated me in R. 

I don't know why. It wasn't an hard problem. It was basically some tree transversal. And I got so frustrated with R that I implemented it in python instead. I'm hoping to get redo this in R later. 

```python
import pandas as pd
import numpy as np

def get_orbits(base):
  '''Recursive tree transversal. Stops backtracking as soon as it gets to a
  node that has already been visited'''
    if base =='COM':
        return 1
    elif ~np.isnan(orbits.loc[orbits['orbiter']==base, 'num_orbits'].iloc[0]):
        return  1 + orbits.loc[orbits['orbiter']==base, 'num_orbits'].iloc[0]
    else:
        return 1 + get_orbits(orbits.loc[orbits['orbiter']==base, 'base'].iloc[0])

num_orbits = pd.Series(index=orbits['orbiter'].unique())


orbits = pd.read_csv('day6.txt', sep=')', names = ['base', 'orbiter'])
for o, orbit in orbits.iterrows():
    num_orbits[orbit['orbiter']] = get_orbits(orbit['base'])

# Part 1
orbits["num_orbits"].sum()


# Part 2
path = pd.DataFrame(index=list(set(orbits['orbiter'].unique().tolist() + orbits['base'].unique().tolist())), 
                    columns=['comes_from', 'distance'])

# Initial set up, getting YOU
current_loc = 'YOU'

path.loc[current_loc, 'distance'] = 0
destinations = []
while 'SAN' not in destinations and 'SAN'!=current_loc:
    new_destinations = orbits.loc[orbits['orbiter']==current_loc, 'base'].values.tolist()
    new_destinations.extend( orbits.loc[orbits['base']==current_loc, 'orbiter'].values.tolist())
    
    
    new_destinations = [d for d in new_destinations if np.isnan(path.loc[d, 'distance']) ]
    
    path.loc[new_destinations, 'comes_from'] = current_loc
    path.loc[new_destinations, 'distance'] = 1 +  path.loc[current_loc, 'distance']

    destinations.extend(new_destinations)
    
    current_loc = destinations.pop()

```
