# RAG System Deployment Guide (Based on RAGFlow)

This repository provides a **step-by-step guide** to deploy a **Retrieval-Augmented Generation (RAG)** system using the open-source engine [RAGFlow](https://github.com/infiniflow/ragflow).

## Caution: Architecture Compatibility

The server setup in this repository is based on the **ARM64** architecture (e.g., Apple Silicon Mac).

> If you are using an **x86-based** machine (e.g., Intel or AMD CPUs), please refer to the official [RAGFlow documentation](https://github.com/infiniflow/ragflow) for the appropriate Docker configuration, as the Dockerfiles may differ.

## Clone the Repository

```bash
git clone https://github.com/Jayden-Xu/RAG
cd RAG
```

## Step 1: Host RAGFlow Locally

```bash
cd docker
docker compose -f docker-compose-macos.yml build
docker compose -f docker-compose-macos.yml up -d
```

Once the containers are built and started, they should be running in the background.

### Access the Web Interface

Open your browser and visit:

```
https://127.0.0.1
```

You should be redirected to the **RAGFlow frontend**.

### What’s Next?

1. Add your **model APIs** through the interface.
2. Upload your **custom knowledge base**.
3. Start chatting with your local RAG system!

