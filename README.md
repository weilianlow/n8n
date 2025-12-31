# n8n & AI Agent Setup
This repository contains the required setup to run n8n workflows integrated with Google Gemini and Qdrant (Vector Database).

## 📋 Prerequisites
Ensure you have the following installed before proceeding:
* [Docker Desktop](https://www.docker.com/products/docker-desktop/) (includes Docker Compose)
* Git

## 🐳 Docker and Services Setup
Navigate to the root directory of this project where the `docker-compose.yml` file is located, then execute the following commands:

1.  **Start n8n:**
    ```bash
    docker compose up -d n8n
    ```
2.  **(Optional) Start Qdrant:**
    Required only if you are using RAG (Retrieval-Augmented Generation) workflows.
    ```bash
    docker compose up -d qdrant
    ```

> **Note:** Once services are running, access n8n at `http://localhost:5678` and Qdrant at `http://localhost:6333/dashboard`.

## 🔑 Credentials Setup

### 1. Google Cloud (Gmail & Drive)
To authorise n8n to access your Gmail and Google Drive, you must create an OAuth 2.0 application.

1.  Log in to the [Google Cloud Console](https://console.cloud.google.com/).
2.  **Enable required APIs:**
    * Navigate to **APIs & Services > Library**.
    * Search for and enable **Gmail API** and **Google Drive API**.
3.  **Configure OAuth Consent Screen:**
    * Go to **APIs & Services > OAuth consent screen**.
    * Select **External** for personal testing and provide the required contact information.
4.  **Create OAuth Credentials:**
    * Navigate to **APIs & Services > Credentials**.
    * Click **+ CREATE CREDENTIALS** → **OAuth client ID**.
    * Select **Web application** as the application type.
    * Name the credential `n8n-local`.
    * Add the following Authorised redirect URI:
        `http://localhost:5678/rest/oauth2-credential/callback`
    * Click **Create**.
5.  **Store Credentials:** Copy and securely save the `Client ID` and `Client Secret` for use in n8n.

### 2. Gemini API
> **Note:** Google Gemini is recommended as it offers a free tier and provides [transparent pricing](https://ai.google.dev/gemini-api/docs/pricing) across all models.

1.  Visit [Google AI Studio](https://aistudio.google.com/app/api-keys).
2.  Click **Create API key**.
3.  Select or create a project, then generate the API key.
4.  **Store Credential:** Copy and securely save the `API Key` for use in n8n.

#### a. [Gemini Models](https://ai.google.dev/gemini-api/docs/models)
Google provides specialised models optimised for different use cases:
* **Gemini Pro:** For complex reasoning and multi-step tasks.
* **Gemini Flash:** For faster responses and cost-efficiency (chatbots, real-time applications).
* **Gemini Flash-Lite:** For high-volume processing and efficient inference (translations, summarisation).

#### b. [Text Embeddings](https://ai.google.dev/gemini-api/docs/embeddings)
The Gemini API provides text embedding models that convert words, phrases, sentences, and code into numerical vectors. These embeddings enable advanced natural language processing tasks such as semantic search, classification, and clustering, delivering more precise and context-aware results compared to keyword-based methods.

> **Important:** Each embedding model produces a fixed number of vector dimensions. Ensure your vector database configuration matches the dimensions of your chosen embedding model (e.g., `text-embedding-004` produces 768-dimensional vectors).

## ⚙️ Application Configuration

### n8n Workflow Setup
1.  Open your browser and navigate to `http://localhost:5678/home/workflows`.
2.  **Import Workflows:**
    * Click **Add workflow** → **Import from file**.
    * Select the `.json` workflow files from this repository (located in the `/workflows` folder).
3.  **Configure Credentials:**
    * Open each imported workflow node that requires authentication (e.g., Gmail, Google Drive).
    * Select **Create New Credential** and enter the `Client ID`, `Client Secret`, or `API Key` that you generated earlier.
4.  **Activate Workflow:** Toggle the workflow to **Active** (top-right corner) to enable it.

### Qdrant Setup (Optional)
*Required only for RAG (Retrieval-Augmented Generation) and vector-based workflows.*

1.  Navigate to `http://localhost:6333/dashboard`.
2.  Click **Create Collection**.
    * **Collection Name:** Enter `my_knowledge_base` (or use the name specified in your n8n workflow).
    * **Vector configuration:**
        * Enable **Global search** (if available) or use standard setup.
        * **Metric:** Select `Cosine`.
        * **Dimensions:** Enter `768` (Critical: This value must match the output dimensions of your chosen embedding model, e.g., `text-embedding-004` produces 768-dimensional vectors).
3.  Click **Create**. The collection is now ready to receive embeddings from n8n.
