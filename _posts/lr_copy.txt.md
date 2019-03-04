---
layout: post
Title: Valuing NBA Player
---

## Introduction

While I was growing up in San Antonio in the 2000’s, the one professional sports franchise in the area, the Spurs, where one of the most dominant and consistent teams in the NBA. We had [the trio](https://en.wikipedia.org/wiki/Big_Three_(San_Antonio_Spurs)) of Tim Duncan, Tony Parker, and Manu Ginobli, all legendary players in their own right. Between 1999 and 2014, this team brought home 5 championships and were one of the most dominant and consistent teams in American sports. Though I was never a huge sports fan, I could not escape the magnetism of the Spurs, especially if it was a year in which they made it deep into the playoffs. So, when it came time to do a project utilizing regression techniques, I ended up choosing this American pasttime.

My objective was to determine whether a player had performed in manner consistent with their salary. Players are expensive, and accurate appraisal of talent is important in building a strong team. In other words, I wanted to find out which players gave the most bang for your buck. 

## Data Collection

The first step in answering this question was to find the right data. I found this in [basketball-reference.com](https://www.basketball-reference.com/), a wonderfully extensive resource containing anything basketball related that can be quantified. I performed web scraping using Beautiful Soup to collect all data for all teams (including all players) from 2008 to 2018, including salary information and salary cap information (more on why that’s important later). 

<!---
description of data
-->

Though I scraped as much info as I could from the the website, I only utilized a small selection. I focused on the Per 36 minute statistics table and the Advanced Statistics table. (maybe include image here) An example of the per 36 minute statistics is Field Goals per 36 Minutes. An example of an advanced statistic is PER or Player Efficiency Rating, which is calculated from a truly monstrous [equation](https://en.wikipedia.org/wiki/Player_efficiency_rating#Calculation). I used the per 36 minute equations because I wanted to normalize the statistics for time on the court regardless. I also wanted to use the advanced statistics because they represented some pre-engineered features of such high quality that I it would be foolish for me to not take advantage of them.

## Regularization

One drawback of Ordinary Least Squares is that it can be prone to overfitting, especially when fitting on engineered features and polynomial features. LASSO and Ridge regression are two variants of OLS which are widely used in order to mitigate the problem of overfitting. Since I was working with a lot of features (over 50 in fact), I decided to use LASSO to cut out some of my extraneous feature. This cut me down to about 14 features.

## Results

For each year, I identified the most overpaid and underpaid players. A list of the results can be found below:

Underpaid table



Name|Team|Year|Salary|Predicted Salary|Predicted Salary Error|
----|----|----|------|----------------|----------------------|
Shaquille O'Neal|BOS|2011|1,352,000|9,554,000|8,202,000|
Tracy McGrady|ATL|2012|1,352,000|8,405,000|7,053,000|
Tim Duncan|SAS|2013|9,638,000|17,077,000|7,438,000|
DeMarcus Cousins|SAC|2014|4,917,000|12,956,000|8,039,000|
Pau Gasol|CHI|2015|7,128,000|16,611,000|9,483,000|
Pau Gasol|CHI|2016|7,449,000|19,362,000|11,913,000|
Giannis Antetokounmpo|MIL|2017|2,995,000|20,495,000|17,500,000|
Dwyane Wade|MIA|2018|1471000|17,827,000|16356000|

			
Name|Team|Year Salary|Predicted Salary|Predicted Salary Error
Rashard Lewis|WAS|2011|19573711.0|5578000.0|-13996000.0
2012|21136631.0|5537000.0|-15600000.0
Amar'e Stoudemire|NYK|2013|19948799.0|6814000.0|-13135000.0
2014|21679893.0|6537000.0|-15143000.0
2015|23410988.0|8337000.0|-15074000.0
Joe Johnson|BRK|2016|24894863.0|9114000.0|-15781000.0
Chandler Parsons|MEM|2017|22116750.0|5922000.0|-16195000.0
Mike Conley|MEM|2018|28530608.0|11148000.0|-17383000.0





