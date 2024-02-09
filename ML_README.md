# [Stage 0] Design Document for Relevant Documents Retrieval System

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

- **Weighing Strategy:** Utilizing ML and AI, the system will create an advanced weight-index of all documents by extracting key features from the raw HTML content. This index will serve as the foundation for the retrieval process, enabling rapid access to relevant documents based on the search term.
- **Retrieval Strategy:** The core of the retrieval system will be a storage designed to operate with O(1) time complexity during the querying phase. This will likely involve a combination of hashing techniques and precomputed relevance scores to ensure immediate access to indexed documents based on the queried word.
- **Relevance Scoring:** An AI-based scoring model will be developed to evaluate the relevance of documents to the search term. This model will consider various factors, including keyword frequency, document structure, and possibly the semantic context of the search term within the document.

## 1. Preprocessing Phase

### 1.1 HTML Parsing and Feature Extraction

- **Text Extraction**: Utilize a parsing API (assumed to be given to us) to extract plain text from HTML documents. This includes all textual content, ignoring scripts and non-visible elements. Extract HTML structural elements such as headings (H1, H2), bold text, links, and lists. These elements often indicate key sections and terms within the document, which can be of more importance and may better represent a given document.

- **Standardization**: We utilize the provided APIs for stop-word removal and stemming to ensure that our extracted words are standardized. This process filters out stop words, which are common but offer little value in determining document relevance, and applies stemming to reduce words to their base or root form. By doing this, we eliminate variations of the same word due to different grammatical constructs, ensuring a more accurate and efficient indexing and retrieval process.
  
- **Structural Analysis and Feature Extraction**: In order to enhance the accuracy of our document retrieval system, we will assign weights to text based on HTML tags. This approach recognizes that certain HTML tags may signal a higher importance of the enclosed text, reflecting its potential relevance to the search query. For instance, text enclosed within `<H1>` tags is often used for main headings, indicating a summary or a focal point of the document, whereas `<strong>` or `<b>` tags emphasize text that the document's author considers important.

Given this, let's proceed with the following initial weighting scheme:

- `<H1>` tags: Weight of 1.5 – Signifies main headings, typically the most relevant summary of the content.
- `<H2>` tags: Weight of 1.3 – Represents secondary headings, still highly relevant but slightly less so than H1.
- `<H3>` to `<H6>` tags: Weight of 1.1 – These are lower-level headings, indicating structured content but of gradually decreasing relevance.
- `<strong>`, `<b>` tags: Weight of 1.2 – Emphasized text that the author deems important, likely to be relevant to the search term.
- `<title>` tag: Weight of 1.5 – Contains the title of the document, which is often highly indicative of the overall content and relevance.
- Plain text: Weight of 1.0 – Standard text with no additional emphasis or structural importance indicated.

It is important to note that these weights are initial estimates and may require adjustment based on empirical testing and analysis to optimize the performance and accuracy of the retrieval system.

- **Semantic Embedding Generation and LLM training**: In order to make sure that we do not ignore the contexual information, we use various LLMs to generate semantic embeddings from the extracted text. These embeddings are generated by training our LLMs with the documents from our local machine. This may seem to be a big task at first, but since each document is around 5-50 kB, for 100k documents in total the size is of the range 500 MB - 5 GB, which is definitely tractable for the proposed analysis.

### 1.2 Document Scoring and Insights Storing Mechanism

- **Word Weighted Frequency Map Construction**: We calculate the frequency of each word within a document, adjusting this frequency based on the weight assigned to the text according to its HTML tag (as outlined in the Structural Analysis and Feature Extraction section). After calculating the weighted score for each word in a document, we then insert this data into a hashmap. This hashmap is structured to associate each word with a nested hashmap, where each key-value pair contains a document identifier (e.g., the document's name or a unique ID) and the corresponding weighted score of the word in that document respectively. The format of the map is as follows:

```
{
  "word1": {
              "doc 1 name": doc_1_weight,
               "doc 2 name": doc_2_weight,
                 ...,
               "doc 100k name": doc_100k_weight
            },
  "word2": {
              "doc 1 name": doc_1_weight,
               "doc 2 name": doc_2_weight,
                 ...,
               "doc 100k name": doc_100k_weight
            },
  ...
}
```

This structure allows for efficient retrieval of document relevance scores based on specific search terms. By aggregating these scores and normalizing them across all documents for a given word, the system provides a ranking of each document between 0 and 1 for a given word.

- **Weight Adjustment Through Pre-trained LLMs**: We provide the pretrained LLMs (e.g. GPT, Bard, Claude, LLaMa etc.) with the above mentioned map, and ask each of them to provide us with a version of the provided map, where the weights of documents are modified on a word-by-word basis based on the "contextual information" that they captured. Then, we collate and average the resulting tables given by each of the available LLMs with the initial weighted map we had provided the LLMs to ensure we have some regularization in our system.

- **Sorting Mechanismbased on the updated weights**: We take the hashmap, and at a word by word level, we access the nested map and sort it by value in descending order. This makes sure that our nested hashmap is now sorted for each word mapping.

## 2. Querying Phase (O(1) Access)

### 2.1 Loading the Map in Memory

- Load the organized hashmap into memory at the start of the querying phase for best performance in retrievals.

### 2.2 Query Processing and Execution

- **Normalization**: Apply the same text normalization (stemming and stop word removal) to the search term as used in preprocessing.
- **Retrieval**: Instantly retrieve the pre-sorted sub-hashmap associated to that word, and return the top k (say 10) document names.

## [FUTURE] Potential Scalability and Optimization (While Staying on the Local Machine)

- **Parallel Processing**: Utilize multi-threading or multiprocessing during the preprocessing phase for faster parsing, feature extraction, and model training purposes.
- **Cosine Similarity for Robustness**: To make the system more error proof, if the entered query word doesn't match is any keys in the parent map constructed, then we check the cosine similarity of the same with some words that are present in the map as keys. 
