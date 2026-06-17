# AI-Powered E-Commerce Analytics Project — End-to-End Notes

A beginner-friendly walkthrough of everything we built, what each piece does, and why it matters. Use this to refresh your memory before interviews or when explaining the project to others.

---

## The Big Picture

The goal was to simulate a real company's data workflow: raw sales data comes in → gets cleaned automatically → gets stored properly → gets analyzed → gets turned into a dashboard → gets summarized into business insights → and the whole pipeline refreshes itself automatically.

```
Raw CSVs → Python cleaning → SQLite database → SQL analysis → Power BI dashboard → Automated insights → GitHub Actions automation
```

Each phase below explains what we did, the tools involved, and why it matters for your portfolio story.

---

## Phase 1-2: Python Data Cleaning

**What we did:** Wrote a Python script (`02_data_cleaning.py`) that loaded 6 raw CSV files (orders, customers, order items, payments, products, category translations), checked for missing values, removed duplicate rows, fixed date formats, and filled in missing product categories with "unknown."

**Tools:** Python, the `pandas` library (the standard tool for working with tabular data in Python).

**Why it matters:** Real-world data is messy — it has missing values, duplicates, and inconsistent formats. Before you can analyze anything, you have to clean it. This step shows you understand data quality, not just analysis.

**Key concept — "data cleaning":** This just means checking your data for problems (missing info, duplicate rows, wrong formats) and fixing them before doing anything else with it.

---

## Phase 3: SQLite Database

**What we did:** Wrote `03_create_database.py`, which took the cleaned CSV files and loaded them into a single database file called `olist.db`, with each CSV becoming its own table.

**Tools:** SQLite (a lightweight, file-based database — no server needed, just a single `.db` file) and Python's built-in `sqlite3` library.

**Why it matters:** Storing data in a proper database (rather than loose CSV files) lets you write SQL queries against it, which is exactly what real companies do. It also makes the data easy to connect to Power BI.

**Key concept — "database":** Think of it as an organized filing cabinet for your data, where each "table" is like a labeled drawer (e.g., a drawer for orders, a drawer for customers), and you can ask questions across drawers using SQL.

---

## Phase 3: SQL Analysis

**What we did:** Wrote SQL queries (saved in `sql/analysis_queries.sql`) to answer business questions — total revenue, average order value, monthly trends, top customers, repeat customer rate, top product categories, payment method breakdown, and more.

**Tools:** SQL, run inside DB Browser for SQLite (a free app for writing and testing SQL queries against a `.db` file).

**Why it matters:** SQL is one of the most commonly tested skills in Data/BI Analyst interviews. This phase gives you real, working examples of joins, aggregations (`SUM`, `COUNT`), window functions (`ROW_NUMBER`), and grouping logic — all things interviewers ask about directly.

---

## Phase 4: Power BI Dashboard

**What we did:** Built a 3-page interactive dashboard:
- **Page 1 (Executive Overview):** KPI cards (revenue, orders, AOV) and a monthly revenue trend chart
- **Page 2 (Customer Insights):** Top customers, repeat vs. one-time customer breakdown, and customer distribution by state
- **Page 3 (Product Insights):** Top categories, payment type breakdown, and installment analysis

**Tools:** Power BI Desktop, connected directly to `olist.db` via Get Data → SQLite database.

**Why it matters:** This is the visual, business-facing output of all the SQL and Python work — the part stakeholders actually look at. It shows you can turn raw analysis into something decision-makers can use.

---

## Phase 5: AI & Automation Layer (the confusing part — explained simply)

**The goal:** Instead of manually typing out the insight text under each dashboard page (like "Repeat customer rate is low..."), build a script that automatically generates that text based on the live data.

**What we tried first — AI APIs (Gemini, then Claude):**
An "API" is just a way for your Python code to send a question to an AI model (like Gemini or Claude) over the internet and get a written response back. The idea was: pull the numbers from the database, send them to the AI with a prompt like "write 3 business insights based on these numbers," and print whatever comes back.

**Why it didn't work out:** Both Google's Gemini and Anthropic's Claude required either enabling billing or purchasing credits before the API would actually respond — there wasn't a truly free option available for our account at the time. Rather than spend money on a portfolio script, we pivoted.

**What we actually built instead — a rule-based script:**
`ai/insights_generator.py` connects to the database, pulls the same key numbers (revenue, repeat rate, top category, etc.), and then uses simple `if/else` logic to pick which sentence to print. For example: *"if repeat_rate is less than 5%, say the business has a critical retention problem."* No AI API involved at all — just conditional logic written by hand.

**Why this is actually a strong story for interviews:** You can explain that you explored using a real AI API, hit a real-world constraint (billing), and engineered a practical alternative that still achieves automated, data-driven insight generation — without depending on a paid third-party service. That's exactly the kind of problem-solving employers want to hear about.

**Key concept — ".env file":** A small text file that stores secret values (like API keys) outside your actual code, so you never accidentally share your password/key when you show your code to someone else. It should NEVER be uploaded to GitHub (we'll cover that below).

---

## Phase 6: Automation (CI/CD Pipeline) — explained simply

**The goal:** Make the data-cleaning step run automatically on a schedule, without you having to open VS Code and run it manually every time.

**What "CI/CD" means:** It stands for "Continuous Integration / Continuous Deployment." In plain English: it just means your code automatically runs and updates itself in the cloud, instead of only running when a human manually triggers it on their own computer.

**What we built:** A file called `.github/workflows/data_pipeline.yml`. This is a configuration file that tells GitHub: "every day at midnight (or whenever I click a button), spin up a temporary computer, install Python, run my cleaning script, rebuild the database, and save the results back to my GitHub repository."

**Breaking down what's actually inside that file:**
- `on: schedule` — tells GitHub when to run automatically (we used daily)
- `on: workflow_dispatch` — adds a manual "Run workflow" button you can click anytime
- `actions/checkout` — downloads a copy of your repository onto GitHub's temporary computer
- `actions/setup-python` — installs Python on that temporary computer
- `pip install pandas` — installs the one library our script needs
- The next two steps actually run `02_data_cleaning.py` and `03_create_database.py`
- The last step commits and pushes any changes back to your repository automatically

**Why it matters for your portfolio:** It shows you understand automation and basic DevOps concepts — most entry-level analysts can show "I built a dashboard," but very few can show "I built a pipeline that keeps itself updated automatically." It also gives you a visible green checkmark badge on your GitHub repo, which is an instant credibility signal.

---

## GitHub Setup (Git Basics — explained simply)

**What "Git" is:** A tool that tracks changes to your code over time, like a very detailed "undo history" for your whole project folder.

**What "GitHub" is:** A website that hosts your Git project online, so other people (like recruiters) can view it, and so tools like GitHub Actions can run against it.

**The basic commands we used, in plain English:**
- `git init` — "start tracking this folder with Git"
- `git add .` — "stage all my current files, ready to be saved"
- `git commit -m "message"` — "actually save this snapshot, with a note describing what changed"
- `git branch -M main` — "name my main line of work 'main'" (the standard convention)
- `git remote add origin [url]` — "connect this local folder to my GitHub repository online"
- `git push` — "upload my saved snapshots to GitHub"
- `git pull` — "download any new snapshots from GitHub that I don't have locally yet"

**The big lesson learned — never commit your `.env` file:** We ran into this multiple times! If your `.gitignore` file doesn't properly list `.env`, Git will track and upload your secret API key to GitHub, which is a real security risk if your repository is public. GitHub even has a built-in "secret scanning" feature that blocks pushes if it detects something that looks like a real API key — which is exactly what saved us from accidentally exposing a key publicly.

---

## README.md — Why It Matters

**What it is:** A markdown file that automatically displays as the homepage of your GitHub repository.

**Why it matters:** It's usually the first (and sometimes only) thing a recruiter or hiring manager reads before deciding whether to look deeper into your code. A good README explains what the project does, what tools you used, what you found, and what skills it demonstrates — all in a way that doesn't require someone to dig through your code to understand your work.

---

## Quick Glossary (for whenever you forget a term)

- **API** — a way for one piece of software to "talk" to another (e.g., your Python script talking to an AI model)
- **API key** — a secret password-like code that proves you're allowed to use a particular API
- **.env file** — a file that stores secret values like API keys, kept separate from your actual code
- **.gitignore** — a file that tells Git which files to never track or upload (like `.env`)
- **Repository ("repo")** — a project folder tracked by Git/GitHub
- **Commit** — a saved snapshot of your code at a point in time
- **Push** — uploading your local commits to GitHub
- **Pull** — downloading new commits from GitHub to your computer
- **CI/CD** — automated processes that run and update your code without manual effort
- **SQLite** — a simple, file-based database (no server required)
- **DAX** — the formula language used inside Power BI to create custom calculations

---

## How to Explain This Project in an Interview (one paragraph)

*"I built an end-to-end data pipeline using the Olist e-commerce dataset. I used Python to clean and validate raw data, loaded it into a SQLite database, and wrote SQL queries to answer key business questions like revenue trends, customer retention, and top product categories. I built a 3-page Power BI dashboard to visualize these findings, then added an automated insights layer — I initially explored using AI APIs like Gemini and Claude to generate written business insights, but ran into billing constraints, so I built a rule-based Python alternative that achieves the same goal using conditional logic. Finally, I set up a GitHub Actions CI/CD pipeline so the data-cleaning process runs automatically on a schedule, and pushed the whole project to GitHub with full documentation."*

---

*Keep this file for your own reference — you don't need to memorize every line, just enough to explain the "why" behind each phase if asked.*
