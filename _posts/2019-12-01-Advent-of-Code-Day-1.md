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

 But really there is no point to learn R, only to treat it like every other procedural language. So I did a bit of reading on `dplyr` and `tidyverse` to get it more R like. 

```R
library(dplyr)
fuel_calc <- function(module_mass)
{
  mass  %/% 3 - 2
}

extra_fuel_needed <- function(module_mass)
{
  module_fuel <- fuel_calc(module_mass)
  total_module_fuel <- module_fuel
  fuel_for_fuel <- fueL_calc(module_fuel)
  while(fuel_for_fuel > 0)
  {
    total_module_fuel <- total_module_fuel + fuel_for_fuel
    fuel_for_fuel <- fuel_calc(fuel_for_fuel)
  }
  total_module_fuel
}

module_mass = read.table('day1.txt', header=FALSE, col.names=c('mass'))
module_mass$module_fuel <- module_mass  %>%
  rowwise() %>%
  mutate(Max.Len= max(Sepal.Length,Petal.Length))
# Total module mass

# Fuel required to launch a given module is based on its mass.
# Specifically, to find the fuel required for a module, take its mass, divide by three, round down, and subtract 2.
total_module_fuel_required <- sum(module_mass$mass %/% 3 - 2 )

# Fuel itself requires fuel just like a module - take its mass, divide by three, round down, and subtract 2.
# However, that fuel also requires fuel, and that fuel requires fuel, and so on.
# Any mass that would require negative fuel should instead be treated as if it requires zero fuel;
# the remaining mass, if any, is instead handled by wishing really hard,

# Calculating module weight separately
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