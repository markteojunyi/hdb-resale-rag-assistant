# 🏠 HDB Resale RAG Assistant

A Streamlit web app that answers questions about buying an HDB flat or Executive Condo in Singapore, using HDB's own published guidance as its source, plus a filterable explorer of ~190,000 resale transactions since 2017.

> Built in October 2024 as a personal GenAI prototype. For learning purposes only, not financial or legal advice.

## The problem

HDB buying rules (eligibility, income ceilings, grants, loan options, procedures) are spread across dozens of pages on HDB's website. A first-time buyer asking a simple question like *"I'm single, can I buy a 3-room resale flat?"* has to piece the answer together themselves.

## What it does

**1. Ask HDB (RAG chatbot)**
Type a question in plain English and get an answer grounded in HDB's official pages, not the model's general knowledge.

**2. Resale price explorer**
Filter recent resale transactions by town, flat type, storey range and more, to sense-check what a flat is actually worth.

## How it works

```
HDB website ──► ScrapeHDB.py ──► scraped_data_buyingflat.json
                                          │
                                          ▼
                      split into chunks ──► OpenAI embeddings ──► Chroma vector DB
                                                                        │
User question ──► find the most relevant chunks ◄───────────────────────┘
                          │
                          ▼
              GPT-4o-mini answers using ONLY those chunks (custom prompt)
```

- **RAG** (Retrieval-Augmented Generation): instead of trusting the AI's memory, the app first *retrieves* the relevant HDB text, then asks the AI to answer from it. This cuts down on made-up answers.
- **Scrape once, reuse:** HDB pages are scraped ahead of time and saved to JSON, so the app starts fast. HDB policies change slowly, so a periodic refresh is enough.
- **Resale data:** a snapshot of the data.gov.sg resale dataset is bundled as CSV for the same reason.
- **Access control:** password-protected via Streamlit secrets; the OpenAI key is read from `st.secrets`, never stored in code.

## Tech stack

Python · Streamlit · LangChain · OpenAI (GPT-4o-mini + embeddings) · ChromaDB · BeautifulSoup · pandas

## Run it locally

```bash
pip install -r requirements.txt
# create .streamlit/secrets.toml with your OpenAI key and app password
streamlit run BuyHDBFlat.py
```

## What I learned

- Retrieval quality matters more than the model: chunk size and the prompt wording changed answer quality far more than switching models.
- A clear disclaimer and a "you must consult HDB" line belong in any GenAI tool that touches financial decisions.

## Credits

Thanks to my colleague **Janice** for her help. The retrieval functions in `RAGFunctions_Janice.py` (used by the live app) build on her contribution.
