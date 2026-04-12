# Transformer Models

This course will teach you about:

* large language models (LLMs) and natural language processing (NLP) using libraries from the:
  * [Hugging Face](https://huggingface.co/) ecosystem —
    * [🤗 Transformers](https://github.com/huggingface/transformers),
    * [🤗 Datasets](https://github.com/huggingface/datasets),
    * [🤗 Tokenizers](https://github.com/huggingface/tokenizers), and
    * [🤗 Accelerate](https://github.com/huggingface/accelerate)
    * as well as the [Hugging Face Hub](https://huggingface.co/models).

We’ll also cover libraries outside the Hugging Face ecosystem. These are amazing contributions to the AI community and incredibly useful tools.

## Understanding NLP and LLMs

While this course was originally focused on NLP (Natural Language Processing), it has evolved to emphasize Large Language Models (LLMs), which represent the latest advancement in the field.

### What’s the difference?

* **NLP (Natural Language Processing)** is the broader field focused on enabling computers to understand, interpret, and generate human language. NLP encompasses many techniques and tasks such as sentiment analysis, named entity recognition, and machine translation.

* **LLMs (Large Language Models)** are a powerful subset of NLP models characterized by their massive size, extensive training data, and ability to perform a wide range of language tasks with minimal task-specific training. Models like the Llama, GPT, or Claude series are examples of LLMs that have revolutionized what’s possible in NLP.

Throughout this course, you’ll learn about both traditional NLP concepts and cutting-edge LLM techniques, as understanding the foundations of NLP is crucial for working effectively with LLMs.

- **Course Overview**
  - Chapters 1–4: Introduction to 🤗 Transformers, model usage, fine-tuning, and sharing on the Hub
  - Chapters 5–8: Basics of 🤗 Datasets and Tokenizers; covers common NLP tasks and LLM techniques
  - Chapter 9: Building and sharing model demos on the 🤗 Hub
  - Chapters 10–12: Advanced LLM topics like fine-tuning, dataset curation, and reasoning models

- **Prerequisites**
  - Good knowledge of Python required
  - Recommended: Introductory deep learning course (e.g., fast.ai or DeepLearning.AI)
  - No prior PyTorch/TensorFlow required (but helpful)

- **Post-Course Recommendation**
  - DeepLearning.AI NLP Specialization for traditional NLP models (e.g., Naive Bayes, LSTMs)

- **Authors**
  - Team of ML engineers and researchers from Hugging Face with backgrounds in NLP, deep learning, and open-source tools
  - Contributors include creators of Gradio and co-authors of NLP with Transformers

- **FAQ Highlights**
  - No certification currently (planned for future)
  - Time commitment: ~6–8 hours per week per chapter
  - Questions: Use Hugging Face forums
  - Code: उपलब्ध via Colab, SageMaker, and GitHub notebooks repo
  - Contributions: Open for issues, translations, and improvements
  - License: Apache 2.0 (free to reuse with attribution)

- **What You’ll Learn First**
  - Using `pipeline()` for NLP tasks (text generation, classification)
  - Understanding Transformer architecture
  - Differences between encoder, decoder, and encoder-decoder models