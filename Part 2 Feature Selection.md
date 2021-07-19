---
title: 'Part 2: Feature Selection'
output: html_document
---

```{r setup, include=FALSE}
knitr::opts_chunk$set(echo = TRUE)
```

# Reading and checking the data

```{r}
library(tidyverse)
library(readr)
cf1<- read_csv("http://bit.ly/CarreFourDataset")
```
```{r}
# Verifying the object's class
class(cf1)
```
```{r}
# checking dimensions
dim(cf1)
```
```{r}
# Previewing our first 5 entries
head(cf1,5) 
```
```{r}
# Previewing our last 5 entries
tail(cf1,5) 
```
```{r}
# checking datatypes
str(cf1)
```
# Tidying the dataset
```{r}
# removing spaces in some column names
names(cf1)<-make.names(names(cf1))
```
the spaces are removed for consistency
```{r}
# Checking whether there are missing values in each column
colSums(is.na(cf1))
```
there are no missing values
```{r}
# checking for duplicates
anyDuplicated(cf1)
```
there are no duplicates
```{r}
# removing unwanted columns
drops <- c("Invoice.ID","Date","Time","gross.margin.percentage")
cf1<-cf1[ , !(names(cf1) %in% drops)]
names(cf1)
```
invoice id , date and time columns are irrelevant gross.margin.percentage has a constant value for all records thus does not bring any new information to the table.
```{r}
# label encoding categorical columns
library(superml)
label <- LabelEncoder$new()
label$fit(cf1$Branch)
cf1$Branch <- label$fit_transform(cf1$Branch)
label$fit(cf1$Customer.type)
cf1$Customer.type <- label$fit_transform(cf1$Customer.type)
label$fit(cf1$Gender)
cf1$Gender <- label$fit_transform(cf1$Gender)
label$fit(cf1$Product.line)
cf1$Product.line <- label$fit_transform(cf1$Product.line)
label$fit(cf1$Payment)
cf1$Payment <- label$fit_transform(cf1$Payment)
```
methods to be used require numerical data hence the need for encoding

# 1: Filter Methods
```{r}
# Installing and loading our caret package
# ---
# 
suppressWarnings(
        suppressMessages(if
                         (!require(caret, quietly=TRUE))
                install.packages("caret")))
library(caret)

# Installing and loading the corrplot package for plotting
# ---
# 
suppressWarnings(
        suppressMessages(if
                         (!require(corrplot, quietly=TRUE))
                install.packages("corrplot")))
library(corrplot)

```
```{r}
# Calculating the correlation matrix
# ---
#
correlationMatrix <- cor(cf1)

# Find attributes that are highly correlated
# ---
#
highlyCorrelated <- findCorrelation(correlationMatrix, cutoff=0.75)

# Highly correlated attributes
# ---
# 
highlyCorrelated

names(cf1[,highlyCorrelated])

```
tax , cogs and gross.income have high correlations
```{r}
# We can remove the variables with a higher correlation 
# and comparing the results graphically as shown below
# ---
# 
# Removing Redundant Features 
# ---
# 
Dataset2<-cf1[-highlyCorrelated]

# Performing our graphical comparison
# ---
# 
par(mfrow = c(1, 2))
corrplot(correlationMatrix,order = "hclust")
corrplot(cor(Dataset2), order = "hclust")

```

# 2: Embedded Methods
```R
# ---
# We will use the ewkm function from the wskm package.
# This is a weighted subspace clustering algorithm that is well suited to very high dimensional data.
# ---
# OUR CODE GOES BELOW
# 

# We install and load our wskm package
# ---
#
suppressWarnings(
        suppressMessages(if
                         (!require(wskm, quietly=TRUE))
                install.packages("wskm")))
library(wskm)

set.seed(2)
model <- ewkm(cf1[1:12], 3, lambda=2, maxiter=1000)

```


```R
# Loading and installing our cluster package
# ---
#
suppressWarnings(
        suppressMessages(if
                         (!require(cluster, quietly=TRUE))
                install.packages("cluster")))
library("cluster")

# Cluster Plot against 1st 2 principal components
# ---
#
clusplot(cf1[1:12], model$cluster, color=TRUE, shade=TRUE,
         labels=2, lines=1,main='Cluster Analysis for CarreFourDataset')

```


```R
# Weights are calculated for each variable and cluster. 
# They are a measure of the relative importance of each variable 
# with regards to the membership of the observations to that cluster. 
# The weights are incorporated into the distance function, 
# typically reducing the distance for more important variables.
# Weights remain stored in the model and we can check them as follows:
# 
round(model$weights*100,2)
```
