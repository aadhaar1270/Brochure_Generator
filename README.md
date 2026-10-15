# Brochure Generator

A small LLM project that looks at a company's website and works out which pages matter for a brochure. It runs entirely on your own machine using [Ollama](https://ollama.com) and `llama3.2`, so there are no API keys and no usage costs.

## What it does

1. **Scrapes the landing page** and collects every link on it (`scraper.py`).
2. **Asks a local Llama model** which links are relevant for a brochure, such as About, Company and Careers pages. The model replies in JSON.
3. **Fetches the content** of each chosen page and combines it with the landing page into one text block, ready to be used as a prompt for writing the brochure.

The notebook is `task.ipynb`.

## How it works

The code uses the `openai` Python package, but points it at Ollama's OpenAI-compatible endpoint instead of OpenAI:

```python
openai = OpenAI(base_url="http://localhost:11434/v1", api_key="ollama")
```

Nothing is sent to OpenAI. The package is only used as an HTTP client.

Key functions in the notebook:

| Function | Purpose |
|---|---|
| `fetch_website_links(url)` | Returns all links on a page (from `scraper.py`) |
| `fetch_website_contents(url)` | Returns a page's title and text, trimmed to 2,000 characters |
| `select_relevant_links(url)` | Asks the model to pick brochure-relevant links, returned as JSON |
| `fetch_page_and_all_relevant_links(url)` | Builds the final combined text |

## Running it

1. Install Ollama from https://ollama.com and pull the model:
   ```bash
   ollama pull llama3.2
   ```
2. Install the Python dependencies:
   ```bash
   pip install -r requirements.txt
   ```
3. Make sure Ollama is running. http://localhost:11434 should say "Ollama is running". Otherwise run `ollama serve`.
4. Open `task.ipynb` and run the cells in order.

## Notes and limitations

- `llama3.2` is a small model. Its answers vary between runs, and it sometimes returns links that do not exist or are not useful, such as social media pages. The fetch step skips pages that cannot be loaded.
- The scraper reads raw HTML, so sites that build their content with JavaScript will return little text.
- Page text is cut to 2,000 characters to keep prompts small.

Built as part of Week 1 of an LLM engineering course.
