# Chapter 23: Advanced RAG Patterns

In this chapter, we'll explore advanced RAG patterns for better results.

## Hybrid Search

Combine keyword and semantic search:

```java
// Keyword search
List<VerseResult> keywordResults = bibleService.searchVerses(keyword);

// Semantic search
List<TextSegment> semanticResults = embeddingStore.search(...);

// Combine and deduplicate
```

## Reranking

Rerank results for better relevance:

```java
// Get initial results
List<TextSegment> results = embeddingStore.search(...);

// Rerank by additional criteria
results.sort((a, b) -> {
    // Custom ranking logic
    return compareRelevance(a, b);
});
```

## Multi-Query RAG

Generate multiple queries:

```java
// Generate query variations
List<String> queries = generateQueryVariations(userQuery);

// Search for each
List<TextSegment> allResults = new ArrayList<>();
for (String query : queries) {
    allResults.addAll(embeddingStore.search(query));
}

// Deduplicate and return
```

## Best Practices

### ✅ Do

- **Combine search methods**
- **Rerank results**
- **Deduplicate**

### ❌ Don't

- Rely on single method
- Ignore duplicates

## Quick Reference

### Hybrid Search

```java
List<Result> keyword = keywordSearch(query);
List<Result> semantic = semanticSearch(query);
return combineAndDeduplicate(keyword, semantic);
```

## Next Steps

1. **Chapter 24**: Security

## Key Takeaways

✅ **Hybrid search** = Better coverage  
✅ **Reranking** = Better relevance  
✅ **Multi-query** = Better recall  

**Remember:** Advanced patterns improve results!

---

## Navigation

| [← Previous](22-custom-embeddings) | [Home](home) | [Next →](24-security-and-privacy) |
|:---|:---:|---:|
| Chapter 22: Custom Embeddings | Building Agentic AI Applications with Java | Chapter 24: Security and Privacy |

