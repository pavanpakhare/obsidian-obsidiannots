[[Spring Boot]]
# **Spring AI Tutorial: Building AI-Powered Applications with Spring**

Spring AI is an emerging project that integrates **Artificial Intelligence (AI) and Machine Learning (ML)** capabilities into Spring applications. This tutorial covers how to use **Spring AI** to build intelligent applications with features like:
- **Chatbots (OpenAI, Gemini, Ollama)**
- **Embeddings & Vector Databases**
- **Prompt Engineering**
- **AI Model Integration**

---

## **Table of Contents**
1. [**Introduction to Spring AI**](#introduction-to-spring-ai)
2. [**Setting Up Spring AI**](#setting-up-spring-ai)
3. [**Chatbots with Spring AI**](#chatbots-with-spring-ai)
   - OpenAI ChatGPT
   - Google Gemini
   - Local Models (Ollama)
4. [**Embeddings & Vector Databases**](#embeddings--vector-databases)
   - Text Embeddings (OpenAI, Hugging Face)
   - Storing in Vector DBs (Pinecone, Weaviate)
5. [**Prompt Engineering**](#prompt-engineering)
   - Structured Prompts
   - Few-Shot Learning
6. [**Advanced AI Integrations**](#advanced-ai-integrations)
   - Image Generation (DALL·E, Stable Diffusion)
   - Function Calling
7. [**Deploying AI Applications**](#deploying-ai-applications)
   - Dockerizing Spring AI Apps
   - Cloud Deployment (AWS, GCP)

---

## **1. Introduction to Spring AI**
Spring AI provides a **unified API** to interact with AI models like:
- **OpenAI (GPT-4, DALL·E)**
- **Google Gemini**
- **Hugging Face**
- **Local LLMs (Ollama, Llama 2)**

### **Key Features**
✅ **Abstraction layer** for multiple AI providers  
✅ **Prompt templates** for structured AI interactions  
✅ **Embedding support** for semantic search  
✅ **Vector database integration** (Pinecone, Weaviate)  

---

## **2. Setting Up Spring AI**
### **Prerequisites**
- Java 17+
- Spring Boot 3.2+
- OpenAI API Key (or Gemini API Key)

### **Step 1: Add Spring AI Dependencies**
```xml
<dependency>
    <groupId>org.springframework.ai</groupId>
    <artifactId>spring-ai-openai-spring-boot-starter</artifactId>
    <version>0.8.0</version>
</dependency>
```
*(Replace `openai` with `gemini` or `ollama` for other models.)*

### **Step 2: Configure API Key**
`application.properties`:
```properties
spring.ai.openai.api-key=YOUR_OPENAI_KEY
```

---

## **3. Chatbots with Spring AI**
### **Using OpenAI ChatGPT**
```java
@RestController
@RequestMapping("/ai")
public class AIController {

    private final ChatClient chatClient;

    public AIController(ChatClient chatClient) {
        this.chatClient = chatClient;
    }

    @GetMapping("/chat")
    public String chat(@RequestParam String message) {
        return chatClient.call(message);
    }
}
```
**Test it:**
```bash
curl "http://localhost:8080/ai/chat?message=Tell%20me%20a%20joke"
```

### **Using Google Gemini**
```properties
spring.ai.gemini.api-key=YOUR_GEMINI_KEY
```
```java
@Bean
public ChatClient geminiChatClient() {
    return new GeminiChatClient();
}
```

### **Using Local Models (Ollama)**
```properties
spring.ai.ollama.base-url=http://localhost:11434
spring.ai.ollama.model=llama2
```
```java
@Bean
public ChatClient ollamaChatClient() {
    return new OllamaChatClient();
}
```

---

## **4. Embeddings & Vector Databases**
### **Generating Embeddings**
```java
@Autowired
private EmbeddingClient embeddingClient;

public List<Double> getEmbedding(String text) {
    return embeddingClient.embed(text);
}
```

### **Storing in Pinecone (Vector DB)**
```properties
spring.ai.vectorstore.pinecone.api-key=YOUR_PINECONE_KEY
spring.ai.vectorstore.pinecone.environment=gcp-starter
```
```java
@Autowired
private VectorStore vectorStore;

public void storeEmbedding(String text) {
    vectorStore.add(List.of(text), List.of(getEmbedding(text)));
}
```

---

## **5. Prompt Engineering**
### **Structured Prompts**
```java
String prompt = """
    You are a helpful AI assistant.
    Answer the following question: {question}
    """;

PromptTemplate template = new PromptTemplate(prompt);
template.add("question", "What is Spring AI?");
String response = chatClient.call(template.render());
```

### **Few-Shot Learning**
```java
String prompt = """
    Examples:
    Q: What is Java? 
    A: Java is a programming language.
    
    Q: {question}
    A:""";

PromptTemplate template = new PromptTemplate(prompt);
template.add("question", "What is Python?");
String answer = chatClient.call(template.render());
```

---

## **6. Advanced AI Integrations**
### **Image Generation (DALL·E)**
```java
@Autowired
private ImageClient imageClient;

public String generateImage(String prompt) {
    ImageResponse response = imageClient.call(
        new ImagePrompt(prompt)
    );
    return response.getResult().getOutput().getUrl();
}
```

### **Function Calling (AI + APIs)**
```java
@Function
public String getWeather(String location) {
    return "Sunny in " + location;
}

// AI will call this function when needed
```

---

## **7. Deploying AI Applications**
### **Dockerizing Spring AI**
```dockerfile
FROM openjdk:17
COPY target/spring-ai-app.jar app.jar
ENTRYPOINT ["java", "-jar", "app.jar"]
```

### **Deploying to AWS**
```bash
aws ecr create-repository --repository-name spring-ai
docker build -t spring-ai .
docker push aws_account_id.dkr.ecr.region.amazonaws.com/spring-ai
```

---

