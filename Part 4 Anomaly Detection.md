---
title: 'Part 4: Anomaly Detection'
output: html_document
---

```{r setup, include=FALSE}
knitr::opts_chunk$set(echo = TRUE)
```

# Reading the data

```{r}
library(tidyverse)
library(readr)
cfs<- read_csv("http://bit.ly/CarreFourSalesDataset")
```
```R
# Load anomalize
install.packages("anomalize")
library(anomalize)
```
```R
# Verifying the object's class
class(cfs)
```
```R
# checking dimensions
dim(cfs)
```
```R
# Previewing our first 5 entries
head(cfs,5) 
```
```R
# Previewing our last 5 entries
tail(cfs,5) 
```
```{r}
# checking datatypes
str(cfs)
```
```{r}
## convert date column to date class
cfs$Date <- as.Date(cfs$Date,
                        format = "%m/%d/%y")
library(tibbletime)                        
cfs <- as_tbl_time(cfs, index = Date)
```
```{r}
cfs %>% 
  time_decompose(Sales, method = "stl", frequency = "auto", trend = "auto") %>%
  anomalize(remainder, method = "iqr", alpha = 0.05, max_anoms = 0.2) %>%
  plot_anomaly_decomposition()
```