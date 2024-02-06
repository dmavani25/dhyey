# Design Document for Document Retrieval System

## Objective

Develop a system to efficiently retrieve and rank static HTML documents based on relevance to a given search term, ensuring fast query responses on an average local machine.

## Overview

The solution involves two main phases: preprocessing (1st pass) and querying (2nd pass). The preprocessing phase prepares the data for efficient querying, while the querying phase retrieves and ranks documents based on their relevance to a specified word.

## 1. Preprocessing Phase

### Goal

To process the input documents and create a data structure that enables O(1) time complexity for querying.

### Steps

1. **Document Parsing**: Use the provided parsing API to extract text from HTML documents, including special treatment for bold words which might indicate higher relevance.

2. **Text Normalization**:
   - Apply the stemming API to reduce words to their base form, enabling consistent matching despite word variations.
   - Use the stop word removal API to filter out common but low-information words.

3. **Index Creation**:
   - Build an inverted index where the key is a word (after stemming and stop word removal) and the value is a list of document identifiers (IDs) where the word appears. This index will support efficient lookup by word.
   - For each document, store additional metadata: the total word count and the count of occurrences of each word. This will help in calculating relevance.

4. **Relevance Scoring Criteria**:
   - Define relevance based on term frequency (TF), with a higher weight for words appearing in bold. The rationale is that terms appearing more frequently, especially if highlighted, are more relevant to the document's topic.
   - Optionally, incorporate inverse document frequency (IDF) if the preprocessing resources allow, to prioritize unique terms.

5. **Data Serialization**:
   - Serialize the inverted index and document metadata to a file or database optimized for read operations, ensuring quick loading during the querying phase.

### Output

A serialized form of the inverted index and document metadata, optimized for quick loading and search.

## 2. Querying Phase

### Goal

To retrieve and rank documents based on their relevance to a given search term in O(1) time.

### Steps

1. **Index Loading**:
   - Load the preprocessed inverted index and document metadata into memory. This operation's time complexity is not considered part of the query time, as it is a one-time setup step.

2. **Search Term Normalization**:
   - Apply stemming and stop word removal to the input word to match the preprocessing phase.

3. **Document Retrieval**:
   - Use the inverted index to instantly retrieve the list of documents containing the search term.

4. **Relevance Ranking**:
   - Rank the retrieved documents based on the predefined relevance scoring criteria, utilizing the stored word counts and occurrence data. This could be as simple as sorting by the term frequency within each document, with adjustments for bold words.

5. **Result Presentation**:
   - Return the ranked list of document IDs or paths, possibly with relevance scores for transparency.

### Data Store

- **Inverted Index and Document Metadata**: Stored on disk in a format that allows for rapid loading into memory. Options include binary formats like Protocol Buffers or custom binary structures designed for this purpose.

## System Requirements

- **Hardware**: Average local machine with sufficient memory to hold the inverted index and metadata in memory during the querying phase.
- **Software**: Requires a runtime environment compatible with the APIs provided for parsing, stemming, and stop word removal.

## Scalability and Optimization

- **Index Compression**: Implement compression techniques on the index to reduce memory usage, such as delta encoding for document IDs.
- **Selective Loading**: If memory constraints are an issue, design the system to load only the parts of the index necessary for querying specific terms.
- **Parallel Processing**: Utilize multi-threading during the preprocessing phase to speed up the processing of documents.

## Conclusion

This design document outlines a system capable of efficiently retrieving and ranking documents based on relevance to a given search term. By leveraging preprocessing to build an inverted index and utilizing efficient querying mechanisms, the system aims to provide rapid responses to search queries on an average local machine.
