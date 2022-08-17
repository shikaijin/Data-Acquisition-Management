|||
| :- | :- |
||BSMM-8730 |
||Data Acquisition and Management |
||Lab Assignment |
|||
![](Aspose.Words.0a863b26-b7d4-4a54-8aba-f18c42fdca78.001.png)

ASSIGNMENT DETAILS ![](Aspose.Words.0a863b26-b7d4-4a54-8aba-f18c42fdca78.002.png)

PREPARED BY  ASSIGNMENT TOPIC  DATE ![](Aspose.Words.0a863b26-b7d4-4a54-8aba-f18c42fdca78.003.png)![](Aspose.Words.0a863b26-b7d4-4a54-8aba-f18c42fdca78.004.png)![](Aspose.Words.0a863b26-b7d4-4a54-8aba-f18c42fdca78.005.png)Zoe Jin  Data Preparation  6/15/2022 

BUSINESS PROBLEM ![](Aspose.Words.0a863b26-b7d4-4a54-8aba-f18c42fdca78.006.png)

A coffee company is interested in entering the African market and is planning to invest in a sustainable coffee farm and factory to meet the market's supply needs. The challenge is to choose a country as their first stop on the African market and to establish a farm and factory that is appropriate to meet their sustainable business objectives.

DATA ![](Aspose.Words.0a863b26-b7d4-4a54-8aba-f18c42fdca78.007.png)

DESCRIPTION  SOURCE  DATE ![](Aspose.Words.0a863b26-b7d4-4a54-8aba-f18c42fdca78.008.png)![](Aspose.Words.0a863b26-b7d4-4a54-8aba-f18c42fdca78.009.png)

RETRIEVED List of countries and  https://en.wikipedia.org/wiki/List\_of\_countries\_and\_dependencies\_by\_population  6/15/2022 dependencies by population: A 

list of the population of each 

country and region in 

descending order of size 

List of countries and  https://en.wikipedia.org/wiki/List\_of\_countries\_and\_dependencies\_by\_area 6/15/2022 dependencies by area: a list of 

the countries of the world and 

their dependent territories by 

land, water, and total area 

List of countries by real GDP  https://en.wikipedia.org/wiki/List\_of\_countries\_by\_real\_GDP\_growth\_rate 6/15/2022 growth rate: countries by yearly 

growth rate 1999–2021 

List of countries by GDP  https://en.wikipedia.org/wiki/List\_of\_countries\_by\_GDP\_(nominal)  6/15/2022 (nominal): a list of nominal 

gross domestic product (GDP) 

for each country or region, 

estimated at official 

government exchange rates, in 

millions of US dollars per hour 

TOOLSET ![](Aspose.Words.0a863b26-b7d4-4a54-8aba-f18c42fdca78.010.png)

NAME  VERSION  PURPOSE ![](Aspose.Words.0a863b26-b7d4-4a54-8aba-f18c42fdca78.011.png)![](Aspose.Words.0a863b26-b7d4-4a54-8aba-f18c42fdca78.012.png)

Excel  2205  Import data from a web page and save it as a xlsx file R  4.1.0  Retrieve, consolidate, and cleanse data from xlsx files 

CODE ![](Aspose.Words.0a863b26-b7d4-4a54-8aba-f18c42fdca78.013.png)

getwd() setwd("C:/Users/ROG/OneDrive/BSMM-8730") 

- read Excel files and select some variables of interest in a data set![](Aspose.Words.0a863b26-b7d4-4a54-8aba-f18c42fdca78.014.png)

library(readxl) 

library(tidyverse) 

population <-  read\_excel("List of countries and dependencies by population.xlsx", sheet = "Sovereign states and dependenci") %>%   rename(Country = "Country / Dependency" ) %>%  

`  `filter(Region == "Africa") %>%  

`  `select(Country, Population) 

area <- read\_excel("List of countries and dependencies by area.xlsx", sheet = "Countries and dependencies")[-1,] %>%    rename(Country = "Country / dependency", Water\_in\_km2 = Water in km2 (mi2) ) %>% 

`  `select(Country, Water\_in\_km2) 

gdp\_rate <- read\_excel("List of countries by real GDP growth rate.xlsx", sheet = "Real GDP growth rate (%)") %>%    rename(Country = "Country", Avg\_GDP\_Growth\_Rate="Avg.") %>%  

`  `select(Country, Avg\_GDP\_Growth\_Rate) 

gdp <- read\_excel("List of countries by GDP.xlsx", sheet = "GDP (US$ million) by country")[-c(1:2),] %>%    rename(Country="Country/Territory",  GDP(US million)  = "IMF[1][12] Estimate") %>%  

`  `filter( UN Region == "Africa") %>%  

`  `select(Country,  GDP(US million) ) 

- use a regular expression to clean the data in a data frame column gdp$ GDP(US million)  < - gsub(",", "", gdp$ GDP(US million) ) 

area$Country <- gsub("Republic of the Congo", "Congo", area$Country) area$Country <- gsub("Democratic Congo", "DR Congo", area$Country) area$Water\_in\_km2 <- gsub(" .\*", "", area$Water\_in\_km2) area$Water\_in\_km2 <- gsub(",", "", area$Water\_in\_km2) 

gdp\_rate$Country <- gsub("Democratic Republic of the Congo", "DR Congo", gdp\_rate$Country) gdp\_rate$Country <- gsub("Republic of Congo", "Congo", gdp\_rate$Country) 

gdp\_rate$Country <- gsub("Cabo", "Cape", gdp\_rate$Country) 

- merge the data frames  

innerjoin <- merge(population, gdp\_rate, by = "Country") %>%    merge(gdp, by="Country") 

leftjoin <- merge(innerjoin, area, by.x = "Country") 

- add GDP per capita column 

africa <- mutate(leftjoin, GDP\_per\_capita = as.numeric( GDP(US million) )\*1000000/Population) str(africa) 

- check missing values 

apply(leftjoin, 2, function(x) sum(is.na(x))) 

- change data structure 

africa$ GDP(US million)  <- as.numeric(africa$ GDP(US million) ) africa$Water\_in\_km2  <- as.numeric(africa$Water\_in\_km2 ) 

- sort data in descending order of water area, return the first parts of data frame and save data frame as an image library(knitr) 

library(kableExtra) ![](Aspose.Words.0a863b26-b7d4-4a54-8aba-f18c42fdca78.015.png)

africa %>%  ![](Aspose.Words.0a863b26-b7d4-4a54-8aba-f18c42fdca78.014.png)

`  `arrange(desc(Water\_in\_km2) %>%    head() %>%  

`  `kable() %>% 

`  `kable\_styling()  

- generate bar chars 

ggplot(head(arrange(africa, desc(Water\_in\_km2))), aes(x=Country, y=Avg\_GDP\_Growth\_Rate))+     geom\_col() + 

`    `ylab("Avg GDP Growth Rate")+ 

`    `ggtitle("Average GDP growth rate of the top 6 water area countries in Africa") 

ggplot(head(arrange(africa, desc(Water\_in\_km2))), aes(x=Country, y= GDP(US million) ))+   geom\_col() + 

`  `ylab("GDP(US million)")+ 

`  `ggtitle("GDP(US million) of the top 6 water area countries in Africa") 

ggplot(head(arrange(africa, desc(Water\_in\_km2))), aes(x=Country, y=GDP\_per\_capita))+   geom\_col() + 

`  `ylab("GDP per capita")+ 

`  `ggtitle("GDP per capita of the top 6 water area countries in Africa") 

OUTPUT ![](Aspose.Words.0a863b26-b7d4-4a54-8aba-f18c42fdca78.016.png)![](Aspose.Words.0a863b26-b7d4-4a54-8aba-f18c42fdca78.015.png)

![](Aspose.Words.0a863b26-b7d4-4a54-8aba-f18c42fdca78.017.png)

|||
| :- | :- |
|||
![](Aspose.Words.0a863b26-b7d4-4a54-8aba-f18c42fdca78.018.jpeg)

![](Aspose.Words.0a863b26-b7d4-4a54-8aba-f18c42fdca78.019.jpeg)

EXPLANATION ![](Aspose.Words.0a863b26-b7d4-4a54-8aba-f18c42fdca78.020.png)

As the original datasets have different header names in the country column, they need to be renamed when loading the data to make merging easier. Because the business question relates to African countries, I need to include rows with Africa as the region. Often, we are only interested in a few variables within a dataset. In this instance, I choose the column that should be focused on the variable. 

The data cleaning process enabled me to unify the names of some countries in the different datasets, and clear some symbols so I would not lose some rows of data after the merge.![](Aspose.Words.0a863b26-b7d4-4a54-8aba-f18c42fdca78.015.png)

|<p>After merging the four variables, I decided to create a new variable based on the existing variables, namely GDP per capita. This new variable will serve as an additional dimension for later comparisons. It is also advisable to convert GDP and Water in km2 to numeric so that they can be sorted later. </p><p>In order to accomplish the goal of investing in a sustainable coffee farm and factory, water area need to be considered, so data is sorted according to water area in descending order. I am only interested in a few countries with large water areas, so I return the first part of the data set, namely the six African countries with the largest water resources. </p><p>As a result, the final dataset contains six columns: country, population, average GDP growth rate, GDP, water area, and GDP per capita. When choosing a suitable country to invest in, it is important to consider the average GDP growth rate, GDP, and the GDP per capita. </p><p>According to the above bar charts, Ethiopia, Tanzania, and the Congo have the highest GDPs and average GDP growth rates. The top three countries by GDP per capita are Tanzania, Uganda, and Ethiopia, in that order.</p><p>CONCLUSIONS/RECOMMENTATIONS </p>|
| :- |
|The water area of Ethiopia is second in Africa without much difference from Sudan, which is first, indicating the availability of water resources for coffee production. The country's GDP and average GDP growth rate are listed first in the table, which means that Ethiopia has the best economic growth out of all the countries in the table, which means that companies are likely to be more profitable there. Furthermore, its GDP per capita is 894, which suggests that labor costs are not high. The coffee company can choose to invest in a coffee farm and factory in Ethiopia as a first point of entry into the African market. |

Page 9 
