# Blog Feed to QA Dataset Converter

This project converts blog feed content into a structured Question-Answer dataset using LLaMA 3.2 (via Ollama) for local processing.

The generated dataset follows a conversational format and can be automatically pushed to Hugging Face.

## Features

- Converts HTML blog content to clean markdown text
- Generates contextual Q&A pairs using LLaMA 3.2
- Maintains conversation flow in generated Q&A pairs
- Automatically handles duplicate/similar questions
- Pushes formatted dataset to Hugging Face Hub
- Processes content locally for data privacy

## Prerequisites

- Python 3.11
- Poetry (for python dependencies)
- Ollama (to run Llama 3.2)
- Hugging Face account (for dataset upload)

## Getting Started

### 1. Install dependencies

```bash
poetry install
poetry run python -m spacy download en_core_web_sm
```

### 2. Install Ollama and pull Llama 3.2

Follow instructions to install Ollama: https://ollama.com/

Select a model to run locally using https://ollama.com/search.

In this case, we want to run `llama3.2:latest` (https://ollama.com/library/llama3.2).

So we can do: 

```bash
ollama pull llama3.2:latest
```

<img src="https://raw.githubusercontent.com/DidierRLopes/turn-blog-feed-into-qa-dataset/main/assets/ollama_pull.png" width="600px" alt="ollama pull">

Then, we can check that the model has been downloaded with:

```bash
ollama list
```

<img src="https://raw.githubusercontent.com/DidierRLopes/turn-blog-feed-into-qa-dataset/main/assets/ollama_list.png" width="600px" alt="ollama list">

Finally, we can test that it works with:

```bash
ollama run llama3.2:latest
```

<img src="https://raw.githubusercontent.com/DidierRLopes/turn-blog-feed-into-qa-dataset/main/assets/ollama_run.png" width="600px" alt="ollama run">

### 3. Configure Hugging Face

- Create a write-enabled token at [Hugging Face](https://huggingface.co/docs/hub/en/security-tokens)

- Create a `.env` file:

```bash
HF_TOKEN=your_token_here
```

## Usage

1. Update the blog feed URL in the notebook. Here's the one we are utilizing:

```python
url = "https://didierlopes.com/blog/feed.json"
```

<details>
<summary>JSON Feed Structure</summary>

This is the feed structure being used - which is the default coming from [Docusaurus](https://docusaurus.io/docs/blog), which is the framework I'm using to auto-generate the feed from my personal blogs.

```json
{
    "version": "https://jsonfeed.org/version/1",
    "title": "Didier Lopes Blog",
    "home_page_url": "https://didierlopes.com/blog",
    "description": "Didier Lopes Blog",
    "items": [
        {
            "id": "URL of the post",
            "content_html": "HTML content of the post",
            "url": "URL of the post",
            "title": "Title of the post",
            "summary": "Brief summary of the post",
            "date_modified": "ISO 8601 date format",
            "tags": [
                "array",
                "of",
                "tags"
            ]
        },
        // ... more items
    ]
}
```

</details>

<br />

2. Set your Hugging Face dataset repository name:

```python
dataset_repo = "didierlopes/my-blog-qa-dataset"
```

This is what the dataset will look like in HuggingFace: https://huggingface.co/datasets/didierlopes/my-blog-qa-dataset/viewer

3. Run the notebook cells sequentially.

The notebook contains detailed explanations throughout to guide you through the process step-by-step.

## Dataset Format

The generated dataset includes:

- `title`: Blog post title
- `conversation`: Array of Q&A pairs in role-based format
- `context`: Original cleaned blog content
- `url`: Source blog post URL
- `date`: Publication date

Note: This is the format of the conversation field:

```
conversation = [
    {
        "role": "user",
        "content": "You mentioned that when ChatGPT launched, everyone rushed to build financial chatbots  What were some of the fundamental truths that those who built these chatbots missed?"
    },
    {
        "role": "assistant",
        "content": "Those building financial chatbots missed two fundamental truths:\n\n1. AI models are useless without access to your data.\n2. Access to data isn't enough - AI needs to handle complete workflows, not just conversations.\n\nThese limitations led to chatbots that can't access proprietary data, can't handle complex workflows and restrict analysts to an unnatural chat interface."
    },
    # ... more Q&A pairs following the same pattern
]
```

## How It Works

1. Fetches blog content from JSON feed
2. Cleans HTML to markdown format
3. Analyzes sentence count to determine Q&A pair quantity
4. Generates contextual questions using LLaMA 3.2
5. Creates corresponding answers
6. Filters and removes duplicate questions
7. Formats data for Hugging Face
8. Pushes to Hugging Face Hub
