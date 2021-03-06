Heart Pumping
================
Emi Tanaka
22/11/2018

This was working in the old version of `gganimate` but since upgrading
to the new one, it has stopped working :(

``` r
## install from Github
devtools::install_github("jespermaag/gganatogram")
```

``` r
library(gganimate)
```

    ## Loading required package: ggplot2

``` r
library(gganatogram)
```

    ## Loading required package: ggpolypath

``` r
library(dplyr)
```

    ## 
    ## Attaching package: 'dplyr'

    ## The following objects are masked from 'package:stats':
    ## 
    ##     filter, lag

    ## The following objects are masked from 'package:base':
    ## 
    ##     intersect, setdiff, setequal, union

``` r
body <- hgMale_list$human_male_outline %>% 
  mutate(index=1:nrow(.)) %>%
  group_by(group) %>%
  summarise(x=NA, y=NA, index=0) %>%
  bind_rows(., hgMale_list$human_male_outline) %>% 
  arrange(group, index) %>% 
  select(x, y)
heart1 <- heart2 <- hgMale_list$heart[-1,]
heart2 <- heart2 %>% 
  mutate(x=1.3*(x - mean(x)) + mean(x),
         y=1.3*(y - mean(y)) + mean(y),
         state=2)
heart1 <- heart1 %>% 
  mutate(x=0.8*(x - mean(x)) + mean(x),
         y=0.8*(y - mean(y)) + mean(y),
         state=1)
heart <- rbind(heart1, heart2) %>% select(x, y, state)
```

I can’t get this `transition_states` function to work and am not sure
why. It worked under an older version of gganimate. Then i updated the
package and now I get an error? this is the error

> Error in is\_quosure(e2) : argument “e2” is missing, with no default

``` r
ggplot(data=body, aes(x, -y)) +
  geom_path() +
  geom_polygon(fill="yellow", colour="black") +
  geom_path(data=heart, aes(x, -y)) + 
  geom_polygon(data=heart, aes(x, -y), fill="red") + 
  theme_void()  +
  ease_aes('quadratic-in-out') +
  
 #  transition_manual(state) 
  transition_states(state,
                    transition_length=3,
                    state_length=1) 

#animate(ga, nframes=10, width=350)  
```

BUt `transition_manual` works. I also learnt that seeting the nframes
argument of the `animate` function to a samll number makes the hear beat
faster than a larger number

``` r
ga <- ggplot(data=body, aes(x, -y)) +
  geom_path() +
  geom_polygon(fill="yellow", colour="black") +
  geom_path(data=heart, aes(x, -y)) + 
  geom_polygon(data=heart, aes(x, -y), fill="red") + 
  theme_void()  +
  ease_aes('quadratic-in-out') +
  transition_manual(state) 
animate(ga, nframes=5, width=350)  
```

    ## nframes and fps adjusted to match transition

    ## Warning: Removed 1 rows containing missing values (geom_path).
    
    ## Warning: Removed 1 rows containing missing values (geom_path).
    
    ## Warning: Removed 1 rows containing missing values (geom_path).

![](example_heart_pumping_files/figure-gfm/transition_manual-1.gif)<!-- -->
