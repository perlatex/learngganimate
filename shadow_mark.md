shadow\_mark
================
Danielle Navarro & Dale Maschette
22/11/2018

This `shadow_mark()` walk through extends the `shadow_wake()` walk through and uses the same animation.

``` r
ntimes <- 20  # how many time points to run the bridge?
nseries <- 5 # how many time series to generate?

# function to generate the brownian bridges
make_bridges <- function(ntimes, nseries) {
  replicate(nseries, c(0,rbridge(frequency = ntimes-1))) %>% as.vector()
}

# construct tibble
tbl <- tibble(
  Time = rep(1:ntimes, nseries),
  Horizontal = make_bridges(ntimes, nseries),
  Vertical = make_bridges(ntimes, nseries),
  Series = gl(nseries, ntimes)
)

# construct the base picture
base_pic <- tbl %>%
  ggplot(aes(
    x = Horizontal, 
    y = Vertical, 
    colour = Series)) + 
  geom_point(
    show.legend = FALSE,
    size = 5) + 
  coord_equal() + 
  xlim(-1.5, 1.5) + 
  ylim(-1.5, 1.5)

# base animation with no shadow
base_anim <- base_pic + transition_time(time = Time) 
base_anim %>% animate()
```

![](shadow_mark_files/figure-markdown_github/createdata-1.gif)

See the other walk through for details.

Minimal use of shadow mark
--------------------------

``` r
mark0 <- base_anim + shadow_mark()
mark0 %>% animate(type = "cairo")
```

![](shadow_mark_files/figure-markdown_github/mark0-1.gif)

Setting fixed aesthetics
------------------------

``` r
mark1<- base_anim + 
  shadow_mark(
    size = 2, 
    alpha = 0.8,
    colour = "black"
  )
mark1 %>% animate(type = "cairo")
```

![](shadow_mark_files/figure-markdown_github/mark1-1.gif)

Excluding layers
----------------

Suppose we want to solve Dale's problem in a much simpler way: keep the colours on the shadow mark, but have the original dots all be black. Solution is to add another layer but exclude it from the shadow mark:

``` r
new_pic <- base_pic + 
  geom_point(colour = "black", size = 5)

mark2 <- new_pic + 
  transition_time(time = Time) + 
  shadow_mark(size = 2, exclude_layer = 2)

mark2 %>% animate(type = "cairo")
```

![](shadow_mark_files/figure-markdown_github/mark2-1.gif)
