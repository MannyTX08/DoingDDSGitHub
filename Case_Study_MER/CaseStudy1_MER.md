---
title: "Case Study: Beer and Breweries"
author: "Manuel Rosales"
date: "2/20/2018"
output:
  html_document:
    keep_md: yes
---

From initial review of the information provided we will show how many breweries are present within each state.
Question 1

```q1
# Read in csv file containing Brewery information and locations.
Breweries <- read.csv("Breweries.csv", header=TRUE, sep = ',')

# Determine number of Breweries within each state.
# Coerce the levels within factor of State into a data frame
BreweryByState <- table(factor(Breweries$State))
BreweryByState <- data.frame(BreweryByState)

# Rename columns to something meaningful
colnames(BreweryByState) <- c('State','Count')

BreweryByState
```

Question 2
Combine data set into a merged frame


```q2
# Read in csv file containing Beer details
Beers <- read.csv("Beers.csv", header=TRUE, sep = ',')

# Merge the data on Beers.Brewery_Id and Breweries.Brew_ _ID then validate merged file
df_merge <- merge(Beers, Breweries, by.x="Brewery_id", by.y = "Brew_ID")
head(df_merge)
tail(df_merge)

#Give the columns more meaningful name
# Rename column 'Name' from Beers data
colnames(df_merge)[2] <- "Beer_name"

# Rename column 'Name' from Breweries data
colnames(df_merge)[8] <- "Brewery_name"
names(df_merge)

#Arrange the columns in more meaningful order
df_merge <- df_merge[c("Brewery_id","Brewery_name","Beer_ID","Beer_name",
                       "ABV","IBU","Style","Ounces", "City","State")]


# Show first 6 and last 6 rows of merged data
head(df_merge,6)
tail(df_merge,6)
```

Question 3


```q3
CountNA <- colSums(is.na(df_merge))
CountNA <- data.frame(CountNA)
CountNA
```

Question 4

```q4
# Remove NA from df_merge
library(ggplot)
df_merge <- na.omit(df_merge)

# Generate the median of alcohol content and international bitterness unit for each State
df_merge_subset <- data.frame(State=df_merge$State, ABV=df_merge$ABV, IBU=df_merge$IBU)

MedianABV <- tapply(df_merge_subset$ABV,df_merge_subset$State,median)
MedianIBU <- tapply(df_merge_subset$IBU,df_merge_subset$State,median)

# Combine with State
plot_frame <- data.frame(MedianABV, MedianIBU)
plot_frame$State <- rownames(plot_frame)
rownames(plot_frame) <- NULL
plot_frame

# Plot MedianABV by State
ggplot(plot_frame, aes(x=State, y=MedianABV)) +
  geom_bar(stat='identity', color='black', fill='light blue') +
  xlab("MedianABV") + ylab("State") +
  ggtitle("MedianABV by State") +
  coord_flip()

# Plot MedianIBU by State  
ggplot(plot_frame, aes(x=State, y=MedianIBU)) +
  geom_bar(stat='identity', color='black', fill='light green') +
  xlab("MedianIBU") + ylab("State") +
  ggtitle("MedianIBU by State") +
  coord_flip()
```

Question 5

```q5
# Sort data to determine State with highest ABV
# Largest ABV is .125 within Kentucky
TopABV <- df_merge_subset[order(-df_merge_subset$ABV),]
TopABV <- TopABV[1,1:2]

# Sort data to determine State with highest IBU
# Largest IBU is 138 within Oregan
TopIBU <- df_merge_subset[order(-df_merge_subset$IBU),]
TopIBU <- TopIBU[1,]
TopIBU[,2] <- NULL
```


Question 6

```q6
summaryABV <- summary(df_merge_subset$ABV)
```

Question 7

```q7
library(ggplot2)
cor(df_merge_subset$IBU, df_merge_subset$ABV)

ggplot(df_merge_subset, aes(x=IBU, y=ABV)) + 
  geom_point(color = "red", size = 3)+
  geom_smooth(method=lm, se = FALSE, color = "black") +
  labs(x="International Bitterness Units of Beer", y="Alcohol by Volume of Beer") + 
  ggtitle("Alchohol by Volume vs International Bitterness Units")
```