# Build LLM from Scratch

Reference: [Busiding LLMs from Scratch -  Vizuara Link](https://www.youtube.com/watch?v=Xpr8D6LeAtw&list=PLPTV0NXA_ZSgsLAr8YCgCwhPIJNNtexWu)

Lectures by:
🎓 Dr. Raj Dandekar (MIT PhD, IIT Madras department topper)
🔗 LinkedIn:   / raj-abhijit-dandekar-67a33118a  


🎓 Dr. Rajat Dandekar (Purdue PhD, IIT Madras department gold medalist)
🔗 LinkedIn:   / rajat-dandekar-901324b1  


🎓 Dr. Sreedath Panat (MIT PhD, IIT Madras department gold medalist)
🔗 LinkedIn:   / sreedath-panat-8a03b69a  

🎓 Sahil Pocker (Machine Learning Engineer at Vizuara)
🔗 LinkedIn:   / sahil-p-a7a30a8b  

🎓 Abhijeet Singh (Software Developer at Vizuara, GSOC 24, SOB 23)
🔗 LinkedIn:   / abhijeet-singh-9a1881192  

🎓 Sourav Jana (Software Developer at Vizuara)
🔗 LinkedIn:   / souravjana131  


## Lectures
* [Lecture 1: Building LLMs from scratch: Series introduction](#lecture-1-building-llms-from-scratch-series-introduction)
* [Lecture 2: Large Language Models (LLM) Basics]()
* [Lecture 3: Pretraining LLMs vs Finetuning LLMs]()
* [Lecture 4: What are transformers?]()
* [Lecture 5: How does GPT-3 really work?]()
* [Lecture 6: Stages of building an LLM from Scratch]()
* [Lecture 7: Code an LLM Tokenizer from Scratch in Python]()
* [Lecture 8: The GPT Tokenizer: Byte Pair Encoding]()
* [Lecture 9: Creating Input-Target data pairs using Python DataLoader]()
* [Lecture 10: What are token embeddings?]()
* [Lecture 11: The importance of Positional Embeddings]()
* [Lecture 12: The entire Data Preprocessing Pipeline of Large Language Models (LLMs)]()
* [Lecture 13: Introduction to the Attention Mechanism in Large Language Models (LLMs)]()
* [Lecture 14: Simplified Attention Mechanism - Coded from scratch in Python | No trainable weights]()
* [Lecture 15: Coding the self attention mechanism with key, query and value matrices]()
* [Lecture 16: Causal Self Attention Mechanism | Coded from scratch in Python]()
* [Lecture 17: Multi Head Attention Part 1 - Basics and Python code]()
* [Lecture 18: Multi Head Attention Part 2 - Entire mathematics explained]()
* [Lecture 19: Birds Eye View of the LLM Architecture]()
* [Lecture 20: Layer Normalization in the LLM Architecture]()
* [GELU Activation Function in the LLM Architecture]()
* [Shortcut connections in the LLM Architecture]()
* [Coding the entire LLM Transformer Block]()
* [Coding the 124 million parameter GPT-2 model]()
* [Coding GPT-2 to predict the next token]()
* [Measuring the LLM loss function]()
* [Evaluating LLM performance on real dataset | Hands on project | Book data]()
* [Coding the entire LLM Pre-training Loop]()
* [Temperature Scaling in Large Language Models (LLMs)]()
* [Top-k sampling in Large Language Models]()
* [Saving and loading LLM model weights using PyTorch]()
* [Loading pre-trained weights from OpenAI GPT-2]()
* [Introduction to LLM Finetuning | Python Coding with hands-on-example]()
* [Dataloaders in LLM Classification Finetuning | Python Coding | Hands on LLM project]()
* [Coding the model architecture for LLM classification fine-tuning]()
* [Coding a fine-tuned LLM spam classification model | From Scratch]()
* [Introduction to LLM Instruction Fine-tuning | Loading Dataset | Alpaca Prompt format]()
* [Data Batching in LLM instruction fine-tuning | Hands on project | Live Python coding]()
* [Dataloaders in Instruction Fine-tuning]()
* [Instruction fine-tuning: Loading pre-trained LLM weights]()
* [LLM fine-tuning training loop | Coded from scratch]()
* [Evaluating fine-tuned LLM using Ollama]()
* [Build LLMs from scratch 20 minutes summary]()

## Lecture 1: Building LLMs from scratch: Series introduction




## Lecture 2: Large Language Models (LLM) Basics

### What is a large language model?

Neural networks designed to understand, generate, and respond to human like text.
Deep neural networks trained on massive amounts of text data.

#### Why LLMs are "Large"

1. **Parameters:** Refers to the internal variables (weights and biases) the model learns during training. Modern LLMs typically contain billions or even trillions of these parameters.
2. **Training Data:** These models are trained on massive datasets (corpora) containing trillions of words from the internet, books, and code.
3. **Compute:** The scale of processing power (GPUs) and energy required to train these architectures is enormous.
4. **Capabilities:** Increased scale leads to "emergent abilities," allowing models to solve complex reasoning tasks they weren't explicitly trained for.

| Model Name | n_params | n_layers | d_model | n_heads | d_head |
| :--- | :--- | :--- | :--- | :--- | :--- |
| GPT-3 Small | 125M | 12 | 768 | 12 | 64 |
| GPT-3 Medium | 350M | 24 | 1024 | 16 | 64 |
| GPT-3 Large | 760M | 24 | 1536 | 16 | 96 |
| GPT-3 XL | 1.3B | 24 | 2048 | 24 | 128 |
| GPT-3 175B | 175B | 96 | 12288 | 96 | 128 |
| LLaMA-7B | 6.7B | 32 | 4096 | 32 | 128 |
| LLaMA-13B | 13.0B | 40 | 5120 | 40 | 128 |
| LLaMA-65B | 65.2B | 80 | 8192 | 64 | 128 |
| PaLM (Small) | 8B | 32 | 4096 | 16 | 256 |

Large but Smaller Models suitable for 16GB RAM Laptops:
| Model Name | n_params | n_layers | d_model | n_heads | d_head | License / Cost |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| TinyLlama-1.1B | 1.1B | 22 | 2048 | 32 | 64 | Free (Apache 2.0) |
| Phi-3 Mini | 3.8B | 32 | 3072 | 32 | 96 | Free (MIT) |
| Llama-3-8B | 8.0B | 32 | 4096 | 32 | 128 | Free (Llama 3 License) |
| Mistral-7B v0.3| 7.2B | 32 | 4096 | 32 | 128 | Free (Apache 2.0) |
| Gemma-2-2B | 2.6B | 26 | 2304 | 8 | 256 | Free (Gemma License) |
| Qwen2.5-1.5B | 1.5B | 28 | 1536 | 12 | 128 | Free (Apache 2.0) |
| Qwen2.5-7B | 7.6B | 28 | 3584 | 28 | 128 | Free (Apache 2.0) |
| Llama-3.2-3B | 3.21B | 28 | 3072 | 24 | 128 | Llama 3.2 | Mobile/Edge apps, summarizing, and rewriting. |
| Phi-3.5-Mini | 3.82B | 32 | 3072 | 32 | 96 | MIT | Logic, reasoning, and multi-lingual tasks. |
| Mistral-7B v0.3 | 7.25B | 32 | 4096 | 32 | 128 | Apache 2.0 | General purpose, RAG, and creative writing. |
| DeepSeek-R1-Distill-Qwen-7B | 7.61B | 28 | 3584 | 28 | 128 | MIT | Complex math, deep reasoning, and logic. |
| Qwen2.5-Coder-7B | 7.62B | 28 | 3584 | 28 | 128 | Apache 2.0 | Specialized programming and code completion. |
| SmolLM2-1.7B | 1.71B | 24 | 2048 | 32 | 64 | Apache 2.0 | Educational content and fast text extraction. |
| Gemma-2-9B | 9.24B | 42 | 3584 | 16 | 256 | Gemma | High-accuracy research and creative tasks. |
| Stable-LM-2-1.6B | 1.64B | 24 | 2048 | 32 | 64 | STBL-2.0 | Ultra-fast local chat and simple automation. |

**Tip for 16GB RAM**

* Gemma-2-9B is slightly larger but can run comfortably on 16GB if you use 4-bit (Q4_K_M) quantization.
* DeepSeek-R1 (Distilled version) is currently the top performer for mathematical and logical reasoning in this size class.



## Lecture 3: Pretraining LLMs vs Finetuning LLMs

## Lecture 4: What are transformers?

## Lecture 5: How does GPT-3 really work?

## Lecture 6: Stages of building an LLM from Scratch

## Lecture 7: Code an LLM Tokenizer from Scratch in Python

## Lecture 8: The GPT Tokenizer: Byte Pair Encoding

## Lecture 9: Creating Input-Target data pairs using Python DataLoader

## Lecture 10: What are token embeddings?

## Lecture 11: The importance of Positional Embeddings

## Lecture 12: The entire Data Preprocessing Pipeline of Large Language Models (LLMs)

## Lecture 13: Introduction to the Attention Mechanism in Large Language Models (LLMs)

## Lecture 14: Simplified Attention Mechanism - Coded from scratch in Python | No trainable weights

## Lecture 15: Coding the self attention mechanism with key, query and value matrices

## Lecture 16: Causal Self Attention Mechanism | Coded from scratch in Python

## Lecture 17: Multi Head Attention Part 1 - Basics and Python code

## Lecture 18: Multi Head Attention Part 2 - Entire mathematics explained

## Lecture 19: Birds Eye View of the LLM Architecture

## Lecture 20: Layer Normalization in the LLM Architecture

## GELU Activation Function in the LLM Architecture

## Shortcut connections in the LLM Architecture

## Coding the entire LLM Transformer Block

## Coding the 124 million parameter GPT-2 model

## Coding GPT-2 to predict the next token

## Measuring the LLM loss function

## Evaluating LLM performance on real dataset | Hands on project | Book data

## Coding the entire LLM Pre-training Loop

## Temperature Scaling in Large Language Models (LLMs)

## Top-k sampling in Large Language Models

## Saving and loading LLM model weights using PyTorch

## Loading pre-trained weights from OpenAI GPT-2

## Introduction to LLM Finetuning | Python Coding with hands-on-example

## Dataloaders in LLM Classification Finetuning | Python Coding | Hands on LLM project

## Coding the model architecture for LLM classification fine-tuning

## Coding a fine-tuned LLM spam classification model | From Scratch

## Introduction to LLM Instruction Fine-tuning | Loading Dataset | Alpaca Prompt format

## Data Batching in LLM instruction fine-tuning | Hands on project | Live Python coding

## Dataloaders in Instruction Fine-tuning

## Instruction fine-tuning: Loading pre-trained LLM weights

## LLM fine-tuning training loop | Coded from scratch

## Evaluating fine-tuned LLM using Ollama

## Build LLMs from scratch 20 minutes summary