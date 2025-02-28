# Gender Pay Gap Overview
Arij Thameur (20 February 2025)

![gender-pay-gap-ratio-15-australian-design-review](https://github.com/user-attachments/assets/0a924c7d-8d2b-469e-9bd0-355e146b281d)

This project is made with R language programme and using the following R librairies.

```r
install.packages(c("tidyverse", "ggplot2", "dplyr", "readr", "knitr", "rmarkdown"))
```

## Introduction

Despite various global efforts to reduce gender inequality in the workplace, significant gaps in pay between men and women persist. Gender equality has been a crucial focus for governments and organizations worldwide, with multiple strategies implemented to tackle this issue. However, progress has been slow, and disparities remain prevalent.

In this report, we will analyze the global gender gap from 2006 to 2022, using quantitative data from "Overall Global Gender Gap Reports" to identify trends and patterns in pay inequality over the years despite the efforts. We will also provide a case study of four countries with strong strategies to combat gender pay gap issues.

## Data exploring

First, I imported the data which contains gender pay gap statistics for women from 1960 to 20222 that I have checked the first few rows and the structure of the data.

```r
# Data loading
library(readxl)
WB_GS <- read_excel("C:/Users/seife/OneDrive/Bureau/dat/WB-GS.xlsx")
View(WB_GS)

# Check the first few rows
head(WB_GS)

# Check the structure of the dataset 
str(gs_data)

```
## Data cleaning
The dataset has the following columns:
Economy ISO3: 3-letter country code.
Economy Name: Country name.
Indicator ID: A unique identifier for each indicator.
Indicator: Description of the indicator (e.g., progression to secondary school, life expectancy).
Attribute 1, 2, 3 and Partner: All are empty columns.
Year columns: Data from 1960 to 2022, where each year is a separate column.

To clean the data i removed the empty columns.
```r
library(dplyr)
library(tidyr)
# Remove empty columns 
WB_GS_clean <- WB_GS %>%
  select(-`Attribute 1`, -`Attribute 2`, -`Attribute 3`, -Partner)
```
Now that the data is understood and sorted, we will proceed to the analysis and finding the results.

## Analysis
First thing I did was summarizing the data since we have multiple entries for each country for different indicators that were considered to calcuulate the average gender pay gap values.

```r
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
For data visualization I used a line plot which is ideal to track changes over time, specially since we're comparing through a long period of time. This will help us identify patterns and understand the data's behavior. Our main focus in this step is to understand the global trend in pay gap over the years.

```r
ggplot(WB_GS_summary, aes(x = Year, y = Average_Pay_Gap, group = `Economy Name`, color = `Economy Name`)) +
  geom_line(alpha = 0.7) +
  labs(title = "Trend of Pay Gap Over the Years", x = "Year", y = "Average Pay Gap") +
  theme_minimal() +
  theme(legend.position = "none")
```
And we found these results.
![Capture d'écran 2025-02-26 225558](https://github.com/user-attachments/assets/19c06d5e-ab8a-4e83-a68f-88039ef7099d)

Even though this data visualization can be representetive of the cnstant fuctuations of pay gap trends over the years it is still not very clear. To acess if gender pay gap has globally increased or decreased over time we need to combine all the different average values of each country and calculate a global average value representetive of the pay gap trend over the years.

So in this step I calculated the global average pay gap for each year by averaging the values of pay gaps across all countries, then visualizing this as a time series. This step provided a genral overview of how the pay gap has evolved globaly from 1960 to 2022 to test our hypothesis.

```r
global_trend <- WB_GS_long %>%
  group_by(Year) %>%
  summarize(global_average_pay_gap = mean(Pay_Gap, na.rm = TRUE))

ggplot(global_trend, aes(x = Year, y = global_average_pay_gap)) +
  geom_line(color = "blue") +
  labs(title = "Global Trend of Pay Gap Over Time", x = "Year", y = "Global Average Pay Gap") +
  theme_minimal()
```

![Capture d'écran 2025-02-28 121410](https://github.com/user-attachments/assets/62fd3fb9-5bca-4ceb-93cb-6f4ce39d27d5)

### Key Observations
#### Initial Stability (1960–1980):

Between 1960 and 1980, the global average pay gap although very high remained relatively stable, hovering around 55%. This indicates that during this period, there was little progress in reducing wage disparities between men and women on a global scale.

#### Significant Drop (1980–1990):
During this perios there was a sharp decline in the pay gap occurred in the 1980s, bringing it down from approximately 55% to below 45%. This period likely reflects significant global efforts toward gender equality, possibly tied to international conventions such as the United Nations' Convention on the Elimination of All Forms of Discrimination Against Women (CEDAW), adopted in 1979.

#### Fluctuations (1990–2000):
All throughout this period, the pay gap experienced some fluctuations. While it continued to decrease overall from a global perspective, there were intermittent periods of stagnation or slight increases. This highligh the uneven progress across regions and countries.The high peak byy the year 2000 is most like ly due to the eraly 2000s recession which the world witnessed a major decline in economic activities which mainly occurred in developped countries but affected many others. For example the European Union introduced a new currency on January 1, 1999. The euro, which was met with much anticipation, had its value immediately plummet, and it continued to be a weak currency throughout 2000 and 2001. This hiiglights the scarcity of women's reality in the work place where their pay is effected first by economic diffuculties.

#### Gradual Decline (2000–2015):
From 2000 onward, the pay gap resumed its downward trend but at a slower pace compared to the sharp drop in the 1980s.This period aligns with increased awareness and policy measures globally, such as pay transparency initiatives and gender equality laws in various countries.

#### Steep Decline (2015–2020):
The most dramatic reduction in the pay gap occurred between 2015 and 2020, with the global average dropping sharply below 40%. This rapid improvement could be attributed to intensified global efforts, including organizations like EPIC and widespread adoption of gender-focused policies in many countries.

To even further the anlysis of this topic; I to see if contries that implimented the most legeslations and strategies to fight gender pay gap to see the impact of that. Based on my research I picked four contries Switzerlan, Germany, Iceland and Japan. iceland for example legally requires companies with 25+ employees to prove equal pay, providing six months of parental leave to both parents at 80% pay and has on of the most heavily subsidizing childcare systems. Switzerland has attracted global attention for its efforts by enshrining gender equality in its constitution and implementing measures to address unexplained wage differences

```r
selected_countries <- c("Switzerland", "Germany", "Iceland", "Japan")
ggplot(WB_GS_summary %>% filter(`Economy Name` %in% selected_countries), 
       aes(x = Year, y = Average_Pay_Gap, group = `Economy Name`, color = `Economy Name`)) +
  geom_line() +
  labs(title = "Trend of Pay Gap for Selected Countries", x = "Year", y = "Average Pay Gap") +
  theme_minimal()
```
![Capture d'écran 2025-02-28 124129](https://github.com/user-attachments/assets/8c3eedff-28cb-4390-9f1a-a86d4fc88511)

We clearly see that the genral trend of the average pay gap is more leaning towards decreasing over time yet we still see different fluctuations in certain years.
So to get a better understanding of how the data represented I used a facet plot of each contry.

```r
selected_countries <- c("Switzerland", "Germany", "Iceland", "Japan")
ggplot(WB_GS_summary %>% filter(`Economy Name` %in% selected_countries), 
       aes(x = Year, y = Average_Pay_Gap, group = `Economy Name`, color = `Economy Name`)) +
  geom_line() +
  labs(title = "Trend of Pay Gap for Selected Countries", x = "Year", y = "Average Pay Gap") +
  theme_minimal()
```
![Capture d'écran 2025-02-28 124410](https://github.com/user-attachments/assets/6976c442-3e00-463b-b9a7-9ce3fa3699c7)

This graph clearly shows that emplementing real strategies works and can enhance the global gender pay gap even though we see a slight rise in average pay gap in Germany and Iceland after 2020. 

## Conclusion 
This project shows an encouraging long-term trend of reducing gender pay gaps globally, with particularly notable improvements since the 1980s. However, periods of stagnation or slower progress highlight persistent challenges that require a multiple facet actions like sustained legislative efforts, cultural shifts, and economic reforms to achieve full parity. Most importantly that despite overall progress fluctuations indicate that progress has not been uniform across all regions or countries. The situation is still very fragile and prone to constant changes. This is due to structural barriers such as unpaid care work, occupational segregation, and cultural norms may have slowed progress at certain times.

## Challenges 
Most studies and data found on the gender pay gap focus exclusively on binary categories—men and women—while largely excluding non-binary individuals and other genders from analysis. This binary approach overlooks wage disparities experienced by individuals across the broader gender spectrum, including transgender and non-binary people, who often face unique challenges in workplaces.


## References
European Commission. (n.d.). Legal aspects of the gender pay gap. Retrieved from https://ec.europa.eu/social/BlobServlet?docId=1775&langId=en
International Labour Organization (ILO). (2024, August 15). More countries affirm support for ending the gender pay gap by 2030. Retrieved from https://www.ilo.org/resource/news/more-countries-affirm-support-ending-gender-pay-gap-2030
OECD. (2024). Communicating gender pay gap reporting rules and results: Making pay transparency work. Retrieved from https://www.oecd.org/content/dam/oecd/en/publications/reports/2024/01/communicating-gender-pay-gap-reporting-rules-and-results_92fcda5a/12e1dd50-en.pdf
World Economic Forum. (2023). Global Gender Gap Report 2023. Retrieved from https://www.weforum.org/publications/global-gender-gap-report-2023/in-full/benchmarking-gender-gaps-2023/
World Economic Forum. (2025, February 20). Explainer: What are pay transparency laws and are they working? Retrieved from https://www.weforum.org/stories/2023/08/gender-pay-gap-salary-transparency/
