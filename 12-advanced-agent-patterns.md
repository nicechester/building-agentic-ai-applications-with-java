# Chapter 12: Advanced Agent Patterns

In this chapter, we'll explore advanced patterns for building more sophisticated agents.

## Pattern 1: Multi-Agent Orchestration

Multiple agents working together:

```java
@Service
public class OrchestrationAgent {
    private final BibleAgent bibleAgent;
    private final AnalyticsAgent analyticsAgent;
    private final FormattingAgent formattingAgent;
    
    public String handleQuery(String query, String sessionId) {
        // Route to appropriate agent
        if (isBibleQuery(query)) {
            return bibleAgent.handleQuery(query, sessionId);
        } else if (isAnalyticsQuery(query)) {
            return analyticsAgent.handleQuery(query, sessionId);
        } else {
            // Use formatting agent to improve response
            String rawResponse = bibleAgent.handleQuery(query, sessionId);
            return formattingAgent.formatResponse(rawResponse);
        }
    }
}
```

## Pattern 2: Agent with Validation

Validate inputs before processing:

```java
public String handleQuery(String userQuery, String sessionId) {
    // Validate input
    if (userQuery == null || userQuery.trim().isEmpty()) {
        return "Please provide a question.";
    }
    if (userQuery.length() > 1000) {
        return "Query is too long. Please limit to 1000 characters.";
    }
    
    // Process
    return assistant.chat(userQuery);
}
```

## Pattern 3: Agent with Retry Logic

Retry on transient failures:

```java
public String handleQuery(String userQuery, String sessionId) {
    int maxRetries = 3;
    int retryCount = 0;
    
    while (retryCount < maxRetries) {
        try {
            return assistant.chat(userQuery);
        } catch (HttpException e) {
            retryCount++;
            if (retryCount >= maxRetries) {
                throw e;
            }
            log.warn("Retry {} for query", retryCount);
            Thread.sleep(1000 * retryCount); // Exponential backoff
        }
    }
    throw new RuntimeException("Failed after retries");
}
```

## Pattern 4: Agent with Caching

Cache responses for common queries:

```java
@Cacheable("agent-responses")
public String handleQuery(String userQuery, String sessionId) {
    return assistant.chat(userQuery);
}
```

## Pattern 5: Agent with Streaming

Stream responses as they're generated:

```java
public Stream<String> handleQueryStream(String userQuery, String sessionId) {
    StreamingChatLanguageModel streamingModel = // ...
    BibleAssistant assistant = AiServices.builder(BibleAssistant.class)
        .streamingChatLanguageModel(streamingModel)
        .build();
    
    return assistant.chatStream(userQuery);
}
```

## Pattern 6: Conditional Tool Usage

Enable/disable tools based on context:

```java
public String handleQuery(String userQuery, String sessionId, boolean allowSearch) {
    List<Object> tools = new ArrayList<>();
    tools.add(bibleTools);
    
    if (allowSearch) {
        tools.add(searchTools);
    }
    
    BibleAssistant assistant = AiServices.builder(BibleAssistant.class)
        .chatModel(chatModel)
        .tools(tools.toArray())
        .build();
    
    return assistant.chat(userQuery);
}
```

## Pattern 7: Agent with Preprocessing

Preprocess queries before sending to agent:

```java
public String handleQuery(String userQuery, String sessionId) {
    // Normalize query
    String normalized = normalizeQuery(userQuery);
    
    // Expand abbreviations
    String expanded = expandAbbreviations(normalized);
    
    // Process with agent
    return assistant.chat(expanded);
}

private String normalizeQuery(String query) {
    return query.trim().toLowerCase();
}

private String expandAbbreviations(String query) {
    return query.replace("OT", "Old Testament")
                .replace("NT", "New Testament");
}
```

## Pattern 8: Agent with Postprocessing

Postprocess responses:

```java
public String handleQuery(String userQuery, String sessionId) {
    String rawResponse = assistant.chat(userQuery);
    
    // Format response
    String formatted = formatResponse(rawResponse);
    
    // Add citations
    String withCitations = addCitations(formatted);
    
    return withCitations;
}
```

## Pattern 9: Agent with Context Injection

Inject additional context:

```java
public String handleQuery(String userQuery, String sessionId, UserContext context) {
    String enhancedQuery = String.format(
        "User: %s (Language: %s)\n\nQuery: %s",
        context.getUserName(),
        context.getLanguage(),
        userQuery
    );
    
    return assistant.chat(enhancedQuery);
}
```

## Pattern 10: Agent with Fallback

Fallback to simpler agent on failure:

```java
public String handleQuery(String userQuery, String sessionId) {
    try {
        return advancedAgent.chat(userQuery);
    } catch (Exception e) {
        log.warn("Advanced agent failed, using fallback", e);
        return fallbackAgent.chat(userQuery);
    }
}
```

## Best Practices

### ✅ Do

- **Validate inputs** before processing
- **Handle errors gracefully** with fallbacks
- **Cache common queries** for performance
- **Retry transient failures** with backoff
- **Log agent operations** for debugging
- **Preprocess/postprocess** when needed

### ❌ Don't

- Ignore errors
- Skip validation
- Cache sensitive data
- Retry indefinitely
- Skip logging

## Quick Reference

### Retry Pattern

```java
int maxRetries = 3;
for (int i = 0; i < maxRetries; i++) {
    try {
        return assistant.chat(query);
    } catch (Exception e) {
        if (i == maxRetries - 1) throw e;
        Thread.sleep(1000 * (i + 1));
    }
}
```

### Caching Pattern

```java
@Cacheable("responses")
public String handleQuery(String query, String sessionId) {
    return assistant.chat(query);
}
```

## Next Steps

Now that you understand advanced patterns:

1. **Chapter 15**: REST API design
2. **Chapter 17**: Error handling
3. **Chapter 18**: Performance optimization

## Key Takeaways

✅ **Multi-agent** = Multiple agents working together  
✅ **Validation** = Check inputs before processing  
✅ **Retry logic** = Handle transient failures  
✅ **Caching** = Improve performance  
✅ **Preprocessing/postprocessing** = Enhance queries/responses  
✅ **Fallback** = Graceful degradation  

**Remember:** Advanced patterns make your agent more robust and capable!

---

## Navigation

| [← Previous](11-session-management) | [Home](home) | [Next →](13-data-preparation) |
|:---|:---:|---:|
| Chapter 11: Session Management | Building Agentic AI Applications with Java | Chapter 13: Data Preparation |

