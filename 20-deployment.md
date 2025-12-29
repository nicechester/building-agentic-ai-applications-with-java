# Chapter 20: Deployment

In this chapter, we'll learn how to deploy your AI agent to production.

## Build JAR

```bash
mvn clean package
```

## Docker Deployment

### Dockerfile

```dockerfile
FROM openjdk:17-jdk-slim
COPY target/bible-ai-*.jar app.jar
EXPOSE 8080
ENTRYPOINT ["java", "-jar", "app.jar"]
```

### Build and Run

```bash
docker build -t bible-ai .
docker run -p 8080:8080 -e GEMINI_API_KEY=your-key bible-ai
```

## Environment Variables

```bash
export GEMINI_API_KEY=your-key
export GEMINI_MODEL_NAME=gemini-2.5-flash-lite
```

## Best Practices

### ✅ Do

- **Use environment variables** for secrets
- **Set resource limits** in Docker
- **Use health checks**
- **Monitor logs**

### ❌ Don't

- Commit API keys
- Skip health checks
- Ignore logs

## Quick Reference

### Docker Commands

```bash
docker build -t app .
docker run -p 8080:8080 -e API_KEY=key app
```

## Next Steps

1. **Chapter 21**: Multi-LLM support
2. **Chapter 24**: Security

## Key Takeaways

✅ **Docker** = Easy deployment  
✅ **Environment variables** = Secure configuration  
✅ **Health checks** = Monitor status  

**Remember:** Secure deployment is critical!

---

## Navigation

| [← Previous](19-testing-your-agent) | [Home](home) | [Next →](21-multi-llm-support) |
|:---|:---:|---:|
| Chapter 19: Testing Your Agent | Building Agentic AI Applications with Java | Chapter 21: Multi-LLM Support |

