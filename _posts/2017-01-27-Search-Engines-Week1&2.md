---
layout: post
title: Search Engines Week1&2
subtitle: 
date: 2017-01-27
categories: Blog
tags: [11642]
catalog: true
---

### Search Engines Week 1&2

试图加深一下理解记忆，欢迎捉虫(/ω＼)

####	Introduction to Shallow Language Processing

一些概念：

terms： 词项

documents：文档 unstructrued information

query: an approximate description of the information need

collections：文集

tokens：word occurrences 词条

types：unique words 词条类

Search Engines consider two types of language properties:

Language-depend properties/ Language-independ properties (focus)

**Term Frequency**: skewed, slop may change

Two laws describe how words are used in human language.

##### Heaps' Law: 

Predicts the number of distinct terms (vocabulary size, V)

V = KN^ℬ 

K: usually [10, 100]

ℬ : usually [0.4, 0.6]

N:  total number of word occurrences

##### Zipf's Law:

MLE probability of observing t in corpus C: P(t) = ctf(t)/N

ctft: Collection term frequency – how often t occurs in C

N: Total word occurrences in corpus C

MLE probability of observing the R’th ranked term: P(tR) = ctf(tR)/N

Empirical observation: P(tR) = ctf(tR)/N ~ A/R   (A ~ 0.1 in English)

**Rank * Frequency = Constant = 0.1\*N**

Satistical Properities of Text:

Vocabulary size: 0.1 × N

Number of terms that occur n times: 0.1\*N/n - 0.1\*N/(n+1)

#### Introduction to Search


Goal: Find documents that satisfy the information need

一些概念：Boolean Query / Query tree／Free／Full-text indexing

Binary Full-Text Representation：Inverted list, position and frequency are ignored


Frequency Full-Text Representation: Record the frequency of each word in each document

##### Unranked Boolean

Exact match retrieval, unordered matching set of documents

Operators: AND, OR, ANDNOT, NEAR, DATE, ...

#####			Ranked Boolean

Ranked by a document score - 		

- Unranked Boolean document scores are 0 and 1
- Ranked Boolean document scores can be anything

Calculating scores: 

tf(t,d): The frequency of term t in document d
• tf(t,d) × idf(t) = tf(t,d) × log (N / dft): Penalize common terms

​	– N: Number of documents in the corpus
​	– dft: The number of documents that contain term t

Both types of weights are used
• tf(t,d): Invented first, easy to implement, only considers the document
• tf(t,d) × idf(t): More effectiv				

• score (qAND (q1 ... qn), dj) = MIN (score (q1, dj), ..., score (qn, dj))

• score (qOR (q1 ... qn), dj) = MAX (score (q1, dj), ..., score (qn, dj))

• score (qOR (q1 ... qn), dj) = MEAN (score (q1, dj), ..., score (qn, dj))	

##### Inverted Lists:

One could compare to query terms (columns)

• Columns are bit vectors
• Columns are called inverted lists Corpus
• Complexity is O(|C|×|Q|)			
Different types of inverted lists support different capabilities

• Binary inverted lists

​	– Unranked retrieval

​	– AND, OR, AND-NOT, FIELD Boolean operators

• Frequency inverted lists
​	– Ranked retrieval
​	– The above operators plus SUM, SYNONYM

• Positional inverted lists
​	– Ranked retrieval
​	– The above operators plus positional operators

​	» NEAR/n, SENTENCE/n, PASSAGE/n, WINDOW/n

- B-tree: O(1) lookup exact-match lookup
- HashTable: O(logn) look up, exact-match lookup	

#### Introduction to Search

Query processing is a simple task
• Input: Two or more lists (e.g., inverted lists)

​	– One list for each query argument• Output: One list

​	– The result list

There are three approaches to query processing

• Term-at-a-Time (TAAT)
• Document-at-a-Time (DAAT)
• TAAT / DAAT hybrids

##### TAAT

Key idea

• Fully process listi before proceeding to listi+1
• When a list is processed, partial document scores are updated

TAAT memory usage is uncontrolled

• Each query operator stores in RAM up to 3 lists simultaneously– arg1, arg2, result

• A query of depth d must store d+2 lists in RAM simultaneously

TAAT systems can run out of memory

• Queries with frequent terms (long lists)
• Complex queries (more lists)
• Systems that process multiple queries in parallel (contention)

Rarely used in large-scale systems			

##### DAAT

Key idea

• Compute a complete score for doci before proceeding to doci+1

The following example assumes an unranked Boolean model.

• All scores are 1
• The same architecture can be used for ranked Boolean

Beginning of loop

• Examine the current document id in each list

• Find the minimum id		

• Examine each list that contains currentId to compute currentScore

• Store the result


DAAT memory usage is easy to control

• It needs simultaneous access to all inverted lists (which seems bad)• But ... inverted lists are read from disk into RAM in blocks

​	– E.g., read the inverted list in 64MB blocks

• When the end of the current block is reached, read the next block
• The block size determines how much RAM the query uses				

Frequently used in large-scale systems			

##### Query operators

**NEAR/n**: Distancebetweenadjacentargumentsis≥0&&≤nterms


Sentence/n: Like NEAR/n, but distance is measured in sentences
Paragraph/n: Like NEAR/n, but distance is measured in paragraphs


The NEAR/n operator is used to match names and phrases

• Arguments must be matched in order

• n specifies the maximum distance between adjacent terms				

The greedy algorithm does not handle duplicate arguments
• The distance constraint can be modified to support this case

​	– E.g., 0 < distance (t1, t2) ≤ n	

**WINDOW/n**:

Arguments can be in any order

n specifies the maximum distance between any pair of terms

##### Three Types of Query Operators

There are three important classes of query operators

• Operators that combine inverted lists

​	– E.g., #NEAR/n, #SYNONYM, #WINDOW/n, ...

​	– Dynamically create new index terms / concepts

• Operators that convert inverted lists to scores
​	– Usually invisible or implied
​	– Usually just one – we will call it #SCORE

• Operators that combine score lists
​	– E.g., #AND, #OR, #AVERAGE, #MAX, ...
​	– Combine evidence				

 2017 新年快乐恭喜发财	
​	

​			
​		
​	


​				
​			
​		
​						
​				
​			
​		
​					
​			
​		
​					
​			
​		
​	


​				
​			
​		
​					
​			
​		
​	

​				
​			
​		
​					
​			
​		
​	
​		
​	


​				
​			
​		
​	



