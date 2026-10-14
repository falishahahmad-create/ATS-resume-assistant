# ATS-resume-assistant
# 📄 ATS Resume Checker

Upload a resume (PDF, DOCX or TXT) and get:

- an **overall ATS score** (0-100) with a breakdown by category
- **missing keywords** (optionally matched against a pasted job description)
- **prioritised improvements**, each with the issue, the fix and an example line
- a suggested professional summary and a downloadable `.txt` report

Built with [Streamlit](https://streamlit.io), the Google Gemini API (Flash model), `pypdf` and `python-docx`.

## How the score works

| Category | Weight | Source |
|---|---|---|
| Keywords & relevance | 30% | Gemini |
| Content & impact | 25% | Gemini |
| ATS-friendly formatting | 20% | 50% Gemini + 50% automatic rule checks |
| Structure & sections | 15% | Gemini |
| Readability & language | 10% | Gemini |

The overall score is calculated in code from the category scores, so the weighting is consistent between runs.
The automatic checks cover email, phone, profile link, section headings, length, bullet points, measurable
results and dates. Scores are estimates: real ATS software differs, and layout details such as columns or
graphics can only be inferred from the extracted text.

## Run locally

1. Install Python 3.10 or newer.
2. Get a free Gemini API key at <https://aistudio.google.com/apikey>.
3. In a terminal, from this folder:

```bash
python -m venv .venv
source .venv/bin/activate        # Windows: .venv\Scripts\activate
pip install -r requirements.txt
streamlit run app.py
```

4. Paste your API key into the sidebar, or set it ahead of time:

```bash
export GEMINI_API_KEY="your-key"        # Windows PowerShell: $env:GEMINI_API_KEY="your-key"
```

### Changing the model

The default model is set by `DEFAULT_MODEL` in `app.py`. You can also change it in the sidebar or with the
`GEMINI_MODEL` environment variable. Google retires Flash versions from time to time, so if you see a
"model name was not accepted" message, check the current names at
<https://ai.google.dev/gemini-api/docs/models>.

## Push to GitHub (using the website, no command line)

1. Sign in at <https://github.com> and click **+** (top right) → **New repository**.
2. Name it (for example `ats-resume-checker`), choose **Public** (Streamlit's free hosting needs access to the
   repo), and click **Create repository**.
3. On the new repository page click **uploading an existing file**.
4. Drag in `app.py`, `requirements.txt` and `README.md` (the files must sit at the top level, not inside a folder).
5. Write a commit message such as `Initial commit` and click **Commit changes**.

**Never upload your API key.** Do not put it in any file in the repository.

## Deploy on Streamlit Community Cloud

1. Go to <https://share.streamlit.io> and sign in with your GitHub account (allow access to your repositories).
2. Click **Create app** → **Deploy a public app from GitHub**.
3. Choose your repository, the branch (`main`) and set **Main file path** to `app.py`.
4. Open **Advanced settings** and paste your key into **Secrets**:

```toml
GEMINI_API_KEY = "your-key-here"
```

5. Click **Deploy**. The first build takes a few minutes. You get a public `*.streamlit.app` URL.

To update the app later, edit a file on GitHub (pencil icon → **Commit changes**) and Streamlit redeploys automatically.

### A note on the shared key

If you store `GEMINI_API_KEY` in Secrets, **every visitor uses your key and your quota**. To make visitors bring
their own key instead, leave the secret out; the app then asks for a key in the sidebar.

## Troubleshooting

| Problem | Fix |
|---|---|
| "Very little text could be extracted" | The PDF is a scan or image. Export a text-based PDF or DOCX. |
| "Gemini rejected the API key" | Check the key and that the Gemini API is enabled for it. |
| "Rate limit or quota reached" | Wait a minute, or use a key with higher limits. |
| "Model name was not accepted" | Update the model name in the sidebar (see link above). |
| Build fails on Streamlit Cloud | Make sure `requirements.txt` is in the repository root. |

## Privacy

The resume text is sent to the Gemini API for analysis. The app does not store files or results on disk; results
live only in your browser session.
