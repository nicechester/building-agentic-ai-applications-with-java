# Chapter 15: REST API Design

In this chapter, we'll design a REST API to expose your AI agent to clients.

## API Design Principles

### 1. RESTful Structure

```
POST /api/bible/query          # Submit query
GET  /api/bible/config         # Get configuration
```

### 2. Request/Response Models

```java
public record QueryRequest(
    String query,
    String sessionId
) {}

public record QueryResponse(
    String response,
    String error,
    Map<String, Object> metadata
) {
    public static QueryResponse success(String response, String sessionId, Map<String, Object> metadata) {
        return new QueryResponse(response, null, metadata);
    }
    
    public static QueryResponse error(String error) {
        return new QueryResponse(null, error, null);
    }
}
```

## Controller Implementation

### Basic Controller

```java
@Log4j2
@RestController
@RequestMapping("/api/bible")
@RequiredArgsConstructor
@CrossOrigin(origins = "*")
public class BibleController {
    
    private final BibleAgent bibleAgent;
    
    @PostMapping("/query")
    public ResponseEntity<QueryResponse> query(@RequestBody QueryRequest request) {
        try {
            String userQuery = request.query();
            String sessionId = request.sessionId();
            log.info("Received query: {} (sessionId: {})", userQuery, 
                sessionId != null ? sessionId : "none");
            
            String agentResponse = bibleAgent.handleQuery(userQuery, sessionId);
            
            return ResponseEntity.ok(QueryResponse.success(agentResponse, sessionId, null));
        } catch (Exception e) {
            log.error("Query processing failed", e);
            return ResponseEntity.ok(QueryResponse.error(e.getMessage()));
        }
    }
    
    @GetMapping("/config")
    public ResponseEntity<Map<String, String>> getConfig() {
        return ResponseEntity.ok(Map.of(
            "version", "개역개정",
            "language", "ko",
            "totalBooks", "66"
        ));
    }
}
```

## Error Handling

### Global Exception Handler

```java
@RestControllerAdvice
public class GlobalExceptionHandler {
    
    @ExceptionHandler(Exception.class)
    public ResponseEntity<QueryResponse> handleException(Exception e) {
        log.error("Unhandled exception", e);
        return ResponseEntity.ok(QueryResponse.error(e.getMessage()));
    }
}
```

## API Versioning

### URL Versioning

```java
@RequestMapping("/api/v1/bible")
public class BibleControllerV1 {
    // ...
}
```

### Header Versioning

```java
@GetMapping(value = "/query", headers = "API-Version=1")
public ResponseEntity<QueryResponse> queryV1(...) {
    // ...
}
```

## Request Validation

### Validation Annotations

```java
public record QueryRequest(
    @NotBlank(message = "Query is required")
    @Size(max = 1000, message = "Query too long")
    String query,
    
    String sessionId
) {}
```

### Validation Handler

```java
@ExceptionHandler(MethodArgumentNotValidException.class)
public ResponseEntity<QueryResponse> handleValidation(MethodArgumentNotValidException e) {
    String error = e.getBindingResult().getFieldErrors().stream()
        .map(DefaultMessageSourceResolvable::getDefaultMessage)
        .collect(Collectors.joining(", "));
    return ResponseEntity.ok(QueryResponse.error(error));
}
```

## Best Practices

### ✅ Do

- **Use RESTful conventions**
- **Return consistent response format**
- **Handle errors gracefully**
- **Log requests/responses**
- **Validate inputs**
- **Use appropriate HTTP methods**
- **Include CORS headers** if needed

### ❌ Don't

- Mix REST and RPC styles
- Return inconsistent formats
- Ignore errors
- Skip validation
- Expose sensitive data

## Quick Reference

### Controller Template

```java
@RestController
@RequestMapping("/api/resource")
@RequiredArgsConstructor
public class ResourceController {
    private final ResourceAgent agent;
    
    @PostMapping("/query")
    public ResponseEntity<QueryResponse> query(@RequestBody QueryRequest request) {
        try {
            String response = agent.handleQuery(request.query(), request.sessionId());
            return ResponseEntity.ok(QueryResponse.success(response, null, null));
        } catch (Exception e) {
            return ResponseEntity.ok(QueryResponse.error(e.getMessage()));
        }
    }
}
```

## Next Steps

Now that you can design REST APIs:

1. **Chapter 16**: Build the frontend
2. **Chapter 17**: Error handling
3. **Chapter 20**: Deployment

## Key Takeaways

✅ **RESTful structure** = Standard API design  
✅ **Request/Response models** = Type-safe communication  
✅ **Error handling** = Graceful failure  
✅ **Validation** = Input safety  
✅ **Logging** = Debugging support  

**Remember:** Good API design makes integration easy!

---

## Navigation

| [← Previous](14-domain-specific-tools) | [Home](home) | [Next →](16-building-the-frontend) |
|:---|:---:|---:|
| Chapter 14: Domain-Specific Tools | Building Agentic AI Applications with Java | Chapter 16: Building the Frontend |

