---
title: 'Part 3: Association Rules'
output: html_document
---

```{r setup, include=FALSE}
knitr::opts_chunk$set(echo = TRUE)
```

# Reading the data

```{r}
library(tidyverse)
library(arules)
library(readr)
sd<- read.transactions("http://bit.ly/SupermarketDatasetII", sep = ",")
```

```R
# Verifying the object's class
class(sd)
```
```R
# checking dimensions
dim(sd)
```
```R
# Previewing our first 5 transactions
inspect(sd[1:5])  
```
```R
# Previewing our last 5 transactions
inspect(sd[7497:7501])  
```
```R
# Generating a summary of the transaction dataset
# ---
# This would give us some information such as the most purchased items, 
# distribution of the item sets (no. of items purchased in each transaction), etc.
# ---
# 
summary(sd)
```
```R
# Producing a chart of frequencies and fitering 
# to consider only items with a minimum percentage 
# of support/ considering a top x of items
# ---
# Displaying top 10 most common items in the dataset 
# and the items whose relative importance is at least 10%
# 
par(mfrow = c(1, 2))

# plot the frequency of items
itemFrequencyPlot(sd, topN = 10,col="darkgreen")
itemFrequencyPlot(sd, support = 0.1,col="darkred")
```
```R
# Building a model based on association rules 
# using the apriori function 
# ---
# We use Min Support as 0.001 and confidence as 0.8
# ---
# 
rules <- apriori (sd, parameter = list(supp = 0.001, conf = 0.8))
rules
# Building a apriori model with Min Support as 0.002 and confidence as 0.8.
rules2 <- apriori (sd,parameter = list(supp = 0.002, conf = 0.8))
rules2
# Building apriori model with Min Support as 0.002 and confidence as 0.6.
rules3 <- apriori (sd, parameter = list(supp = 0.001, conf = 0.6))
rules3
```
```{r}
# getting the summary of the rules
summary(rules)
```
```R
# Observing rules built in our model i.e. first 5 model rules
# ---
# 
inspect(rules[1:5])
```
if someone buys {mushroom cream sauce,pasta} they  are 95% likely to buy escalope