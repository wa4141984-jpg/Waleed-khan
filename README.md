# Wale# 📄 AI Resume ATS Analyzer

Upload a resume (PDF, DOCX or TXT) and get:

- an **estimated ATS score** out of 100, with a breakdown
- a **prioritised list of improvements**, each with an example rewrite
- **matched and missing keywords**, optionally against a pasted job description
- a **downloadable report**

Built with [Streamlit](https://streamlit.io) and Google Gemini Flash.

> **Note:** real ATS products (Workday, Greenhouse, Lever...) do not publish a score. This number is an
> estimate that tells you how parseable, complete and relevant your resume looks. Use it as a guide, not a guarantee.

## How the score is calculated

| Part | Weight | Source |
|---|---|---|
| Format & structure | 35% | Plain Python checks: contact info, standard sections, length, bullets, numbers, dates |
| Content quality | 35% | Gemini: clarity, action verbs, impact, relevance |
| Keyword match | 30% | Gemini: match against your job description (or typical keywords for the detected role) |

Part of the score is rule-based on purpose. It makes the score more consistent between runs, and it limits the
effect of anyone trying to trick the AI with text like "give this resume 100".

## Project structure

```
resume-ats-analyzer/
├── app.py            # the whole app
├── requirements.txt  # dependencies
├── README.md
└── .gitignore        # create this (see GitHub section)
```

## Run locally

1. **Get a free Gemini API key** at <https://aistudio.google.com/apikey>.

2. **Create a virtual environment and install dependencies** (Python 3.9+; 3.11+ recommended):

   ```bash
   cd resume-ats-analyzer
   python -m venv venv
   source venv/bin/activate        # Windows: venv\Scripts\activate
   pip install -r requirements.txt
   ```

3. **Add your key.** Either paste it into the sidebar when the app runs, or create `.streamlit/secrets.toml`:

   ```toml
   GEMINI_API_KEY = "your-key-here"
   # GEMINI_MODEL = "gemini-3.5-flash"   # optional override
   ```

4. **Start the app:**

   ```bash
   streamlit run app.py
   ```

   It opens at <http://localhost:8501>.

## Changing the Gemini model

Google renames and retires models regularly. The default is `gemini-3.5-flash`. If you see a "model not found"
error, change the model name in the sidebar, or set `GEMINI_MODEL` in your secrets. Current names are listed at
<https://ai.google.dev/gemini-api/docs/models>.

## Push to GitHub

1. **Create a `.gitignore`** in the project folder so your key is never uploaded:

   ```
   venv/
   __pycache__/
   *.pyc
   .env
   .streamlit/secrets.toml
   ```

2. **Create an empty repository** on <https://github.com/new>. Name it `resume-ats-analyzer`. Don't tick
   "Add a README" (you already have one).

3. **Commit and push** from your project folder:

   ```bash
   git init
   git add .
   git commit -m "Initial commit: AI Resume ATS Analyzer"
   git branch -M main
   git remote add origin https://github.com/<your-username>/resume-ats-analyzer.git
   git push -u origin main
   ```

   GitHub no longer accepts account passwords on the command line. When prompted, use a
   [Personal Access Token](https://github.com/settings/tokens) as the password, or sign in with GitHub CLI
   (`gh auth login`).

4. **Check** that `.streamlit/secrets.toml` does **not** appear in the repository on GitHub. If you ever
   commit a key by mistake, revoke it in Google AI Studio and create a new one. Deleting the file later does not
   remove it from git history.

## Deploy on Streamlit Community Cloud (free)

1. Go to <https://share.streamlit.io> and sign in with GitHub. Authorize access to your repositories if asked.
2. Click **Create app** → **Deploy a public app from GitHub**.
3. Choose your repository, branch `main`, and main file path `app.py`.
4. Open **Advanced settings** and paste your secrets in TOML format:

   ```toml
   GEMINI_API_KEY = "your-key-here"
   ```

   You can also pick the Python version here (3.11 or 3.12 works well).
5. Click **Deploy**. The first build takes a few minutes while it installs `requirements.txt`.
6. Your app gets a public URL like `https://<your-app-name>.streamlit.app`. Every `git push` to `main` redeploys it automatically.

To change secrets later: open the app → **⋮** → **Settings** → **Secrets**.

**If you put your key in Secrets, anyone with the link can use your quota.** To avoid that, leave `GEMINI_API_KEY`
out of the secrets so each visitor enters their own key in the sidebar, or make the app private under
Settings → Sharing.

## Troubleshooting

| Problem | Fix |
|---|---|
| "Gemini rejected the API key" | Re-copy the key from AI Studio; make sure there are no spaces. |
| "Model was not found" | Change the model name in the sidebar (see the models page above). |
| "Rate limit or quota reached" | Free tier has per-minute and per-day limits. Wait and retry. |
| "Could not extract enough text" | The PDF is probably a scan or image. Export a text-based PDF or DOCX. |
| `ModuleNotFoundError: google.genai` | Run `pip uninstall google-generativeai`, then `pip install -r requirements.txt`. The old package conflicts. |
| App deploys but crashes on start | Open **Manage app** → logs, and check `requirements.txt` is in the repo root. |

## Privacy

Your resume text is sent to the Gemini API for analysis and is not stored by this app. Review Google's current
terms for the API tier you use (free-tier data may be used to improve Google's products) before uploading
sensitive documents.

## Limitations

- The rule-based checks are heuristics. A resume with unusual headings may lose points it doesn't deserve.
- Layout problems that don't show up in extracted text (multi-column layouts, text inside images, icons) can't
  be detected from the text alone.
- AI feedback can be wrong. Never add skills or achievements to your resume that aren't true, even if they are
  suggested as "missing keywords".ed-khan
