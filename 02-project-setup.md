# Chapter 2: Project Setup

In this chapter, we'll set up a Spring Boot project from scratch and configure it for building an Agentic AI application. We'll use the Bible-AI project structure as our reference.

## Prerequisites

Before we begin, make sure you have:

- **Java 17+** (Java 25 is used in Bible-AI, but 17+ works fine)
- **Maven 3.6+** (for dependency management)
- **IDE** (IntelliJ IDEA, Eclipse, or VS Code)
- **Basic Spring Boot knowledge** (if you're new, don't worry - we'll explain as we go)

## Step 1: Create a Spring Boot Project

### Option A: Using Spring Initializr (Recommended)

1. Go to [https://start.spring.io](https://start.spring.io)
2. Fill in the project metadata:
   - **Project**: Maven
   - **Language**: Java
   - **Spring Boot**: 3.5.4 (or latest stable)
   - **Group**: `io.github.nicechester` (or your own)
   - **Artifact**: `bible-ai` (or your project name)
   - **Name**: `Bible AI`
   - **Description**: `Conversational Bible Study Agent`
   - **Package name**: `io.github.nicechester.bibleai`
   - **Packaging**: Jar
   - **Java**: 17 (or higher)

3. Add dependencies:
   - **Spring Web** (for REST API)
   - **Spring Boot Actuator** (for health checks)
   - **Lombok** (for reducing boilerplate code)

4. Click **Generate** and download the ZIP file
5. Extract and open in your IDE

### Option B: Using Maven Archetype

```bash
mvn archetype:generate \
  -DgroupId=io.github.nicechester \
  -DartifactId=bible-ai \
  -DarchetypeArtifactId=maven-archetype-quickstart \
  -DinteractiveMode=false
```

Then manually add Spring Boot parent and dependencies.

## Step 2: Add LangChain4j Dependencies

Open your `pom.xml` and add the following dependencies:

### Core LangChain4j Dependencies

```xml
<properties>
    <java.version>17</java.version>
    <langchain4j.version>1.2.0</langchain4j.version>
</properties>

<dependencies>
    <!-- Spring Boot Starter Web -->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
    
    <!-- Spring Boot Actuator -->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-actuator</artifactId>
    </dependency>
    
    <!-- Lombok -->
    <dependency>
        <groupId>org.projectlombok</groupId>
        <artifactId>lombok</artifactId>
        <version>1.18.42</version>
        <scope>provided</scope>
    </dependency>
    
    <!-- LangChain4j Core -->
    <dependency>
        <groupId>dev.langchain4j</groupId>
        <artifactId>langchain4j</artifactId>
        <version>${langchain4j.version}</version>
    </dependency>
    
    <!-- LangChain4j Spring Boot Starter (optional but helpful) -->
    <dependency>
        <groupId>dev.langchain4j</groupId>
        <artifactId>langchain4j-spring-boot-starter</artifactId>
        <version>${langchain4j.version}</version>
    </dependency>
    
    <!-- Google Gemini (or your preferred LLM) -->
    <dependency>
        <groupId>dev.langchain4j</groupId>
        <artifactId>langchain4j-google-ai-gemini</artifactId>
        <version>${langchain4j.version}</version>
    </dependency>
    
    <!-- Embeddings (for RAG) -->
    <dependency>
        <groupId>dev.langchain4j</groupId>
        <artifactId>langchain4j-embeddings-all-minilm-l6-v2-q</artifactId>
        <version>${langchain4j.version}</version>
    </dependency>
    
    <!-- Easy RAG (optional, for simplified RAG setup) -->
    <dependency>
        <groupId>dev.langchain4j</groupId>
        <artifactId>langchain4j-easy-rag</artifactId>
        <version>${langchain4j.version}</version>
    </dependency>
    
    <!-- Jackson for JSON processing -->
    <dependency>
        <groupId>com.fasterxml.jackson.core</groupId>
        <artifactId>jackson-databind</artifactId>
    </dependency>
</dependencies>
```

### Understanding the Dependencies

- **langchain4j**: Core library for building AI agents
- **langchain4j-google-ai-gemini**: Integration with Google Gemini LLM
- **langchain4j-embeddings-all-minilm-l6-v2-q**: Embedding model for RAG (quantized, runs locally)
- **langchain4j-easy-rag**: Simplified RAG setup (optional)
- **lombok**: Reduces boilerplate code (getters, setters, constructors)

## Step 3: Project Structure

Create the following directory structure:

```
src/main/java/io/github/nicechester/bibleai/
├── BibleAiApplication.java          # Main Spring Boot application
├── agent/                            # AI agent classes
│   └── BibleAgent.java
├── config/                           # Configuration classes
│   ├── LLMConfig.java               # LLM configuration
│   └── RAGConfig.java               # RAG/embedding configuration
├── controller/                       # REST API controllers
│   └── BibleController.java
├── model/                            # Data transfer objects
│   ├── QueryRequest.java
│   └── QueryResponse.java
├── service/                          # Business logic services
│   ├── BibleService.java
│   └── SessionMemoryManager.java
└── tool/                            # AI agent tools
    └── BibleTools.java

src/main/resources/
├── application.yml                   # Application configuration
├── bible/                            # Bible data files
│   ├── bible_krv.json
│   └── bible_asv.json
└── static/                          # Frontend files
    └── index.html
```

### Create Main Application Class

```java
package io.github.nicechester.bibleai;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.scheduling.annotation.EnableScheduling;

@SpringBootApplication
@EnableScheduling  // For scheduled tasks like session cleanup
public class BibleAiApplication {
    public static void main(String[] args) {
        SpringApplication.run(BibleAiApplication.class, args);
    }
}
```

## Step 4: Basic Configuration

Create `src/main/resources/application.yml`:

```yaml
spring:
  application:
    name: Bible-AI

# LangChain4j Configuration
langchain4j:
  llm:
    gemini:
      model-name: ${GEMINI_MODEL_NAME:gemini-2.5-flash-lite}
      api-key: ${GEMINI_API_KEY:}
  splitter:
    text:
      maxSegmentSize: 500
      maxOverlapSize: 50

# Application-specific configuration
bible:
  data:
    json-path: ${BIBLE_JSON_PATH:classpath:bible/bible_krv.json}
    asv-json-path: ${BIBLE_ASV_JSON_PATH:classpath:bible/bible_asv.json}
  rag:
    max-results: 3
    min-score: 0.6

# Actuator endpoints
management:
  endpoints:
    web:
      exposure:
        include: health,info
  endpoint:
    health:
      show-details: when-authorized
      show-components: always
```

### Configuration Explained

- **langchain4j.llm.gemini**: Google Gemini API configuration
  - `model-name`: Which Gemini model to use
  - `api-key`: Your Google API key (from environment variable)
  
- **langchain4j.splitter.text**: Text splitting for RAG
  - `maxSegmentSize`: Maximum characters per chunk (500)
  - `maxOverlapSize`: Overlap between chunks (50)
  
- **bible.data**: Paths to Bible JSON files
- **bible.rag**: RAG retrieval settings
- **management**: Spring Boot Actuator for health checks

## Step 5: Environment Variables

Create a `.env` file (or set in your IDE/OS):

```bash
# Google Gemini API Key
GEMINI_API_KEY=your-api-key-here

# Optional: Override default model
GEMINI_MODEL_NAME=gemini-2.5-flash-lite

# Optional: Custom Bible data paths
BIBLE_JSON_PATH=classpath:bible/bible_krv.json
BIBLE_ASV_JSON_PATH=classpath:bible/bible_asv.json
```

### Getting a Google Gemini API Key

1. Go to [Google AI Studio](https://makersuite.google.com/app/apikey)
2. Sign in with your Google account
3. Click "Create API Key"
4. Copy the key and set it as `GEMINI_API_KEY`

**Important**: Never commit API keys to version control! Use environment variables.

## Step 6: Verify Setup

### Build the Project

```bash
mvn clean install
```

This should:
- Download all dependencies
- Compile the code
- Run tests (if any)
- Create a JAR file

### Run the Application

```bash
mvn spring-boot:run
```

Or in your IDE, run the `main` method in `BibleAiApplication.java`.

### Check Health Endpoint

Once running, visit:
- http://localhost:8080/actuator/health

You should see:
```json
{
  "status": "UP"
}
```

## Step 7: Common Setup Issues

### Issue 1: Maven Dependencies Not Downloading

**Solution:**
```bash
mvn clean install -U
```

The `-U` flag forces Maven to update dependencies.

### Issue 2: Lombok Not Working in IDE

**Solution:**
- **IntelliJ IDEA**: Install Lombok plugin, enable annotation processing
- **Eclipse**: Install Lombok from [projectlombok.org](https://projectlombok.org)
- **VS Code**: Install "Language Support for Java" extension

### Issue 3: ONNX Runtime Warnings

**Expected**: You may see ONNX runtime logs during startup. This is normal - the embedding model is initializing.

### Issue 4: API Key Not Found

**Error**: `GEMINI_API_KEY environment variable is not set`

**Solution**: Set the environment variable:
```bash
export GEMINI_API_KEY=your-key-here
```

Or add to `application.yml` (only for development!):
```yaml
langchain4j:
  llm:
    gemini:
      api-key: your-key-here  # ⚠️ Remove before committing!
```

## Step 8: Project Structure Best Practices

### Package Organization

```
com.yourcompany.yourapp/
├── Application.java              # Main class
├── agent/                        # AI agents
├── config/                       # Configuration beans
├── controller/                   # REST endpoints
├── model/                        # DTOs, entities
├── service/                      # Business logic
└── tool/                         # AI tools
```

### Naming Conventions

- **Agents**: `*Agent.java` (e.g., `BibleAgent.java`)
- **Tools**: `*Tools.java` (e.g., `BibleTools.java`)
- **Services**: `*Service.java` (e.g., `BibleService.java`)
- **Controllers**: `*Controller.java` (e.g., `BibleController.java`)
- **Config**: `*Config.java` (e.g., `LLMConfig.java`)

## Next Steps

Now that your project is set up, you're ready to:

1. **Chapter 3**: Learn LangChain4j basics
2. **Chapter 4**: Configure your LLM
3. **Chapter 5**: Build your first tool

## Key Takeaways

✅ Spring Boot project structure  
✅ LangChain4j dependencies added  
✅ Basic configuration in place  
✅ Environment variables set up  
✅ Project builds and runs successfully  

**Remember**: 
- Keep API keys in environment variables
- Use Lombok to reduce boilerplate
- Follow the package structure for organization
- Test that the application starts before moving on

## Quick Reference

### Essential Dependencies
```xml
<!-- Core -->
<dependency>
    <groupId>dev.langchain4j</groupId>
    <artifactId>langchain4j</artifactId>
</dependency>

<!-- LLM -->
<dependency>
    <groupId>dev.langchain4j</groupId>
    <artifactId>langchain4j-google-ai-gemini</artifactId>
</dependency>

<!-- Embeddings -->
<dependency>
    <groupId>dev.langchain4j</groupId>
    <artifactId>langchain4j-embeddings-all-minilm-l6-v2-q</artifactId>
</dependency>
```

### Essential Configuration
```yaml
langchain4j:
  llm:
    gemini:
      api-key: ${GEMINI_API_KEY:}
```

### Essential Environment Variable
```bash
export GEMINI_API_KEY=your-key-here
```

Ready to build your first AI agent? Let's move to Chapter 3! 🚀

---

## Navigation

| [← Previous](01-introduction) | [Home](home) | [Next →](03-langchain4j-basics) |
|:---|:---:|---:|
| Chapter 1: Introduction to Agentic AI | Building Agentic AI Applications with Java | Chapter 3: Understanding LangChain4j |

