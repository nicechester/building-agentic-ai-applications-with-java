# Chapter 9: Implementing RAG

Now that you understand RAG concepts and have embeddings set up, let's implement RAG retrieval in your application.

## Two RAG Patterns

### Pattern 1: Automatic RAG

RAG is automatically used for every query:

```java
BibleAssistant assistant = AiServices.builder(BibleAssistant.class)
    .chatModel(chatModel)
    .contentRetriever(bibleRetriever)  // Automatic RAG
    .tools(bibleTools)
    .build();
```

**How it works:**
1. User asks question
2. LangChain4j automatically retrieves relevant content
3. Adds to LLM context
4. LLM generates response

**Pros:** Simple, always uses RAG
**Cons:** Less control, may retrieve irrelevant content

### Pattern 2: Reverse RAG (Recommended)

RAG is available as a tool - LLM decides when to use it:

```java
BibleAssistant assistant = AiServices.builder(BibleAssistant.class)
    .chatModel(chatModel)
    .tools(bibleTools)  // Includes searchVersesBySemanticSimilarity()
    // No automatic contentRetriever
    .build();
```

**How it works:**
1. User asks question
2. LLM decides if semantic search is needed
3. Calls `searchVersesBySemanticSimilarity()` tool if needed
4. LLM uses results in response

**Pros:** Better control, more accurate
**Cons:** Requires tool implementation

**Bible-AI uses this pattern!**

## Implementing Automatic RAG

### Step 1: Create ContentRetriever

```java
@Bean
public ContentRetriever bibleRetriever(
        EmbeddingStore<TextSegment> bibleEmbeddingStore,
        EmbeddingModel embeddingModel,
        @Value("${bible.rag.max-results:3}") int maxResults,
        @Value("${bible.rag.min-score:0.6}") double minScore) {
    return EmbeddingStoreContentRetriever.builder()
            .embeddingStore(bibleEmbeddingStore)
            .embeddingModel(embeddingModel)
            .maxResults(maxResults)
            .minScore(minScore)
            .build();
}
```

### Step 2: Add to Agent

```java
BibleAssistant assistant = AiServices.builder(BibleAssistant.class)
    .chatModel(chatModel)
    .contentRetriever(bibleRetriever)  // Add here
    .tools(bibleTools)
    .chatMemory(sessionMemory)
    .build();
```

### Step 3: Configure

```yaml
bible:
  rag:
    max-results: 3      # How many segments to retrieve
    min-score: 0.6       # Similarity threshold
```

## Implementing Reverse RAG

### Step 1: Create Embedding Search Tool

```java
@Tool("Search Bible verses using semantic similarity (embedding search). " +
      "Use this when you need to find verses that are semantically related to a topic.")
public String searchVersesBySemanticSimilarity(String query, int maxResults) {
    log.info("Searching verses by semantic similarity: {} (maxResults: {})", 
             query, maxResults);
    
    try {
        // Create embedding for the query
        Embedding queryEmbedding = embeddingModel.embed(query).content();
        
        // Search embedding store
        EmbeddingSearchRequest searchRequest = EmbeddingSearchRequest.builder()
                .queryEmbedding(queryEmbedding)
                .maxResults(maxResults)
                .minScore(0.5)  // Similarity threshold
                .build();
        
        List<TextSegment> matches = bibleEmbeddingStore.search(searchRequest)
                .matches().stream()
                .map(EmbeddingMatch::embedded)
                .toList();
        
        if (matches.isEmpty()) {
            return String.format("No semantically similar verses found for: %s", query);
        }
        
        // Format results
        StringBuilder sb = new StringBuilder();
        sb.append("Semantically similar verses for '").append(query).append("':\n\n");
        for (TextSegment segment : matches) {
            sb.append(segment.text()).append("\n\n");
        }
        
        return sb.toString();
    } catch (Exception e) {
        log.error("Failed to search verses by semantic similarity", e);
        return "Error: " + e.getMessage();
    }
}
```

### Step 2: Add Tool to Agent

```java
BibleAssistant assistant = AiServices.builder(BibleAssistant.class)
    .chatModel(chatModel)
    .tools(bibleTools)  // Includes searchVersesBySemanticSimilarity
    // No automatic contentRetriever
    .build();
```

### Step 3: System Message Guidance

```java
String systemMessage = """
    You are a Bible study assistant.
    
    CRITICAL: EMBEDDING SEARCH AS A TOOL (Reverse RAG Pattern)
    - Embedding search is available as a tool: searchVersesBySemanticSimilarity()
    - LLM decides when to use embedding search, not automatic RAG
    - The embedding model has limitations with Korean text
    - When using searchVersesBySemanticSimilarity():
      * Always verify results are from correct books
      * If results are from wrong books, ignore them
      * Use other search tools for more accurate results
    - Prefer keyword-based search tools over semantic search for Korean text
    """;
```

## Complete Example: Bible-AI RAG Setup

### RAGConfig.java

```java
@Configuration
public class RAGConfig {
    
    @Bean
    public EmbeddingModel embeddingModel() {
        return new AllMiniLmL6V2QuantizedEmbeddingModel();
    }
    
    @Bean
    public EmbeddingStore<TextSegment> bibleEmbeddingStore(
            EmbeddingModel embeddingModel,
            @Value("${langchain4j.splitter.text.maxSegmentSize:500}") int maxSegmentSize,
            @Value("${langchain4j.splitter.text.maxOverlapSize:50}") int maxOverlapSize,
            @Value("${bible.data.json-path}") String bibleJsonPath,
            @Value("${bible.data.asv-json-path}") String asvJsonPath) {
        
        // Load and format Bible data
        StringBuilder bibleContent = new StringBuilder();
        loadBibleJson(bibleJsonPath, bibleContent, "KRV");
        loadBibleJson(asvJsonPath, bibleContent, "ASV");
        
        // Split into segments
        Document document = Document.from(bibleContent.toString());
        List<TextSegment> segments = DocumentSplitters.recursive(
            maxSegmentSize, maxOverlapSize).split(document);
        
        // Create embeddings
        List<Embedding> embeddings = embeddingModel.embedAll(segments).content();
        
        // Store
        EmbeddingStore<TextSegment> store = new InMemoryEmbeddingStore<>();
        store.addAll(embeddings, segments);
        
        return store;
    }
    
    // Optional: Automatic RAG retriever (not used in Reverse RAG pattern)
    @Bean
    public ContentRetriever bibleRetriever(
            EmbeddingStore<TextSegment> bibleEmbeddingStore,
            EmbeddingModel embeddingModel,
            @Value("${bible.rag.max-results:3}") int maxResults,
            @Value("${bible.rag.min-score:0.6}") double minScore) {
        return EmbeddingStoreContentRetriever.builder()
                .embeddingStore(bibleEmbeddingStore)
                .embeddingModel(embeddingModel)
                .maxResults(maxResults)
                .minScore(minScore)
                .build();
    }
}
```

### BibleTools.java (Reverse RAG Tool)

```java
@Component
@RequiredArgsConstructor
public class BibleTools {
    
    @Qualifier("bibleEmbeddingStore")
    private final EmbeddingStore<TextSegment> bibleEmbeddingStore;
    private final EmbeddingModel embeddingModel;
    
    @Tool("Search Bible verses using semantic similarity (embedding search). " +
          "Use this when you need to find verses that are semantically related to a topic.")
    public String searchVersesBySemanticSimilarity(String query, int maxResults) {
        // Implementation from above
    }
}
```

### BibleAgent.java (Using Reverse RAG)

```java
@Service
@RequiredArgsConstructor
public class BibleAgent {
    private final BibleTools bibleTools;  // Includes embedding search tool
    private final ChatModel chatModel;
    // Note: bibleRetriever is NOT used (Reverse RAG pattern)
    
    public String handleQuery(String userQuery, String sessionId) {
        ChatMemory sessionMemory = sessionMemoryManager.getOrCreateMemory(sessionId);
        
        BibleAssistant assistant = AiServices.builder(BibleAssistant.class)
            .chatModel(chatModel)
            .tools(bibleTools)  // Tools include embedding search
            .chatMemory(sessionMemory)
            .systemMessageProvider(chatId -> systemMessage)
            // No .contentRetriever() - Reverse RAG pattern
            .build();
        
        return assistant.chat(userQuery);
    }
}
```

## Tuning RAG Parameters

### max-results

**Too few (1-2):**
- May miss relevant content
- Less context for LLM

**Too many (10+):**
- May include irrelevant content
- Overwhelms LLM context

**Recommended:** 3-5

### min-score

**Too high (0.8+):**
- Very relevant results only
- May miss some relevant content

**Too low (0.3-):**
- Includes irrelevant results
- Noise in context

**Recommended:** 0.6 for balanced results

### Adjusting for Your Use Case

```yaml
# For precise queries
bible:
  rag:
    max-results: 3
    min-score: 0.7

# For exploratory queries
bible:
  rag:
    max-results: 5
    min-score: 0.5
```

## Testing RAG

### Test Automatic RAG

```java
@Test
void testAutomaticRAG() {
    BibleAssistant assistant = AiServices.builder(BibleAssistant.class)
        .chatModel(chatModel)
        .contentRetriever(bibleRetriever)
        .build();
    
    String response = assistant.chat("What did Jesus say about love?");
    assertNotNull(response);
    // Should include relevant verses from RAG
}
```

### Test Reverse RAG Tool

```java
@Test
void testEmbeddingSearchTool() {
    String result = bibleTools.searchVersesBySemanticSimilarity("God's love", 3);
    assertNotNull(result);
    assertTrue(result.contains("love") || result.contains("God"));
}
```

## Troubleshooting

### Issue 1: No Results Returned

**Problem:** RAG returns empty results

**Solutions:**
- Lower `min-score` threshold
- Check if embedding model supports your language
- Verify data was loaded correctly
- Check embedding store size

### Issue 2: Irrelevant Results

**Problem:** RAG returns unrelated content

**Solutions:**
- Increase `min-score` threshold
- Reduce `max-results`
- Improve data formatting (include metadata)
- Consider using tools instead for exact queries

### Issue 3: Slow Retrieval

**Problem:** RAG is slow

**Solutions:**
- Use quantized embedding models
- Consider persistent embedding store (Redis)
- Reduce `max-results`
- Cache embeddings

## Best Practices

### ✅ Do

- **Use Reverse RAG** for better control
- **Format data** with metadata (book, chapter, verse)
- **Tune parameters** based on your use case
- **Combine with tools** for best results
- **Verify results** especially for non-English text

### ❌ Don't

- Rely solely on RAG for exact queries
- Use RAG for real-time data
- Ignore embedding model language limitations
- Set thresholds too high or too low
- Skip result verification

## Quick Reference

### Automatic RAG Setup

```java
@Bean
public ContentRetriever retriever(...) {
    return EmbeddingStoreContentRetriever.builder()
        .embeddingStore(store)
        .embeddingModel(model)
        .maxResults(3)
        .minScore(0.6)
        .build();
}

// In agent
.contentRetriever(retriever)
```

### Reverse RAG Setup

```java
@Tool("Search using semantic similarity")
public String searchBySemanticSimilarity(String query, int maxResults) {
    Embedding queryEmbedding = embeddingModel.embed(query).content();
    EmbeddingSearchRequest request = EmbeddingSearchRequest.builder()
        .queryEmbedding(queryEmbedding)
        .maxResults(maxResults)
        .minScore(0.5)
        .build();
    // Search and return results
}

// In agent
.tools(tools)  // Includes search tool
// No contentRetriever
```

## Next Steps

Now that you can implement RAG:

1. **Chapter 10**: Create your complete agent
2. **Chapter 23**: Advanced RAG patterns
3. **Chapter 22**: Custom embedding models

## Key Takeaways

✅ **Automatic RAG** = Simple but less control  
✅ **Reverse RAG** = Better control, LLM decides when to use  
✅ **max-results** = 3-5 recommended  
✅ **min-score** = 0.6 recommended  
✅ **Format data** = Include metadata for better results  
✅ **Test and tune** = Adjust parameters for your use case  
✅ **Combine with tools** = Best of both worlds  

**Remember:** RAG is powerful but not always the best choice. Use tools for exact queries, RAG for semantic search!

---

## Navigation

| [← Previous](08-setting-up-embeddings) | [Home](home) | [Next →](10-creating-your-agent) |
|:---|:---:|---:|
| Chapter 8: Setting Up Embeddings | Building Agentic AI Applications with Java | Chapter 10: Creating Your Agent |

