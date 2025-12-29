# Chapter 17: Error Handling

Robust error handling is essential for production applications. In this chapter, we'll learn how to handle errors gracefully.

## Error Types

### 1. LLM API Errors

```java
try {
    return assistant.chat(query);
} catch (dev.langchain4j.exception.HttpException e) {
    log.error("LLM API error", e);
    return "I'm having trouble connecting to the AI service. Please try again.";
}
```

### 2. Tool Errors

```java
@Tool("Get a verse")
public String getVerse(String bookName, int chapter, int verse) {
    try {
        VerseResult result = bibleService.getVerse(bookName, chapter, verse);
        if (result == null) {
            return String.format("Verse not found: %s %d:%d", bookName, chapter, verse);
        }
        return formatVerseResult(result);
    } catch (Exception e) {
        log.error("Failed to get verse", e);
        return "Error: " + e.getMessage();
    }
}
```

### 3. Validation Errors

```java
public String handleQuery(String userQuery, String sessionId) {
    if (userQuery == null || userQuery.trim().isEmpty()) {
        return "Please provide a question.";
    }
    if (userQuery.length() > 1000) {
        return "Query is too long. Please limit to 1000 characters.";
    }
    // Process...
}
```

## Error Handling Patterns

### Pattern 1: Try-Catch in Agent

```java
public String handleQuery(String userQuery, String sessionId) {
    try {
        return assistant.chat(userQuery);
    } catch (Exception e) {
        log.error("Failed to handle query", e);
        return "I encountered an error. Please try again.";
    }
}
```

### Pattern 2: Graceful Degradation

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

### Pattern 3: Retry with Backoff

```java
public String handleQuery(String userQuery, String sessionId) {
    int maxRetries = 3;
    for (int i = 0; i < maxRetries; i++) {
        try {
            return assistant.chat(userQuery);
        } catch (HttpException e) {
            if (i == maxRetries - 1) throw e;
            Thread.sleep(1000 * (i + 1)); // Exponential backoff
        }
    }
    throw new RuntimeException("Failed after retries");
}
```

## Global Exception Handler

### REST Controller

```java
@RestControllerAdvice
public class GlobalExceptionHandler {
    
    @ExceptionHandler(Exception.class)
    public ResponseEntity<QueryResponse> handleException(Exception e) {
        log.error("Unhandled exception", e);
        return ResponseEntity.ok(QueryResponse.error(
            "An error occurred. Please try again."));
    }
    
    @ExceptionHandler(IllegalArgumentException.class)
    public ResponseEntity<QueryResponse> handleValidation(IllegalArgumentException e) {
        return ResponseEntity.ok(QueryResponse.error(e.getMessage()));
    }
}
```

## Error Messages

### User-Friendly Messages

```java
// ❌ Bad
return "Error: java.lang.NullPointerException at line 42";

// ✅ Good
return "I couldn't find that verse. Please check the book name and verse number.";
```

### Contextual Messages

```java
if (result == null) {
    return String.format("Verse not found: %s %d:%d. " +
        "Please check the book name and verse number.", 
        bookName, chapter, verse);
}
```

## Logging

### Structured Logging

```java
log.error("Failed to get verse: book={}, chapter={}, verse={}, error={}", 
    bookName, chapter, verse, e.getMessage(), e);
```

### Log Levels

- **ERROR**: Unexpected errors that need attention
- **WARN**: Recoverable issues
- **INFO**: Normal operations
- **DEBUG**: Detailed debugging information

## Best Practices

### ✅ Do

- **Handle all exceptions** gracefully
- **Return user-friendly messages**
- **Log errors** with context
- **Use try-catch** in critical paths
- **Validate inputs** before processing
- **Provide fallbacks** when possible

### ❌ Don't

- Expose stack traces to users
- Ignore exceptions
- Return technical error messages
- Skip logging
- Let exceptions crash the app

## Quick Reference

### Error Handling Template

```java
public String handleQuery(String query, String sessionId) {
    try {
        // Validate
        if (query == null || query.trim().isEmpty()) {
            return "Please provide a question.";
        }
        
        // Process
        return assistant.chat(query);
    } catch (HttpException e) {
        log.error("LLM API error", e);
        return "I'm having trouble connecting. Please try again.";
    } catch (Exception e) {
        log.error("Unexpected error", e);
        return "I encountered an error. Please try rephrasing your question.";
    }
}
```

## Next Steps

Now that you can handle errors:

1. **Chapter 18**: Performance optimization
2. **Chapter 19**: Testing
3. **Chapter 20**: Deployment

## Key Takeaways

✅ **Try-catch** = Handle exceptions gracefully  
✅ **User-friendly messages** = Better UX  
✅ **Logging** = Debugging support  
✅ **Validation** = Prevent errors  
✅ **Fallbacks** = Graceful degradation  

**Remember:** Good error handling makes your app robust!

---

## Navigation

| [← Previous](16-building-the-frontend) | [Home](home) | [Next →](18-performance-optimization) |
|:---|:---:|---:|
| Chapter 16: Building the Frontend | Building Agentic AI Applications with Java | Chapter 18: Performance Optimization |

