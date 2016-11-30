---
layout: post
title: Learning R cheatsheet
---

Getting help:

```
help(x) or ?x	# help on function `x`
example(x) 	# print an example of using `x`
??x	   	# search help for instances of string x
apropos('x')	# list all objects with `x` in the name
```

Types/objects:

```
mode(x)		# type of an object (storage mode)
ls()		# list the objects in the current workspace
rm(x)		# delete the object from curren workspace
```

File management:

```
getwd()		# list working directory
setwd('dir')   	# set working directory
dir()           # list directories
dir.create(...) # create a directory
```

Workspace:

```
save(...)       # save objects to a file
save.image()    # save entire image to a file
load('file')    # load objects written by save

history()       # display last few commands
savehistory('f')# save history to a file
loadhistory('f')# load history from a file

options()       # list available options (globals)
options(x=3)	# set an option
q()             # quit session
```

Stream management:

```
source('f')	# run commands from file `f`
sink('f', split=TRUE) # Tee output into a file
```

Package management:

```
.libPaths()	   # dir where are packages saved
installed.packages() # see details/versions/etc.

install.packages() # installation of packages
update.packages()  # updating to latest

library()      	   # list of installed packages
library(x)	   # load package

help(package='x')  # get help on a package
```

Basic stats/math:

```
data()      # list available datasets

runif(x)	# generate x uniformly distributed numbers
rnorm(x)	# generate x normally distributed numbers
summary(x)	# print summary info for statistical objects

lm(x~y[, data=z]) # linear regression

integrate(f, i, j) # integrate `f` in range
```

Plotting basics:

```
dev.new()       # create new plotting device and set active
def.off()       # delete the last plotting device
png/pdf('x')    # write graphics to a file

hist(x)		      # compute a histogram object (and plot by default)
plot(x, y)        # plot `x` against `y`
plot(x~y)         # plot `x` against `y`
plot(x~y, data=z) # plot `x` against `y` from dataframe
abline(...)       # add a line to plot

curve(dnorm, -4, 4)  # plot a function
```

Vectors:

```
x<-c(1, 2, 3)     # constructor
x[1]              # 1-based indexes
x[c(1,2)]         # get multiple indexes
1:5               # range (inclusive)
```

Data frames:

```
data.frame(v1, v2)    # populate a two-column data frame
names(x)<-c('a', 'b') # name the columns

x[2]                  # get a column
x['b']                # get a column
x$b                   # get a column

x[2:3]                # get multiple columns
x[,2:3]               # get multiple columns

with(x, { a })        # refer to a column, save typing

x[2:3,]               # get multiple rows
```
