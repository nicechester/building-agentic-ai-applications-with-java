# Chapter 18: Performance Optimization

In this chapter, we'll learn how to optimize your AI agent for better performance.

## Caching Strategies

### Response Caching

```java
@Cacheable("agent-responses")
public String handleQuery(String userQuery, String sessionId) {
    return assistant.chat(userQuery);
}
```

### Tool Result Caching

```java
@Cacheable("verse-results")
public String getVerse(String bookName, int chapter, int verse) {
    return bibleService.getVerse(bookName, chapter, verse).toString();
}
```

## Memory Optimization

### Limit Session Memory

```java
private static final int MAX_MESSAGES_PER_SESSION = 10;
ChatMemory memory = MessageWindowChatMemory.withMaxMessages(MAX_MESSAGES_PER_SESSION);
```

### Clean Up Sessions

```java
@Scheduled(fixedRate = 10 * 60 * 1000)
public void cleanupExpiredSessions() {
    // Remove inactive sessions
}
```

## Embedding Store Optimization

### Use Persistent Store

```java
// Instead of InMemoryEmbeddingStore
EmbeddingStore<TextSegment> store = new RedisEmbeddingStore<>(redisClient);
```

### Batch Operations

```java
// Batch embedding creation
List<Embedding> embeddings = embeddingModel.embedAll(segments).content();
store.addAll(embeddings, segments);
```

## LLM Optimization

### Model Selection

- Use faster models for development (Gemini Flash Lite)
- Use more capable models for production (Gemini Pro)

### Token Limits

```java
.maxTokens(1000)  // Limit response length
```

## Best Practices

### ✅ Do

- **Cache common queries**
- **Limit memory size**
- **Use batch operations**
- **Choose appropriate models**
- **Clean up resources**

### ❌ Don't

- Cache everything (memory issues)
- Use unlimited memory
- Process one-by-one when batching is possible

## Quick Reference

### Caching

```java
@Cacheable("responses")
public String handleQuery(String query) {
    return assistant.chat(query);
}
```

### Memory Limits

```java
MessageWindowChatMemory.withMaxMessages(10)
```

## Next Steps

1. **Chapter 19**: Testing
2. **Chapter 20**: Deployment

## Key Takeaways

✅ **Caching** = Faster responses  
✅ **Memory limits** = Prevent overflow  
✅ **Batch operations** = Better performance  
✅ **Model selection** = Balance speed/quality  

**Remember:** Optimize based on your use case!

---

## Navigation

| [← Previous](17-error-handling) | [Home](home) | [Next →](19-testing-your-agent) |
|:---|:---:|---:|
| Chapter 17: Error Handling | Building Agentic AI Applications with Java | Chapter 19: Testing Your Agent |

