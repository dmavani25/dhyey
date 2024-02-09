# [Stage 0] Design Document for Document Retrieval System

### Author Information
- **Name:** Dhyey Mavani
- **Major:** Computer Science, Mathematics and Statistics
- **Institution:** Amherst College
- **Class Year:** 2025
  
## Objective

The primary goal of this project is to develop a document retrieval system capable of accurately identifying and ranking static HTML documents based on their relevance to a given single-word search term. The system will employ machine learning (ML) and artificial intelligence (AI) techniques for sophisticated feature extraction and scoring to optimize search results. This initiative seeks to deliver efficient querying capabilities on standard local machines by achieving an O(1) time complexity for result retrieval. This efficiency is made possible by conducting the bulk of the computation-intensive processing in advance, thus streamlining the querying process and significantly reducing computational time during the search phase.

## Assumptions and Specifications

### Given Conditions
- **Document Count:** The system will handle a dataset comprising 100,000 documents.
- **Document Stability:** Documents are considered static, with no changes expected post-indexing.
- **Document Format:** All documents are in raw HTML format, with an average file size of 4 kB.
- **Computational Environment:** The entire indexing and retrieval process is designed to be executed on a local machine without the need for extensive computing resources.
- **Synonyms Handling:** The current scope of the project does not include synonym resolution; this aspect may be considered for future enhancements.
- **Relevance Definition:** The system allows for a flexible interpretation of "relevance" based on the specific requirements of the search query and the underlying logic of the retrieval algorithms. The criteria for relevance will be explicitly defined and justified within the project documentation.
- **Preprocessing Tools:** It is assumed that necessary preprocessing tools, including parsing, stemming, and stop-word removal APIs, are readily available and accessible for use in the project. These tools are critical for preparing the documents for efficient indexing and retrieval.

### Technical Specifications

- **Indexing Strategy:** Utilizing ML and AI, the system will create an advanced index of all documents by extracting key features and metadata from the raw HTML content. This index will serve as the foundation for the retrieval process, enabling rapid access to relevant documents based on the search term.
- **Retrieval Strategy:** The core of the retrieval system will be a storage designed to operate with O(1) time complexity during the querying phase. This will likely involve a combination of hashing techniques and precomputed relevance scores to ensure immediate access to indexed documents based on the queried word.
- **Relevance Scoring:** An AI-based scoring model will be developed to evaluate the relevance of documents to the search term. This model will consider various factors, including keyword frequency, document structure, and possibly the semantic context of the search term within the document.
- **Performance Optimization:** Special attention will be given to optimizing the indexing and retrieval processes to ensure they can be executed efficiently on a local machine. This will involve careful management of computational resources and possibly the implementation of parallel processing techniques.

## 1. Preprocessing Phase

### 1.1 HTML Parsing and Feature Extraction

- **Text Extraction**: Utilize a parsing API (assumed to be given to us) to extract plain text from HTML documents. This includes all textual content, ignoring scripts and non-visible elements.
- **Structural Analysis**: Extract HTML structural elements such as headings (H1, H2), bold text, links, and lists. These elements often indicate key sections and terms within the document, which can be of more importance and may better represent a given document.
- **Semantic Embedding Generation**: Use NLP models (e.g., BERT, GPT) to generate semantic embeddings from the extracted text. These embeddings capture the contextual meanings of phrases and terms within each document.
- **Structural Feature Extraction**: Assign weights to text based on HTML tags, recognizing that certain tags (e.g., H1, bold) may indicate higher importance of the enclosed text.

### 1.2 Document Scoring

#### Relevance Scoring Criteria

- **Term Frequency (TF)**: Calculate the frequency of each term within a document. Terms appearing more frequently are deemed more relevant.
- **Inverse Document Frequency (IDF)**: Calculate the IDF for each term to determine its importance across the entire document corpus. Rare terms receive higher scores.
- **HTML Tag Weighting**: Assign additional weight to terms found within specific HTML tags (e.g., headers, bold text), indicating their increased relevance.

#### ML Model for Scoring

- **Training a Custom Model**: Train a supervised ML model to predict document relevance based on the features extracted above. This could involve:
  - A dataset of documents with manually labeled relevance scores for various terms. And, by the way, the manual labels can be much more easily generated nowadays with low human effort through guided prompt engineering coupled with the use of powerful LLMs (like ChatGPT 4.0 with extensions).
  - Feature vectors composed of TF-IDF scores, semantic embeddings, and HTML tag weights.
  - A regression or classification model (e.g., Random Forest, Gradient Boosting, or a simple neural network) that learns to predict the relevance of a document to specific terms based on these features.

### 1.3 Inverted Index Creation with Relevance Scores

- **Index Structure**: Create an inverted index where each entry consists of a term and a sorted list of top k "most relevant" documents containing that term. 
- **Serialization**: Serialize the inverted index into a format optimized for quick loading and access, ensuring the querying phase can retrieve documents efficiently.

## 2. Querying Phase

### 2.1 Index Loading

- Load the serialized inverted index into memory at the start of the querying phase.

### 2.2 Query Processing

- **Normalization**: Apply the same text normalization (stemming and stop word removal) to the search term as used in preprocessing.
- **Retrieval**: Instantly retrieve the pre-sorted list of documents and their relevance scores for the search term from the inverted index.

### 2.3 Filtering and Ranking

- **Threshold Application**: Filter the retrieved documents to include only those with relevance scores above a predetermined threshold. This ensures only the most relevant documents are presented.
- **Ranking**: Since the documents are pre-sorted by relevance score in the index, they are already in the correct order for presentation.

## Data Structures and Storage

- **Enhanced Inverted Index**: A key component of the system, it maps terms to documents and their relevance scores, enabling efficient O(1) querying.
- **[Future Work] Document Metadata**: Includes additional information required for ranking and presentation, such as document length, title, and URL.

## ML Models and Technologies

- **NLP Models for Semantic Analysis**: Pre-trained models like BERT or GPT, fine-tuned for the specific task of semantic embedding generation from document text.
- **ML Frameworks**: TensorFlow or PyTorch for developing and training custom models for document scoring, leveraging features extracted from text and HTML structure.

## System Requirements

- **Hardware**: An average local machine with enhanced capabilities (e.g., additional RAM for large indices, GPU for ML model training).
- **Software**: Requires NLP libraries for text processing, ML libraries for model training and inference, and custom software for managing the inverted index and handling queries.

## Potential Scalability and Optimization

- **Parallel Processing**: Utilize multi-threading or multiprocessing during the preprocessing phase for faster parsing, feature extraction, and model training.

## Conclusion

This design document outlines a sophisticated document retrieval system that uses machine learning to enhance the analysis, scoring, and indexing of HTML documents for relevance. By employing advanced NLP techniques and custom ML models, the system can accurately rank documents based on their relevance to search terms, ensuring efficient and effective retrieval on an average local machine.
