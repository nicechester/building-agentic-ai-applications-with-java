# Building Agentic AI Applications with Java

A comprehensive guide to building conversational AI agents using Java, Spring Boot, and LangChain4j. This guide uses the Bible-AI project as a practical example.

## Target Audience

This guide is designed for:
- **Entry-level Java developers** with basic Spring Boot knowledge
- **Developers new to Agentic AI** who want to learn by building
- **Anyone interested** in creating AI-powered conversational applications

## Prerequisites

- Java 17+ (Java 25 used in examples)
- Basic knowledge of Spring Boot
- Familiarity with Maven
- Understanding of REST APIs
- No prior AI/ML experience required

## Table of Contents

### Part 1: Foundations

1. [Introduction to Agentic AI](01-introduction)
   - What is Agentic AI?
   - How does it differ from traditional chatbots?
   - Key concepts: LLMs, Tools, RAG, Agents
   - Overview of the Bible-AI project

2. [Project Setup](02-project-setup)
   - Creating a Spring Boot project
   - Adding LangChain4j dependencies
   - Project structure overview
   - Basic configuration

3. [Understanding LangChain4j](03-langchain4j-basics)
   - What is LangChain4j?
   - Core components: ChatModel, Tools, Memory, RAG
   - How it works: Request → Agent → Tools → Response
   - First simple example

### Part 2: Core Components

4. [LLM Configuration](04-llm-configuration)
   - Setting up Google Gemini
   - Alternative LLM providers (OpenAI, Ollama)
   - API keys and authentication
   - Model selection and parameters
   - Error handling

5. [Building Tools](05-building-tools)
   - What are Tools in Agentic AI?
   - Creating your first tool with `@Tool` annotation
   - Tool parameters and return types
   - Best practices for tool design
   - Example: Bible verse lookup tool

6. [Advanced Tools](06-advanced-tools)
   - Complex tool examples
   - Filtering and search tools
   - Statistics and analysis tools
   - Error handling in tools
   - Tool composition patterns

### Part 3: RAG (Retrieval-Augmented Generation)

7. [Introduction to RAG](07-rag-introduction)
   - What is RAG and why use it?
   - Embeddings and vector stores
   - When to use RAG vs. Tools
   - RAG architecture overview

8. [Setting Up Embeddings](08-setting-up-embeddings)
   - Choosing an embedding model
   - All-MiniLM-L6-v2 setup (ONNX-based)
   - Loading and preparing data
   - Creating embeddings
   - Multilingual considerations

9. [Implementing RAG](09-implementing-rag)
   - Building an embedding store
   - Document splitting strategies
   - Content retrieval
   - Reverse RAG pattern (LLM-first)
   - Embedding search as a tool

### Part 4: Building the Agent

10. [Creating Your Agent](10-creating-agent)
    - What is an AI Agent?
    - Using AiServices builder
    - Connecting ChatModel, Tools, and Memory
    - System prompts and instructions
    - First working agent

11. [Session Management](11-session-management)
    - Why sessions matter
    - ChatMemory basics
    - Implementing session isolation
    - Memory cleanup strategies
    - Handling session timeouts
    - API-specific considerations (Gemini function calling)

12. [Advanced Agent Patterns](12-advanced-agent-patterns)
    - Multi-step reasoning
    - Tool selection strategies
    - Error recovery
    - Context management
    - Performance optimization

### Part 5: Data and Domain

13. [Data Preparation](13-data-preparation)
    - Structuring domain data
    - JSON format design
    - Data loading strategies
    - Handling large datasets
    - Example: Bible data parsing

14. [Domain-Specific Tools](14-domain-specific-tools)
    - Designing tools for your domain
    - Search and retrieval tools
    - Statistics and analysis tools
    - Filtering and querying
    - Example: Bible search tools

### Part 6: Frontend Integration

15. [REST API Design](15-rest-api-design)
    - Designing agent endpoints
    - Request/Response models
    - Session handling in APIs
    - Error responses
    - API versioning

16. [Building the Frontend](16-building-frontend)
    - React integration
    - Session management in UI
    - Real-time updates
    - Error handling
    - Markdown and Mermaid rendering

### Part 7: Production Considerations

17. [Error Handling](17-error-handling)
    - Common errors and solutions
    - LLM API errors
    - Tool execution errors
    - Session corruption
    - Graceful degradation

18. [Performance Optimization](18-performance-optimization)
    - Embedding model optimization
    - Caching strategies
    - Memory management
    - Response time optimization
    - Scaling considerations

19. [Testing Your Agent](19-testing-agent)
    - Unit testing tools
    - Integration testing
    - Testing agent responses
    - Mocking LLM calls
    - Test data preparation

20. [Deployment](20-deployment)
    - Docker containerization
    - Environment configuration
    - Health checks
    - Monitoring and logging
    - Production best practices

### Part 8: Advanced Topics

21. [Multi-LLM Support](21-multi-llm-support)
    - Supporting multiple LLM providers
    - Switching between models
    - Fallback strategies
    - Cost optimization

22. [Custom Embeddings](22-custom-embeddings)
    - Choosing embedding models
    - Multilingual embeddings
    - Fine-tuning considerations
    - Embedding quality evaluation

23. [Advanced RAG Patterns](23-advanced-rag-patterns)
    - Hybrid search
    - Re-ranking
    - Query expansion
    - Multi-vector retrieval

24. [Security and Privacy](24-security-privacy)
    - API key management
    - Data privacy
    - User session security
    - Input validation
    - Rate limiting

### Appendices

A. [LangChain4j API Reference](appendix-a-langchain4j-api)
   - Common classes and methods
   - Configuration options
   - Best practices

B. [Troubleshooting Guide](appendix-b-troubleshooting)
   - Common issues and solutions
   - Debugging tips
   - Performance issues

C. [Example Projects](appendix-c-examples)
   - Complete code examples
   - Different use cases
   - Reference implementations

## How to Use This Guide

1. **Start with Part 1** if you're completely new to Agentic AI
2. **Follow sequentially** - each part builds on previous concepts
3. **Try the examples** - hands-on practice is essential
4. **Reference the appendices** when you need quick answers
5. **Use Bible-AI as reference** - all examples are based on a working project

## Quick Start

If you want to jump right in:

1. Read [Introduction](01-introduction) (10 min)
2. Follow [Project Setup](02-project-setup) (15 min)
3. Build [Your First Tool](05-building-tools) (30 min)
4. Create [Your First Agent](10-creating-agent) (30 min)

Total time: ~1.5 hours to get a working agent!

## Contributing

Found an error or want to improve this guide? Contributions are welcome!

## License

This guide is provided as-is for educational purposes.

