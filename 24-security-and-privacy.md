# Chapter 24: Security and Privacy

In this chapter, we'll learn about security and privacy considerations for AI agents.

## API Key Security

### Environment Variables

```yaml
# ❌ Never commit
api-key: AIzaSyAbc123...

# ✅ Use environment variables
api-key: ${GEMINI_API_KEY:}
```

### Secrets Management

- Use environment variables
- Use secret management services (AWS Secrets Manager, etc.)
- Never commit keys to version control

## Input Validation

### Sanitize Inputs

```java
public String handleQuery(String userQuery, String sessionId) {
    // Validate
    if (userQuery == null || userQuery.trim().isEmpty()) {
        return "Please provide a question.";
    }
    if (userQuery.length() > 1000) {
        return "Query is too long.";
    }
    
    // Sanitize
    String sanitized = sanitizeInput(userQuery);
    
    // Process
    return assistant.chat(sanitized);
}
```

## Rate Limiting

### Prevent Abuse

```java
@RateLimiter(name = "query")
public String handleQuery(String userQuery, String sessionId) {
    return assistant.chat(userQuery);
}
```

## Data Privacy

### Don't Log Sensitive Data

```java
// ❌ Bad
log.info("User query: {}", userQuery);  // May contain sensitive info

// ✅ Good
log.info("Query received (length: {})", userQuery.length());
```

## Best Practices

### ✅ Do

- **Use environment variables** for secrets
- **Validate inputs**
- **Rate limit** requests
- **Don't log sensitive data**
- **Use HTTPS**

### ❌ Don't

- Commit API keys
- Skip validation
- Log sensitive information
- Use HTTP in production

## Quick Reference

### Security Checklist

- [ ] API keys in environment variables
- [ ] Input validation
- [ ] Rate limiting
- [ ] HTTPS enabled
- [ ] No sensitive data in logs
- [ ] Error messages don't expose internals

## Next Steps

1. **Appendix A**: API Reference
2. **Appendix B**: Troubleshooting

## Key Takeaways

✅ **Environment variables** = Secure secrets  
✅ **Input validation** = Prevent attacks  
✅ **Rate limiting** = Prevent abuse  
✅ **Privacy** = Don't log sensitive data  

**Remember:** Security is critical for production!

---

## Navigation

| [← Previous](23-advanced-rag-patterns) | [Home](home) | [Next →](appendix-a) |
|:---|:---:|---:|
| Chapter 23: Advanced RAG Patterns | Building Agentic AI Applications with Java | Appendix A: LangChain4j API Reference |

