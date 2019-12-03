---
layout: single
author_profile: true
except: 
header:
    image: /assets/header_images/advent_code_1.JPG
    alt-text: "to Come"
---

Learning R more has been in my to do list for a while, so I thought I would attempt __[Advent of Code](https://adventofcode.com/2019/about)__ in R. Here is Day 1.

Initially I had a simple loop that tracked the cumulative sum. It worked fine. I go the righ answers. At least I did after I realised the extra fuel had to be calculate per module.
```R 
# Part 1
total_module_fuel_required <- sum(module_mass$mass %/% 3 - 2 )

# Part 2
total_fuel <- 0
for(module in module_mass$mass)
{
  module_fuel <- module  %/% 3 - 2
  total_module_fuel <- module_fuel
  fuel_for_fuel <- module_fuel  %/% 3 - 2
  while(fuel_for_fuel > 0)
  {
    total_module_fuel <- total_module_fuel + fuel_for_fuel
    fuel_for_fuel <- fuel_for_fuel  %/% 3 - 2
  }
  total_fuel <- total_fuel + total_module_fuel
}
```

 But really there is no point to learn R, only to treat it like every other procedural language. So this is what I ended up with. What gets me about R is that what is all done in pandas in python, seems to be down by multiple packages: `dplyr`, `tidyverse` and `purrr`.
```R
library(purrr)
fuel_calc <- function(module_mass)
{
  module_mass  %/% 3 - 2 
}

fuel_calc_extra_fuel <- function(module_mass)
{
  module_fuel <- fuel_calc(module_mass) 
  total_module_fuel <- module_fuel
  fuel_for_fuel <- fuel_calc(module_fuel)
  while(fuel_for_fuel > 0)
  {
    total_module_fuel <- total_module_fuel + fuel_for_fuel
    fuel_for_fuel <- fuel_calc(fuel_for_fuel)
  }
  total_module_fuel
}

module_mass = read.table('day1.txt', header=FALSE, col.names=c('mass'))

# Part 1
module_mass$module_fuel <- purrr::map_dbl(module_mass$mass, fuel_calc)
print(sum(module_mass$module_fuel))

# Part 2
module_mass$module_fuel_extra <- purrr::map_dbl(module_mass$mass, fuel_calc_extra_fuel)
print(sum(module_mass$extra_fuel))
```