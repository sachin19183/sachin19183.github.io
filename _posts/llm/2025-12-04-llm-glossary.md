---
layout: single
title: LLM Text Processing Glossary
date: 2025-12-04
categories: [LLM, Fundamentals]
---

A beginner-friendly glossary of key terminology used in Large Language Models (LLMs) and natural language text processing.

---
## Core Concepts
- Tokenization :  The process of spliiting up sentences into individual words or subwords called tokens.
- Vocabulary : The full set of tokens a model can understand or predict
- Embedding : Numerical vector representationof tokens used so models can process them mathemaiclly (the language that computers understand)
- Transformer : The neural architecture buil around modern LLMs -built around self attention
---

## Model Behavior & Mechanics
- Context window : The maxium number of tokens the model can consider at once
- Attention : Mechanism that helps the model focus on relevant parts of the input while predicting
- Self Attention : Model attends to the different parts of the same text sequence to understand relationship between them
- Positional encoding : Adds order information to tokens so the model knows sequence position
---

## Model Lifecycle
- Parameters : Model's learned weights (they represent its knowledge) - billions in large LLMs
- Training :LLM phase where the model learns patterns and languages by adjusting parameters using large datasets
- Inference : Model generating output from learned knowledge (The LLM phase post training)
- Fine Tuning : Training the LLM on domain specific data for specialization
- Alignment : Making the model's behaviour safe, helpful
---

## Prompting & Output Generation
- Prompt: The text input feeded to the LLM model by the end user
- Temperature : Controls randomness in text generation ( higher value means more creative, less value means more grounded and based on facts)
- Sampling : Method for selecting the next token
---

## Evaluation & Reliability
- RAG : Retrieval Augmented Generation, Combines an LLM with an external knowledge source (like a vector database) to improve factual accuracy and reduce hallucinations during inference
- Loss Function : Measures prediction error during training 
- Hallucination : When the model generates wrong output with confidence
---

## Model Optimization
- Quantization : Reducing model precision to run faster and smaller
- Pruning : Removes less important parameters to make the model lightweight
- Distillation : Trains a smaller "Student" model to mimic a larger "teacher" model
