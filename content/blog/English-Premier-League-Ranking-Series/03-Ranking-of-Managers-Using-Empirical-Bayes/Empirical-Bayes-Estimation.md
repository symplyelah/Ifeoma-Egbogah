---
title: "English Premier League Managers"
weight: 3
subtitle: "The Best and Worst EPL Managers Using Emprirical Bayes"
excerpt: " "
date: 2023-04-17
draft: false
editor_options: 
  chunk_output_type: console
---


## Introduction

In the last post I mentioned I read an article where the __English Premier League__ managers' were ranked based on their raw winning averages. I felt this was a bit unfair. In this post I am going to attempt to rank each managers' performance in the premier league using the Empirical Bayes method to estimate their winning average as against using their raw winning averages and the manager with the highest average is our best manager.This borrows from Dave's post on __"Understanding Empirical Bayes Estimation Using Baseball Statistics".__ (http://varianceexplained.org/). It is a great read. 

So we are going to use the empirical Bayes method to:
** estimate the winning average of each manager
** estimate their credible interval


Let's load our data set (data used in the Data-Exploration blog post (https://ifeoma-egbogah.netlify.app/blog/english-premier-league-ranking-series/02-data-exploration/data-exploration/)) and packages into R.





## Raw Winning Average

A manager's performance is judged primarily on the outcome of the matches he is in charge of. A match's outcome could be a win, draw or a loss. If a win seems out of reach, a manager would prefer a draw than a loss. At least his team would gain a point as against zero if they lose. A win gives the team three points.

SO, who are the best and worst managers? Let's find out.....

         Raw Winning Average = Games Won/Total Games Managed




Here are the top ten managers with the highest raw winning average.

{{< panelset class = "greetings" >}}
{{< panel name = "Top 10 Managers based on Raw Winning Average" >}}

|Managers          | Total Games Managed| Games Won| Games Drawn| Games Lost|   Average|
|:-----------------|-------------------:|---------:|-----------:|----------:|---------:|
|Jim Barron        |                   1|         1|           0|          0| 1.0000000|
|Eddie Newton      |                   1|         1|           0|          0| 1.0000000|
|Josep Guardiola   |                  76|        55|          13|          8| 0.7236842|
|Antonio Conte     |                  76|        51|          10|         15| 0.6710526|
|Trevor Brooking   |                   3|         2|           1|          0| 0.6666667|
|Alex Ferguson     |                 810|       528|         168|        114| 0.6518519|
|Jose Mourinho     |                 288|       183|          65|         40| 0.6354167|
|Carlo Ancelotti   |                  76|        48|          13|         15| 0.6315789|
|Roberto Mancini   |                 133|        82|          27|         24| 0.6165414|
|Manuel Pellegrini |                 114|        70|          21|         23| 0.6140351|

{{< /panel >}}
{{< panel name = "Code" >}}

```r
rwa_top <- epl %>%
  arrange(desc(average)) %>%
  dplyr::select(managers, total_games_managed, games_won, games_drawn, games_lost, average) %>%
  head(10)


knitr::kable(rwa_top, col.names = str_to_title(str_replace_all(names(rwa_top), "_", " ")))  
```



|Managers          | Total Games Managed| Games Won| Games Drawn| Games Lost|   Average|
|:-----------------|-------------------:|---------:|-----------:|----------:|---------:|
|Jim Barron        |                   1|         1|           0|          0| 1.0000000|
|Eddie Newton      |                   1|         1|           0|          0| 1.0000000|
|Josep Guardiola   |                  76|        55|          13|          8| 0.7236842|
|Antonio Conte     |                  76|        51|          10|         15| 0.6710526|
|Trevor Brooking   |                   3|         2|           1|          0| 0.6666667|
|Alex Ferguson     |                 810|       528|         168|        114| 0.6518519|
|Jose Mourinho     |                 288|       183|          65|         40| 0.6354167|
|Carlo Ancelotti   |                  76|        48|          13|         15| 0.6315789|
|Roberto Mancini   |                 133|        82|          27|         24| 0.6165414|
|Manuel Pellegrini |                 114|        70|          21|         23| 0.6140351|
{{< /panel >}}
{{< /panelset >}}

.....and these are the bottom ten managers with lowest raw winning average. 

{{< panelset class = "greetings" >}}
{{< panel name = "Bottom 10 Managers Based on Raw Winning AVerages" >}}

|Managers      | Total Games Managed| Games Won| Games Drawn| Games Lost| Average|
|:-------------|-------------------:|---------:|-----------:|----------:|-------:|
|Eric Black    |                   9|         0|           1|          8|       0|
|Archie Knox   |                   1|         0|           0|          1|       0|
|Staurt McCall |                   2|         0|           0|          2|       0|
|Steve Holland |                   1|         0|           0|          1|       0|
|Frank de Boer |                   4|         0|           0|          4|       0|
|Billy McEwan  |                   1|         0|           0|          1|       0|
|Jimmy Gabriel |                   7|         0|           1|          6|       0|
|Ray Lewington |                   3|         0|           2|          1|       0|
|Paul Goddard  |                   2|         0|           2|          1|       0|
|Tony Book     |                   1|         0|           1|          0|       0|

{{< /panel >}}
{{< panel name = "Code" >}}

```r
rwa_bottom <- epl %>%
  arrange(average) %>%
  dplyr::select(managers, total_games_managed, games_won, games_drawn, games_lost, average) %>%
  head(10)


knitr::kable(rwa_bottom, col.names = str_to_title(str_replace_all(names(rwa_bottom), "_", " ")))
```



|Managers      | Total Games Managed| Games Won| Games Drawn| Games Lost| Average|
|:-------------|-------------------:|---------:|-----------:|----------:|-------:|
|Eric Black    |                   9|         0|           1|          8|       0|
|Archie Knox   |                   1|         0|           0|          1|       0|
|Staurt McCall |                   2|         0|           0|          2|       0|
|Steve Holland |                   1|         0|           0|          1|       0|
|Frank de Boer |                   4|         0|           0|          4|       0|
|Billy McEwan  |                   1|         0|           0|          1|       0|
|Jimmy Gabriel |                   7|         0|           1|          6|       0|
|Ray Lewington |                   3|         0|           2|          1|       0|
|Paul Goddard  |                   2|         0|           2|          1|       0|
|Tony Book     |                   1|         0|           1|          0|       0|
{{< /panel >}}
{{< /panelset >}}

Wow! Isn't that interesting. Looking at the table we know these are neither the best nor worst managers in the league. The raw winning averages definitely isn't a good estimate for determining who the best and worst managers are in the league. This estimate gives us managers who took charge of a singe game and were 'lucky' or 'unlucky' to win or lose the match. The overall mean raw winning average is 0.282.




We definitely need a better estimate. *Let's get it.*

## Distribution of the Raw Winning Average

We will begin by looking at the distribution of the raw winning averages of the managers. Examining a distribution is usually done using by plotting a histogram. Based on the graph the beta distribution looks like is a good choice for our prior distribution and also our data is binomial.


{{< panelset class = "greetings" >}}
{{< panel name = "Raw Winning Average Distribution of Managers" >}}
<img src="/blog/English-Premier-League-Ranking-Series/03-Ranking-of-Managers-Using-Empirical-Bayes/Empirical-Bayes-Estimation_files/figure-html/rwa_distri-1.png" width="2400" />
{{< /panel >}}
{{< panel name = "Code" >}}

```r
epl%>%
  dplyr::select(managers, total_games_managed, games_won, games_drawn, games_lost)%>%
  #pivot_longer(cols = starts_with("game"), names_to = "outcome", values_to = "games")%>%
  mutate(average = games_won/total_games_managed)%>%
  ggplot(aes(average)) + 
  geom_histogram(binwidth = 0.1, fill="darkorchid4") + 
  labs(x = "Winning Averages",
       y = "Counts",
       title = "Distribution of EPL Managers' Winning Average from 1992 to 2018") +
  theme(plot.title = element_text(hjust = 0.5, size = 45),
        axis.title = element_text(size = 30),
        axis.text = element_text(size = 30))
```
{{< /panel >}}
{{< /panelset >}}

## Bayesian Inference

Bayesian inference is basically the reversal of ones belief on the basis of evidence. To use Bayesian method we need to choose a prior distribution (this is what we believe about the parameters before evidence) for the parameters we want to estimate. 

## Empirical Bayes

### Step 1: Prior Distribution

The first step in Bayesian analysis is to choose a prior distribution. Since we are using empirical Bayes we will be taking our prior distribution from the data. Not a very Bayesian approach. I know, I know. Since I have a reasonable amount of data I decided to use empirical Bayes. This is what our model looks like: 

`$$Y\sim\mbox{Binomial} (Total\ Games\ Managed,\ X)$$`

`$$X\sim\mbox{Beta}(\alpha,\ \beta)$$`

`$$\alpha$$` and `$$\beta$$` are called "hyper parameters" of our model. To get the hyper parameters `$$\alpha$$` and `$$\beta$$` for our model we will be using the method of moments. The maximum likelihood estimation can also be used. The method of moments uses the mean and the variance of the data. The formula for the method of moments is: 


`$$\mu\ = mean(X)$$`
`$$\sigma\ = var(X)$$`

`$$\alpha = ((1 - \mu)/\sigma - 1/\mu) * \mu^2$$`
`$$\beta = \alpha * (1 /\mu -1 )$$`


The *fitdist* function from the *fitdistrplus* package can be used to calculate the method of moments. I filtered out the total games managed that were less than 10 to reduce the noise. This gives us `$$\alpha$$` = 3.77, `$$\beta$$` = 8.631. 

{{< panelset class = "greetings" >}}
{{< panel name = "Maximum Likelihood Estimation of Shape Parameters" >}}

```
## Fitting of the distribution ' beta ' by matching moments 
## Parameters : 
##        estimate
## shape1 3.773622
## shape2 8.631809
## Loglikelihood:  -Inf   AIC:  Inf   BIC:  Inf
```

```
##   shape1 
## 3.773622
```

```
##   shape2 
## 8.631809
```
{{< /panel >}}
{{< panel name = "Code" >}}

```r
epl_man <- epl %>%
  filter(total_games_managed >= 10)


e <- fitdist(epl_man$average, "beta", method="mme")

summary(e)
```

```
## Fitting of the distribution ' beta ' by matching moments 
## Parameters : 
##        estimate
## shape1 3.773622
## shape2 8.631809
## Loglikelihood:  -Inf   AIC:  Inf   BIC:  Inf
```

```r
alpha <- e$estimate[1]
beta <- e$estimate[2]

alpha
```

```
##   shape1 
## 3.773622
```

```r
beta
```

```
##   shape2 
## 8.631809
```
{{< /panel >}}
{{< /panelset >}}

Hmmmm..... Not bad! So we have our shape parameters for the beta distribution. How does this fit to the distribution of the raw winning averages shown by the histogram? Let's plot and see.

{{< panelset class = "greetings" >}}
{{< panel name = "Prior Distribution" >}}
<img src="/blog/English-Premier-League-Ranking-Series/03-Ranking-of-Managers-Using-Empirical-Bayes/Empirical-Bayes-Estimation_files/figure-html/prior_distribution-1.png" width="2400" />
{{< /panel >}}
{{< panel name = "Code" >}}

```r
epl_man%>%
  ggplot() + 
  geom_histogram(aes(average, y = after_stat(density)), binwidth=0.1, fill = "darkorchid4") + 
  stat_function(fun = function(x) dbeta(x, alpha, beta), colour="red", linewidth = 0.6) + 
  labs(x = "Winning Averages",
  y = "Density",
  title = "Prior Distribution Using Maximum Likelihood Estimation") +
  theme(plot.title = element_text(hjust = 0.5, size = 45),
        axis.title = element_text(size = 30),
        axis.text = element_text(size = 30))
```
{{< /panel >}}
{{< /panelset >}}

Not perfect but not to bad. Next stop empirical Bayes estimate, then posterior probability and finally credible interval.


### Step 2: Using the Distribution as a Prior for Each Manager's Winning Average 

Since we have our prior probability distribution we can now calculate our posterior winning average for each manager by updating based on individual evidence. We do this by:
  
`$$Posterior\ Winning\ Estimate = \frac{\alpha + Games\ Won}{\alpha + \beta + Total\ Games\ Managed}$$`

Simple right.

{{< panelset class = "greetings" >}}
{{< panel name = "Results" >}}

|Managers        |   Average| Eb Winning Average|
|:---------------|---------:|------------------:|
|Eddie Howe      | 0.2982456|          0.2988291|
|Brue Rioch      | 0.4473684|          0.4121306|
|Arsene Wenger   | 0.5748792|          0.5708835|
|Stewart Houston | 0.3684211|          0.3430496|
|David O'Leary   | 0.4314869|          0.4270436|
|Jim Barron      | 1.0000000|          0.3560961|
|Tim Sherwood    | 0.4222222|          0.3967155|
|Graham Taylor   | 0.2359551|          0.2443027|
|Brian Little    | 0.3680556|          0.3629901|
|Remi Garde      | 0.1000000|          0.1781683|
{{< /panel >}}
{{< panel name = "Code" >}}

```r
epl <-epl %>%
  mutate(eb_winning_average = (games_won + alpha)/(total_games_managed + alpha + beta))


epl%>%
  dplyr::select(managers, average, eb_winning_average)%>%
  head(10)%>%
  knitr::kable(col.names = str_to_title(str_replace_all(names(.), "_", " ")))
```



|Managers        |   Average| Eb Winning Average|
|:---------------|---------:|------------------:|
|Eddie Howe      | 0.2982456|          0.2988291|
|Brue Rioch      | 0.4473684|          0.4121306|
|Arsene Wenger   | 0.5748792|          0.5708835|
|Stewart Houston | 0.3684211|          0.3430496|
|David O'Leary   | 0.4314869|          0.4270436|
|Jim Barron      | 1.0000000|          0.3560961|
|Tim Sherwood    | 0.4222222|          0.3967155|
|Graham Taylor   | 0.2359551|          0.2443027|
|Brian Little    | 0.3680556|          0.3629901|
|Remi Garde      | 0.1000000|          0.1781683|
{{< /panel >}}
{{< /panelset >}}


### Results

So, who are the best managers by this estimate?
Drum roll, please...... I present the top ten managers!!!!!! Pep Josep Guadiola tops the pack with a winning average of 0.665 (66.5%). Very impressive for a guy who has only spent two seasons in the league based on this data. Alex Ferguson is second with a winning average of 0.647 (64.7%). Seems the Prof (Arsene Wenger) comes in at number 8 with a winning average of 0.571 (57.10%). 

{{< panelset class = "greetings" >}}
{{< panel name = "Top 10 Managers Using Empirical Bayes" >}}

|Managers          | Total Games Managed| Games Won|   Average| Eb Winning Average|
|:-----------------|-------------------:|---------:|---------:|------------------:|
|Josep Guardiola   |                  76|        55| 0.7236842|          0.6648191|
|Alex Ferguson     |                 810|       528| 0.6518519|          0.6466076|
|Jose Mourinho     |                 288|       183| 0.6354167|          0.6217385|
|Antonio Conte     |                  76|        51| 0.6710526|          0.6195730|
|Roberto Mancini   |                 133|        82| 0.6165414|          0.5898928|
|Carlo Ancelotti   |                  76|        48| 0.6315789|          0.5856385|
|Manuel Pellegrini |                 114|        70| 0.6140351|          0.5836270|
|Arsene Wenger     |                 828|       476| 0.5748792|          0.5708835|
|Rafael Benitez    |                 302|       156| 0.5165563|          0.5081770|
|Jurgen Klopp      |                 106|        56| 0.5283019|          0.5048216|
{{< /panel >}}
{{< panel name = "Code" >}}

```r
epl_man2 <- epl %>%
  arrange(desc(eb_winning_average)) %>%
  dplyr::select(managers, total_games_managed, games_won, average, eb_winning_average) %>%
  head(10)

knitr::kable(epl_man2, col.names = str_to_title(str_replace_all(names(epl_man2), "_", " ")))
```



|Managers          | Total Games Managed| Games Won|   Average| Eb Winning Average|
|:-----------------|-------------------:|---------:|---------:|------------------:|
|Josep Guardiola   |                  76|        55| 0.7236842|          0.6648191|
|Alex Ferguson     |                 810|       528| 0.6518519|          0.6466076|
|Jose Mourinho     |                 288|       183| 0.6354167|          0.6217385|
|Antonio Conte     |                  76|        51| 0.6710526|          0.6195730|
|Roberto Mancini   |                 133|        82| 0.6165414|          0.5898928|
|Carlo Ancelotti   |                  76|        48| 0.6315789|          0.5856385|
|Manuel Pellegrini |                 114|        70| 0.6140351|          0.5836270|
|Arsene Wenger     |                 828|       476| 0.5748792|          0.5708835|
|Rafael Benitez    |                 302|       156| 0.5165563|          0.5081770|
|Jurgen Klopp      |                 106|        56| 0.5283019|          0.5048216|
{{< /panel >}}
{{< /panelset >}}


Onto the bottom ten managers in the premier league from our estimates, Terry Connor had the lest winning average of 0.1504 (15.04%). As we can see empirical Bayes estimation didn't select managers who managed one or two games unlike the raw winning average.


{{< panelset class = "greetings" >}}
{{< panel name = "Bottom 10 Managers Using Empirical Bayes" >}}

|Managers        | Total Games Managed| Games Won|   Average| Eb Winning Average|
|:---------------|-------------------:|---------:|---------:|------------------:|
|Terry Connor    |                  13|         0| 0.0000000|          0.1485360|
|John Gorman     |                  42|         5| 0.1190476|          0.1612637|
|Roy McFarland   |                  22|         2| 0.0909091|          0.1678114|
|Steve Wigley    |                  16|         1| 0.0625000|          0.1680531|
|Aidy Boothroyd  |                  38|         5| 0.1315789|          0.1740610|
|Eric Black      |                   9|         0| 0.0000000|          0.1762927|
|Remi Garde      |                  20|         2| 0.1000000|          0.1781683|
|Billy Davies    |                  14|         1| 0.0714286|          0.1807818|
|Mark McGhee     |                  24|         3| 0.1250000|          0.1860608|
|Chris Hutchings |                  24|         3| 0.1250000|          0.1860608|
{{< /panel >}}
{{< panel name = "Code" >}}

{{< /panel >}}
{{< /panelset >}}

## Step 3: Posterior Distribution

We have our posterior winning average, we can determine the shape parameters of the posterior probability distribution for each manager. This is easily done by updating the shape parameters of the prior beta distribution. This is shown in the R code.


{{< panelset class = "greetings" >}}
{{< panel name = "Posterior Distribution" >}}

|Managers        |     Alpha2|      Beta2|
|:---------------|----------:|----------:|
|Eddie Howe      |  37.773622|  88.631809|
|Brue Rioch      |  20.773622|  29.631809|
|Arsene Wenger   | 479.773622| 360.631809|
|Stewart Houston |  10.773622|  20.631809|
|David O'Leary   | 151.773622| 203.631809|
|Jim Barron      |   4.773622|   8.631809|
|Tim Sherwood    |  22.773622|  34.631809|
|Graham Taylor   |  24.773622|  76.631809|
|Brian Little    |  56.773622|  99.631809|
|Remi Garde      |   5.773622|  26.631809|
{{< /panel >}}
{{< panel name = "Code" >}}

```r
epl <- epl%>%
  mutate(alpha2 = games_won + alpha,
         beta2 = total_games_managed - games_won + beta)

top_10 <- epl%>%
  head(10)

top_10%>%
  dplyr::select(managers, alpha2, beta2)%>%
knitr::kable(col.names = str_to_title(names(.)))
```



|Managers        |     Alpha2|      Beta2|
|:---------------|----------:|----------:|
|Eddie Howe      |  37.773622|  88.631809|
|Brue Rioch      |  20.773622|  29.631809|
|Arsene Wenger   | 479.773622| 360.631809|
|Stewart Houston |  10.773622|  20.631809|
|David O'Leary   | 151.773622| 203.631809|
|Jim Barron      |   4.773622|   8.631809|
|Tim Sherwood    |  22.773622|  34.631809|
|Graham Taylor   |  24.773622|  76.631809|
|Brian Little    |  56.773622|  99.631809|
|Remi Garde      |   5.773622|  26.631809|
{{< /panel >}}
{{< /panelset >}}

Now that we have the `$$\alpha$$` and `$$\beta$$` for each manager we can now visualize their posterior probability distribution. We will begin by looking at the posterior probability distribution of the top 5 managers based on our empirical Bayes estimation. 


{{< panelset class = "greetings" >}}
{{< panel name = "Posterior Distribution of Top 5 Managers" >}}

```
## Warning: Using `size` aesthetic for lines was deprecated in ggplot2 3.4.0.
## i Please use `linewidth` instead.
## This warning is displayed once every 8 hours.
## Call `lifecycle::last_lifecycle_warnings()` to see where this warning was
## generated.
```

<img src="/blog/English-Premier-League-Ranking-Series/03-Ranking-of-Managers-Using-Empirical-Bayes/Empirical-Bayes-Estimation_files/figure-html/pd_top-1.png" width="2400" />
{{< /panel >}}
{{< panel name = "Code" >}}

```r
epl_man4 <- epl %>%
  arrange(desc(eb_winning_average)) %>%
  top_n(5, eb_winning_average) %>%
  crossing(x = seq(0.01, 0.8, 0.001))%>%
  ungroup()%>%
  mutate(density = dbeta(x, games_won + alpha2,
         total_games_managed - games_won + beta2)) 

epl_man4%>%  
  ggplot(aes(x, density, colour = managers)) + 
  geom_line(linewidth = 1.8) +
  stat_function(fun=function(x) dbeta(x, alpha, beta), lty = 2, colour = "grey35", size = 2) + 
  scale_colour_scico_d(palette = "lajolla") +
  labs(x = "Winning Averages", 
       y = "Density", 
       title="Prior and Posterior Distribution", 
       subtitle="Posterior Distribution of the Top Five Managers Using Emprical Bayes",
       colour = "Managers") + 
  theme(plot.title = element_text(hjust = 0.5, size = 50),
        plot.subtitle = element_text(size = 35),
        axis.title = element_text(size = 30),
        axis.text = element_text(size = 30),
        legend.title = element_text(size = 40),
        legend.key.size  = unit(3, "cm"),
        legend.text = element_text(size = 30))
```
{{< /panel >}}
{{< /panelset >}}


Sir Alex Ferguson has a narrower posterior probability distribution (since he managed more games, we have enough evidence) compared to the broader posterior probability distribution of Josep Guardiola and Antonio Conte (both managed fewer games than Sir Ferguson). The dashed curve is the prior distribution.


Just for fun lets compare the posterior probability distribution of a few managers outside the top 5.



{{< panelset class = "greetings" >}}
{{< panel name = "Posterior Distribution of Some Managers" >}}
<img src="/blog/English-Premier-League-Ranking-Series/03-Ranking-of-Managers-Using-Empirical-Bayes/Empirical-Bayes-Estimation_files/figure-html/some_manager-1.png" width="2880" />
{{< /panel >}}
{{< panel name = "Code" >}}

```r
manager <- c("Arsene Wenger", "Alex Ferguson", "Ryan Giggs", "Josep Guardiola", "Eric Black", "Alan Shearer")

manager_career <- epl %>%
  filter(managers %in% manager)

manager <- manager_career %>%
  crossing(x= seq(0.01, 0.9, 0.001)) %>%
  ungroup() %>%
  mutate(density = dbeta(x, alpha2, beta2))
  

manager%>%
  ggplot(aes(x, density, colour = managers)) + 
  geom_line(linewidth = 1.8) + 
  stat_function(fun=function(x) dbeta(x, alpha, beta), lty =2, colour="grey35", size = 2) +  
  scale_colour_scico_d(palette = "lajolla") +
  labs(x ="Winning Averages", 
       y = "Density", 
       title = "Prior and Posterior Distribution", 
       subtitle="Posterior Distribution of Some Selected Managers",
       colour = "Managers") + 
  theme(plot.title = element_text(hjust = 0.5, size = 50),
        plot.subtitle = element_text(size = 35),
        axis.title = element_text(size = 30),
        axis.text = element_text(size = 30),
        legend.title = element_text(size = 40),
        legend.key.size  = unit(3, "cm"),
        legend.text = element_text(size = 30))
```
{{< /panel >}}
{{< /panelset >}}


## Empirical Bayes Estimste vs Raw Average

How did our empirical Bayes winning average change compared to the raw winning average? Let's make another plot. The red dashed horizontal line marks `$$y=\frac{\alpha}{\alpha + \beta}$$` this shows what each manager's estimate would be if we had no evidence. Points above and below the line move towards this line. The diagonal line marks `$$x=y$$`. Points close to the line are estimates that didn't get shrunk by empirical Bayes because we have enough evidence so their values are close to the raw estimate and they are managers that over saw more than 300 games. 



{{< panelset class = "greetings" >}}
{{< panel name = "Empirical Bayes Estimate vs Raw Winning Average" >}}
<img src="/blog/English-Premier-League-Ranking-Series/03-Ranking-of-Managers-Using-Empirical-Bayes/Empirical-Bayes-Estimation_files/figure-html/eb_vs_rwa-1.png" width="2880" />
{{< /panel >}}
{{< panel name = "Code" >}}

```r
library(ggrepel)

epl%>%
  ggplot(aes(average, eb_winning_average, colour = total_games_managed)) + 
  geom_hline(yintercept = alpha/(alpha + beta), colour=  "red", lty=2, linewidth = 1.5)  + 
  geom_point(size = 10) + 
  geom_text_repel(data = subset(epl, total_games_managed >= 450), aes(label = managers), box.padding=unit(0.5, "lines"), colour="black", size = 12) + 
  geom_abline(colour = "red", linetype = 1, linewidth = 1.5) + 
  #scale_color_gradient(trans= "log", low="midnightblue", high="pink", name="Games Managed", breaks = 10^(1:5)) + 
  scale_colour_scico(palette = "bam", trans = "log", breaks = 10^(1:5), direction = -1) +
  labs(x = "Raw Winning Averages",
       y = "Empirical Bayes Estimate of Winning Average",
       title = "Empirical Bayes Estimate vs Raw Winning Average", 
       subtitle = "Names of Managers Who Managed More Than 450 games",
       colour = "Total Games Managed") + 
 theme(plot.title = element_text(hjust = 0.5, size = 55),
        plot.subtitle = element_text(size = 35),
        axis.title = element_text(size = 35),
        axis.text = element_text(size = 35),
        legend.title = element_text(size = 35),
        legend.key.size  = unit(3, "cm"),
        legend.text = element_text(size = 30))
```
{{< /panel >}}
{{< /panelset >}}

The estimates are only different for managers who over saw fewer games (less than 2 games). The raw winning average is 0 or 1 for such managers however, the empirical Bayes estimate for such managers are close to the overall mean of the prior beta distribution. This is known as __shrinkage__.


{{< panelset class = "greetings" >}}
{{< panel name = "Managers Who Oversaw Less Than Two Games" >}}

```
## Warning: ggrepel: 17 unlabeled data points (too many overlaps). Consider
## increasing max.overlaps
```

<img src="/blog/English-Premier-League-Ranking-Series/03-Ranking-of-Managers-Using-Empirical-Bayes/Empirical-Bayes-Estimation_files/figure-html/less_two-1.png" width="2880" />
{{< /panel >}}
{{< panel name = "Code" >}}

```r
epl%>%
  ggplot(aes(average, eb_winning_average, colour = total_games_managed)) +
  geom_hline(yintercept = alpha/(alpha + beta), colour=  "red", lty=2, linewidth = 1.5)  +
  geom_point(size = 10) + 
  geom_text_repel(data=subset(epl, total_games_managed <= 2), aes(label =  managers, colour="grey50"), box.padding = unit(0.5, "lines"), force=1.9, colour="black", size= 12) + 
  geom_abline(colour = "red", linetype=1, linewidth = 1.5) + 
 # scale_color_gradient(trans= "log", low="midnightblue", high="pink", name ="Games Managed", breaks = 10^(1:5)) + 
  scale_colour_scico(palette = "bam", trans = "log", breaks = 10^(1:5), direction = -1) +
  labs(x = "Winning Averages",
       y = "Empirical Bayes Estimate of Winning Averages",
       title = "Empirical Bayes Estimate vs Raw Winning Average", 
       subtitle = "Managers Who Managed Less Than Two games",
       colour = "Games Managed") +
 theme(plot.title = element_text(hjust = 0.5, size = 55),
        plot.subtitle = element_text(size = 35),
        axis.title = element_text(size = 35),
        axis.text = element_text(size = 35),
        legend.title = element_text(size = 35),
        legend.key.size  = unit(3, "cm"),
        legend.text = element_text(size = 30))
```

```
## Warning: ggrepel: 21 unlabeled data points (too many overlaps). Consider
## increasing max.overlaps
```
{{< /panel >}}
{{< /panelset >}}


## Step 4: Credible Interval

Credible interval tells what percentage (for this example 95%) of the the posterior winning average distribution lies within the interval. It shows how much uncertainty is present in our estimate. This computed using qbeta.
  




{{< panelset class = "greetings" >}}
{{< panel name = "Credible Interval" >}}

Table: Table 1: Credible Interval

|Managers          | Games Won| Total Games Managed| Eb Winning Average|       Low|      High|
|:-----------------|---------:|-------------------:|------------------:|---------:|---------:|
|Josep Guardiola   |        55|                  76|          0.6648191| 0.6316136| 0.7588512|
|Alex Ferguson     |       528|                 810|          0.6466076| 0.6354285| 0.6789108|
|Jose Mourinho     |       183|                 288|          0.6217385| 0.6030078| 0.6756766|
|Antonio Conte     |        51|                  76|          0.6195730| 0.5852000| 0.7173755|
|Roberto Mancini   |        82|                 133|          0.5898928| 0.5625841| 0.6682698|
|Carlo Ancelotti   |        48|                  76|          0.5856385| 0.5506062| 0.6856520|
|Manuel Pellegrini |        70|                 114|          0.5836270| 0.5542667| 0.6678195|
|Arsene Wenger     |       476|                 828|          0.5708835| 0.5593968| 0.6041582|
|Rafael Benitez    |       156|                 302|          0.5081770| 0.4891613| 0.5632634|
|Jurgen Klopp      |        56|                 106|          0.5048216| 0.4738096| 0.5942600|
{{< /panel >}}
{{< panel name = "Code" >}}

```r
credible_interval <- epl%>%
  arrange(desc(eb_winning_average))%>%
  dplyr::select(managers, games_won, total_games_managed, eb_winning_average, low, high)%>%
  head(10)


knitr::kable(credible_interval, caption = "Credible Interval", col.names = str_to_title(str_replace_all(names(credible_interval), "_", " ")))
```



Table: Table 2: Credible Interval

|Managers          | Games Won| Total Games Managed| Eb Winning Average|       Low|      High|
|:-----------------|---------:|-------------------:|------------------:|---------:|---------:|
|Josep Guardiola   |        55|                  76|          0.6648191| 0.6316136| 0.7588512|
|Alex Ferguson     |       528|                 810|          0.6466076| 0.6354285| 0.6789108|
|Jose Mourinho     |       183|                 288|          0.6217385| 0.6030078| 0.6756766|
|Antonio Conte     |        51|                  76|          0.6195730| 0.5852000| 0.7173755|
|Roberto Mancini   |        82|                 133|          0.5898928| 0.5625841| 0.6682698|
|Carlo Ancelotti   |        48|                  76|          0.5856385| 0.5506062| 0.6856520|
|Manuel Pellegrini |        70|                 114|          0.5836270| 0.5542667| 0.6678195|
|Arsene Wenger     |       476|                 828|          0.5708835| 0.5593968| 0.6041582|
|Rafael Benitez    |       156|                 302|          0.5081770| 0.4891613| 0.5632634|
|Jurgen Klopp      |        56|                 106|          0.5048216| 0.4738096| 0.5942600|
{{< /panel >}}
{{< /panelset >}}

Managers in the top 20, their credible intervals are narrow. Managers such as Alex Ferguson and Arsene Wenger who have overseen over 800 games our uncertainty is low however managers such as Felipe Scolari and Guus Hiddink have a much broader credible interval as they have managed fewer games (less than 40 games). Let's visualize this in a plot.

{{< panelset class = "greetings" >}}
{{< panel name = "Alex Ferguson vs Felipe Scolari" >}}
<img src="/blog/English-Premier-League-Ranking-Series/03-Ranking-of-Managers-Using-Empirical-Bayes/Empirical-Bayes-Estimation_files/figure-html/af_vs_fs-1.png" width="2880" />
{{< /panel >}}
{{< panel name = "Code" >}}

```r
manager_ci <- c("Alex Ferguson", "Felipe Scolari")

ci <- epl %>%
  filter(managers %in% manager_ci)

ci2 <- ci %>%
  crossing(x = seq(0.005, 0.9, 0.001)) %>%
  ungroup() %>% 
  mutate(density1 = dbeta(x, alpha2, beta2))
 
  
ci3 <- ci2 %>%
  mutate(cum = pbeta(x, alpha2, beta2)) %>%
  filter(cum > .025, cum < .975)
  

ci2 %>%
  ggplot(aes(x, density1, colour = managers)) + 
  geom_line(show.legend = FALSE, linewidth = 1.5) + 
  geom_ribbon(aes(ymin = 0, ymax = density1), data= ci3, alpha = 0.5, fill = "pink", show.legend = FALSE) + 
  stat_function(fun=function(x) dbeta(x, alpha, beta), colour="grey35", lty=2, linewidth = 1.5) + 
  geom_errorbarh(aes(xmin= qbeta(0.025, alpha2, beta2), xmax = qbeta(0.975, alpha2, beta2), y = 0), height = 2, colour = "red", linewidth = 1.3) + 
  facet_wrap(~managers) + 
  scale_colour_scico_d(palette = "acton") +
  labs(x = "Credible Interval",
       y = "Posterior Distribution",
       title = "Posterior Distribution and Credible Interval\nfor Sir Alex Ferguson and Felipe Scolari") +  
  theme(plot.title = element_text(hjust = 0.5, size = 50),
        plot.subtitle = element_text(size = 35),
        axis.title = element_text(size = 35),
        axis.text = element_text(size = 35),
        strip.text = element_text(size = 40, colour = "black"),
        legend.title = element_text(size = 35),
        legend.key.size  = unit(3, "cm"),
        legend.text = element_text(size = 30))
```
{{< /panel >}}
{{< /panelset >}}


Since it is difficult to visualize the credible interval and posterior probability distribution density plot for all the managers in the top 20 lets make a point and error bar plot instead.

{{< panelset class = "greetings" >}}
{{< panel name = "Top Twenty" >}}
<img src="/blog/English-Premier-League-Ranking-Series/03-Ranking-of-Managers-Using-Empirical-Bayes/Empirical-Bayes-Estimation_files/figure-html/top20-1.png" width="2880" />
{{< /panel >}}
{{< panel name = "Code" >}}


```r
epl_man5 <- epl %>%
  arrange(desc(eb_winning_average)) %>%
  top_n(20, eb_winning_average) %>%
  mutate(managers = reorder(managers, eb_winning_average)) 

  
epl_man5%>%
  ggplot(aes(eb_winning_average, managers, colour = managers)) +
  geom_errorbarh(aes(xmin= low, xmax= high), show.legend = FALSE, linewidth = 1.5) +
  geom_point(show.legend = FALSE, size = 10) + 
  geom_vline(xintercept = alpha/(alpha + beta), colour="grey50", lty=2, linewidth = 1.5)  +
  scale_colour_scico_d(palette = "lajolla", direction = -1) +
  labs(x = "Empirical Bayes Winning Averages with 95% Credible Interval",
       y = "Managers",
       title = "Empirical Bayes Winning Averages and Credible Interval\nfor the Top 20 Managers",
       colour = "Managers")+
  theme(plot.title = element_text(hjust = 0.5, size = 55),
        plot.subtitle = element_text(size = 35),
        axis.title = element_text(size = 35),
        axis.text = element_text(size = 35),
        legend.title = element_text(size = 35),
        legend.key.size  = unit(3, "cm"),
        legend.text = element_text(size = 30))
```
{{< /panel >}}
{{< /panelset >}}
