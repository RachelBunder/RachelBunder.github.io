---
layout: single
author_profile: true
published: true
date: 2019-12-03 12:00 f+1000
---

__[Third day of Advent of Code](https://adventofcode.com/2019/day/3)__. During undergrad, my favourite comment I got on an assignment was 'This code should be taken out and be shot'[^1]. I feel like my solution for Day 3 is another example of this.

I started out with good intentions. I wanted to create a list of tuples showing where the wires went, take the intersection, then calculate the distances. But I couldn't work out how to generate a list of tuples. And then my OR background kicked in and I thought matrices, they are nice. Lets make matrices of the path. 

A matrix was a solution. But they are very big, break if the path goes outside their bounds, slow and lots of wasted memory. This is one of the problems I would like to go back and resolve once I have a better handle on R funamentals.

In the meantime, I'm reading __[R for data science](https://r4ds.had.co.nz/)__ and properly understanding these R solution for day 3  - __[solution 1](https://www.reddit.com/r/adventofcode/comments/e5bz2w/2019_day_3_solutions/?utm_source=share&utm_medium=web2x)__ and --(solution 2)[https://www.reddit.com/r/adventofcode/comments/e5bz2w/2019_day_3_solutions/?utm_source=share&utm_medium=web2x)__. My other big learning is to develop with the example as input: it is much quicker to debug if you don't need to load up a 30,000x30,000 matrix.

```R
ibrary(tidyr)
library(dplyr)
library(purrr)

define_directions <- function(direction, value)
{
  if (direction == 'R'){
    movement <- c(0, value)
  }
  else if (direction == 'L'){
    movement <- c(0, -1*value)
  }
  else if (direction =='U'){
    movement <- c(value, 0)
  }
  else if (direction == 'D'){
    movement <- c(-1*value, 0)
  }
  else{
    print('Unknown direction')
  }
  movement
}

manhatten <- function(pos1, pos2)
{
  abs(pos1[1] - pos2[1]) + abs(pos1[2] - pos2[2])
}

# There is a warning message saying incomplete final line, but comparing the lengths it it fine
steps <- data.frame(t(read.table('day3.txt', sep=',',header=FALSE, row.name=c('wire1', 'wire2'))))
                    
steps <- steps %>% separate(wire1,
                   into=c('wrire1_dir', 'wire1_steps'),                                      
                   sep="(?<=[A-Z])(?=[0-9])",
                   convert=TRUE)

steps <- steps %>% separate(wire2,
                   into=c('wrire2_dir', 'wire2_steps'),                                      
                   sep="(?<=[A-Z])(?=[0-9])",
                   convert=TRUE)

current_pos = c(0,0)

#steps <- 
#cal <-  purrr::map2(steps$wire1_dir, steps$wire1_steps, define_dire

matrix_size <- 30000
#matrix_size <-20
wire1_path <- matrix(0L, nrow = matrix_size, ncol = matrix_size)
wire2_path <- matrix(0L, nrow = matrix_size, ncol = matrix_size)
centre_point <- c(matrix_size/2, matrix_size/2)

wire1_last_position <- centre_point
wire2_last_position <- centre_point

wire1_path[wire1_last_position] <- -1
wire2_path[wire2_last_position] <- -1

wire1_steps <- 0
wire2_steps <- 0

for(row in 1:nrow(steps))
{
  wire1_move <- wire1_last_position + define_directions(steps[row, 'wrire1_dir'], steps[row, 'wire1_steps'])
  wire2_move <- wire2_last_position + define_directions(steps[row, 'wrire2_dir'], steps[row, 'wire2_steps'])
  
  # Setting all the posisitions transversed in the last move to 1
  wire1_path[wire1_last_position[1]:wire1_move[1], wire1_last_position[2]:wire1_move[2]] <- pmin(wire1_steps + 0:steps[row, 'wire1_steps'])
  wire2_path[wire2_last_position[1]:wire2_move[1], wire2_last_position[2]:wire2_move[2]] <- pmin(wire2_steps + 0:steps[row, 'wire2_steps'])
  
  print(wire1_steps + 0:steps[row, 'wire1_steps'])
  
  wire1_steps <- wire1_steps + steps[row, 'wire1_steps']
  wire2_steps <- wire2_steps + steps[row, 'wire2_steps']
  
  
  wire1_last_position <- wire1_move
  wire2_last_position <- wire2_move
}


# 
min_dist_manhat = 10000000000
min_dist_steps = 10000000000
for(row in 1:matrix_size)
{
  for(col in 1:matrix_size)
  {
    if(wire1_path[row, col]>0 & wire2_path[row, col] >0 & row != centre_point[1] & col != centre_point[2])
    {
      dist <- manhatten(centre_point, c(row, col))
      min_dist_manhat <- min(c(dist, min_dist_manhat))
      
      dist <- wire1_path[row, col] + wire2_path[row, col]
      min_dist_steps <- min(c(dist, min_dist_steps))
    }
  }    
}
# Part 1
print(min_dist_manhat)
# Part 2
print(min_dist_steps)

```



[^1]: Don't try to write your own shuffle algorithm. __(Fisher-Yates)[https://en.wikipedia.org/wiki/Fisher%E2%80%93Yates_shuffle]__ is your friend