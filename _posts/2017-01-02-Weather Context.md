---
layout: single
author_profile: true
except: 
header:
    image: /assets/header_images/aquaduct.jpg
    caption: " "
---

# Weather Context

At Govhack 2016 (zz link!) I (and my team partner) had decided to make a twitterbot. We didn't know what this twitterbot would do, but we wanted to build one. Poking around in the code repositories I discovered that you can get all the historical weather data form the BOM (zzz link!). I like random weather trivia so my first plan was to build a twitterbot that tells you how hot or cold it has been, relative to the historical data - "Today is the 4th hottest 9th of February". In the end we couldn't do it for govhack as it didn't use enough data repositories (we made Creature Feasutres (zzz link!) instead).



I still liked the idea so eventually I gave myself a break from some other projects and made it. The basic motivation is to put today's weather in context by comparing it to the historical data for that day. 

This bot quickly became the depressing climate change bot. Essentially every day

## What does it do?

Everyday at 0400UTC (4pm Sydney time, 5pm during light savings) the bot will tweet what the maximum temperature is, how much it deviates from the historical average for the date, how "likely" the maximum temperature is and the temperatures ranking. If the absolute vaue of the z-score is:

* 1 - very likely
* 2  - likely
* 3 -  unlikley
* greater than 4  - very unlikely

Two graphs are atached - a scatter plot showing the year vs. maximum tempreature and the distribution. What really got me is how depressing the scatter plots can be: nealry every day the trend line is slightly positive. Rarely do you see a day that 

## Future Plans

* Do the same for the minimum temperature
* Compare the maximum/minimum pairs
* Do a end of month/year review. 



## Things I've learnt

* AWS lambda doesn't allow functions greater than 250MB. This is a problem if you need pandas, numpy and seaborn (To be fair I could make do with seaborn, but as numpy and scipy are seaborn dependences I might as well sue them)
* crontab needs to have a blank line at the end of the file
* The maximum temperature can occur after 5pm
* It is a pain to install everything that comes with anaconda by hand. I thought it would be a good idea at the time. 


* ​