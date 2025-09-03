
Transform unstructured data (PDFs, DOCs, TXT, YouTube videos, web pages, etc.) into a structured Knowledge Graph stored in Neo4j using the power of Large Language Models (LLMs) and the LangChain framework.

This application allows you to upload files from various sources (local machine, GCS, S3 bucket, or web sources), choose your preferred LLM model, and generate a Knowledge Graph. 

---

## Key Features

### **Knowledge Graph Creation**
- Seamlessly transform unstructured data into structured Knowledge Graphs using advanced LLMs.
- Extract nodes, relationships, and their properties to create structured graphs.


### **Schema Support**
- Use a custom schema or existing schemas configured in the settings to generate graphs.

### **Graph Visualization**
- View graphs for specific or multiple data sources simultaneously in **Neo4j Bloom**.

### **Chat with Data**
- Interact with your data in the Neo4j database through conversational queries.
- Retrieve metadata about the source of responses to your queries.
- For a dedicated chat interface, use the standalone chat application with **[/chat-only](/chat-only) route.** 

### **LLMs Supported**
1. OpenAI
2. Gemini
3. Diffbot
4. Azure OpenAI(dev deployed version)
5. Anthropic(dev deployed version)
6. Fireworks(dev deployed version)
7. Groq(dev deployed version)
8. Amazon Bedrock(dev deployed version)
9. Ollama(dev deployed version)
10. Deepseek(dev deployed version)
11. Other OpenAI Compatible baseurl models(dev deployed version)
    
---

## Getting Started

### **Prerequisites**
- Neo4j Database **5.23 or later** with APOC installed.
  - **Neo4j Aura** databases (including the free tier) are supported.
  - If using **Neo4j Desktop**, you will need to deploy the backend and frontend separately (docker-compose is not supported).

---

## Deployment Options

### **Local Deployment**

#### Using Docker-Compose
Run the application using the default `docker-compose` configuration.

1. **Supported LLM Models**: 
   - By default, only OpenAI and Diffbot are enabled. Gemini requires additional GCP configurations. 
   - Use the `VITE_LLM_MODELS_PROD` variable to configure the models you need. Example:
     ```bash
     VITE_LLM_MODELS_PROD="openai_gpt_4o,openai_gpt_4o_mini,diffbot,gemini_1.5_flash"
     ```

2. **Input Sources**: 
   - By default, the following sources are enabled: `local`, `YouTube`, `Wikipedia`, `AWS S3`, and `web`. 
   - To add Google Cloud Storage (GCS) integration, include `gcs` and your Google client ID:
     ```bash
     VITE_REACT_APP_SOURCES="local,youtube,wiki,s3,gcs,web"
     VITE_GOOGLE_CLIENT_ID="your-google-client-id"
     ```

#### Chat Modes
Configure chat modes using the `VITE_CHAT_MODES` variable:
- By default, all modes are enabled: `vector`, `graph_vector`, `graph`, `fulltext`, `graph_vector_fulltext`, `entity_vector`, and `global_vector`. 
- To specify specific modes, update the variable. For example:
  ```bash
  VITE_CHAT_MODES="vector,graph"
  ```

---

### **Running Backend and Frontend Separately**

For development, you can run the backend and frontend independently.

#### **Frontend Setup**
1. Create the `.env` file in the `frontend` folder by copying `frontend/example.env`.
2. Update environment variables as needed.
3. Run:
   ```bash
   cd frontend
   yarn
   yarn run dev
   ```

#### **Backend Setup**
1. Create the `.env` file in the `backend` folder by copying `backend/example.env`.
2. Preconfigure user credentials in the `.env` file to bypass the login dialog:
   ```bash
   NEO4J_URI=<your-neo4j-uri>
   NEO4J_USERNAME=<your-username>
   NEO4J_PASSWORD=<your-password>
   NEO4J_DATABASE=<your-database-name>
   ```
3. Run:
   ```bash
   cd backend
   python -m venv envName
   source envName/bin/activate
   pip install -r requirements.txt
   uvicorn score:app --reload
   ```

---

### **Cloud Deployment**

Deploy the application on **Google Cloud Platform** using the following commands:

#### **Frontend Deployment**
```bash
gcloud run deploy dev-frontend \
  --source . \
  --region us-central1 \
  --allow-unauthenticated
```

#### **Backend Deployment**
```bash
gcloud run deploy dev-backend \
  --set-env-vars "OPENAI_API_KEY=<your-openai-api-key>" \
  --set-env-vars "DIFFBOT_API_KEY=<your-diffbot-api-key>" \
  --set-env-vars "NEO4J_URI=<your-neo4j-uri>" \
  --set-env-vars "NEO4J_USERNAME=<your-username>" \
  --set-env-vars "NEO4J_PASSWORD=<your-password>" \
  --source . \
  --region us-central1 \
  --allow-unauthenticated
```

---
## For local llms (Ollama)
1. Pull the docker imgage of ollama
```bash
docker pull ollama/ollama
```
2. Run the ollama docker image
```bash
docker run -d -v ollama:/root/.ollama -p 11434:11434 --name ollama ollama/ollama
```
3. Execute any llm model ex llama3
```bash
docker exec -it ollama ollama run llama3
```
4. Configure  env variable in docker compose.
```env
LLM_MODEL_CONFIG_ollama_<model_name>
#example
LLM_MODEL_CONFIG_ollama_llama3=${LLM_MODEL_CONFIG_ollama_llama3-llama3,
http://host.docker.internal:11434}
```
5. Configure the backend API url
```env
VITE_BACKEND_API_URL=${VITE_BACKEND_API_URL-backendurl}
```
6. Open the application in browser and select the ollama model for the extraction.
7. Enjoy Graph Building.
---


## Usage
1. Connect to Neo4j Aura Instance which can be both AURA DS or AURA DB by passing URI and password through Backend env, fill using login dialog or drag and drop the Neo4j credentials file.
2. To differntiate we have added different icons. For AURA DB we have a database icon and for AURA DS we have scientific molecule icon right under Neo4j Connection details label.
3. Choose your source from a list of Unstructured sources to create graph.
4. Change the LLM (if required) from drop down, which will be used to generate graph.
5. Optionally, define schema(nodes and relationship labels) in entity graph extraction settings.
6. Either select multiple files to 'Generate Graph' or all the files in 'New' status will be processed for graph creation.
7. Have a look at the graph for individual files using 'View' in grid or select one or more files and 'Preview Graph'
8. Ask questions related to the processed/completed sources to chat-bot, Also get detailed information about your answers generated by LLM.

---

