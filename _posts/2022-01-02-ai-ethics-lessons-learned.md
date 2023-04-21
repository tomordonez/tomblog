---
layout: post
title: "AI Ethics Lessons Learned"
description: "I took AI Ethics at Georgia Tech and here is what I learned."
author: tom
image: assets/images/1.jpg
tags: [ai ethics, machine learning, gatech]
comments: true
---

I took AI Ethics at Georgia Tech and here is what I learned. Here are innovations related to AI/ML to put some context:

* NLP: Gmail can autocomplete the emails you are typing. Siri can also autocomplete your texts or understand your voice. The same with Alexa. Amazon or any search engine can try to predict what you would type.
* Computer Vision: Security cameras can identify criminals like in the Boston marathon. Automated vehicles can drive themselves by recognizing objects on the road. Your funny TikTok video can distinguish between your face and your dog's face.
* And so many more applications like recommendation systems, anti-fraud, supply chain, etc.

## AI and Machine Learning algorithms may be biased

In products where humans are the end-user, most datasets used to train AI/ML algorithms are built for the majority of the demographic population.

So far, this doesn't sound so bad. However, let's look at some demographic statistics. United States demographic data as seen in the Census website [here](https://www.census.gov/) and Wikipedia [here](https://en.wikipedia.org/wiki/Race_and_ethnicity_in_the_United_States) show that between 2020 and 2021, the US has the following races:

* 57.8% White
* 18.7% Hispanic
* 12.1% Black
* 5.9% Asian
* 4.1% Two or more races
* 0.7% Native American
* Others

This means that most datasets in AI/ML are built to meet the 57.8% White with high sensitivity (true positive rate). Not an ML expert but what I understand about AI metrics is that anything above 50% is better at predicting than flipping a coin.

Why does this matter? If it has an 80% true positive rate for 57.8% of the US population, then it's good enough, right?

The problem is when you are not part of that demographic. If you are Hispanic, Black, Asian, or other, then this metric, the true positive rate is not as good, or as good as flipping a coin.

## Why does this matter?

If the product is in the computer vision domain about security cameras that flag criminals in the population and the algorithm has more than 90% chance of identifying a criminal from the white population then it has only a 50% chance of identifying a criminal from a minority (Hispanic/Black/Asian/Other) population. There is a 50/50 chance that the algorithm makes a mistake.

## Are algorithms biased?

AI Ethics it's not only about demographics of race. Algorithms also affect regulated domains and legally recognized protected classes.

*Regulated domains*
* Credit
* Education
* Employment
* Housing

*Legally recognized protected classes*
* Race, Color, Sex
* Religion, National Origin, citizenship
* Age, pregnancy, family status, disability status
* Veteran status, genetic information

Many research papers and datasets show that in the majority of surveys, responders are white males. The algorithm is deployed into a product and it only identifies and benefits this population. The algorithm would affect these populations: race and color (non-white), sex (female), probably national origin and citizenship, pregnancy, and family status.

## AI algorithms are black boxes

To protect IP, most AI algorithms are black boxes, and you cannot see how they work. All the content that you put online is delivered to an algorithm to learn and this algorithm might have intentional or unintentional biases.

## Misuse and unfair use of AI/ML algorithms

Taking AI Ethics was the "I wonder what's behind this curtain of Machine Learning? Woah!"

There is an application called COMPAS used to predict if a criminal would commit another crime (aka recidivism), which is some sort of Minority Report app, although not as sci-fi. Blacks who didn't re-offend were two times misclassified as compared to whites.

Google search for 'CEO' in images used to show 89% male pictures. This has been corrected since this was reported though and now shows a more balanced result. However, at some point in the past, it also showed Black people when querying gorillas.

Amazon tested a recruiting tool to automate what most candidates think is automated, job applications. The product was shut down after the algorithm classified women as less favorable candidates. The dataset was built from historical data where the demographic was mostly male.

## Pre-existing biases in datasets. 

There isn't a governing board when datasets are generated. There aren't many regulations for building datasets. If you are uploading videos to TikTok, most likely they are using your data to feed the algorithm, and you have no idea what the algorithm does, other than showing similar videos to like and share.

In my class, someone asked a question about the homework and had categorized the demographics as White, Black, and Mexican. It's not the first time I see someone categorizing all Hispanic under the Mexican category.

In Amazon's AI recruiting tool, I would assume a biased dataset could be that to parse resumes the engineers could try to scrape all male names from a system. Or just didn't come to realize the unbalanced set of demographics.

An article from the Bay Area said a Black couple put up their house for sale and the realtor estimated the house price at way below the market price. They asked their White friend to pretend to be the homeowner and asked another realtor, who priced the house at market price. Why would this happen? The research showed that owner-occupied houses are undervalued in neighborhoods that are 50% percent black.

## It's easy to mislead with shiny objects

The population is often misled by disinformation, shiny visualizations, and a lack of knowledge of statistics or simple math.

Biases in datasets can start from sampling. Let's say the study is to learn the behavior of teenagers in public parks. The researcher goes to one park to collect all the data and creates a dataset but missed to realize this park is in an affluent part of the city. The research is published as 'behavior of teenagers in parks', instead of 'behavior of affluent teenagers in parks'.

Biases in datasets can come from poor analysis. They surveyed football quarterbacks in the US for vaccination status. Responders said they were vaccinated. One quarterback answered that he was immunized. What's the definition of 'immunized'? Fox News publishes an article using Department of Labor data where they asked the US population if they are currently working. The survey had 60K observations implying that 147M people are employed. However, those that answered 'Yes' (I am working) didn't explain if they are working full time or part-time or volunteering or were part of the workforce.

Biases in datasets can come from shiny visualizations. Any visualization can mislead if they have these:

* The Y-axis doesn't have a zero. For example, a scatter plot that shows a big difference among observations might be misleading and once you zoom out, the observations all appear the same.
* Inverting the axes X with Y, perhaps to better fit the graph on a website or to make it look cooler could mislead to a wrong conclusion.

These visualizations don't show up only on Fox News. I read research papers from respected organizations that contained similar graphs. These also show up all over social media to attract web visitors. They generate the 'oh wow, really?' but they mislead assuming the reader's ignorance.

## Median vs Mean

I analyzed the popular Stackoverflow survey to understand fair compensation across all demographics and reduce the biases with an ML algorithm. Exploring the dataset with bar charts in matplotlib always showed a very high mean in compensation. A popular social media website could publish this as 'highly compensated engineers get paid beyond $300,000'. However, the dataset contained some really strange outliers where using the median would cut the mean compensation by half.

## Simpson's Paradox

Not named after Homer Simpson. It's something that appears as a trend in individual groups and shows the opposite trend in combined groups.

That's like if an analysis said that COVID was increasing in individual states but was decreasing in general in the US. A publication could mislead the population saying that COVID is decreasing across the country.

Stanford's example is a bit more technical. It suggested that coffee intake can increase IQ test performance, however testing individual groups, decreased performance. 'The reason for the confounding is the causal impact of the hidden covariate, education level, on both coffee consumption and performance'. More [here](https://plato.stanford.edu/entries/paradox-simpson/#NonCateDataLineRegr). In English, this means that the analysis can mislead that drinking coffee makes you smarter while taking a test, but it shows the opposite effect when analyzing individual groups divided by education level.

## Correlation vs Causation

This is the typical topic when talking about statistics. Correlation doesn't imply causation. Umbrellas protect you from rain but buying a lot of umbrellas doesn't imply it will rain.

Here is one related to recruiting.

Recruiting is a numbers game.

Candidates find jobs through recruiters' emails. It doesn't imply that sending hundreds or thousands of messages will help candidates find jobs. As seen on Linkedin, a software engineer posted a picture of a recruiter message asking them to join a company. The engineer said this was the 3rd email they got and told them again weren't interested.

## Is word2vec evil?

Word2vec is an algorithm for NLP created by Google and the algorithm is used by most companies that have a social media, search engine, or ecommerce app to produce sentiment analysis, understand user search queries, or for recommendation systems.

Why is it evil?

It doesn't play well with these demographics:

* Hispanic
* Black
* Asian
* Women

Has the algorithm been fixed for bias since being published? No. Although Google Scholar shows about 3,800 results when querying 'word2vec bias' and 5,800 results without the word 'bias'.

Why is word2vec biased in the first place? If the corpus (whole dataset of sentences and words) has the word engineer with a male pronoun then it will associate this word with males. Try reading the Mythical Man Month. Most of the book associates the word engineer with a male pronoun and rarely uses 'she'. If this book was fed into an NLP algorithm, it would always associate engineers as male.

## Is LFW evil?

LFW (Labeled faces in the wild) is a dataset of face pictures used for facial recognition. This technology can be used on any app that uses pictures or videos. Those apps where you like to post your pictures and your videos.

Why is it evil?

It's not evil. It works well. Unless you are part of these demographics:

* Hispanic
* Black
* Asian

A query for LFW in Google Scholar shows 5,000 results for 'LFW bias' and 31,000 results without the word 'bias'.

Have they fixed the dataset for bias? No.

I tested a popular web app for face recognition. It identifies Jason Momoa as Hispanic and John Legend as Asian.

Want to know if your pictures were used in biometric surveillance research? Try searching your name or search by hashtag in Flickr. If you ever used Flickr as far as 2004. Completely unregulated and lack data privacy or data management practices. Oh and also you can't remove your photos from these datasets.

## Main source of bias is human

The conclusion is the main source of bias is human. As they say garbage in, garbage out. The source of bias is human when the data is collected about humans, through surveys, and through users interacting with apps.

AI Ethics proposes methods for explainable AI and fairness of AI and machine learning algorithms. However, I see the current reality is far from this dream. There is hope to grow teams focused on responsible AI and for regulation in dataset governance.

[![Ask me anything on Linkedin]({{ site.baseurl }}/assets/images/ama-linkedin-tomordonez.png)](https://www.linkedin.com/in/tomordonez/)