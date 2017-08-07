---
title: 'Tricks for R: List and Order'
date: 2017-03-10 09:15:14
tags: R
---
I'm busy with my senior thesis these days. When using R to clean data for thesis, I find some tricks.
<!--more-->
- - - -
The first problem is that how can I reach the exact element in all members of a list. For example, I have a list `List`:
```
> List <- list(c(1, 2), c(3, 4), c(5, 6))
> List
[[1]]
[1] 1 2
[[2]]
[1] 3 4
[[3]]
[1] 5 6
```
I want to get the first number in each member of `List`, like `1 3 5`. And there is a solution:
```
> unlist(lapply(List, function(x) x[1]))
[1] 1 3 5
```
If you want to get every second element, just change `x[1]` to `x[2]`:
```
> unlist(lapply(List, function(x) x[2]))
[1] 2 4 6
```
- - -
And I find that `tapply()` sorts the result by `INDEX` argument automatically:
```
> tapply(c(1,2,3,4,5,6), c(3,2,1,3,2,1),mean)
  1   2   3 
4.5 3.5 2.5 
```
As the example shown above, result of index `1` (`(3+6)/2=4.5`) is displayed first. What if `INDEX` is a character vector?
```
> tapply(c(1:14), c("a","B","b","C","ab","ac","1","a","B","b","C","ab","ac","1"),mean)
   1    a   ab   ac    b    B    C 
10.5  4.5  8.5  9.5  6.5  5.5  7.5 
```
Hah, the output shows that numbers come first, next there are characters in alphabetic order.
- - -
The last trick about order I found during my thesis is about `rbind`. When using `rbind`, column order of output is  same as first argument:
```
> D1 <- data.frame(c(1,2), c(1,2))
> colnames(D1) <- c("c","a")
> D2 <- data.frame(c(3,4), c(3,4))
> colnames(D2) <- c("c","a")
> D3 <- data.frame(c(3:4), c(3:4))
> colnames(D3) <- c("a","c")
> rbind(D1, D2)
  c a
1 1 1
2 2 2
3 3 3
4 4 4
> rbind(D1, D3)
  c a
1 1 1
2 2 2
3 3 3
4 4 4
> 
> M1 <- matrix(c(1,2,1,2), ncol = 2)
> colnames(M1) <- c("c","a")
> M2 <- matrix(c(3,4,3,4), ncol = 2)
> colnames(M2) <- c("c","a")
> M3 <- matrix(c(3,4,3,4), ncol = 2)
> colnames(M3) <- c("a","c")
> rbind(M1, M2)
     c a
[1,] 1 1
[2,] 2 2
[3,] 3 3
[4,] 4 4
> rbind(M1, M3)
     c a
[1,] 1 1
[2,] 2 2
[3,] 3 3
[4,] 4 4
```
It is shown that the combining is column-wise, no metter putting `data.frame` or `matrix` into `rbind()`.