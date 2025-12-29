# Chapter 14: Domain-Specific Tools

Domain-specific tools are tailored to your application's needs. In this chapter, we'll learn how to design effective tools for your domain.

## Tool Design Principles

### 1. Match User Intent

**User asks:** "Show me John 3:16"
**Tool:** `getVerse("John", 3, 16)`

**User asks:** "What did Jesus say about love?"
**Tool:** `searchVerses("love")` + filter by Gospels

### 2. Provide Context

Include metadata in tool results:

```java
@Tool("Get a verse")
public String getVerse(String bookName, int chapter, int verse) {
    VerseResult result = bibleService.getVerse(bookName, chapter, verse);
    // Format with reference
    return String.format("%s %d:%d\n%s", 
        result.getReference(), 
        result.getText());
}
```

### 3. Handle Edge Cases

```java
@Tool("Get a verse")
public String getVerse(String bookName, int chapter, int verse) {
    // Validate
    if (bookName == null || bookName.isEmpty()) {
        return "Error: Book name is required";
    }
    
    VerseResult result = bibleService.getVerse(bookName, chapter, verse);
    if (result == null) {
        return String.format("Verse not found: %s %d:%d", bookName, chapter, verse);
    }
    
    return formatVerseResult(result);
}
```

## Bible-AI Tool Examples

### Lookup Tools

```java
@Tool("Get a specific Bible verse by book name, chapter, and verse number.")
public String getVerse(String bookName, int chapter, int verse) {
    // Exact lookup
}

@Tool("Get all verses in a chapter.")
public String getChapter(String bookName, int chapter) {
    // Chapter lookup
}

@Tool("Get verses in a range.")
public String getVerseRange(String bookName, int chapter, 
                           int startVerse, int endVerse) {
    // Range lookup
}
```

### Search Tools

```java
@Tool("Search for Bible verses containing a keyword.")
public String searchVerses(String keyword) {
    // Keyword search
}

@Tool("Search for Bible verses by phrase.")
public String searchByPhrase(String phrase) {
    // Phrase search
}

@Tool("Search using semantic similarity.")
public String searchVersesBySemanticSimilarity(String query, int maxResults) {
    // Embedding search
}
```

### Analysis Tools

```java
@Tool("Get statistics about a keyword.")
public String getKeywordStatistics(String keyword, 
                                   Integer testament, 
                                   String bookType) {
    // Statistics with filters
}
```

## Tool Composition

### Pattern: Search Then Lookup

```java
// LLM workflow:
// 1. searchVerses("love") → finds verses
// 2. getVerse("John", 3, 16) → gets specific verse
// 3. getVerseWithContext("John", 3, 16, 3) → gets context
```

### Pattern: Filter Then Analyze

```java
// LLM workflow:
// 1. getKeywordStatistics("love", 2, "Gospels") → statistics
// 2. searchVerses("love") → finds verses
// 3. getVerse(...) → gets specific verses
```

## Tool Descriptions

### ✅ Good Description

```java
@Tool("Get a specific Bible verse by book name, chapter, and verse number. " +
      "Returns the verse text with reference (e.g., 'John 3:16'). " +
      "Use this when user asks for a specific verse.")
```

**Why:**
- Explains what it does
- Shows example
- Tells when to use it

### ❌ Bad Description

```java
@Tool("Get verse")
```

**Why:**
- Too vague
- No context
- LLM doesn't know when to use it

## Best Practices

### ✅ Do

- **Match user intent** with tool design
- **Include metadata** in results
- **Handle edge cases** gracefully
- **Write clear descriptions** for tools
- **Limit results** to prevent overwhelming responses
- **Format output** clearly

### ❌ Don't

- Create tools that don't match user needs
- Return raw data without formatting
- Ignore edge cases
- Write vague descriptions
- Return unlimited results

## Quick Reference

### Tool Template

```java
@Tool("Clear description of what this tool does and when to use it")
public String myTool(String param1, int param2) {
    // Validate
    if (param1 == null) {
        return "Error: param1 is required";
    }
    
    // Process
    try {
        Result result = service.process(param1, param2);
        return formatResult(result);
    } catch (Exception e) {
        log.error("Tool failed", e);
        return "Error: " + e.getMessage();
    }
}
```

## Next Steps

Now that you can design domain-specific tools:

1. **Chapter 10**: Create your agent with tools
2. **Chapter 19**: Test your tools
3. **Chapter 23**: Advanced patterns

## Key Takeaways

✅ **Match user intent** = Tools should solve user problems  
✅ **Include metadata** = Better context in results  
✅ **Handle edge cases** = Robust tools  
✅ **Clear descriptions** = LLM knows when to use tools  
✅ **Limit results** = Prevent overwhelming responses  
✅ **Format output** = Clear, readable results  

**Remember:** Good tools are the foundation of a good agent!

---

## Navigation

| [← Previous](13-data-preparation) | [Home](home) | [Next →](15-rest-api-design) |
|:---|:---:|---:|
| Chapter 13: Data Preparation | Building Agentic AI Applications with Java | Chapter 15: REST API Design |

