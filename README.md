# Autonomous GitHub Development Daemon

An experimental AI agent that autonomously handles software development from issue creation to pull request merge.

---

## Core Concept

This project is an open-source implementation of a persistent, autonomous AI agent designed to act as a junior DevOps and software engineer. Unlike "one-shot" code generation scripts, this agent operates as a **daemon**, continuously monitoring a GitHub repository for new issues.

When a new issue is detected, the daemon spawns a dedicated "worker agent" to handle the task. This worker operates within a complete, end-to-end Git workflow: it creates a new branch, writes code, tests its changes, creates a pull request, merges the PR upon completion, and cleans up its own branch before closing the original issue.

This is a "glass box" architecture, designed to be understood, modified, and used as a blueprint for the next generation of AI-driven software development.

## How It Works: The Agent's Lifecycle

The daemon operates in two primary phases: Project Creation and Issue Monitoring.

```
PHASE 1: PROJECT CREATION (Manual Trigger)
===========================================
[ User provides a high-level prompt ]
              |
              V
[ Agent creates a private GitHub repo ]
              |
              V
[ Agent bootstraps the project locally ]
(Writes initial code, e.g., README, main.py)
              |
              V
[ Agent tests its initial setup ]
              |
              V
[ Agent commits and pushes to 'main' ]
              |
              V
[ Project is registered in projects.json ]


PHASE 2: DAEMON MONITORING (Automatic Loop)
============================================
[ Daemon starts, monitors a registered repo ]
              |
              V
[ Checks for new GitHub issues every 60 seconds ]
              |
              +------> [ No new issues? Wait and repeat. ]
              |
[ NEW ISSUE DETECTED! ]
              |
              V
[ Spawns a dedicated "Worker Agent" ]
              |
              V
[ Worker creates a new feature branch (e.g., 'fix/issue-123') ]
              |
              V
[ Worker reads files, writes code, tests changes ] <--+
(This is the main development loop)                    |
              ^----------------------------------------+
              |
              V
[ Worker commits and pushes to the feature branch ]
              |
              V
[ Worker creates a Pull Request on GitHub ]
              |
              V
[ Worker merges its own PR into 'main' ]
              |
              V
[ Worker cleans up by deleting the feature branch ]
              |
              V
[ Worker closes the original GitHub issue with a comment ]
              |
              V
[ Worker terminates. Daemon continues monitoring. ]
```

## Core Features

*   **End-to-End Git Workflow:** The agent correctly uses branches, commits, pull requests, and merges for every task.
*   **Persistent Daemon Monitoring:** The agent runs continuously, watching for new work without needing manual triggers for every task.
*   **GitHub Issue Integration:** Uses GitHub as a natural "inbox" for development tasks, creating a seamless workflow.
*   **Surgical Code Editing:** Utilizes a `replace_text_in_file` tool, allowing the AI to make precise changes rather than rewriting entire files, significantly reducing errors.
*   **Stateful Project Management:** Remembers the projects it manages via a local `projects.json` file, allowing the daemon to be restarted without losing context.
*   **Extensible Toolbelt:** A clean `AITools` class makes it easy to add new capabilities for the AI to use.

> ⚠️ **WARNING: This is a powerful, experimental tool.**
> This agent can autonomously write, commit, and push code to your GitHub repositories. It interacts with your local file system and executes shell commands. While designed to be robust, you use this software **at your own risk.** Always supervise the agent and never run it in a production environment or with sensitive credentials.

## Setup & Installation

**1. Clone the Repository**

```bash
git clone https://github.com/YOUR_USERNAME/your-repo-name.git
cd your-repo-name
```

**2. Install Dependencies**

It's highly recommended to use a virtual environment.

```bash
python -m venv venv
source venv/bin/activate  # On Windows use `venv\Scripts\activate`
pip install -r requirements.txt
```
**3. Configure Environment Variables**

This project uses a `.env` file to manage secret keys. **Never commit your `.env` file to Git.**

*   Create a file named `.env` in the root of the project directory.
*   Copy the contents of `.env.example` into your new `.env` file.
*   Fill in the required values:

```env
# The API key for the Cerebras cloud platform.
CEREBRAS_API_KEY="csk-xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"

# A GitHub Personal Access Token.
# Go to GitHub > Settings > Developer settings > Personal access tokens > Tokens (classic)
# Generate a new token with full "repo" permissions.
GITHUB_TOKEN="github_pat_xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"
```

## How to Use

Run the main script from your terminal:

```bash
python main.py
```

You will be presented with a menu:

1.  **Create a new project:** The agent will ask you for a high-level goal, create a private GitHub repo, write the initial code, and push it.
2.  **Monitor an existing project:** The agent will enter its main daemon loop, watching a previously created or registered project for new issues.
3.  **Add an existing project to the monitor list:** Allows you to register a project you've already created, either manually or with a previous run of the agent.

## Future Development (Roadmap)

This project is a proof-of-concept. The next steps to making it even more robust include:

*   **Enhanced Security:** Implement a more secure execution environment for the agent's commands, such as using Docker containers for process sandboxing.
*   **Expanded Toolset:** Add more specialized tools for the AI, such as a `search_code` tool for better code navigation or a `git_diff` tool for self-correction.

## License

This project is licensed under the **MIT License**. See the `LICENSE` file for details.
