# Appendix A: LangChain4j API Reference

Quick reference for common LangChain4j APIs.

## AiServices

```java
AiServices.builder(MyAssistant.class)
    .chatModel(chatModel)
    .tools(tools)
    .chatMemory(memory)
    .contentRetriever(retriever)
    .systemMessageProvider(chatId -> systemMessage)
    .build();
```

## ChatModel

```java
// Gemini
GoogleAiGeminiChatModel.builder()
    .modelName("gemini-2.5-flash-lite")
    .apiKey(apiKey)
    .build();

// OpenAI
OpenAiChatModel.builder()
    .apiKey(apiKey)
    .modelName("gpt-4o-mini")
    .build();

// Ollama
OllamaChatModel.builder()
    .baseUrl("http://localhost:11434")
    .modelName("mistral:7b")
    .build();
```

## Tools

```java
@Tool("Description")
public String myTool(String param) {
    // Implementation
}
```

## Memory

```java
ChatMemory memory = MessageWindowChatMemory.withMaxMessages(10);
```

## Embeddings

```java
EmbeddingModel model = new AllMiniLmL6V2QuantizedEmbeddingModel();
List<Embedding> embeddings = model.embedAll(segments).content();
```

## Embedding Store

```java
EmbeddingStore<TextSegment> store = new InMemoryEmbeddingStore<>();
store.addAll(embeddings, segments);
```

## Content Retriever

```java
ContentRetriever retriever = EmbeddingStoreContentRetriever.builder()
    .embeddingStore(store)
    .embeddingModel(model)
    .maxResults(3)
    .minScore(0.6)
    .build();
```

## Quick Reference

### Essential Imports

```java
import dev.langchain4j.service.AiServices;
import dev.langchain4j.service.UserMessage;
import dev.langchain4j.agent.tool.Tool;
import dev.langchain4j.model.chat.ChatModel;
import dev.langchain4j.memory.ChatMemory;
import dev.langchain4j.memory.chat.MessageWindowChatMemory;
```

## Next Steps

1. **Appendix B**: Troubleshooting
2. **Appendix C**: Example Projects

## Key Takeaways

✅ **AiServices.builder()** = Main API for agents  
✅ **ChatModel** = LLM interface  
✅ **@Tool** = Tool annotation  
✅ **ChatMemory** = Conversation history  

**Remember:** This is a quick reference. See LangChain4j docs for details!

---

## Navigation

| [← Previous](24-security-and-privacy) | [Home](home) | [Next →](appendix-b) |
|:---|:---:|---:|
| Chapter 24: Security and Privacy | Building Agentic AI Applications with Java | Appendix B: Troubleshooting Guide |

