<div align="center">

# Legal AI Assistant

**A Gradio legal assistant for Pakistani law** — ask questions about the Penal
Code and other frameworks, attach a case file as PDF or image, and export the
whole conversation as a PDF.

`Python` `Gradio` `Groq` `Llama 3 8B` `PyMuPDF` `Tesseract`

</div>

---

## What it does

A document-grounded legal chat assistant. The interesting part is the
**document ingestion path**: upload a case file as a PDF or an image and the
extracted text is injected into the prompt as grounding context, so the model
answers against the actual file rather than from memory.

- **Legal Q&A** — a system prompt scopes the model to Pakistani law, the
  Penal Code, recent amendments and landmark rulings
- **Document grounding** — PDF via PyMuPDF, images via Tesseract OCR
- **Multi-turn conversation** — full history maintained across messages
- **PDF export** — the conversation is rendered to a downloadable PDF via FPDF
- **Session control** — start a new conversation to clear history

---

## Setup

Prerequisites: **Python 3.10+**. Tesseract OCR is required for image uploads.

```bash
pip install groq gradio pymupdf pillow pytesseract fpdf
```

Set your key — get one at [console.groq.com/keys](https://console.groq.com/keys):

```bash
export GROQ_API_KEY="gsk_..."
```

Install Tesseract if you want image uploads (macOS: `brew install tesseract`,
Debian/Ubuntu: `sudo apt install tesseract-ocr`).

Then run — in Colab, paste and run the cell. As a plain script (after removing
the `!pip install` line):

```bash
python copy_of_legal_ai_assistant.py
```

Gradio launches with `share=True`, so it returns a public share link. **Be aware
that a share link makes the interface reachable by anyone with the URL** — don't
use one for anything sensitive.

### Environment variable

| Variable | Required | Description |
| --- | --- | --- |
| `GROQ_API_KEY` | **Yes** | Groq API key. The app raises a clear error at startup if unset |

---

## Implementation notes

- **Model** — `llama3-8b-8192` via Groq. The model id is a single constant at
  the top of the file if you want to swap it.
- **Conversation history** — a module-level list. Deliberately simple, and the
  reason a fresh process starts clean.
- **Text extraction** — PDF path uses PyMuPDF's native text layer, so scanned
  PDFs need OCR separately. Images go through Tesseract directly.
- **PDF export** — FPDF with auto page-break at 15mm margins.

---

## Limitations — read before using this

- **This is a demonstration, not a legal advice service.** The system prompt
  casts the model as a seasoned legal expert; that framing is a prompt
  convention, not a qualification, and it can produce confident-sounding wrong
  answers on real legal questions.
- **No retrieval, no citations.** Despite the framing, this does not ground
  answers in a real case database — it grounds on the file you upload plus the
  model's weights. A version of this that actually did RAG over Pakistani
  statute would be a significantly better and more honest tool.
- **The whole conversation is exported to PDF and, in share mode, is as public
  as the link.** Legal queries are sensitive.
- **No auth, no rate limiting, no persistence.**

---

## Repository layout

| Path | Contents |
| --- | --- |
| `copy_of_legal_ai_assistant.py` | The entire application — extraction, chat, PDF export, Gradio UI |
| `documents/` | Engineering specs: requirements, data-flow diagram, use cases, architecture |

The filename is a Colab export artifact. The file is a **Colab script**: it
still contains a `!pip install` magic on line 10, so it is not valid plain
Python. Either open it in Colab, or delete that line and run it as a normal
script after installing dependencies yourself.

## License

MIT — use commercially, no attribution required.
