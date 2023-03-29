---
title: "English Premier League Managers"
weight: 2
subtitle: "Data Exploration"
excerpt: "I recently came across a post that ranked __English Premier League (EPL)__ managers based on their winning percentages. I felt that this approach was unfair to some exceptional managers whose winning averages may not accurately reflect their capabilities due to limited opportunities to manage games."
date: 2023-03-29
draft: false
editor_options: 
  chunk_output_type: console
---

{{< here >}}


## What in the world!!!

I recently came across a post that ranked __English Premier League (EPL)__ managers based on their winning percentages. I felt that this approach was unfair to some exceptional managers whose winning averages may not accurately reflect their capabilities due to limited opportunities to manage games. For example, comparing a manager who oversaw 16 wins out of 80 games to one with 70 wins out of 350 games, both with a winning average of 20%, would be unfair.

To address this, I decided to use Bayesian inference to determine the best manager in the Premier League, hoping that it would be Arsene Wenger, as I am a devoted Arsenal fan. Fingers crossed.

As a first step, I loaded the relevant data and packages into R, with the data obtained from the Premier League website (www.premierleague.com). From the league's inception in 1992 to 2018, a total of 219 managers made their debut in the EPL.



{{< panelset class = "greetings" >}}
{{< panel name = "Data" >}}


```
## Selecting by debut_match
```



|Managers            |Clubs                | Total Games Managed| Games Won| Games Drawn| Games Lost| Num Of Pl Seasons|Nationality |Date Of Birth |Pl Debut   |Debut Match |
|:-------------------|:--------------------|-------------------:|---------:|-----------:|----------:|-----------------:|:-----------|:-------------|:----------|:-----------|
|Arsene Wenger       |Arsenal              |                 828|       476|         199|        153|                22|France      |1949-10-22    |1996-10-12 |Won         |
|Stewart Houston     |Arsenal              |                  19|         7|           4|          8|                 2|Scotland    |1949-08-20    |1995-02-21 |Won         |
|Jim Barron          |Aston Villa          |                   1|         1|           0|          0|                 1|England     |1943-10-19    |1994-11-19 |Won         |
|Tim Sherwood        |Aston Villa          |                  45|        19|           5|         21|                 3|England     |1969-02-06    |2013-12-22 |Won         |
|Kevin MacDonald     |Aston Villa          |                  10|         3|           2|          5|                 3|Scotland    |1960-11-22    |1994-11-23 |Won         |
|Alex McLeish        |Aston Villa          |                 138|        33|          52|         53|                 4|Scotland    |1959-01-21    |2007-12-02 |Won         |
|Ray Harford         |Blackburn            |                  48|        18|          11|         19|                 2|England     |1945-06-01    |1995-08-19 |Won         |
|Paul Ince           |Blackburn            |                  17|         3|           4|         10|                 1|England     |1967-10-21    |2008-08-16 |Won         |
|Antonio Conte       |Chelsea              |                  76|        51|          10|         15|                 2|Italy       |1969-07-31    |2016-08-15 |Won         |
|Carlo Ancelotti     |Chelsea              |                  76|        48|          13|         15|                 2|Italy       |1959-06-10    |2009-08-15 |Won         |
|Felipe Scolari      |Chelsea              |                  25|        14|           7|          4|                 1|Brazil      |1948-11-09    |2008-08-17 |Won         |
|Guus Hiddink        |Chelsea              |                  34|        18|          12|          4|                 2|Netherlands |1946-11-08    |2009-02-21 |Won         |
|Eddie Newton        |Chelsea              |                   1|         1|           0|          0|                 1|England     |1971-12-13    |2015-12-19 |Won         |
|Ray Wilkins         |Chelsea              |                  67|        23|          11|         33|                 3|England     |1956-09-14    |1994-11-19 |Won         |
|Bobby Gould         |Coventry             |                  54|        16|          19|         19|                 2|Wales       |1946-01-12    |1992-08-15 |Won         |
|Roy Hodgson         |Crystal Palace       |                 250|        86|          65|         99|                 8|England     |1947-08-09    |1997-08-09 |Won         |
|Ian Holloway        |Crystal Palace       |                  46|        11|           9|         26|                 2|England     |1963-03-12    |2010-08-14 |Won         |
|Colin Todd          |Derby County         |                  70|        19|          16|         35|                 3|England     |1948-12-12    |1996-01-13 |Won         |
|John Gregory        |Derby County         |                 162|        64|          48|         50|                 5|England     |1954-05-11    |1998-02-28 |Won         |
|Paul Jewell         |Derby County         |                 138|        34|          28|         76|                 4|England     |1964-09-28    |1999-08-07 |Won         |
|Mike Walker         |Everton              |                  98|        37|          27|         34|                 3|Wales       |1945-11-28    |1992-08-15 |Won         |
|Sam Allardyce       |Everton              |                 512|       174|         138|        200|                16|England     |1954-10-19    |2001-08-18 |Won         |
|Roberto Martinez    |Everton              |                 265|        81|          77|        107|                 7|Spain       |1973-07-13    |2009-08-15 |Won         |
|David Unsworth      |Everton              |                   6|         3|           1|          2|                 2|England     |1973-10-16    |2016-05-15 |Won         |
|Marco Silva         |Everton              |                  42|        13|           8|         21|                 2|Portugal    |1977-07-12    |2017-01-14 |Won         |
|Chris Coleman       |Fulham               |                 152|        50|          39|         63|                 5|Wales       |1970-06-10    |2003-04-19 |Won         |
|David Wagner        |Huddersfield Town    |                  38|         9|          10|         19|                 1|Germany     |1971-10-19    |2017-08-12 |Won         |
|Phil Brown          |Hull City            |                  67|        13|          20|         34|                 2|England     |1959-05-30    |2008-08-16 |Won         |
|Mike Phelan         |Hull City            |                  20|         3|           4|         13|                 1|England     |1962-09-24    |2016-08-13 |Won         |
|Dave Bassett        |Leicester City       |                 131|        27|          43|         61|                 5|England     |1944-09-04    |1992-08-15 |Won         |
|Craig Shakespeare   |Leicester City       |                  21|         8|           5|          8|                 2|England     |1963-10-26    |2017-02-27 |Won         |
|Alan Ball           |Manchester City      |                  98|        28|          33|         37|                 3|England     |1945-05-12    |1994-01-15 |Won         |
|Brain Kidd          |Manchester City      |                  25|         6|          11|          8|                 2|England     |1949-05-29    |1998-12-05 |Won         |
|Sven-Goran Eriksson |Manchester City      |                  38|        15|          10|         13|                 1|Sweden      |1948-02-05    |2007-08-11 |Won         |
|Roberto Mancini     |Manchester City      |                 133|        82|          27|         24|                 4|Italy       |1964-11-27    |2009-12-26 |Won         |
|Josep Guardiola     |Manchester City      |                  76|        55|          13|          8|                 2|Spain       |1971-01-18    |2016-08-13 |Won         |
|Jose Mourinho       |Manchester United    |                 288|       183|          65|         40|                 4|Portugal    |1963-01-26    |2004-08-15 |Won         |
|Ryan Giggs          |Manchester United    |                   4|         2|           1|          1|                 1|Wales       |1973-11-29    |2014-04-26 |Won         |
|John Carver         |Newcastle United     |                  20|         4|           4|         12|                 2|England     |1965-01-16    |2004-09-11 |Won         |
|Brian Clough        |Nottingham Forest    |                  42|        10|          10|         22|                 1|England     |1935-03-21    |1992-08-16 |Won         |
|Frank Clark         |Nottingham Forest    |                  98|        39|          31|         28|                 3|England     |1942-09-09    |1994-08-20 |Won         |
|Velimir Zajec       |Portsmouth           |                  18|         4|           4|         10|                 1|Croatia     |1956-02-12    |2004-11-27 |Won         |
|Alain Perrin        |Portsmouth           |                  20|         4|           6|         10|                 2|France      |1956-10-07    |2005-04-09 |Won         |
|Paul Hart           |Portsmouth           |                  27|         6|           6|         15|                 2|England     |1953-05-04    |2009-02-14 |Won         |
|Paul Sturrock       |Southamptom          |                  13|         5|           2|          6|                 2|Scotland    |1956-10-10    |2004-03-14 |Won         |
|Mark Hughes         |Southamptom          |                 452|       157|         121|        174|                14|Wales       |1963-11-01    |2004-09-18 |Won         |
|Howard Wilkinson    |Sunderland           |                 189|        66|          57|         66|                 6|England     |1943-11-13    |1992-08-15 |Won         |
|Roy Keane           |Sunderland           |                  53|        15|           9|         29|                 2|Ireland     |1971-08-10    |2007-08-11 |Won         |
|Michael Laudrup     |Swansea City         |                  62|        17|          19|         26|                 2|Denmark     |1964-06-15    |2012-08-18 |Won         |
|Garry Monk          |Swansea City         |                  67|        24|          16|         27|                 3|England     |1979-03-06    |2014-02-08 |Won         |
|Francesco Guidolin  |Swansea City         |                  23|         8|           5|         10|                 2|Italy       |1955-10-03    |2016-01-24 |Won         |
|Paul Clement        |Swansea City         |                  37|        12|           5|         20|                 2|England     |1972-01-08    |2017-01-03 |Won         |
|Carlos Carvalhal    |Swansea City         |                  18|         5|           5|          8|                 1|Portugal    |1965-12-04    |2017-12-30 |Won         |
|Osvaldo Ardiles     |Tottenham Hotspur    |                  54|        16|          14|         24|                 2|Argentina   |1952-08-03    |1993-08-14 |Won         |
|Chistian Gross      |Tottenham Hotspur    |                  26|         9|           7|         10|                 2|Switzerland |1954-08-14    |1997-11-29 |Won         |
|Alan Pardew         |West Bromwich Albion |                 320|       109|          68|        143|                10|England     |1961-07-18    |2005-08-13 |Won         |
|David Moyes         |West Ham United      |                 526|       204|         144|        178|                15|Scotland    |1963-04-25    |2002-03-16 |Won         |
|Trevor Brooking     |West Ham United      |                   3|         2|           1|          0|                 1|England     |1948-10-02    |2003-04-27 |Won         |
|Gianfranco Zola     |West Ham United      |                  72|        20|          20|         32|                 2|Italy       |1966-07-05    |2008-09-20 |Won         |
|Manuel Pellegrini   |West Ham United      |                 114|        70|          21|         23|                 3|Chile       |1953-09-16    |2013-08-19 |Won         |
|Slaven Bilic        |West Ham United      |                  87|        30|          26|         31|                 3|Croatia     |1968-09-11    |2015-08-09 |Won         |
|Egil Roger Olsen    |Wimbledon            |                  38|         7|          12|         19|                 1|Norway      |1942-04-22    |1999-08-07 |Won         |

{{< /panel >}}
{{< /panelset >}}

## I spy?
### Managers' Age on Debut

Let's start by getting to know the data and see what information we can glean from it. This data covers from 1992 to 2018. I begin by calculating the ages of the managers and the mean age at which managers make a debut in the league. The youngest managers in the league were __Chris Coleman__ and __Attilio Lombardo__ while the oldest was __Dick Advocaat__, making a debut at ages __32__ and __67 years__ respectively. The mean age was __45 years__.

{{< panelset class = "greetings" >}}
{{< panel name = "Age" >}}

Table: Table 1: Youngest and Oldest Managers

|Managers         | Age|
|:----------------|---:|
|Attilio Lombardo |  32|
|Chris Coleman    |  32|
|Dick Advocaat    |  67|

{{< /panel >}}
{{< panel name = "Code" >}}

```r
epl <- epl%>%
  mutate(age = year(as.period(interval((date_of_birth), (pl_debut)))))

summary_epl <- epl%>%
  summarise(mean_age = mean(age),
            youngest = min(age),
            oldest = max(age))

age_table <- epl%>%
  select(managers, age)%>%
  filter(age %in% c(32, 67))

knitr::kable(age_table, col.names = c("Managers", "Age"), caption = "Youngest and Oldest Managers")
```



Table: Table 2: Youngest and Oldest Managers

|Managers         | Age|
|:----------------|---:|
|Attilio Lombardo |  32|
|Chris Coleman    |  32|
|Dick Advocaat    |  67|

{{< /panel >}}
{{< /panelset >}}

Lets get a better view of what the age distribution looks like by plotting a histogram. 

{{< panelset class ="greetings" >}}
{{< panel name = "Age Distribution" >}}

<img src="DATAEX~1/figure-html/Age Distribution-1.png" width="2400" />

{{< /panel >}}
{{< panel name = "Code" >}}

```r
age <- epl%>%
ggplot(aes(age)) + 
  geom_histogram(binwidth = 2, fill = scales::muted("darkorchid"), alpha = 0.8) +
  labs(x = "Age",
      y = "Count",
      title = "Distribution of Managers' Age on Premier League Debut") +
  theme(plot.title = element_text(hjust = 0.5, size = 50),
        axis.title = element_text(size = 30),
        axis.text = element_text(size = 25))

age
```
{{< /panel >}}
{{< /panelset  >}}


### Where are they from?

One thing the English League is known for is the wide range in the nationality of not only the players but also the managers of the clubs. So, What is the most common nationality of premier league managers? Let's find out.

{{< panelset class = "greetings" >}}
{{< panel name = "Nationality" >}}
<img src="DATAEX~1/figure-html/Nationality-1.png" width="2400" />
{{< /panel >}}
{{< panel name = "Code" >}}

```r
nationality <- epl %>%
  count(nationality, sort=TRUE) %>%
 # mutate(nationality = fct_reorder(nationality, n))%>%
  ggplot(aes(nationality, n)) + 
  geom_col(aes(fill = nationality)) + 
  labs(x = "Country of Origin",
       y = "Count", 
       title = "Nationality of Premier League Managers") +
  scale_fill_scico_d(palette = "bam") +
  theme(legend.position = "none",
        plot.title = element_text(hjust = 0.5, size = 50),
        axis.title = element_text(size = 30),
        axis.text = element_text(size = 25)) + 
  coord_flip()

nationality
```

{{< /panel >}}
{{< /panelset >}}

Err... it looks a bit scattered let's arrange it in descending order based on there percentage so as to get a clearer picture.

{{< panelset class = "greetings" >}}
{{< panel name = "Nationality Arranged" >}}
<img src="DATAEX~1/figure-html/Origin-1.png" width="2400" />
{{< /panel >}}
{{< panel name = "Code" >}}

```r
Origin <- epl%>%
  count(nationality, sort=TRUE) %>%
  mutate(percent = (n/sum(n))) %>%
  mutate(country = fct_reorder(nationality, percent))%>%
  ggplot(aes(country, percent)) + 
  geom_col(aes(fill = country)) + 
  labs(x = "Country of Origin",
       y = "Percentage",
       title = "Nationality for Premier League Managers") +
  scale_fill_scico_d(palette = "bam") +
  scale_y_continuous(labels = label_percent()) +
  theme(legend.position = "none",
        plot.title = element_text(hjust = 0.5, size = 50),
        axis.title = element_text(size = 30),
        axis.text = element_text(size = 25)) +
  coord_flip()


Origin  
```
{{< /panel >}}
{{< /panelset >}}


That's much better. Here we can see that most managers in the league are __English__ with 51%, followed by the __Scots__ at 13%. __Uruguay, United States, Switzerland, Sweden, Israel, Denmark, Chile and Brazil__ have each had one manager from these countries debuting in the premier league.

### Who Runs the Top Flight Clubs?
A popular opinion among sports pundits is that English managers haven't been given the chance to over see the top flight clubs in the league. How true is this? Let's find out.


{{< panelset class = "greetings" >}}
{{< panel name = "Club" >}}
<img src="DATAEX~1/figure-html/Clubs-1.png" width="2400" /><img src="DATAEX~1/figure-html/Clubs-2.png" width="2400" />

{{< /panel >}}
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
  coord_flip() +
  theme(plot.title = element_text(hjust = 0.5, size = 50),
        axis.title = element_text(size = 30),
        axis.text = element_text(size = 25),
        legend.title = element_text(size = 40),
        legend.key.size  = unit(3, "cm"),
        legend.text = element_text(size = 20))



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
  coord_flip() +
  theme(plot.title = element_text(hjust = 0.5, size = 40),
        axis.title = element_text(size = 30),
        axis.text = element_text(size = 22),
        legend.title = element_text(size = 35),
        legend.key.size  = unit(2, "cm"),
        legend.text = element_text(size = 25))

all_club
```

```r
top_club
```
{{< /panel >}}
{{< /panelset >}}

The bar chart above shows that all the top 6 clubs except Manchester United and Arsenal have had an English Manager in charge. Manchester united not having an English manager since the creation of the league is partially because Sir Alex Ferguson (who debuted at the club in 1986) was the manager of the club before the league was created and he spent over 20 years at the helm of affairs in the club before handing over at the end of 2012/13 season.


## Match Result on Premier League Debut

David O'Leary and Martin O'Neill had no information on their English premier league debut. So I'll filter them out. Most managers lost on their first English premier league debut (42.5%).

{{< panelset class = "greetings" >}}
{{< panel name = "Match Result" >}}
<img src="DATAEX~1/figure-html/Match Result-1.png" width="2400" />
{{< /panel >}}
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
  theme(plot.title = element_text(hjust = 0.5, size = 50),
        plot.subtitle = element_text(face = "italic", size = 30, colour = "grey"),
        legend.text = element_text(size = 15),
        axis.title = element_text(size = 30),
        axis.text = element_text(size = 25))

debut  
```

{{< /panel >}}
{{< /panelset >}}

Out of the 112 English managers in the league 50 lost their debut matches. So far no Argentine, German, Portuguese or Spanish managers have lost their debut match.

{{< panelset class = "greetings" >}}
{{< panel name = "Debut Result by Nationality" >}}
<img src="DATAEX~1/figure-html/Debut Result-1.png" width="2400" />
{{< /panel >}}
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
       fill = "Match Result") +
  theme(plot.title = element_text(hjust = 0.5, size = 50),
        axis.title = element_text(size = 30),
        axis.text = element_text(size = 25),
        legend.title = element_text(size = 35),
        legend.key.size  = unit(3, "cm"),
        legend.text = element_text(size = 20))

debut_nat  
```
{{< /panel >}}
{{< /panelset >}}
  
Out of the 112 English managers in the league 50 lost their debut matches. So far no Argentine, German, Portuguese or Spanish managers have lost their debut match.
  

Now that we have explored our data, we will begin analyzing in the next post.

