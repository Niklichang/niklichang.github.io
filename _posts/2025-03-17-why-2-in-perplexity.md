---
layout: post
title:  "Why does perplexity have a base of 2?"
date:   2025-03-17
last_modified_at: 2025-03-17
categories: [Information Theory, Machine Learning]
---

&emsp; In my most recent class in Natural Language Processing, we were going over the derivation for Perplexity. My friend asked, "why is there a base of '2' in the equation?" 

&emsp; My professor replied, "Oh... you have to go back to Shannon's paper on Communication Theory... from 19... 40s..."

&emsp; This is genuinely how he talks, and I was both intruiged and unsettled. Why just give the equation, and tell someone to go back to a research paper and learn yourself? Most people won't, and so just telling people that 'this is perplexity' is a horrible way to make something stick. 

&emsp; At first glance, however, it makes sense: entropy is measured in bits, with a log base 2, and perplexity has a base 2. Buy why?

&emsp; Let us first review how we come upon this "thing" defined as perplexity in the context of language modeling. 

We start with the intuition that a good language model should be able to assign a real sentence in the target language with high probability, and like any good model, it should the best to predict on an unseen test. 

For a test set $\mathcal{D}$,
The probability for a sentence $s$ is given by the joint probability $p(s) = p(w_1, w_2, \dots, w_n)$, where $w_i$ is a word in that sentence.

And for the whole dataset, $\prod_{s\in \mathcal{D}} p(s)$

Next, normalize over all $M$ words in $\mathcal{D}$, $\sqrt[M]{\prod_{s\in \mathcal{D}} p(s)}$. A geometric mean normalization is useful in this situation since the probabilities are extremely varied in magnitude, and it scales the result back to a dimension consistent with the original values of $p(s)$.

We then simplify with log, $\frac{1}{M}\sum_{s \in \mathcal{D}}\text{log}\space p(s)$

And now the questionable equation, the perplexity of $\mathcal{D}$:
$$
Perplexity(\mathcal{D})= 2^{-\frac{1}{M}\sum_{s \in \mathcal{D}}\text{log}\space p(s)}
$$
The lower the perplexity, the better the LM. However, we can see that there are a lot of holes in this derivation. Is there a concrete reason for geometric normalization? Why simplify with log? Where did that negative sign come from? One hint that we have is that the exponential looks awfully familiar: negative sign, averaging over $M$, summation of log probabilities... It looks uncannily like the equation for entropy! Let us take a break from language models to explore wikipedia for an answer.

In information theory, perplexity is a measure of uncertainty in the value of a sample from a discrete probability distribution. The larger the perplexity, the less likely it is that an observer can guess the value which will be drawn from the distribution ([Wikipedia](https://en.wikipedia.org/wiki/Perplexity)).

As we can see, perplexity is actually a topic in information theory, and it has been applied to NLP/language modeling! 

Let us look at some of the math:

Perplexity is defined as:

$$
PP(p) := 2^{H(p)} = 2^{- \sum_x p(x) \log_2 p(x)} = \prod_x p(x)^{-p(x)}
$$
Where $H(p)$ is the entropy(in bits) of the distribution, and $x$ ranges over the events.

Furthermore, when this is applied to probability modeling, where we have the proposed model $q$ and the unknown distribution $p$, better models of $q$ of that unknown distribution $p$ will tend to assign higher probabilities $q(x_i)$ to the test events. Thus, they have lower perplexity because they are less "surprised" by the test model, which is equivalent to saying that better models have higher likelihoods for the test data, which means lower perplexity. 

In probability modeling, the perplexity of model $q$ is defined as 
$$
b^{-\frac{1}{N} \sum_{i=1}^N \log_b q(x_i)} = \left( \prod_i q(x_i) \right)^{-1/N}
$$
where $b$ is usually 2. 

Where did the extra probability before the log probability go? Well, the exponent $-\frac{1}{N} \sum_{i=1}^{N} \log_b q(x_i)$ may be also interpreted as cross-entropy:
$$
H(\tilde{p}, q) = - \sum_{x} \tilde{p}(x) \log_b q(x)
$$
where $\tilde{p}$ denotes the empirical distribution of the test sample (i.e., $\tilde{p}(x) = n/N$ if $x$ appeared $n$ times in the test sample of size $N$).

However, we often disregard this $\tilde{p}$ since the empirical distribution is is usually assumed to uniform over the test sample. When each test sample is considered equally as likely, it is easy to consistently focus on evaluating the model's predictive performance. 

Entropy tells us the expected or "average" number of bits required to encode the the outcome of a random variable using an optimal variable-length code. Since entropy is literally the exponential of perplexity, having root 2 would just tell us a more straightforward quantity. 

So, to answer the question of the root 2 in perplexity, we can say that the definition of perplexity has always been intimately tied with information theory, and more specifically entropy. While one tells you the possible number of bits to represent uncertainty, the other just unravels that number and tells you a more direct version of uncertainty! Simple idea, yet and deep and complex history.

I think this is a fault of the modern higher education system: specific majors, such as computer science, data science, and engineering, learn equations without realizing their mathematical roots. Is this really understanding? To understand is to take the lifelong path of education and learning (see the history of the Chinese word 知道)。One cannot understand an equation if they are only told to apply it. They have to realize why.