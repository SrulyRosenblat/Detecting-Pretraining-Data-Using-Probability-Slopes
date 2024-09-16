![AI Disclusures Logo](https://www.ssrc.org/wp-content/themes/ssrcorg-child/assets/images/aicr_logo2.png)


# Perplexity Slope Method
***Created as part of the AI Disclosures Project at the Social Science Research Council***

## Introduction

The **Perplexity Slope Method** is a novel approach for recognizing when text generated by a Large Language Model (LLM) is included in its training data. The theory is based on the observation that an LLM becomes more confident in its predictions as it processes text it has seen before. By isolating this increase in confidence, we aim to detect when a model recognizes familiar text.

## Theoretical Background

When an LLM encounters a sequence it has seen in its training data, its "confidence" or internal probability of predicting the next token should tend to rise as more of the sequence is processed. By analyzing the slope of this increasing confidence, we hypothesize that it is possible to detect when the model is recognizing text it has been trained on.

## Methodology

The core of our method is measuring the change in probabilities (or sureness) as the model generates text. However, simply analyzing the probabilities is insufficient, as some tokens are easier to predict regardless of context (e.g., punctuation or common words). To account for this, we employ various normalization techniques. Below is a step-by-step breakdown of our method:

### 1. Fitting a Line to Token Probabilities

To quantify the rise in token prediction confidence over time, we use **Linear Regression** (via `sklearn`'s `LinearRegression` model) to fit a line through the predicted probabilities of each token in the sequence. The coefficient (slope) of this line serves as our main metric, representing the increase in confidence.

### 2. Normalization of Probabilities

Some tokens are inherently easier to predict (e.g., periods at the end of sentences). To mitigate the effect of such tokens on the overall slope, we employ several normalization strategies.

#### 2.1 N-gram Normalization

We introduce an **N-gram Normalization** technique to remove the influence of particularly predictable tokens. Specifically, for a given token *t*, we subtract the probability of *t* given *n* preceding tokens from the probability of *t* given all preceding tokens. This allows us to adjust for how much of the token's predictability comes from general sentence structure rather than specific sequence context.

#### 2.2 Mean and Z-Score Normalization

Another method involves normalizing the slope by applying the **mean** and **z-score** to the token probabilities. After calculating the slope using either raw probabilities or N-gram adjusted probabilities, we compute the mean and standard deviation of the same probabilities. These statistics are then used to normalize the slope, accounting for outliers or irregularities in the data.

### 3. Measuring and Analyzing the Slope

Once the normalization is applied, the slope of the line fitted to the sequence's probabilities provides a measure of increasing confidence. If the slope is consistently positive and steeper than for unknown sequences, it can be an indicator that the model has seen the text before.

## Preliminary Results
*all results tested on the mamba 1.4b*
| Method                           | O'Reilly dataset (Technical books)      | BookTection dataset (fiction)   | WikiMia (Wiki articles, 128 word subset) | ArXiv dataset (academic papers) |
|-----------------------------------|-----------------------------------------|--------------------------------|------------------------------------------|---------------------------------|
| Slope Detection (1-gram mean adjusted) | 0.617 (0.572, 0.656)               | 0.789 (0.754, 0.822)           | 0.588 (0.517, 0.655)                    | 0.480 (0.434, 0.526)            |
| Slope Detection (best case)       | 0.631 (0.591, 0.671)                   | 0.824 (0.790, 0.855)           | 0.599 (0.521, 0.674)                    | 0.572 (0.528, 0.615)            |
| Mink++ (best case)                | 0.541 (0.498, 0.584)                   | 0.623 (0.580, 0.668)           | 0.689 (0.624, 0.751)                    | 0.736 (0.697, 0.776)            |




![image visualizing results](results.png)




