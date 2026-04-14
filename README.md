# Analytics Malloy Publisher: Project Overview

This project is organized into two primary areas to separate data development from business consumption.

## 📂 Project Structure

### [A] Semantic Layer (Development)
**Location:** `A_Semantic_Layer/`

This area contains the logic, staging, and experimental analysis used to build data models.
* **Architecture:** Follows a 4-layer structure (Data -> Sources -> Models -> Analysis).
* **Environment:** This folder is for data engineers, analysts, and data scientists.
* **Research:** Contains Malloy files, as well as Python and Malloy notebooks for exploration and research.

### [B] Published Semantic Layer (Production)
**Location:** `B_Published_Semantic_Layer/`

This area contains clean, verified Malloy assets ready for consumption.
* **Malloy Publisher:** Files are picked up by the Malloy Publisher and exposed via an MCP Server.
* **AI & Business Interface:** Acts as the entry point for business users and AI Agents to query data using natural language.
* **Simplified:** Technical complexity is hidden so users and AI agents only see defined metrics and tables.

## Malloy Publisher Demo: https://malloy-publisher-bolcwt6srq-nn.a.run.app/
## Malloy MCP Demo:       https://malloy-publisher-mcp-bolcwt6srq-nn.a.run.app/

---

## 🚀 Workflow

1.  **Develop:** Build and test the semantic model inside folder [A].
2.  **Publish:** Move final .malloy files to folder [B] to make them available to the MCP server and AI tools.

---

## 🔗 Resources
* [Malloy Official Docs](https://docs.malloydata.dev/)
* [Modeling Best Practices](https://docs.malloydata.dev/documentation/language/modeling)
* [Malloy Publisher Info](https://github.com/malloydata/malloy-publisher)