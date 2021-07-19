---
title: 'Part 1: Dimensionality Reduction'
output: html_document
---

```{r setup, include=FALSE}
knitr::opts_chunk$set(echo = TRUE)
```

# Reading the data

```{r}
library(tidyverse)
library(readr)
cf<- read_csv("http://bit.ly/CarreFourDataset")
```
```R
# Verifying the object's class
class(cf)
```
```R
# checking dimensions
dim(cf)
```
```R
# Previewing our first 5 entries
head(cf,5) 
```
```R
# Previewing our last 5 entries
tail(cf,5) 
```
```{r}
# checking datatypes
str(cf)
```
# Tidying the dataset
```{r}
# removing spaces in some column names
names(cf)<-make.names(names(cf))
```
```{r}
# Checking whether there are missing values in each column
colSums(is.na(cf))
```
```{r}
# checking for duplicates
anyDuplicated(cf)
```
```{r}
# removing unwanted columns
drops <- c("Invoice.ID","Date","Time")
cf<-cf[ , !(names(cf) %in% drops)]
names(cf)
```
```{r}
# label encoding categorical columns
library(superml)
label <- LabelEncoder$new()
label$fit(cf$Branch)
cf$Branch <- label$fit_transform(cf$Branch)
label$fit(cf$Customer.type)
cf$Customer.type <- label$fit_transform(cf$Customer.type)
label$fit(cf$Gender)
cf$Gender <- label$fit_transform(cf$Gender)
label$fit(cf$Product.line)
cf$Product.line <- label$fit_transform(cf$Product.line)
label$fit(cf$Payment)
cf$Payment <- label$fit_transform(cf$Payment)
```
# t-SNE
```{R}
require(tsne)

cols <- rainbow(10)

# this is the epoch callback function used by tsne. 
# x is an NxK table where N is the number of data rows passed to tsne, and K is the dimension of the map. 
# Here, K is 2, since we use tsne to map the rows to a 2D representation (map).
ecb = function(x, y){ plot(x, t='n'); text(x, labels=cf[,13]); }

tsne_res = tsne(cf[,1:13], epoch_callback = ecb, perplexity=50, epoch=50)
plot(tsne_res) 
```
4 distinct clusters can be seen from the result