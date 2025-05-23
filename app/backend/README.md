# Backend Documentation

This document provides an overview of the backend in `app/backend/` for the Azure Search + OpenAI Demo project. The backend is responsible for serving the API, handling authentication, document ingestion, and integrating with Azure AI services.

---

## Table of Contents
- [Overview](#overview)
- [Architecture](#architecture)
- [Key Modules](#key-modules)
- [Setup & Running](#setup--running)
- [Configuration](#configuration)
- [APIs](#apis)
- [Document Ingestion](#document-ingestion)
- [Approaches](#approaches)
- [Monitoring & Logging](#monitoring--logging)
- [Testing](#testing)
- [Customization](#customization)
- [References](#references)

---

## Overview
The backend in `app/backend/` is built using [Quart](https://quart.palletsprojects.com/), an async Python web framework. It exposes REST APIs for chat, Q&A, document upload, and integrates with Azure OpenAI, Azure AI Search, and other Azure services. It supports advanced features like Retrieval Augmented Generation (RAG), vision (GPT-4V), user authentication, and speech. The backend is designed for extensibility, security, and scalability in enterprise environments.

## Architecture
- **Entrypoint:** `main.py` (creates the Quart app and launches the server)
- **App Factory:** `app.py` (registers routes, configures services, sets up dependency injection)
- **Approaches:** `approaches/` (RAG, chat, vision, and custom approaches)
- **Core Logic:** `core/` (authentication, helpers, and utility functions)
- **Document Prep:** `prepdocs.py` and `prepdocslib/` (data ingestion, parsing, embedding, and indexing)
- **Config:** `config.py` (environment/config variables and feature toggles)
- **Error Handling:** `error.py` (centralized error responses)
- **Decorators:** `decorators.py` (authentication, authorization, and route protection)

## Key Modules
- `app.py`: Main app logic, route registration, service setup, dependency injection, and middleware configuration.
- `main.py`: Entrypoint for running the backend (e.g., with Gunicorn or locally).
- `config.py`: Loads and manages configuration from environment variables, including Azure and OpenAI settings.
- `prepdocs.py`: Script for document ingestion, parsing, embedding, and indexing into Azure AI Search.
- `prepdocslib/`: Library for file parsing, embedding, and ingestion strategies, supporting multiple file formats and vectorization.
- `approaches/`: Implements different RAG and chat approaches (e.g., ChatReadRetrieveReadApproach, RetrieveThenReadApproach, vision-enabled approaches). Easily extensible for custom logic.
- `core/`: Authentication and utility helpers, including support for Microsoft Entra ID (Azure AD).
- `error.py`: Centralized error handling and response formatting.
- `decorators.py`: Route decorators for authentication and authorization.

## Setup & Running
1. **Install dependencies:**
   ```sh
   pip install -r requirements.txt
   ```
2. **Set environment variables:** See [Configuration](#configuration) for required and optional variables.
3. **Run the backend:**
   ```sh
   python3 -m gunicorn main:app
   ```
   Or use the provided `start.sh` script for local development.

## Configuration
Configuration is managed via environment variables. Key variables include:
- `AZURE_STORAGE_ACCOUNT`, `AZURE_STORAGE_CONTAINER`: Azure Blob Storage for documents.
- `AZURE_SEARCH_SERVICE`, `AZURE_SEARCH_INDEX`: Azure AI Search service and index.
- `OPENAI_HOST`, `AZURE_OPENAI_CHATGPT_MODEL`, `AZURE_OPENAI_EMB_MODEL_NAME`, etc.: OpenAI integration.
- `USE_GPT4V`, `USE_USER_UPLOAD`, `USE_SPEECH_INPUT_BROWSER`, etc.: Feature toggles for vision, user uploads, and speech features.
- `APPLICATIONINSIGHTS_CONNECTION_STRING`: For Application Insights monitoring and tracing.
- `ALLOWED_ORIGIN`: CORS configuration for frontend-backend communication.
- `APP_LOG_LEVEL`: Controls logging verbosity (e.g., DEBUG, INFO, WARNING).
- See `config.py` and `app.py` for the full list and descriptions.

## APIs
- **Chat/Q&A:** `/chat`, `/ask` (POST) — Main endpoints for chat and question answering.
- **Config:** `/config` (GET) — Returns backend feature flags and configuration for the frontend.
- **Auth:** `/auth_setup` (GET) — Returns authentication setup/configuration for the client.
- **Document Upload:** `/upload` (POST, if enabled) — Allows users to upload documents for ingestion.
- **Static/Assets:** `/assets/<path>` — Serves static files and assets for the frontend.

See `app.py` for all route definitions and additional endpoints.

## Document Ingestion
- Use `prepdocs.py` to ingest and index documents into Azure AI Search.
- Supports PDF, HTML, JSON, Markdown, and plain text (see `prepdocslib/` for supported formats).
- Example usage:
  ```sh
  python prepdocs.py './data/*' --storageaccount <account> --container <container> --searchservice <service> --index <index>
  ```
- Supports Azure Data Lake, integrated vectorization, and Access Control Lists (ACLs) for document-level security.
- Embeddings are generated using Azure OpenAI or OpenAI API, with support for custom models and dimensions.

## Approaches
- **ChatReadRetrieveReadApproach:** Multi-turn chat with retrieval-augmented generation (RAG).
- **RetrieveThenReadApproach:** Single-turn Q&A with retrieval and synthesis.
- **Vision-enabled:** GPT-4V for image-based and multimodal documents.
- **Custom approaches:** Easily extendable by adding new classes in `approaches/`.
- **Security:** All approaches support document-level security and user/group-based filtering.

## Monitoring & Logging
- Integrated with Application Insights if `APPLICATIONINSIGHTS_CONNECTION_STRING` is set.
- Logging level controlled by `APP_LOG_LEVEL` (default: INFO, production: WARNING).
- All HTTP requests, errors, and performance metrics are tracked for observability.
- See [docs/appservice.md](../../docs/appservice.md#configuring-log-levels) for advanced logging configuration and troubleshooting.

## Testing
- Tests are in the `tests/` folder at the project root.
- Use `pytest` to run tests:
  ```sh
  pytest
  ```
- Includes unit, integration, and end-to-end tests for backend logic, authentication, and document ingestion.

## Customization
- See [docs/customization.md](../../docs/customization.md) for backend customization guidance.
- Main customization points:
  - `approaches/`: Add or modify RAG/chat/vision approaches.
  - `prepdocslib/`: Extend file parsing, embedding, or ingestion strategies.
  - Environment variables: Enable/disable features, change models, or adjust security.
- Supports enterprise features such as authentication, private endpoints, and custom data connectors.

## References
- [Project README](../../README.md)
- [Data Ingestion Guide](../../docs/data_ingestion.md)
- [App Customization](../../docs/customization.md)
- [Productionizing](../../docs/productionizing.md)
- [App Service Debugging](../../docs/appservice.md)
- [Azure AI Services Documentation](https://learn.microsoft.com/azure/cognitive-services/openai/)
- [Quart Documentation](https://quart.palletsprojects.com/)

---

For further details, see the code and linked documentation above. For advanced deployment, security, and scaling, consult the `docs/` folder and Azure documentation.
