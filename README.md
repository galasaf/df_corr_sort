# df_corr_sort
Sorts the columns of a dataframe by correlation order, so columns with the strongest (positive or negative) correlations appear first.

I created this because I thought it would make it easier to read the output of a parallel coordinates plot. For example, if you have 100 variables, it might help to visualize the data if the variables were arranged in order from strongest correlation to weakest correlation in the parallel coordinates plot.

A couple caveats:

1. I ran the function on a simplistic dataframe I created, and I felt like it helped. However, I'm not sure how well it works on realistic high-dimensional datasets.

2. The output of the function is not necessarily the "best" one. It uses a "greedy" algorithm that incrementally adds variables based on whichever one has the highest correlation at that step, but does not take into account the entirety of possibilities, so it is possible that a better arrangement exists. When a tie exists between two variables, it simply uses whichever one came first, and does not look ahead to see which one would produce a better outcome. I felt like I had to write the algorithm like this, or else the number of computations would be too high.


Example:

If a dataframe has three columns, “a”, “b”, and “c”, and “a <-> c” are almost linearly dependent, and “b” is not dependent on the other variables, then the function will return “a”, “c”, “b”. 


Another example (with R code):

Create five variables, a, b, c, d, e. Each variable is linearly dependent on the preceding one, but adds in an error term. The error term is increasing in each subsequent variable, so that the highest correlation will be between the first and second variables, then the second and third variables, etc. Then, scramble the order of the variables, and put them in a dataframe.

a <- rnorm(100, 10, 3)

b <- (a + 5) + rnorm(100, 0, 1)

c <- (-b - 2) + rnorm(100, 0, 2)

d <- (-c + 10) + rnorm(100, 0, 3)

e <- (d) + rnorm(100, 0, 4)

df <- data.frame(c, e, a, b, d)


library(GGally)

ggparcoord(df, columns=1:5, scale="uniminmax") ## Create a parallel coordinates plot, without regard for correlations between variables.

ggparcoord(df, columns=order_by_cor(df), scale="uniminmax") ## Create a parallel coordinates plot, with variables sorted by correlation order. It is now a bit easier to see the linear relationships between variables.


