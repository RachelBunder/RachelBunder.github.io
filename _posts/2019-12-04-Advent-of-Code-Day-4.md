---
layout: single
author_profile: true
published: true
date: 2019-12-04 12:00 f+1000
classes: wide
---

__[Fourth day of Advent of Code](https://adventofcode.com/2019/day/4)__, featuring poor password practices and elves with unusual memories. 

I don't have too much to say about this one. I had to look up some regex things, but the __[regex crossword](https://regexcrossword.com/)__ that I completed a few months ago made this a much less painful process than usual. Especially combined with __[regex101](https://regex101.com/)__ to double check my logic.

```R
library(dplyr)
library(stringr)
min_password <- 152085
max_password <- 670283

to_int_array <- function(num)
{
  num <- toString(num)
  num <- strsplit(num, split='')
  num <- lapply(num, as.numeric)[[1]]
  num
}

monotonically_increasing <- function(nums)
{
  all(nums == cummax(nums))
}

double_digit <- function(nums)
{
  lag1 <- paste(nums - lag(nums), collapse="")
  
  "0" %in% lag1
}

double_digit_no_triple <- function(nums)
{
  lag1 <- paste(nums - lag(nums), collapse="")
  
  lag1 %in% str_extract_all(lag1, "NA(([0-9]*[1-9])?0([1-9][0-9]*){1}|([0-9]*[1-9]){1}0([1-9][0-9]*)?)")
}


password_count_1 <- 0
password_count_2 <- 0
for(i in min_password:max_password)
{
  password <- to_int_array(i)
  
  if(length(password)==6&
     monotonically_increasing(password)&
     double_digit(password)
  )
  {
    password_count_1 <- password_count_1 + 1
  }
  if(length(password)==6&
     monotonically_increasing(password)&
     double_digit_no_triple(password)
  )
  {
    password_count_2 <- password_count_2 + 1
  }
}
print(password_count_1)
print(password_count_2)
```