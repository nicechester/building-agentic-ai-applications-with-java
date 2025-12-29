# Chapter 19: Testing Your Agent

In this chapter, we'll learn how to test your AI agent effectively.

## Unit Testing

### Test Agent

```java
@SpringBootTest
class BibleAgentTest {
    
    @Autowired
    private BibleAgent bibleAgent;
    
    @Test
    void testSimpleQuery() {
        String response = bibleAgent.handleQuery("Show me John 3:16", "test-session");
        assertNotNull(response);
        assertTrue(response.contains("John") || response.contains("3:16"));
    }
}
```

### Test Tools

```java
@SpringBootTest
class BibleToolsTest {
    
    @Autowired
    private BibleTools bibleTools;
    
    @Test
    void testGetVerse() {
        String result = bibleTools.getVerse("John", 3, 16);
        assertNotNull(result);
        assertTrue(result.contains("John 3:16"));
    }
}
```

## Integration Testing

### Test Full Flow

```java
@SpringBootTest
@AutoConfigureMockMvc
class BibleControllerTest {
    
    @Autowired
    private MockMvc mockMvc;
    
    @Test
    void testQueryEndpoint() throws Exception {
        QueryRequest request = new QueryRequest("Show me John 3:16", "test-session");
        
        mockMvc.perform(post("/api/bible/query")
                .contentType(MediaType.APPLICATION_JSON)
                .content(objectMapper.writeValueAsString(request)))
            .andExpect(status().isOk())
            .andExpect(jsonPath("$.response").exists());
    }
}
```

## Best Practices

### ✅ Do

- **Test tools independently**
- **Test agent with various queries**
- **Test error cases**
- **Use test sessions**

### ❌ Don't

- Skip testing
- Test only happy paths
- Use production data

## Quick Reference

### Test Template

```java
@SpringBootTest
class AgentTest {
    @Autowired
    private MyAgent agent;
    
    @Test
    void testQuery() {
        String response = agent.handleQuery("test query", "session");
        assertNotNull(response);
    }
}
```

## Next Steps

1. **Chapter 20**: Deployment

## Key Takeaways

✅ **Unit tests** = Test components independently  
✅ **Integration tests** = Test full flow  
✅ **Error cases** = Test failures  

**Remember:** Testing ensures your agent works correctly!

---

## Navigation

| [← Previous](18-performance-optimization) | [Home](home) | [Next →](20-deployment) |
|:---|:---:|---:|
| Chapter 18: Performance Optimization | Building Agentic AI Applications with Java | Chapter 20: Deployment |

