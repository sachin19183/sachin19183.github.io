---
layout: single
title: History / Progression of Language AI Part-1
date: 2025-12-04
categories: [LLM]
tags: [Fundamentals]
---

## Bag of Words

Bag of Words (BoW) is a technique used for representing unstructured text. It originated in the 1950s but became widely popular in the early 2000s.

It provides a simple way to understand early text processing and how modern LLMs evolved over time.

### How it works

1. **Tokenize** the sentences provided as input.  
   Example:  
   `"Hello, how are you? Are you from India?"`  
   becomes  
   `"Hello"`, `","`, `"how"`, `"are"`, `"you"`, `"?"`, `"Are"`, `"you"`, `"from"`, `"India"`, `"?"`

2. All the **unique tokens** form the **vocabulary**.  
   Example vocabulary:  
   `"Hello"`, `"how"`, `"are"`, `"you"`, `"Are"`, `"from"`, `"India"`, `","`, `"?"`

3. Using the vocabulary, count the **frequency** of each word in the input.  
   This creates a numeric **vector representation** of text.

📌 *Illustration:*  
![Bag of Words](/images/bag_of_words.png)

---

### Limitations

- Space as a delimiter doesn’t work in languages like Mandarin.
- Completely ignores the **meaning** and **context** of words.
- Treats language as just a bag of words — order doesn’t matter.

➡️ This highlights how intelligent humans really are when understanding language 😄

---

## Word2Vec

In 2013, **Word2Vec** became one of the first highly successful techniques for capturing **semantics** using **word embeddings** — numeric vectors that encode meaning.

It is trained using **large corpora** of text (e.g., Wikipedia) using **neural networks** with weights (parameters) between layers.

📌 *Illustration:*  
![Neural Network](/images/neural%20networks.png)

---

### How it learns semantic meaning

Word2Vec learns by observing which words frequently appear near each other.

Example sentence:  
> “The cat sat on the mat.”

- “cat” is near “sat”
- “mat” is near “sat”
- “cat” and “mat” appear in similar contexts → **similar meanings**

Initially, every word gets a random vector like: [0.12, -0.44, 0.88, ...]

Then the model repeatedly tries to **predict neighboring words**:

| Model Prediction | Result |
|-----------------|--------|
| Correct | reward / reinforce vector positions |
| Wrong | adjust vectors |

Over time, **related words cluster together** in vector space:

| Words | Relationship |
|-------|--------------|
| king ↔ queen | close (similar roles) |
| coffee ↔ dog | far apart (unrelated) |

This leads to surprisingly meaningful behavior:

> **king – man + woman ≈ queen**

---


Embeddings can have many properties to represent the meaning of a word. 
Since the size of embeddings is fixed, their properties are chosen to create a mental representation of the word

📌 *Illustration:*  
![Embeding representation](/images/word2vec.png)

Together, these properties make sense to a computer and serve as a good way to translate human language into computer language.

---




