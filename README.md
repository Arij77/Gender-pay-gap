# Gender Pay Gap Overview
Arij Thameur (20 February 2025)

![gender-pay-gap-ratio-15-australian-design-review](https://github.com/user-attachments/assets/0a924c7d-8d2b-469e-9bd0-355e146b281d)

This project is made with R language programme and using the following R librairies.

```r
install.packages(c("tidyverse", "ggplot2", "dplyr", "readr", "knitr", "rmarkdown"))
```

## Introduction

Despite various global efforts to reduce gender inequality in the workplace, significant gaps in pay between men and women persist. Gender equality has been a crucial focus for governments and organizations worldwide, with multiple strategies implemented to tackle this issue. However, progress has been slow, and disparities remain prevalent.

In this report, we will analyze the global gender gap from 2006 to 2022, using quantitative data from "Overall Global Gender Gap Reports" to identify trends and patterns in pay inequality over the years despite the efforts.

## Data exploring

First, I imported the data which contains gender pay gap statistics for women from 1960 to 20222 that I have checked the first row of the data.

```r
# Data loading
library(readxl)
WB_GS <- read_excel("C:/Users/seife/OneDrive/Bureau/dat/WB-GS.xlsx")
View(WB_GS)

# Check the first few rows
head(WB_GS)
```

Then I proceded to data cleaning to get a better understanding of it. Moreover, this data contains multiple entries for each countries since it provided statistics of gender pay gap based on multiple indicators so I checked for the data structure to understand the data types and column names then I used the summarize function to simplife it.

```r
# Check the structure of the dataset 
str(gs_data)

# Summary statistics 
summary(WB_GS)
```
These are the results I found for 217 countries 
![Capture d'écran 2025-02-28 111317](https://github.com/user-attachments/assets/70b1bcf5-7a8c-45fb-a440-3ce6aa9671ec)

Then I converted the data from wide format to long format. The Year columns are converted into a single column, and the pay gap values are stored in a new Pay_Gap column. This step is important because long format is more suitable for time series analysis and plotting trends over time. This will simplifie working with the data across multiple years.

```r
WB_GS_long <- WB_GS %>%
  pivot_longer(cols = `1960`:`2022`, names_to = "Year", values_to = "Pay_Gap") %>%
  mutate(Year = as.numeric(Year))
```
Then to summarize the average pay gap by country name and year, I grooped the data by country (Economy Name) and year and used this code to calculate the average pay gap for each country. This analysis focuses on country level evolution of pay gap trends over time so we can see how the global change overtime is represented which will then compare with specefic countries. I also used the na.rm = True function to remove missing values from the calculations to avoid erros.

```r
WB_GS_summary <- WB_GS_long %>%
  group_by(`Economy Name`, Year) %>%
  summarize(Average_Pay_Gap = mean(Pay_Gap, na.rm = TRUE))
```
For data visualization I used a line plot which is ideal to track changes over time, specially since we're comparing through a long period of time. This will help us identify patterns and understand the data's behavior. Our main focus in this step is to understand the global trend in pay gap over the years. nul hypothesis/...?????

```r
ggplot(WB_GS_summary, aes(x = Year, y = Average_Pay_Gap, group = `Economy Name`, color = `Economy Name`)) +
  geom_line(alpha = 0.7) +
  labs(title = "Trend of Pay Gap Over the Years", x = "Year", y = "Average Pay Gap") +
  theme_minimal() +
  theme(legend.position = "none")
```
And we found these results.
[Trend of Pay Gap Over the Years.pdf](https://github.com/user-attachments/files/19026427/Trend.of.Pay.Gap.Over.the.Years.pdf)
