# Blog-Rewriter-using-open-source-AI-models
This project is part of my journey to build an automated pipeline that scrapes blog articles from external websites, rewrites them using an LLM (Large Language Model), and prepares them for republishing on my own blog. The end goal is to have a one-click system that handles everything—from fetching content to rewriting and posting it.

While I haven't fully achieved that level of automation yet, I've built a solid foundation and learned a lot through trial and error. This repo documents the process, tools, and challenges I encountered.

---

## 🚀 Project Goals

- Scrape blogs from a target website (I wont disclose what website I scraped)
- Rewrite the scraped content using an open-source LLM
- Prepare content for publishing (clean, coherent, and SEO-friendly)
- Move toward a fully automated “scrape ➝ rewrite ➝ post” system

---

## 🛠️ Tech Stack

| Layer               | Tool/Library                             | Purpose                                 |
|--------------------|-------------------------------------------|-----------------------------------------|
| **Scraping**       | Selenium (with headless Chrome)          | To extract blog links and full content  |
| **Data Handling**  | CSV, TXT                                 | To store links, blog titles, and content|
| **LLMs**           | Gemma 2B, DeepSeek, Mistral, GPT-J, etc. | To experiment with content rewriting    |
| **Execution Env**  | Google Colab, Local Python               | For model inference and preprocessing   |

---

## 🔄 Workflow

1. **Scrape blog links**  
   I used Selenium to navigate blog listing pages and extract individual blog post links. Links were written to a `links.txt` file.

2. **Extract blog content**  
   For each link, I used another Selenium script to grab the **title** and **main content** of the blog. The script handled dynamic loading and fallback selectors using both CSS and XPath.

3. **Save to CSV**  
   I stored all collected data (title, content, URL) in a CSV file for batch processing.

4. **Rewrite the content**  
   This part went through multiple iterations (see next section). I eventually processed each blog paragraph-by-paragraph using the **Gemma 2B** model, which gave the most consistent results.

5. **Clean and prepare**  
   After model rewriting, I had to **manually edit** the output CSV to remove overly verbose or chatty model generations (e.g., long intros, overly apologetic language, unnecessary metaphors).

---

## 🧪 Model Experiments

### Models I Tried

- **GPT-J**
- **GPT-NeoX**
- **DeepSeek**
- **Mistral 7B**
- **text-generation-webui (locally hosted)**
- **LM Studio**
- **Gemma 2B** ✅ *(final choice)*

### Why Gemma?

Most open-source models were either:
- **Too verbose** (added fluff or polite padding),
- **Too limited** (couldn’t handle longer paragraphs or lost structure),
- **Resource-heavy** (required more RAM or VRAM than I had access to on Colab or local machine).

Gemma struck the right balance:
- Lightweight enough to run reliably.
- Output was clean, direct, and editable.
- Good at preserving structure and semantics.

> ✅ I used **chunked rewriting**—processing the blog one paragraph at a time to avoid input length errors and retain local coherence (meaning within each section).

---

## ⚠️ Challenges Faced

### 1. **Token and context limitations**  
Open-source models like GPT-J or Mistral often couldn't handle full blogs at once. I had to split each article into chunks (paragraphs or sections) and reassemble them post-rewrite.

### 2. **Model verbosity**  
Many models defaulted to overly verbose language, padding output with unnecessary commentary. This made it hard to maintain tone and keep output professional. I had to go back and clean the rewritten CSV manually to remove extra fluff.

### 3. **Colab limitations**  
I used Google Colab for GPU access, but:
- Sessions expired often.
- Resource caps limited the models I could run.
- It wasn’t suitable for end-to-end automation.

### 4. **Formatting and structure**  
Some models reordered paragraphs or removed list formatting. I had to create logic to preserve original structure (like headers, bullet points, etc.) during rewriting.

---

## 🔧 Sample Output Flow

```mermaid
graph TD
  A[Scrape Blog Listing] --> B[Write Links to links.txt]
  B --> C[Extract Blog Title + Content]
  C --> D[Save as blog_data.csv]
  D --> E[Chunk Each Blog into Paragraphs]
  E --> F[Rewrite using Gemma 2B]
  F --> G[Clean Output Manually]
  G --> H[Post-Processing / Ready for Publish]
