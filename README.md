# 🦜 LangChain Chains Playground

A hands-on collection of LangChain Expression Language (LCEL) chains demonstrating **sequential**, **parallel**, and **conditional** workflows using OpenRouter-compatible chat models.

![Python](https://img.shields.io/badge/Python-3.10%2B-blue?logo=python&logoColor=white)
![LangChain](https://img.shields.io/badge/LangChain-LCEL-green?logo=langchain&logoColor=white)
![OpenRouter](https://img.shields.io/badge/LLM-OpenRouter-orange)
![License](https://img.shields.io/badge/License-MIT-lightgrey)
![Status](https://img.shields.io/badge/Status-Learning%20Project-yellow)

## 🎬 Live Demo

Run any chain from the terminal:

```bash
python sequential_chain.py
python parallel_chain.py
python conditional_chain.py
```

Each script sends a prompt to an LLM through OpenRouter, processes the response, and prints the final result in the terminal.

Example:

```bash
python sequential_chain.py
```

Example terminal output:

```text
1. Unemployment in India remains a major socio-economic challenge, particularly among youth and rural populations.
2. Key causes include slow job creation, skill mismatches, automation, and limited access to quality education.
3. The informal sector employs a large share of workers, often without job security or social benefits.
4. Government schemes such as MGNREGA and skill-development programs aim to improve employment opportunities.
5. Addressing unemployment requires coordinated investment in education, manufacturing, infrastructure, and entrepreneurship.
```

> Actual wording varies because LLM responses are non-deterministic.

## 📸 Screenshots / GIF

_Add a terminal screenshot or GIF here after running the project._

```markdown


```

Recommended screenshots:

- Sequential chain output
- Parallel chain ASCII graph
- Conditional chain sentiment-routing output

## 📑 Table of Contents

- [Project Goal](#-project-goal)
- [Problem Statement](#-problem-statement)
- [What It Does](#-what-it-does)
- [Features](#-features)
- [How It Works](#-how-it-works)
- [Architecture](#-architecture)
- [Tech Stack](#-tech-stack)
- [Prerequisites](#-prerequisites)
- [Quick Start](#-quick-start)
- [Environment Variables](#-environment-variables)
- [Usage](#-usage)
- [Project Structure](#-project-structure)
- [Example Output](#-example-output)
- [Testing](#-testing)
- [Current Status](#-current-status)
- [Milestones](#-milestones)
- [Roadmap](#-roadmap)
- [Known Limitations](#-known-limitations)
- [Data and Privacy](#-data-and-privacy)
- [Deployment](#-deployment)
- [Contributing](#-contributing)
- [License](#-license)
- [Connect](#-connect)
- [Acknowledgments](#-acknowledgments)
- [FAQ / Troubleshooting](#-faq--troubleshooting)

## 🎯 Project Goal

This project demonstrates how to build practical LLM applications using **LangChain chains** rather than isolated prompt calls.

It focuses on:

- Composing prompts, models, and parsers with the pipe operator (`|`)
- Running multiple LLM tasks simultaneously
- Routing workflows based on model-generated classifications
- Understanding modern LangChain Runnable components

## ❗ Problem Statement

A single LLM prompt is often insufficient for real applications.

For example, a useful study assistant may need to:

1. Read a long text
2. Generate notes
3. Generate quiz questions
4. Merge both outputs into one document

Similarly, a customer-feedback system may need to:

1. Classify feedback as positive or negative
2. Send it to a different response workflow
3. Return an appropriate final response

This project solves those patterns using composable LangChain chains.

## ✨ What It Does

The repository contains three core chain patterns:

| Script | Pattern | Purpose |
|---|---|---|
| `sequential_chain.py` | Sequential chain | Generate a detailed report, then summarize it into five points |
| `parallel_chain.py` | Parallel chain | Generate notes and quiz questions simultaneously, then merge them |
| `conditional_chain.py` | Conditional chain | Classify feedback sentiment and route it to the correct response chain |

## 🚀 Features

- **LangChain Expression Language (LCEL)** using `prompt | model | parser`
- **Sequential execution** for multi-step generation
- **Parallel execution** using `RunnableParallel`
- **Conditional routing** using `RunnableBranch`
- **Structured output** using Pydantic and `PydanticOutputParser`
- **OpenRouter integration** for accessing multiple model providers through one API
- **Environment-based configuration** using `python-dotenv`
- **Chain visualization** using `chain.get_graph().print_ascii()`
- **Secure API-key handling** through a local `.env` file

## ⚙️ How It Works

LangChain chains are built from Runnable components.

A basic chain follows this flow:

```text
Prompt Template → Chat Model → Output Parser → Final Result
```

In Python:

```python
chain = prompt | model | parser
result = chain.invoke({"topic": "Unemployment in India"})
```

### 1. Sequential Chain

The first model generates a detailed report. Its text output becomes the input for the second prompt, which creates a five-point summary.

```text
Topic → Report prompt → LLM → Report text → Summary prompt → LLM → Summary
```

### 2. Parallel Chain

Two independent chains run at the same time:

```text
                 ┌── Notes prompt → LLM → Notes ──┐
Input text ──────┤                                ├── Merge prompt → LLM → Final document
                 └── Quiz prompt  → LLM → Quiz ───┘
```

`RunnableParallel` sends the same input text to both branches and returns their outputs as a dictionary:

```python
{
    "notes": "...",
    "quiz": "..."
}
```

### 3. Conditional Chain

The first chain classifies feedback into `positive` or `negative` using a Pydantic schema. `RunnableBranch` then selects the appropriate response chain.

```text
Feedback → Sentiment classifier → Positive response chain
                                → Negative response chain
                                → Fallback response
```

`RunnableBranch` evaluates conditions in order and runs the first matching branch; if none match, it runs the default branch. [126]

## 🏗️ Architecture

```text
User Input
    ↓
Prompt Template
    ↓
LLM via OpenRouter
    ↓
Output Parser
    ↓
Optional Parallel / Conditional Routing
    ↓
Final Response
```

### Sequential architecture

```text
prompt1 → model → parser → prompt2 → model → parser
```

### Parallel architecture

```text
                    ┌─ prompt1 → model1 → parser ─┐
input text ────────►│                             │─► prompt3 → model1 → parser
                    └─ prompt2 → model2 → parser ─┘
```

### Conditional architecture

```text
feedback → classifier chain → sentiment
                                 ├─ positive → response chain
                                 ├─ negative → response chain
                                 └─ default → fallback message
```

## 🧰 Tech Stack

| Technology | Purpose |
|---|---|
| Python | Primary programming language |
| LangChain | LLM application framework |
| LangChain Core | Prompts, output parsers, and Runnables |
| LangChain OpenAI | OpenAI-compatible chat-model integration |
| OpenRouter | Unified access to multiple LLM providers |
| Pydantic | Structured output validation |
| python-dotenv | Loads API keys from `.env` |
| grandalf | ASCII graph visualization |

OpenRouter provides an OpenAI-compatible API, allowing OpenAI-style clients to access models from multiple providers through one base URL. [128][131]

## ✅ Prerequisites

Install:

- Python 3.10 or newer
- pip
- Git
- An OpenRouter account
- An OpenRouter API key

Check Python:

```bash
python3 --version
```

## ⚡ Quick Start

### 1. Clone the repository

```bash
git clone [https://github.com/](https://github.com/)<your-username>/langchain-chains.git
cd langchain-chains/chains
```

### 2. Create and activate a virtual environment

```bash
python3 -m venv .venv
source .venv/bin/activate
```

On Windows:

```bash
.venv\Scripts\activate
```

### 3. Install dependencies

```bash
pip install -U langchain langchain-openai langchain-anthropic python-dotenv grandalf
```

If you use OpenRouter for all models, `langchain-anthropic` is optional.

### 4. Create `.env`

```bash
touch .env
```

Add:

```env
OPENROUTER_API_KEY=your_openrouter_api_key
```

### 5. Run a chain

```bash
python sequential_chain.py
```

## 🔐 Environment Variables

Create a `.env` file in the same folder as the Python scripts.

```env
OPENROUTER_API_KEY=your_openrouter_api_key
```

Optional, only if calling Anthropic directly:

```env
ANTHROPIC_API_KEY=your_anthropic_api_key
```

Never commit `.env` to GitHub.

Add this to `.gitignore`:

```gitignore
.env
.venv/
__pycache__/
*.pyc
```

## 🖥️ Usage

### Run the sequential chain

```bash
python sequential_chain.py
```

Input:

```python
{"topic": "Unemployment in India"}
```

Output:

```text
1. Unemployment remains a major challenge in India, especially among youth.
2. Skill mismatches and limited quality education contribute to the problem.
3. The informal sector employs many workers without adequate protections.
4. Government programs aim to create jobs and improve employability.
5. Long-term solutions require investment in education, industry, and infrastructure.
```

### Run the parallel chain

```bash
python parallel_chain.py
```

Input:

```python
{"text": "Support vector machines are supervised learning methods..."}
```

The chain creates notes and quiz questions in parallel, then merges them.

Simplified output:

```text
Notes:
- SVMs are supervised learning methods for classification, regression, and outlier detection.
- They work well in high-dimensional spaces.
- Support vectors are the training points used in the decision function.

Quiz:
Q1. What tasks can SVMs perform?
A1. Classification, regression, and outlier detection.

Q2. What are support vectors?
A2. The subset of training points used in the SVM decision function.
```

### Run the conditional chain

```bash
python conditional_chain.py
```

Input:

```python
{"feedback": "This is a beautiful phone"}
```

The classifier detects:

```json
{
  "sentiment": "positive"
}
```

Final output:

```text
Thank you for your positive feedback! We're glad you love the phone.
```

For negative feedback:

```python
{"feedback": "The phone battery drains too quickly"}
```

The classifier detects:

```json
{
  "sentiment": "negative"
}
```

Final output:

```text
We're sorry to hear about the battery issue. Please contact our support team so we can help resolve it.
```

## 📁 Project Structure

```text
langchain-chains/
│
├── chains/
│   ├── .env                        # API keys (never commit)
│   ├── .venv/                      # Python virtual environment
│   ├── sequential_chain.py         # Report → summary chain
│   ├── parallel_chain.py           # Notes + quiz → merged document
│   ├── conditional_chain.py        # Sentiment → routed response
│   └── requirements.txt            # Optional dependency list
│
├── docs/
│   └── screenshots/                # Add project screenshots here
│
├── .gitignore
├── LICENSE
└── README.md
```

## 📄 Example Output

### Sequential chain graph

```text
+-------------+       +-------------+       +-------------+       +-------------+       +-------------+       +-------------+
| PromptTemplate | → | ChatOpenAI  | → | StrOutputParser | → | PromptTemplate | → | ChatOpenAI  | → | StrOutputParser |
+-------------+       +-------------+       +-------------+       +-------------+       +-------------+       +-------------+
```

### Parallel chain graph

```text
                         +----------------------+
                         |    RunnableParallel  |
                         +----------+-----------+
                                    |
                 +------------------+------------------+
                 |                                     |
        +--------+--------+                   +--------+--------+
        | Notes branch    |                   | Quiz branch     |
        | prompt → LLM    |                   | prompt → LLM    |
        +--------+--------+                   +--------+--------+
                 |                                     |
                 +------------------+------------------+
                                    |
                         +----------+-----------+
                         |    Merge chain       |
                         +----------------------+
```

### Conditional chain graph

```text
Feedback
   ↓
Sentiment classifier
   ↓
RunnableBranch
   ├── positive → positive response chain
   ├── negative → negative response chain
   └── default  → fallback response
```

## 🧪 Testing

Currently, this is a learning project without automated tests.

Manual test checklist:

```bash
# Check Python environment
python --version

# Verify OpenRouter key is loaded
python -c "import os; from dotenv import load_dotenv; load_dotenv(); print(bool(os.getenv('OPENROUTER_API_KEY')))"

# Verify OpenAI-compatible integration
python -c "from langchain_openai import ChatOpenAI; print('ChatOpenAI OK')"

# Run all chains
python sequential_chain.py
python parallel_chain.py
python conditional_chain.py
```

Future automated tests can use mocked LLM responses to avoid API costs.

## 📌 Current Status

- [x] Basic LangChain setup
- [x] Sequential chain
- [x] Parallel chain
- [x] Conditional chain
- [x] OpenRouter integration
- [x] Pydantic structured output
- [x] Chain graph visualization
- [ ] Automated unit tests
- [ ] Streamlit or FastAPI interface
- [ ] Docker support
- [ ] CI/CD workflow

## 🏁 Milestones

| Milestone | Status |
|---|---|
| Understand prompts, models, and parsers | ✅ Completed |
| Build sequential chains | ✅ Completed |
| Build parallel chains | ✅ Completed |
| Build conditional chains | ✅ Completed |
| Add structured output with Pydantic | ✅ Completed |
| Add a web interface | ⏳ Planned |
| Add persistent chat history | ⏳ Planned |
| Add RAG-based document Q&A | ⏳ Planned |

## 🗺️ Roadmap

- Add a Streamlit UI for selecting chain type and entering input
- Add streaming responses
- Add conversation memory
- Add RAG support using vector databases
- Add error handling and retry logic
- Add cost and token-usage tracking
- Add Docker Compose deployment
- Add GitHub Actions CI

## ⚠️ Known Limitations

- LLM outputs are non-deterministic and may vary between runs.
- The project currently depends on external LLM APIs, so it requires internet access.
- API usage may incur costs depending on your OpenRouter plan and selected models.
- Model names can change; verify the exact model ID in your OpenRouter dashboard.
- The project does not yet include retries, rate-limit handling, or comprehensive logging.
- `print_ascii()` requires the optional `grandalf` package.

## 🔒 Data and Privacy

- API keys are stored locally in `.env` and should never be committed to version control.
- Prompt inputs are sent to the selected LLM provider through OpenRouter.
- Do not submit passwords, personal identification data, confidential business data, or private customer data.
- Review OpenRouter and model-provider privacy policies before using sensitive data.

## 🚢 Deployment

This project is currently designed for local execution.

For a simple cloud deployment:

1. Move API keys to a secure secret manager.
2. Add a web interface using Streamlit or FastAPI.
3. Create a `requirements.txt`.
4. Add a `Dockerfile`.
5. Deploy to Render, Railway, Hugging Face Spaces, or a cloud VM.

Example `requirements.txt`:

```text
langchain
langchain-openai
langchain-core
python-dotenv
grandalf
```

## 🤝 Contributing

Contributions are welcome.

1. Fork the repository
2. Create a feature branch:

```bash
git checkout -b feature/new-chain
```

3. Commit changes:

```bash
git commit -m "Add new chain example"
```

4. Push the branch:

```bash
git push origin feature/new-chain
```

5. Open a pull request

## 📜 License

This project is licensed under the MIT License.

```text
MIT License

Copyright (c) 2026 Vaibhav Yadav

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
```

## 🔗 Connect

Created by **Vaibhav Yadav**

- GitHub: [your-github-username](https://github.com/your-github-username)
- LinkedIn: [your-linkedin-username](https://www.linkedin.com/in/your-linkedin-username)
- Email: your-email@example.com

## 🙏 Acknowledgments

- [LangChain Documentation](https://docs.langchain.com/)
- [LangChain Python Reference](https://reference.langchain.com/python/)
- [OpenRouter Documentation](https://openrouter.ai/docs)
- [CampusX LangChain Chains repository](https://github.com/campusx-official/langchain-chains)
- Pydantic and python-dotenv communities

## ❓ FAQ / Troubleshooting

### 1. `zsh: command not found: python`

On macOS, use:

```bash
python3 script.py
```

Or create an alias:

```bash
echo 'alias python=python3' >> ~/.zshrc
source ~/.zshrc
```

### 2. `ModuleNotFoundError: No module named 'langchain_openai'`

Install the integration package:

```bash
pip install -U langchain-openai
```

### 3. `ModuleNotFoundError: No module named 'dotenv'`

The import is `dotenv`, but the package is `python-dotenv`:

```bash
pip install python-dotenv
```

### 4. `Missing credentials` or `Incorrect API key provided`

Check that `.env` exists in the same folder as your script and contains:

```env
OPENROUTER_API_KEY=your_real_key
```

Ensure `load_dotenv()` is called before creating the model:

```python
from dotenv import load_dotenv
load_dotenv()
```

### 5. `401 Incorrect API key provided: sk-or-v1-...`

A key beginning with `sk-or-v1-` is an **OpenRouter** key. Do not use it with direct OpenAI access.

Use:

```python
model = ChatOpenAI(
    model="openai/gpt-4o-mini",
    api_key=os.getenv("OPENROUTER_API_KEY"),
    base_url="[https://openrouter.ai/api/v1](https://openrouter.ai/api/v1)",
)
```

### 6. `Could not resolve authentication method` with ChatAnthropic

`ChatAnthropic()` requires an Anthropic API key:

```env
ANTHROPIC_API_KEY=sk-ant-...
```

If you only have an OpenRouter key, use `ChatOpenAI` with OpenRouter’s base URL instead.

### 7. `ImportError: Install grandalf to draw graphs`

Install:

```bash
pip install grandalf
```

Or remove this line:

```python
chain.get_graph().print_ascii()
```

### 8. `ModuleNotFoundError: No module named 'langchain.schema'`

Use the current import path:

```python
from langchain_core.runnables import RunnableParallel, RunnableBranch, RunnableLambda
```

### 9. How do I change the model?

Change the `model` value:

```python
model = ChatOpenAI(
    model="openai/gpt-4o-mini",
    api_key=os.getenv("OPENROUTER_API_KEY"),
    base_url="[https://openrouter.ai/api/v1](https://openrouter.ai/api/v1)",
)
```

For Claude through OpenRouter:

```python
model = ChatOpenAI(
    model="anthropic/claude-3.7-sonnet",
    api_key=os.getenv("OPENROUTER_API_KEY"),
    base_url="[https://openrouter.ai/api/v1](https://openrouter.ai/api/v1)",
)
```

### 10. Why is my API key exposed?

If you accidentally paste an API key into chat, a screenshot, or GitHub:

1. Revoke it immediately in the provider dashboard.
2. Create a new key.
3. Update `.env`.
4. Ensure `.env` is in `.gitignore`.# LangChain_Chains_playground
