---
title: "Data Refinement"
output: word_document
---

```{r setup, include=FALSE}
knitr::opts_chunk$set(echo = TRUE)
```

```{r load-packages, include=FALSE}
library(dplyr)
library(knitr)
```

# Titanic Data

## Load Titanic data

```{r}
titanicMessData <- read.table('TitanicMess.tsv', header = TRUE, sep = "\t", row.names = NULL)
summary(titanicMessData) 
```

## Dataset refinement

We have 13 dataset attributes, 
but we can expect, that not every attribute would be useful in data exploration.

### Handling duplicates

It can happen, that the dataset contians duplicated data.
First we want to remove rows containing duplicated data from the dataset.

We can use the "distinct()" function from package "dplyr":

```{r}
titanicMessData <- distinct(titanicMessData)
summary(titanicMessData)
```

We had 892 rows of data, now the dataset has 889 rows.

### Defining valuable attributes

Now we want to take a short look onto the dataset, to see the specificity of each column:
      
```{r}
head(titanicMessData, 40)
```

Taking a short look we grasp, that column PassengerId and Ship are not useful.
We know that the ship is Titanic, and PassengerId's are unique. We don't need this columns.

We remove columns "PassengersId" (col id 1) and "Ship" (col id 13):

```{r}
titanicMessData <- titanicMessData[, -c(1,13)]
head(titanicMessData, 12)
```

We removed columns, which values are not helpful in the analysis.

### Handling missing values

Many datasets often contain data rows with some missing attribute values.
These missing values can be marked as: " ", "Na", "NaN", etc...

Let's explicitly mark them out in our dataset:

```{r}

titanicMessData[
  titanicMessData == ''
  | titanicMessData == ' '
  | titanicMessData == "Na"
  | titanicMessData == "NaN"
  ] <- NA

```

Let's now check the count of missing values for each column containing missing values:

```{r}
colSums(is.na(titanicMessData))
```
Out of almost 900 rows at least 685 contain missing values.
In order to solve the problem of missing values we can insert mode values, or remove the rows.

However, in this case there is also other possibility, which is removing of the "Cabin" column.
We choose to remove this column, for it would give much noise in dataset: 
- replacing makes the analysis results wander from the truth, 
- removing rows would leave a small part of a dataset.

Removing "Cabin" 

```{r}
titanicMessData <- titanicMessData[, -10]
summary(titanicMessData)
```
We remember, there are still two more columns with missing values:

```{r}
colSums(is.na(titanicMessData))
```
Now the number of rows containing missing values is around 20%.
Therefore we decide to remove the rows with missing values:

```{r}
titanicMessData <- na.omit(titanicMessData)
head(titanicMessData, 12)
```

Now the dataset is set free from the rows including missing values.

```{r}
head(titanicMessData, 40)
summary(titanicMessData)
```

Done. The dataset after the refinement has 714 data records 
and is ready for further analysis and processing.

## Saving refined dataset

```{r}
write.table(titanicMessData, file = "TitanicCleaned.tsv", sep = "\t", row.names=FALSE)
```

# https://mybinder.org/v2/gh/EmanuelGaw/SIwB/HEAD
