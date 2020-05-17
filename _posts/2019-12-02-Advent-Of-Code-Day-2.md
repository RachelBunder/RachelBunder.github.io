---
layout: single
author_profile: true
published: true
date: 2019-12-02 12:00 f+1000
classes: wide
---

__[Second day of Advent of Code](https://adventofcode.com/2019/day/2)__. This time our computer burst into flames and we have to reset it. I'm not sure how the code is meant to work if the computer just got burnt...

R has lots of different ways of doing the same thing: there are a lot of ways to read in a file and I'm not really sure what the differences are. There is `read.csv` and `read.csv2`. Why have both?  I used scan and that gave me the data as a list, rather than a table which was better for this problem.

I also had to remember that R indices start at 1 rather than 0, which meant I had to remember to offset each of the indices. 

My first solution was 466644  which was too low. I tried some of the examples on the problem and they all worked fine. Then I read the last paragraph of the problem - and found out I had to change the input file. And then it worked.



```R
# I changed the actual file with the correct input
op_codes <- scan('day2.txt', sep=',')

i <- 1
while(i <= length(op_codes))
{
  # Adds
  if(op_codes[i]==1)
  {
    op_codes[op_codes[i+3]+1] <- op_codes[op_codes[i+1]+1] + op_codes[op_codes[i+2]+1]
  }
  else if (op_codes[i]==2)
  {
    op_codes[op_codes[i+3]+1] <- op_codes[op_codes[i+1]+1] * op_codes[op_codes[i+2]+1]
  }
  else if (op_codes[i]==99)
  {
    print('Got to 99, broke')
    break
  }
  else
  {
    print("Didn't meet conditions, investigate")
  }
  print(i)
  print(length(op_codes))
  i <- i + 4
}
op_codes[1]
```

For part 2, I couldn't see of a good way other than brute forcing all options. I was tempted to try a solution in an alebraic system, but there is a good chance that the solution would be a not so nice polynomial. 

This one was straight forward. The only thing I found strange was that `sprintf` doesn't actually print to the screen, I still needed to wrap `print` around it. __[This old answer](https://stat.ethz.ch/pipermail/r-help/2011-February/268969.html)__ on the r-help mail list explained it. 

```R
run_ops_codes <- function(noun, verb)
{
  op_codes <- scan('day2.txt', sep=',', quiet=TRUE)
  op_codes[2] <- noun
  op_codes[3] <- verb
  
  i <- 1
  while(i <= length(op_codes))
  {
    # Adds
    if(op_codes[i]==1)
    {
      op_codes[op_codes[i+3]+1] <- op_codes[op_codes[i+1]+1] + op_codes[op_codes[i+2]+1]
    }
    else if (op_codes[i]==2)
    {
      op_codes[op_codes[i+3]+1] <- op_codes[op_codes[i+1]+1] * op_codes[op_codes[i+2]+1]
    }
    else if (op_codes[i]==99)
    {
      break
    }
    else
    {
      print("Didn't meet conditions, investigate")
    }
    i <- i + 4
  }
  op_codes
}

target_value <- 19690720
found=FALSE
for(noun in 0:99)
{
  for(verb in 0:99)
  {
    result <- run_ops_codes(noun, verb)
    if(target_value == result[1])
    {
      print(sprintf('Found target, %s, %s', noun, verb))
      found=TRUE
      break
    }
  }
  if(found)
  {
    break
  }
  
}
print(100*noun + verb)?
```