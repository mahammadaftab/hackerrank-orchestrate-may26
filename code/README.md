# Terminal Support Triage Agent

This implementation is a terminal-first, corpus-grounded triage system for HackerRank, Claude, and Visa support tickets.

## Folder structure

```text
code/
  main.py                 # CLI entry point
  triage_agent.py         # Backward-compatible wrapper
  triage/
    __init__.py
    models.py             # Typed data models
    corpus.py             # Corpus loader + TF-IDF retrieval index
    engine.py             # Classification, escalation, response generation
```

## What this agent does

- Reads support articles only from `data/` (no external calls).
- Classifies each ticket into `request_type`.
- Retrieves the most relevant documentation by company + content similarity.
- Applies safety/escalation rules for sensitive or unsupported requests.
- Writes required outputs to `support_tickets/output.csv`:
  - `status`
  - `product_area`
  - `response`
  - `justification`
  - `request_type`

## Run

From repository root:

```bash
python code/main.py --input support_tickets/support_tickets.csv --output support_tickets/output.csv --data-dir data
```

Legacy command (still supported):

```bash
python code/triage_agent.py --input support_tickets/support_tickets.csv --output support_tickets/output.csv --data-dir data
```

## Browser UI (best UX)

Install dependencies:

```bash
pip install -r code/requirements.txt
```

Launch browser app:

```bash
streamlit run code/web_app.py
```

What you get:

- Single-ticket triage form with instant decision output
- Batch CSV upload (`Issue`, `Subject`, `Company`) and one-click `output.csv` download
- Clear status/request-type/product-area panels and concise justifications
- Local-only processing using your corpus in `data/`

## Design notes

- Deterministic: standard-library only, no random sampling.
- Grounded: responses are extracted from retrieved support documents and include source links when present.
- Safe defaults: escalates low-confidence, high-risk, permission-sensitive, or security-vulnerability cases.
