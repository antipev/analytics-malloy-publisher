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


## 🤖 Gemini AI Setup
Objective 1: to use AI agent for coding => Technical users, Aanlysts, Engeneers
Objective 2: to use AI agent for quering MCP Server => for Analysts, Business Users (if UI for Gemeni CLI is available)

### Step 1: Install NVM
Run this command in your terminal to download and install NVM:
```
Bash
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.40.1/install.sh | bash
```

### Step 2: Reload your terminal environment
To make the nvm command available without closing and reopening your terminal, run:
```
Bash
source ~/.bashrc
(Note: If you are using Zsh instead of Bash, run source ~/.zshrc instead).
```

### Step 3: Install Node.js
Now, you can install the latest Long Term Support (LTS) version of Node.js, which is fully compatible with the Gemini CLI:
```
Bash
nvm install --lts
```
### Step 4: Verify the installation
Check that Node and NPM installed correctly and are on the right versions:
```
Bash
node -v
npm -v
```

(You should see a version number starting with v18, v20, or v22).

________________________________________
### Step 5: Install the Gemini CLI
Now that your environment is properly set up, you can successfully install and run the Gemini CLI:
```
Bash
npm install -g @google/gemini-cli
```
Once it finishes, just type:

```
Bash
gemini
```

It will prompt you to log in via your browser, and you'll be ready to use it!

Reference: https://geminicli.com/docs/get-started/installation/


## 🤖 Connecting Gemini CLI to Malloy Publisher MCP
The Gemini CLI uses a `settings.json` file to manage external tool connections. Here is exactly how to set it up.

### Step 1: Open the Gemini CLI settings file
The global configuration file for the Gemini CLI lives in a hidden .gemini folder in your home directory.
Run this command in your terminal to create the directory (if it doesn't exist) and open/create the file `settings.json`(if it does't exist yet):
```
Bash
mkdir -p ~/.gemini

```
### Step 2: Add your MCP server configuration
In the editor, you need to define an mcpServers object. In this particular example, the MCPserver is hosted on Google Cloud Run (https://...a.run.app/), 

Update settings: Cloud Run server specifically uses the /mcp endpoint (not /sse), and it strictly requires that Accept header so it knows to stream the events back to you.
Run this command in your terminal:
```
cat << 'EOF' > ~/.gemini/settings.json
{
  "ide": {
    "hasSeenNudge": true,
    "enabled": true
  },
  "security": {
    "auth": {
      "selectedType": "oauth-personal"
    }
  },
  "mcpServers": {
    "malloy-cloud": {
      "url": "https://malloy-publisher-mcp-bolcwt6srq-nn.a.run.app/mcp",
      "headers": {
        "Accept": "application/json, text/event-stream"
      }
    }
  }
}
EOF
```

### Step 3: Verify the connection
Now, let's make sure the Gemini CLI successfully reads the file and connects to your Malloy server.
Launch the CLI by running:
```
Bash
gemini
Once the prompt is ready, type the following built-in slash command to check your active servers:
Bash
/mcp list
```

If the configuration is correct, you should see a 🟢 malloyPublisher - Ready status indicator along with a list of the specific tools and prompts your Malloy server exposes!
________________________________________



## 🤖 GEMINI Skills set up
In oder to correctly query Malloy Publisher MCP server skill set for Gemeni needed.

Follow these guide here:
https://geminicli.com/docs/cli/skills/
https://geminicli.com/docs/cli/creating-skills/

For this particular case current repository contains `.gemini` folder (usually outside of this repository, but for this case added to repository for convenience) with "malloy-query-best-practices" skill for testing