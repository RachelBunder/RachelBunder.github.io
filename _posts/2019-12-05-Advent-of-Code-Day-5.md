---
layout: single
author_profile: true
published: true
date: 2019-12-05 12:00 f+1000
---

__[Fifth day of Advent of Code](https://adventofcode.com/2019/day/4)__, with another ops-code problem. 

I struggled with this one. Partly because there weren't too many smaller examples to step through, partly because I don't get R-studio's debug tools. There are breakpoints, but then you need to source the code and sometimes it gets stuck in a loop of executing while sourcing. In the interest of time, it was easier to spam debugging print statements

I did learn something about R's order of operations. In this snippet:

```R 
l = c(1,2,3,4)
l[length(l)-2:length(l)]
```
returns `2 1` rather than my expected `2 3 4`. What is happening is that `:` operator takes precedence over substraction, so 

So it appears that `:` operator takes precedence over substraction as `l[(length(l)-2):length(l)]` works. Breaking down the calculation steps:

```
l[length(l)-2:length(l)]
-> l[4-2:4]
-> l[4-(2 3 4)]
-> l[(2 1 0)]
2 1
```
In the last step, R ignores the zeros in the index request. It won't allow negatives and any index over the actual length of the returns `NA`. 

```R
llibrary(dplyr)
library(stringr)


run_ops_codes <- function()
{
  # The expected number of parameters for the code
  number_params <- c('01'=3, '02'=3, '03'=1, '04'=1, '05'=2, '06'=2, '07'=3, '08'=3)
  
  op_codes <- scan('day5_test.txt', sep=',', quiet=TRUE)
  i <- 1
  while(i <= length(op_codes))
  {
  #  print(paste('iteration', i))
    instruction <- toString(op_codes[i]) %>% str_split("")
    instruction <- instruction[[1]]
    
    # Extracting ops code form instruction
    # If instuction is a single number, pad with 0
    if(length(instruction)==1)
    {
      code <- instruction
      code[2] <- 0
      code <- paste(rev(code), collapse='')
      
      # The additional parameters needed will be padded out later
      parameters <- c(0)
    }
    else
    {
      code <- paste(instruction[(length(instruction)-1):length(instruction)], collapse='')
      
      # Separate parameters and add in 0 for unspecified values
      parameters <- instruction[1:(length(instruction)-2)[1]] %>% rev()
    }

    if(code=='99')
    {
      return(op_codes)
    }
    
  #  print(paste('code', code))
    parameters <- parameters[1:number_params[code]]
    parameters[is.na(parameters)] <- '0'
   # print(parameters)
 
    # Reassign the value of each parameter to be what is actually needed
    # I'm pretty sure this could be done in lapply, but I'm not sure how to get
    # p value in there as well
    p <- 1
    for(param in parameters)
    {
      # if in position mode, look at the value in the position
      # If writing, we ust want the location value, not the value in it
      if(p==3 | (p==2 & param=='1' & (code=='05' | code=='06')))
      {
     #   print(paste(p, op_codes[i+p]))
        parameters[p] <- op_codes[i+p]
      }
    # Reading, always going to save to the right place
    else if (code == '03')
    {
      parameters[p] <- op_codes[i+p]
    }
      # if in relative mode, it is just itself. 
      else if(param == '1')
      {
       # print(paste('parameter', p, parameters[p]))
        parameters[p] <- op_codes[i + p]
      }
      else if(param == '0')
      {
          parameters[p] <- op_codes[op_codes[i+p]+1]
      }
      else
      {
        print("Not known mode")
      }
      p <- p + 1
    }
    parameters <- strtoi(parameters)
   # print(paste(i, code, instruction))
    # Adds
    if(code=='01')
    {
      op_codes[parameters[3]+1] <- parameters[1] + parameters[2]
      step_size <- 4
    }
    # Multiplies
    else if (code=='02')
    {
      op_codes[parameters[3]+1] <-  parameters[1] * parameters[2]
      step_size <- 4
    }
    # Value to position
    else if (code == '03')
    {
      op_codes[parameters[1] + 1] <- readline(prompt='Input needed: ') %>% as.integer()
      step_size <- 2
    }
    # Print
    else if (code=='04')
    {
      print(parameters[1])
      step_size <- 2
    }
    else if (code=='05')
    {
      if(parameters[1]!= 0)
      {
        i <- parameters[2] + 1  # pretty sure about needing to offfset it by -1
        step_size <- 0
      }
      else
      {
        print(paste('No jump', parameters[1]))
        step_size <- 3
      }
    }
    else if (code=='06')
    {
      if(parameters[1]== 0)
      {
        i <- parameters[2] + 1 # pretty sure about needing to offfset it by -1
        step_size <- 0
      }
      else
      {
        step_size <- 3
      }
    }
    else if (code=='07')
    {
      if(parameters[1] < parameters[2])
      {
        op_codes[parameters[3]+1] <- 1
      }
      else
      {
        op_codes[parameters[3]+1] <- 0
      }
      step_size <- 4
    }
    else if (code=='08')
    {
      if(parameters[1] == parameters[2])
      {
        op_codes[parameters[3]+1] <- 1
      }
      else
      {
        op_codes[parameters[3]+1] <- 0
      }
      step_size <- 4
    }
    else if (op_codes[i]=='99' )
    {
      break
    }
    else
    {
      #print(code)
      print("Didn't meet conditions, investigate")
    }
    i <- i + step_size
  }
  op_codes
}
result <- run_ops_codes()

result
```