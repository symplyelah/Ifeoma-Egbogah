---
title: "English Premier League Managers"
date: 2023-02-22
author: "Ifeoma Egbogah"
draft: false
# layout options: single, single-sidebar
layout: single-sidebar
categories:
- project, football
---



## Premier League Managerial Ranking Pt 1

Recently, I saw a post ranking the English Premier League (EPL) managers based on there winning percentage and I thought, hey that's not fair, there are many awesome managers whose winning averages aren't great because they haven't had the chance to manage a lot of football (English name for soccer) games. Does that make them terrible managers? Just imagine comparing a guy who has over seen 16 wins out of 80 games with someone who has 70 wins in 350 games. Both have a winning average of 20%, does that mean they are equals? 

Well time to find out, as I use bayesian inference to determine who is/was the best manager in the Premier League. Hope it's Arsene Wenger given I am a huge Arsenal fan. Gunners for life!

Let's start by loading in the data and packages into R. Data was gotten from the premier league website (www.premierleague.com). So far 219 managers have debuted in the league since its inception in 1992 to 2018.

{{< panelset class = "greetings" >}}
{{< panel name = "Packages" >}}


```r
library(tidyverse)
```

```
## -- Attaching packages --------------------------------------- tidyverse 1.3.1 --
```

```
## v ggplot2 3.3.5     v purrr   0.3.4
## v tibble  3.1.6     v dplyr   1.0.8
## v tidyr   1.2.0     v stringr 1.4.0
## v readr   2.1.2     v forcats 0.5.1
```

```
## -- Conflicts ------------------------------------------ tidyverse_conflicts() --
## x dplyr::filter() masks stats::filter()
## x dplyr::lag()    masks stats::lag()
```

```r
library(lubridate)
```

```
## 
## Attaching package: 'lubridate'
```

```
## The following objects are masked from 'package:base':
## 
##     date, intersect, setdiff, union
```

```r
library(ggthemes)
library(janitor)
```

```
## 
## Attaching package: 'janitor'
```

```
## The following objects are masked from 'package:stats':
## 
##     chisq.test, fisher.test
```

```r
library(here)
```

```
## here() starts at C:/Users/Egbogah-2/Documents/Rworks/rsites/Ifeoma-Egbogah
```

```r
library(scales)
```

```
## 
## Attaching package: 'scales'
```

```
## The following object is masked from 'package:purrr':
## 
##     discard
```

```
## The following object is masked from 'package:readr':
## 
##     col_factor
```

```r
library(scico)
theme_set(theme_light())

epl <- clean_names(read_csv(here("content", "project", "English Premier League Managers", "EPL.csv")))
```

```
## Rows: 219 Columns: 11
```

```
## -- Column specification --------------------------------------------------------
## Delimiter: ","
## chr  (4): Managers, Clubs, Nationality, Debut_Match
## dbl  (5): Total.Games.Managed, Games_Won, Games_Drawn, Games_Lost, Num_of_PL...
## date (2): Date_of_birth, PL_Debut
## 
## i Use `spec()` to retrieve the full column specification for this data.
## i Specify the column types or set `show_col_types = FALSE` to quiet this message.
```

```r
glimpse(epl)
```

```
## Rows: 219
## Columns: 11
## $ managers            <chr> "Eddie Howe", "Brue Rioch", "Arsene Wenger", "Stew~
## $ clubs               <chr> "Bournemouth", "Arsenal", "Arsenal", "Arsenal", "A~
## $ total_games_managed <dbl> 114, 38, 828, 19, 343, 1, 45, 89, 144, 20, 10, 249~
## $ games_won           <dbl> 34, 17, 476, 7, 148, 1, 19, 21, 53, 2, 3, 118, 12,~
## $ games_drawn         <dbl> 30, 12, 199, 4, 82, 0, 5, 19, 39, 6, 2, 65, 20, 52~
## $ games_lost          <dbl> 50, 9, 153, 8, 104, 0, 21, 49, 52, 12, 5, 66, 22, ~
## $ num_of_pl_seasons   <dbl> 3, 1, 22, 2, 7, 1, 3, 3, 4, 1, 3, 7, 2, 4, 2, 3, 2~
## $ nationality         <chr> "England", "Scotland", "France", "Scotland", "Engl~
## $ date_of_birth       <date> 1977-11-29, 1947-09-06, 1949-10-22, 1949-08-20, 1~
## $ pl_debut            <date> 2015-08-08, 1995-08-20, 1996-10-12, 1995-02-21, 1~
## $ debut_match         <chr> "Lost", "Drawn", "Won", "Won", "na", "Won", "Won",~
```

{{< /panel >}}
{{< /panelset >}}

## Data Exploration
### Managers' Age on Debut

Let's start by getting to know the data and see what information we can glean from it. This data covers from 1992 to 2018. I begin by calculating the ages of the managers and the mean age at which managers make a debut in the league. The youngest managers in the league were __Chris Coleman__ and __Attilio Lombardo__ while the oldest was __Dick Advocaat__, making a debut at ages __32__ and __67 years__ respectively. The mean age was __45 years__.

{{< panelset class = "greetings" >}}
{{< panel name = "Code" >}}

```r
epl <- epl%>%
  mutate(age = year(as.period(interval((date_of_birth), (pl_debut)))))

epl%>%
  summarise(mean_age = mean(age),
            youngest = min(age),
            oldest = max(age))
```

```
## # A tibble: 1 x 3
##   mean_age youngest oldest
##      <dbl>    <dbl>  <dbl>
## 1     45.9       32     67
```

```r
age_table <- epl%>%
  select(managers, age)%>%
  filter(age %in% c(32, 67))
```

{{< /panel >}}
{{< panel name = "Table" >}}


```r
knitr::kable(age_table, col.names = c("Managers", "Age"))
```



|Managers         | Age|
|:----------------|---:|
|Attilio Lombardo |  32|
|Chris Coleman    |  32|
|Dick Advocaat    |  67|

{{< /panel >}}
{{< /panelset >}}

Lets get a better view of what the age distribution looks like by plotting a histogram. 

{{< panelset class = "greetings" >}}
{{< panel name = "Code" >}}

```r
age <- epl%>%
ggplot(aes(age)) + 
  geom_histogram(binwidth = 2, fill = scales::muted("darkorchid"), alpha = 0.8) +
  labs(x = "Age",
      y = "Count",
      title = "Distribution of Managers' Age on Premier League Debut")
```

{{< /panel >}}
{{< panel name = "Distribution" >}}


```r
age
```

<img src="/project/English Premier League Managers/English-Premier-League-Managers-Pt-1_files/figure-html/unnamed-chunk-4-1.png" width="672" />

{{< /panel >}}
{{< /panelset >}}


### Managers' Nationality

What is the most common nationality of premier league managers? 

{{< panelset class = "greetings" >}}
{{< panel name = "Code" >}}

```r
nationality <- epl %>%
  count(nationality, sort=TRUE) %>%
 # mutate(nationality = fct_reorder(nationality, n))%>%
  ggplot(aes(nationality, n)) + 
  geom_col(aes(fill = nationality)) + 
  labs(x = "Country of Origin",
       y = "Count", 
       title = "Country of Origin of Premier League Managers") +
  scale_fill_scico_d(palette = "bam") +
  theme(legend.position = "none") + 
  coord_flip()
```
{{< /panel >}}
{{< panel name = "Nationality" >}}


```r
nationality
```

<img src="/project/English Premier League Managers/English-Premier-League-Managers-Pt-1_files/figure-html/unnamed-chunk-6-1.png" width="2400" />

{{< /panel >}}
{{< /panelset >}}

Err... it looks a bit scattered let's arrange it in descending order based on there percentage so as to get a clearer picture.

{{< panelset class = "greetings" >}}
{{< panel name = "Code" >}}

```r
national <- epl%>%
  count(nationality, sort=TRUE) %>%
  mutate(percent = (n/sum(n))) %>%
  mutate(country = fct_reorder(nationality, percent))%>%
  ggplot(aes(country, percent)) + 
  geom_col(aes(fill = country)) + 
  labs(x = "Country of Origin",
       y = "Percentage",
       title = "Country of Origin for Premier League Managers") +
  scale_fill_scico_d(palette = "bam") +
  scale_y_continuous(labels = label_percent()) +
  theme(legend.position = "none") +
  coord_flip()
```
{{< /panel >}}
{{< panel name = "Origin" >}}


```r
national
```

<img src="/project/English Premier League Managers/English-Premier-League-Managers-Pt-1_files/figure-html/unnamed-chunk-8-1.png" width="2400" />

{{< /panel >}}
{{< /panelset >}}


That's much better. Here we can see that most managers in the league are __English__ with 51%, followed by the __Scots__ at 13%. __Uruguay, United States, Switzerland, Sweden, Israel, Denmark, Chile and Brazil__ have each had one manager from these countries debuting in the premier league.

### Nationality of Managers Overseeing the Top Flight Clubs
A popular opinion among sports pundits is that English managers haven't been given the chance to over see the top flight clubs in the league. How true is this? Let's find out.


{{< panelset class = "greetings" >}}
{{< panel name = "Code" >}}


```r
all_club <- epl%>%
  count(nationality, clubs, sort = TRUE)%>%
  group_by(clubs)%>%
  mutate(total = sum(n))%>%
  ggplot(aes(fct_reorder(clubs, total), n)) + 
  geom_col(aes(fill= nationality)) +
  labs(x = "Country of Origin",
       y = "Counts",
       fill = "Nationality",
       title = "Nationality of the Premier League Managers for Each Clubs") +
  scale_fill_scico_d(palette = "bam") +
  scale_y_continuous(breaks = seq(2, 12, 2)) +
  coord_flip() 


top_club <- epl%>%
  filter(clubs %in% c("Arsenal", "Chelsea", "Manchester United", "Manchester City", "Liverpool", "Tottenham Hotspur"))%>%
  count(nationality, clubs, sort = TRUE)%>%
  group_by(clubs)%>%
  mutate(total = sum(n))%>%
  ggplot(aes(fct_reorder(clubs, total), n)) + 
  geom_col(aes(fill= nationality)) +
  labs(x = "Country of Origin",
       y = "Counts",
       fill = "Nationality",
       title = "Nationality of the Premier League Managers for the Top Flight Clubs") +
  scale_fill_scico_d(palette = "bam") +
  scale_y_continuous(breaks = seq(2, 12, 2)) +
  coord_flip() 
```

{{< /panel >}}
{{< panel name = "All Clubs" >}}

```r
all_club
```

<img src="/project/English Premier League Managers/English-Premier-League-Managers-Pt-1_files/figure-html/unnamed-chunk-10-1.png" width="2400" />

{{< /panel >}}
{{< panel name = "Top Six" >}}

```r
top_club
```

<img src="/project/English Premier League Managers/English-Premier-League-Managers-Pt-1_files/figure-html/unnamed-chunk-11-1.png" width="672" />

{{< /panel >}}
{{< /panelset >}}

The bar chart above shows that all the top 6 clubs except Manchester United and Arsenal have had an English Manager in charge. Manchester united not having an English manager since the creation of the league is partially because Sir Alex Ferguson (who debuted at the club in 1986) was the manager of the club before the league was created and he spent over 20 years at the helm of affairs in the club before handing over at the end of 2012/13 season.


## Match Result on Premier League Debut

David O'Leary and Martin O'Neill had no information on their English premier league debut. So I'll filter them out. Most managers lost on their first English premier league debut (42.5%).

{{< panelset class = "greetings" >}}
{{< panel name = "Code" >}}

```r
debut <- epl %>%
  filter(debut_match != "na") %>%
  count(debut_match, sort=TRUE) %>%
  mutate(percent = (n/sum(n)))%>%
  ggplot(aes(percent, debut_match)) +
  geom_col(fill = scales::muted("darkorchid"), alpha = 0.8) +
  scale_x_continuous(labels = scales::label_percent())+
  labs(x = "Percent (%)",
       y = "Debut Match",
       title = "Percentage of Managers' Debut Match Win, Loss or Draw",
       subtitle = "They say the English League is very intense and quite competitive.\nFamous managers like Arsene Wenger, Josep Guardiola, Jose Mourinho won their Premier League debut match.\nSir Alex lost his debut macth while Jurgen Klopp drew his debut match.") +
  theme(plot.title = element_text(hjust = 0.5),
        plot.subtitle = element_text(face = "italic", colour = "darkorchid"))
```
{{< /panel >}}
{{< panel name = "Debut" >}}


```r
debut
```

<img src="/project/English Premier League Managers/English-Premier-League-Managers-Pt-1_files/figure-html/unnamed-chunk-13-1.png" width="2400" />

{{< /panel >}}
{{< /panelset >}}

Out of the 112 English managers in the league 50 lost their debut matches. So far no Argentine, German, Portuguese or Spanish managers have lost their debut match.

{{< panelset class = "greetings" >}}
{{< panel name = "Code" >}}

```r
debut_nat <- epl %>%
  filter(debut_match != "na") %>%
  count(debut_match, nationality, sort=TRUE)%>%
  group_by(nationality)%>%
  mutate(total = sum(n))%>%
  ggplot(aes(fct_reorder(nationality, total), n)) + 
  geom_col(aes(fill = debut_match)) +
  coord_flip() +
  scale_fill_scico_d(palette = "bam", direction = -1) +
  scale_y_continuous(breaks = seq(0, 130, 20)) +
  labs(x = "Nationality of Managers",
       y = "No of Matches",
       title = "Number of Matches Won, Drawn, or Lost by Managers on Debut Day",
       fill = "Match Result")
```
{{< /panel >}}
{{< panel name = "Match Result" >}}


```r
debut_nat
```

<img src="/project/English Premier League Managers/English-Premier-League-Managers-Pt-1_files/figure-html/unnamed-chunk-15-1.png" width="2400" />
{{< /panel >}}
{{< /panelset >}}
  
Out of the 112 English managers in the league 50 lost their debut matches. So far no Argentine, German, Portuguese or Spanish managers have lost their debut match.
  

Now that we have explored our data, we will begin analyzing in the next post.
