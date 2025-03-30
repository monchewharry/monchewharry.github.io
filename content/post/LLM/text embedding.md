---
draft: false
authors:
  - admin
title: Text embedding
date: 2025-03-14
summary: Overview of text embedding.
tags: [NLP,LLM]
categories: [LLM]
---

Text embeddings are a powerful tool for representing meaning in a way that machines can understand. They bridge the gap between human language and mathematical representations, enabling a wide range of NLP applications.
## 1. Linguistic Aspects: Representing Meaning as Vectors

* **Core Idea:**
    * Embeddings aim to capture the semantic meaning of words, phrases, or even entire documents as dense vectors in a high-dimensional space.
    * Instead of treating words as discrete symbols (like in one-hot encoding), embeddings represent them as continuous vectors, where similar words have vectors that are close to each other in the vector space (Linear Space).
* **Semantic Relationships:**
    * The key advantage is that embeddings encode semantic relationships. For example, the vectors for "king" and "queen" will be closer than the vectors for "king" and "kind."
    * This allows models to understand analogies ("king - man + woman = queen") and other complex linguistic relationships.
* **Contextual Embeddings:**
    * Modern embeddings, like those from BERT or GPT, are contextual. This means that the embedding of a word depends on the surrounding words in the sentence.
    * For example, "bank" in "river bank" and "bank account" will have different embeddings.
    * This addresses the issue of polysemy (words with multiple meanings).
* **Beyond Words:**
    * Embeddings are not limited to words. They can represent:
        * Phrases
        * Sentences
        * Documents
        * Even concepts or entities in a knowledge graph.
    * This allows for many kinds of natural language processing tasks.

## 2. Mathematical Aspects: Vector Spaces and Distance Metrics

* **Vector Spaces:**
    * Embeddings live in a high-dimensional vector space (e.g., 300 dimensions).
    * Each dimension represents a latent feature of the word or concept. These features are not easily interpretable by humans, but they capture subtle aspects of meaning.
* **Distance Metrics:**
    * To measure the similarity between embeddings, we use distance metrics:
        * **Cosine Similarity:** Measures the angle between two vectors. It's often preferred for embeddings because it focuses on the direction of the vectors, rather than their magnitude.
        * **Euclidean Distance:** Measures the straight-line distance between two vectors.
        * These metrics allow us to quantify how semantically similar two pieces of text are.
* **Dimensionality Reduction:**
    * Techniques like [[PCA]], t-SNE, UMAP, MDS can be used to visualize embeddings in 2D or 3D, making it easier to see how words are clustered based on their meaning.
* **Mathematical Operations:**
    * Vector arithmetic can be performed on word embeddings to produce interesting results. For example:
        * `vector("king") - vector("man") + vector("woman") ≈ vector("queen")`

## 3. Programming Aspects: Implementation and Usage

* **Libraries:**
    * Popular libraries for working with embeddings:
        * **TensorFlow/Keras:** For training and using custom embeddings.
        * **PyTorch:** Another powerful deep learning framework with extensive embedding support.
        * **spaCy:** Provides pre-trained word and document embeddings.
        * **Transformers (Hugging Face):** Offers easy access to state-of-the-art pre-trained models like BERT, GPT, and their embeddings.
        * **Gensim:** A library for topic modeling and word embeddings (including Word2Vec).
* **Pre-trained Embeddings:**
    * Often, we use pre-trained embeddings (e.g., Word2Vec, GloVe, FastText, BERT embeddings) rather than training them from scratch.
    * These embeddings have been trained on massive text corpora and capture rich semantic information.
* **Embedding Layers:**
    * In neural networks, an embedding layer is used to convert input tokens (words or other discrete units) into dense vectors.
    * This layer can be trained as part of the network or initialized with pre-trained embeddings.
* **Workflow:**
    1.  [[tokenization]]: Convert text into tokens (words, subwords, etc.) then to integer indices within the vocabulary.
    2.  **Embedding Lookup:** Look up the embedding vector for each token.
    3.  **Vector Processing:** Use the embeddings as input to a neural network or other machine learning model.
    4.  **Similarity Calculations:** Use cosine similarity or other metrics to compare embeddings.
* **Example python code using hugging face transformers:**

```python
from transformers import BertTokenizer, BertModel
import torch
from sklearn.metrics.pairwise import cosine_similarity

tokenizer = BertTokenizer.from_pretrained('bert-base-uncased')
model = BertModel.from_pretrained('bert-base-uncased')

def get_embedding(text):
    inputs = tokenizer(text, return_tensors='pt')
    with torch.no_grad():
        outputs = model(**inputs)
    return outputs.last_hidden_state.mean(dim=1) # Mean pooling to get sentence embedding

text1 = "The cat sat on the mat."
text2 = "A feline rested on the rug."

embedding1 = get_embedding(text1)
embedding2 = get_embedding(text2)

similarity = cosine_similarity(embedding1, embedding2)
print(f"Cosine similarity: {similarity[0][0]}")
```

## Embedding storage

{{< callout note >}}
> **Key Considerations:**
> 
> * **Scale:** The volume of embeddings and the required query performance.
> * **Similarity Search Efficiency:** The speed and accuracy of similarity search.
> * **Metadata Storage:** The ability to store and query metadata associated with embeddings.
> * **Integration:** How well the storage solution integrates with existing systems.

{{< /callout >}}

**1. Vector Databases:**

* **Purpose:**
    * Specifically designed for efficient storage and retrieval of vector embeddings.
    * Optimized for similarity search, which is crucial for applications using embeddings.
* **Examples:**
    * **Chroma:** An open-source embedding database that simplifies LLM application development.
    * **Pinecone:** A popular vector database service.
    * **Weaviate:** An open-source vector database.
    * **Milvus:** Another open-source vector database built for AI applications.
* **Key Features:**
    * Efficient similarity search algorithms (e.g., approximate nearest neighbor).
    * Scalability to handle large volumes of embeddings.
    * [[metadata]] storage alongside vectors.

**2. Traditional Databases with Vector Extensions:**

* **Purpose:**
    * Leveraging existing database infrastructure to store and query embeddings.
    * Adding vector search capabilities to traditional databases.
* **Examples:**
    * **PostgreSQL with <mark>pgvector</mark>:** The pgvector extension enables PostgreSQL to store and query vector embeddings efficiently.
    * **BigQuery:** Google BigQuery now offers vector search capabilities.
* **Advantages:**
    * Integration with existing database systems.
    * Combining vector search with other database operations.

**3. Cloud-Based Vector Search Services:**

* **Purpose:**
    * Cloud providers offer managed services for <mark>vector search</mark>.
    * Simplifying the deployment and management of vector search infrastructure.
* **Examples:**
    * **Google Vertex AI Vector Search:** A managed service for efficient similarity search.
    * **Amazon OpenSearch Service:** Offers k-NN (k-nearest neighbors) search for vector embeddings.
* **Benefits:**
    * Scalability and reliability provided by cloud infrastructure.
    * Reduced operational overhead.

**4. Simple File Storage:**

* **Purpose:**
    * For smaller-scale applications or development purposes, embeddings can be stored in files.
    * Examples of file types that can be used are pickle files, or CSV files.
* **Limitations:**
    * Not suitable for large-scale applications due to performance limitations.
    * Lack of efficient similarity search capabilities.