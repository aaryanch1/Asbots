# AsBots — AI Senior Medical Assistant

A Streamlit chat app powered by the Groq API. It behaves as a senior,
evidence-based medical assistant: it only answers medical questions with
established, medically-proven information, in a professional clinical tone,
and stays strictly on-topic.

## Files
- `asbots.py` — the Streamlit app
- `.env` — holds your `GROQ_API_KEY` (already filled in with the key you gave me)
- `.streamlit/config.toml` — forces Streamlit's native dark theme (keep this
  folder alongside `asbots.py` — it's what makes dropdowns/sliders/inputs
  match the dark UI, not just the chat area)
- `requirements.txt`
-streamlit
-groq
— Python dependencies

## Setup

```bash
# 1. (Recommended) create a virtual environment
python -m venv venv
source venv/bin/activate      # Windows: venv\Scripts\activate
import streamlit as st
from groq import Groq
# 2. Install dependencies — do this INSIDE the venv you'll run the app from
pip install -r requirements.txt
# or directly:
pip install streamlit groq python-dotenv

# 3. Run the app
streamlit run asbots.py
```

The app will open in your browser at `http://localhost:8501`.

### Fix for "ModuleNotFoundError: No module named 'dotenv'"
This means `python-dotenv` isn't installed in the Python environment Streamlit
is actually running from (common when a venv is active in one terminal but
the app is launched from another, or the package was never installed).
Two things now guard against this:
1. `pip install python-dotenv` (or `pip install -r requirements.txt`) into
   the **same** venv/interpreter you use to run `streamlit run asbots.py`.
2. The script itself no longer hard-crashes without `dotenv` — it's now
   wrapped in a `try/except ImportError`, and your API key is also hardcoded
   as a fallback (`DEFAULT_GROQ_API_KEY` near the top of `asbots.py`), so the
   app works even if `.env`/`dotenv` aren't set up at all.

If you're on Windows and unsure which Python `streamlit` is using, run
`where streamlit` and `where python` in the same terminal you launch it from
to confirm they point to the same venv.

## Notes

- **API key**: now embedded directly in `asbots.py` as `DEFAULT_GROQ_API_KEY`
  (as requested) so the app runs with zero config. It can still be
  overridden with a `GROQ_API_KEY` environment variable or `.env` file if
  you ever want to swap keys without editing the script. Since this key has
  now been shared in chat and hardcoded in a file, consider rotating it in
  your Groq console (groq.com → API Keys) if you plan to share this project
  or push it anywhere public.
- **Models**: defaults to `llama-3.3-70b-versatile` on Groq (most capable
  currently offered); a faster `llama-3.1-8b-instant` option is in the
  sidebar dropdown. If Groq later renames/retires a model, just update
  `AVAILABLE_MODELS` in `asbots.py`.
- **Live internet search**: not included. Groq's API serves LLM inference
  only — it has no built-in web browsing. The bot answers from the model's
  trained medical knowledge, guided by a strict system prompt to stick to
  established, evidence-based information. If you want real-time web lookup
  added later (e.g. for latest drug recalls or outbreak data), that requires
  wiring in a separate search API (Tavily, SerpAPI, Bing, etc.) — happy to
  add that if you get a key for one of those.
- **Scope guardrails**: the system prompt instructs the model to decline
  non-medical questions, avoid filler/small talk, avoid giving individual
  diagnoses or specific prescriptions, and to flag emergency red-flag
  symptoms with a call-to-action to seek immediate care. This isn't a
  replacement for a licensed physician and shouldn't be presented to end
  users as one.

## UI features
- Clinical-themed header and layout, chat-bubble interface
- Model selector + response-focus (temperature) slider
- Streaming responses
- 📋 Copy button under every AI response
- 💾 Save full conversation as a `.txt` file
- 🗑️ Clear chat
- Persistent emergency-care banner and disclaimer footer
