# Search Technologies Summary

## Initial Search Challenges in E-Commerce Context

- In 2005, e-commerce companies faced the challenge of implementing product search functionality over thousands of items (e.g., 5,000 products).
- The conventional approach involved using SQL queries with `LIKE` operators for pattern matching, e.g., `SELECT * FROM products WHERE name LIKE '%laptop%' OR description LIKE '%laptop%'`.
- This method worked efficiently with small datasets, returning results quickly (e.g., 50 milliseconds).
- However, as the product catalog grew to millions, the same queries slowed drastically (up to 30 seconds), frustrating users and management.
- The requirements evolved beyond speed:
  - **Faster search results**
  - **Relevance-based ranking** (e.g., prioritize MacBook Pro over a laptop bag)
  - **Robustness to typos and frequent user mistakes**, especially during time-sensitive sales.
- These increasing demands highlighted the limitations of traditional relational database search and motivated the development and adoption of specialized search engines like **Elasticsearch**.

---

## Relational Databases as Librarians and the Fundamental Limitations

- A relational database can be analogized to a librarian who knows where every book is but must check each book individually to find matches.
- For a query like "machine learning," the librarian/database scans every book/title/description sequentially.
- This linear scan results in **slow search times**, especially as the dataset scales to millions or billions of records.
- Additionally, traditional databases lack **relevance understanding**:
  - They cannot prioritize results based on how closely the content matches the query.
  - For example, a book titled *Introduction to Machine Learning* should rank higher than one which only mentions the term on a single page.
- Consequently, databases return matching results in a **random or arbitrary order**, negatively impacting user experience.

---

## The Information Explosion and the Need for Scalable, Relevant Search

- Around 2005, the explosion of digital content (websites, product catalogs, user profiles) made efficient search a critical problem for companies like Google, Amazon, and LinkedIn.
- Latency tolerance shrank to milliseconds, with anything over a couple of seconds considered unacceptable.
- These companies could not afford to lose users due to slow or irrelevant search results, as it directly impacted revenue and user retention.
- The search problem was not new but had been studied extensively since the 1960s by information retrieval researchers.
- This long research history paved the way for **transformative search technologies** based on novel indexing concepts.

---

## Introduction to the Inverted Index: The Paradigm Shift in Text Search

- The key breakthrough was the invention of the **inverted index**, which reverses the traditional search approach:
  - Instead of scanning every document/book for a term, the system builds an index mapping terms to their locations in documents.
- For example, for the term "machine," the inverted index stores a list of books and pages where "machine" appears (e.g., *Introduction to Machine Learning*: pages 1, 15, 23; *The Machine Age*: pages 5, 89).
- This index is created once when the documents arrive, enabling **fast lookups by term** rather than scanning entire content.
- The inverted index allows search engines to retrieve relevant documents quickly by looking up terms and their occurrences rather than scanning every document sequentially.
- This concept underpins popular search engines like **Elasticsearch**, which uses the **Apache Lucene** library to implement this inverted index technology.

---

## How Inverted Index Improves Search Relevance and Speed

- With the inverted index, the "librarian" can instantly find all books containing the query terms.
- Search engines add **relevance scoring** to rank results intelligently:
  - Term frequency (how often a term appears in a document) boosts relevance.
  - Term presence in important fields (e.g., title vs. content) increases relevance further.
- For example:

| Book Title                         | Term Frequency | Relevance Rank Reason                          | Rank |
|------------------------------------|----------------|------------------------------------------------|------|
| Introduction to Machine Learning   | High (hundreds)| Term appears in title + multiple times         | 1    |
| The Machine Age                    | Medium (few)   | Term appears in title but less frequent        | 2    |
| Coffee Machine Manual              | Low (few)      | Term appears in title but low frequency        | 3    |

- Relevance ranking ensures users see the **most meaningful results first** rather than random matches.

---

## Elasticsearch and the BM25 Algorithm

- Elasticsearch leverages Apache Lucene and an algorithm called **BM25** for relevance scoring.
- BM25 uses several parameters to rank documents:
  1. **Term Frequency (TF):** How often the search term appears within a document.
  2. **Document Frequency (DF):** How common the term is across all documents (rarer terms get more weight).
  3. **Document Length:** Adjusts scores so longer documents don't unfairly dominate.
  4. **Field Boosting:** Allows weighting terms differently depending on where they appear (e.g., title > description > content).
- These parameters are customizable through Elasticsearch's **JSON-based query DSL**, enabling flexible and powerful search configurations.

---

## Typo Tolerance and User Experience Enhancements with Elasticsearch

- Elasticsearch supports **typo tolerance** (fuzzy search), improving user experience by returning relevant results even when queries contain spelling mistakes.
- Example: Searching for "what is treading today" (typo for "trending") still returns results for "what is trending today."
- This feature is critical during high-pressure scenarios like sales, where users type quickly and may make errors.
- Similar search experiences are seen in Google and Amazon, where **type-ahead** and **autocomplete** features rely on such technologies.

---

## Choosing Between PostgreSQL Full Text Search and Elasticsearch

- Modern relational databases like PostgreSQL have introduced **full text search (FTS)** capabilities, making them viable for some search use cases.
- Elasticsearch is a specialized tool widely used not only for search but also for **log management and analytics** within the **ELK Stack** (Elasticsearch, Logstash, Kibana).
- Companies already using Elasticsearch for logs can leverage the same infrastructure for search, providing efficiency and consistency.
- The choice depends on existing infrastructure, scale, and search complexity.

---

## Demonstration Project: Comparing PostgreSQL Search vs Elasticsearch Search Performance

- A demo project was built using **Next.js** to compare traditional PostgreSQL search and Elasticsearch:
  - Dataset: 50,000 product reviews with sentiment labels (positive/negative).
  - PostgreSQL table schema: id, review (text), sentiment (keyword).
  - Elasticsearch index: mappings for review (text) and sentiment (keyword).
- Data ingestion:
  - CSV file with 50,000 entries inserted in batches into PostgreSQL and bulk-indexed into Elasticsearch.
  - Both systems hosted in US-West regions to minimize network latency differences.
- Query handling:
  - API accepts search terms and streams results from both PostgreSQL and Elasticsearch independently to avoid blocking.
  - PostgreSQL query: `SELECT * FROM reviews WHERE review ILIKE '%search_term%'`.
  - Elasticsearch query: JSON DSL search in the `reviews` index with query string and fields for matching.

---

## Performance and Result Comparison from Demo

| Query Term      | Elasticsearch Time | PostgreSQL Time | Result Count                              |
|-----------------|--------------------|-----------------|-------------------------------------------|
| laptop          | ~1 second          | ~3 seconds      | *Not specified* (same number of results)  |
| something only  | ~0.5 seconds       | ~7.5 seconds    | ~8,000                                    |

- Elasticsearch consistently returns results faster (approximately 3 to 15 times faster in the demo).
- Both systems return comparable numbers of results due to similar query criteria (case-insensitive, substring matching).
- Streaming results independently allows immediate display of faster search engine results.

---

## Key Takeaways for Backend Engineers

- Mastery of relational database fundamentals is critical for backend engineers, as databases power most backend operations.
- Knowledge of Elasticsearch or full text search tools is valuable but does not require deep mastery; many use cases can be covered by leveraging existing documentation and code snippets.
- Elasticsearch is a powerful addition to the backend engineer's toolkit for building fast, relevant, typo-tolerant search experiences.
- For most practical purposes, integrating Elasticsearch or PostgreSQL full text search suffices without needing to understand the complex underlying algorithms fully.
- Elasticsearch's ecosystem (e.g., ELK stack) adds value beyond search, including log management and data visualization.

---

## Summary Table: Search Technologies Comparison

| Feature                       | Traditional Relational DB Search (LIKE) | Elasticsearch (Inverted Index)                            | PostgreSQL Full Text Search (Modern) |
|-------------------------------|----------------------------------------|-----------------------------------------------------------|--------------------------------------|
| Search Method                 | Sequential scan of all rows            | Term-to-document inverted index                           | Inverted index-based (similar)       |
| Speed                         | Slow for large datasets (~seconds)     | Fast (~milliseconds)                                      | Fast (varies by use case)            |
| Relevance Ranking             | None (random order)                    | BM25 algorithm with term frequency, field boosting, etc.  | Relevance ranking available          |
| Typo Tolerance/Fuzzy Search   | No                                     | Yes                                                       | Limited                              |
| Flexibility in Query Language | SQL (simple pattern matching)          | JSON DSL with rich features                               | SQL with FTS extensions              |
| Additional Use Cases          | General data storage                   | Search, log analytics (ELK stack)                         | General data storage + search        |
| Complexity                    | Low                                    | Medium-High (but usable with docs)                        | Medium                               |

---

## Key Concepts and Terms

- **Inverted Index:** A data structure mapping terms to documents and positions, enabling fast search.
- **BM25 Algorithm:** A ranking function used by Elasticsearch to score document relevance based on term frequency, document frequency, and document length.
- **Field Boosting:** Adjusting the importance of terms depending on which part of the document they appear in (e.g., title vs. content).
- **Typo Tolerance/Fuzzy Search:** The ability to handle misspelled queries and still return relevant results.
- **ELK Stack:** A collection of tools (Elasticsearch, Logstash, Kibana) often used for log management and analytics but also applicable to search.

---

## Final Conclusion

The evolution from simple SQL `LIKE` queries to specialized search engines like Elasticsearch illustrates the need for scalable, fast, and relevant search in modern applications. The **inverted index** is the foundational innovation enabling this leap, allowing systems to return highly relevant results in milliseconds even over massive datasets. Tools like Elasticsearch abstract away much of the complexity while offering powerful features such as typo tolerance, relevance scoring (BM25), and flexible query capabilities. Backend engineers should be familiar with these technologies as part of their toolkit, balancing deep database expertise with practical knowledge of search systems to deliver excellent user experiences.
