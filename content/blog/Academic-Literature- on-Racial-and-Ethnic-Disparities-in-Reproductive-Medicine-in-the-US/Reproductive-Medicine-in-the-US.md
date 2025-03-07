---
title: "Uncovering Racial and Ethnic Disparities in Reproductive Medicine: Insights from Topic Modeling Analysis"
author: "Ifeoma Egbogah"
date: 2025-03-05
layout: single-sidebar
show_post_thumbnail: true
draft: false
categories:
- blog, tidytuesday, LDA, Reproductive, Health, Topic modelling
editor_options: 
  chunk_output_type: console
output:
  html_document:
    fig_width: 20
    fig_height: 60

---

<style>
.plot-container {
  width: 100%;
  height: auto;
}
</style>

Over the past decade, researchers have been increasingly investigating racial and ethnic disparities in reproductive medicine, aiming to uncover patterns of inequality and inform policy changes. This week's [#TidyTuesday dataset](https://github.com/rfordatascience/tidytuesday) features a collection of studies from the **eight highest-impact peer-reviewed Ob/Gyn journals**, covering publications from **January 1, 2010, through June 30, 2023**. These studies were reviewed in a recent article, *Racial and Ethnic Disparities in Reproductive Medicine in the United States: A Narrative Review of Contemporary High-Quality Evidence*, published in the *American Journal of Obstetrics and Gynecology* in **January 2025**.



# Topic Modeling Analysis

In an era where vast amounts of text data are generated daily—from research papers and news articles to social media posts—understanding and organizing this information is crucial. **Topic modeling** is a powerful natural language processing (NLP) technique that helps uncover hidden patterns and themes in large text collections.  

### What is Topic Modeling?  
Topic modeling is an **unsupervised machine learning** method used to automatically identify abstract "topics" within a collection of documents. Instead of manually sorting through thousands of texts, topic modeling helps us group similar content by recognizing patterns in word usage.  

Think of it like sorting a library of books based on themes without reading each book. The algorithm scans the words used in different documents and clusters them into distinct topics, making it easier to analyze large datasets.  

### How Does Topic Modeling Work? 
The key idea behind topic modeling is that documents contain **multiple topics**, and each topic is characterized by a group of words that frequently appear together. One of the most popular topic modeling techniques is **Latent Dirichlet Allocation (LDA)**, a Bayesian probabilistic model that assumes:  

1. **Each document is a mixture of topics** – A research paper on maternal health might discuss topics related to pregnancy risks, racial disparities, and medical treatments.  
2. **Each topic is a distribution of words** – For example, a "cancer treatment" topic may frequently include words like chemotherapy, survival rates, and risk factors.  
3. **Words in a document are generated from the topic distribution** – The model assigns words to topics based on how often they appear together in different texts.  

## What Can We Learn from Topic Modeling?  
By applying topic modeling to a collection of documents, we can answer several important questions:  
✅ **What is the main topic of a document?**  
   - Helps categorize articles, papers, or reports into meaningful themes.  
✅ **Which documents have similar topics?**  
   - Useful for recommending related research papers or grouping similar news articles.  
✅ **How do topics evolve over time?**  
   - Tracks shifts in discussion trends, such as how conversations around reproductive health disparities have changed in the past decade.  


In this post, we will explore insights using the **Latent Dirichlet Allocation (LDA) topic modeling** to analyze common themes across these studies and identify key areas of racial and ethnic inequality in reproductive health research.


# Why Study Racial and Ethnic Disparities in Reproductive Medicine?

Race and ethnicity have long been debated in medical research. Are they **biological constructs**, **social determinants**, or simply **proxies for systemic racism**? This blog aims to **synthesize evidence on racial and ethnic inequalities in obstetrics and gynecology (Ob/Gyn)** while also developing recommendations for future research.

Using **LDA topic modeling**, we extracted key themes from the dataset, revealing significant areas of focus.



{{< panelset class = "greetings" >}}
{{< panel name = "Code" >}}


``` r
data("stop_words")


article_abs <-  article_dat %>% 
  select(pmid, doi, year, journal, keywords, abstract) %>% 
  mutate(abstract = str_replace_all(abstract, "[^[:ascii:]]", ""),
         abstract = str_remove_all(abstract, "[0-9]+|[[:punct:]]"),
         abstract = str_remove_all(abstract, regex("ci", ignore_case = TRUE)),  
         abstract = str_trim(abstract)) %>% 

  unnest_tokens(word, abstract) %>% 
  anti_join(stop_words) %>% 
  count(journal, word, sort = TRUE) 
```

```
## Joining with `by = join_by(word)`
```

``` r
#topic modelling
journal_cast_abs <- article_abs %>% 
  cast_dtm(journal, word, n)

journal_lda_abs <- LDA(journal_cast_abs, k = 6, control = list(seed = 1234))
```
{{< /panel >}}
{{< /panelset >}}

# Key Findings from Topic Modeling

Applying **Latent Dirichlet Allocation (LDA)**, a Bayesian approach for topic modeling, below are the **six major topics** identified and their most relevant keywords:

-   **Topic 1** has words like "women," "black," "confidence," "risk," "maternal," and "disparities," suggesting it might relate to **racial disparities in maternal healthcare**.

-   **Topic 2** includes "patients," "cancer," "survival," "treatment," and "ovarian," indicating a **cancer treatment-related topic**.

-   **Topic 3** has words like "infertility," "pregnancy," "birth," "fertility," "Asian," and "Hispanic," which suggests a focus on **fertility treatments and racial disparities in reproductive health**.

-   **Topic 4** emphasizes "non-Hispanic," "adjusted," "severe," "delivery," "ethnicity," and "factors," indicating a **focus on birth outcomes by ethnicity**.

-   **Topic 5** includes "pregnancy," "fibroids," "cardiovascular," "disease," and "GDM (gestational diabetes mellitus)," suggesting a focus on **pregnancy-related health risks**.

-   **Topic 6** has "maternal," "black," "white," "race," "Hispanic," "mortality," "cesarean," and "care," suggesting a topic on **maternal health disparities and mortality**.



``` r
tidy_journal_lda <- tidy(journal_lda_abs, matrix = "beta")
```






``` r
bar_chart
```

<img src="/blog/Academic-Literature- on-Racial-and-Ethnic-Disparities-in-Reproductive-Medicine-in-the-US/Reproductive-Medicine-in-the-US_files/figure-html/unnamed-chunk-4-1.png" width="960" />



# How Do These Topics Vary Across Medical Journals?

To understand which journals focus on which topics, we examined **topic distributions across six high-impact Ob/Gyn journals**:

## Observations:

-   **"American Journal of Obstetrics and Gynecology"** is strongly associated with **Topic 1**.
-   **"Gynecologic Oncology"** is almost entirely linked to **Topic 2** (cancer-related topic).
-   **"Fertility and Sterility"** is highly associated with **Topic 3** (infertility and reproductive health).
-   **"BJOG: An International Journal of Obstetrics and Gynecology"** is mostly linked to **Topic 4**.
-   **"Human Reproduction"** has a high proportion of **Topic 5** (pregnancy risks).
-   **"Obstetrics and Gynecology"** is strongly associated with **Topic 6** (maternal health disparities).

# Why This Matters

This plot helps understand **which journals focus on which topics**, providing insights into how different medical journals specialize in different aspects of obstetrics, gynecology, and women's health.

As discussions continue around whether race should be considered in medical research, this data-driven approach provides an **objective way to identify key disparities and inform policy recommendations**.




{{< panelset class = "greetings" >}}
{{< panel name = "Boxplot" >}}

<img src="/blog/Academic-Literature- on-Racial-and-Ethnic-Disparities-in-Reproductive-Medicine-in-the-US/Reproductive-Medicine-in-the-US_files/figure-html/unnamed-chunk-5-1.png" width="672" />


{{< /panel >}}
{{< panel name = "Code" >}}

``` r
box_plot <- 
  tidy(journal_lda_abs, matrix = "gamma") %>%
  mutate(document = str_to_title(reorder(document, gamma * topic))) %>%
  ggplot(aes(factor(topic), gamma)) +
  geom_boxplot(aes(colour = factor(topic)), show.legend = FALSE) +
  facet_wrap(~ str_wrap(document, width = 30)) +
  scale_colour_manual(values = pal) +
  labs(title = "Distribution of Research Topics Across High-Impact Ob/Gyn Journals",
       caption = "Data: American Journal of Obstetrics and Gynecology • Visualization: Ifeoma Egbogah",
       y = expression(gamma),
       x = "Topic") +
  
  theme_minimal() +
  
 theme(plot.title = element_markdown(color = "black", hjust = 0.5, face = "bold"),
        axis.text.y = element_blank(),
        axis.ticks.y = element_blank(),
        axis.title = element_text(face = "bold")) 
```
{{< /panel >}}
{{< /panelset >}}


## Final Thoughts

The conversation around **race, ethnicity, and healthcare** is evolving, with increasing calls for equitable healthcare policies. The studies analyzed in this dataset offer **critical insights into reproductive health disparities** and underscore the need for targeted interventions to address them.

Future research should focus on:

-   **Understanding the root causes** of these disparities.
-   **Developing policies** that address systemic inequalities.
-   **Ensuring equal access** to reproductive healthcare for all racial and ethnic groups.

As we move forward, using **data science and natural language processing (NLP) techniques like LDA** can help us **uncover hidden patterns** and contribute to more informed, evidence-based discussions on healthcare equity.

### Want to Explore the Data?

Check out the dataset on the [**TidyTuesday GitHub page**](https://github.com/rfordatascience/tidytuesday)


