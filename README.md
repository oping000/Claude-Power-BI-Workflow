# Power BI + Claude AI + MCP Workflow
### AI-Assisted Power BI Model Building via Model Context Protocol (MCP)

> ⚠️ **Copyright Notice:** This repository is for viewing and reference only.  
> Copying, redistributing, or reusing any part of this workflow without written permission is prohibited. See [LICENSE](LICENSE) for details.

---

## 📌 What This Is

A complete, production-ready workflow for connecting **Claude AI** to **Power BI Desktop** via **MCP (Model Context Protocol)** — allowing Claude to read and build your entire Power BI data model automatically.

Once connected, Claude can:
- 🔗 Build all table relationships
- 📐 Create DAX measures (revenue, profit, KPIs, time intelligence)
- 🧮 Add calculated columns (age groups, income buckets, full names, date parts)
- 🧹 Clean up the model (hide LocalDateTables, organize display folders)
- 📁 Export the model as TMDL for version control on GitHub

**Data sources supported:** CSV files, PostgreSQL, Snowflake, SQL Server, Excel — the workflow is identical regardless of source.

---

## 🗂️ Repository Structure

```
powerbi-claude-workflow/
│
├── README.md                  ← This file
├── LICENSE                    ← All Rights Reserved
│
├── adventureworks/            ← Adventure Works CSV model (TMDL)
│   ├── database.tmdl
│   ├── relationships.tmdl
│   └── tables/
│       ├── Sales Data.tmdl
│       ├── Customer Lookup.tmdl
│       ├── Calendar Lookup.tmdl
│       └── ...
│
└── dvdrental/                 ← PostgreSQL DVD Rental model (TMDL)
    ├── database.tmdl
    ├── relationships.tmdl
    └── tables/
        ├── public payment.tmdl
        ├── public rental.tmdl
        └── ...
```

---

## ⚙️ Full Setup Process

### Phase 1 — One Time Setup (never repeat)

**Step 1 — Install VS Code**
- Download from [code.visualstudio.com](https://code.visualstudio.com)
- This is the editor that hosts the MCP extension

**Step 2 — Install the Power BI Modeling MCP Extension**
- Open VS Code → Extensions (`Ctrl+Shift+X`)
- Search **"Power BI Modeling MCP"**
- Install it
- This is the bridge that lets Claude talk to your open Power BI file

**Step 3 — Configure Claude Desktop JSON** ⚠️ *Required*

Open the config file:
- Claude Desktop → **Settings → Developer → Edit Config**
- Or navigate to `%APPDATA%\Claude\claude_desktop_config.json`

Add this block:
```json
{
  "mcpServers": {
    "powerbi-modeling-mcp": {
      "command": "C:\\Users\\YOUR_USERNAME\\.vscode\\extensions\\analysis-services.powerbi-modeling-mcp-0.4.0-win32-x64\\server\\powerbi-modeling-mcp.exe",
      "args": ["--start"]
    }
  }
}
```

> ⚠️ **Important Notes:**
> - Replace `YOUR_USERNAME` with your actual Windows username
> - The version number `0.4.0` may differ — check your actual folder at `C:\Users\YOUR_USERNAME\.vscode\extensions\`
> - Use **double backslashes** `\\` in the path
> - After saving, fully restart Claude Desktop
> - To verify: Settings → Developer → look for **powerbi-modeling-mcp** with a green **"running"** badge
> - The hammer icon may not appear — the Developer settings page is the reliable confirmation

**Step 4 — Install Git**
- Download from [git-scm.com](https://git-scm.com)
- Needed for pushing your model to GitHub

**Step 5 — Create a GitHub Account**
- [github.com](https://github.com) → Sign up
- Where your TMDL model files will live

---

### Phase 2 — Per Project Setup (once per new dataset)

**Step 6 — Get Your Data Ready**
- **CSV files** → organize in folders (e.g. `SalesData` subfolder for files to combine)
- **Database** → have your connection string ready (host, port, credentials)

**Step 7 — Open Power BI Desktop & Load Data**
- **CSV** → Get Data → Text/CSV or **Folder** (use Folder connector to combine multiple files)
- **Database** → Get Data → PostgreSQL / Snowflake / SQL Server etc.
- Clean data types in Power Query if needed
- Close & Apply

> 💡 **Tip for multiple CSV files of the same type** (e.g. Sales 2020, 2021, 2022):  
> Put them in a dedicated subfolder and use **Get Data → Folder → Combine & Transform**.  
> This stacks them into one table automatically — like a SQL UNION.

**Step 8 — Save the .pbix File**
- Give it a meaningful name e.g. `Adventure Works Claude PowerBI Connection`

**Step 9 — Tell Claude the File is Open**
- Start a new chat in Claude (inside your Project)
- Say **"file is open"**
- Claude connects automatically via MCP

---

### Phase 3 — Model Building (every session)

**Step 10 — Claude Reads the Model**
- Lists all tables, columns, existing relationships
- Identifies what's missing

**Step 11 — Claude Builds Relationships**
- Creates all missing table relationships
- Marks inactive ones where ambiguity exists
- Power BI enforces referential integrity — Claude handles conflicts automatically

**Step 12 — Claude Hides LocalDateTables**
- Cleans up the field pane
- LocalDateTables still function for time intelligence behind the scenes

**Step 13 — Claude Creates DAX Measures**

Example measures created automatically:

| Folder | Measure | DAX Pattern |
|--------|---------|-------------|
| Revenue | Total Revenue | `SUMX` with related price |
| Revenue | Total Profit | Revenue - Cost |
| Revenue | Profit Margin % | `DIVIDE` |
| Revenue | Revenue MoM % | `DATEADD` time intelligence |
| Orders | Total Orders | `COUNTROWS` |
| Customers | Revenue per Customer | `DIVIDE` |
| Returns | Return Rate % | `DIVIDE` |

**Step 14 — Claude Adds Calculated Columns**

Example columns created automatically:

| Table | Column | Logic |
|-------|--------|-------|
| Customer | Full Name | FirstName & " " & LastName |
| Customer | Age | `DATEDIFF` from BirthDate |
| Customer | Age Group | `SWITCH(TRUE())` buckets |
| Customer | Income Level | Low / Medium / High buckets |
| Sales | Order Year/Month/Day | `FORMAT` and `YEAR` |
| Product | Price Range | Budget / Mid-Range / Premium |
| Calendar | Year, Quarter, Month | Full date intelligence |

**Step 15 — Save the .pbix** (`Ctrl+S`)

---

### Phase 4 — Version Control (after each session)

**Step 16 — Claude Exports TMDL**
- Exports model as readable text files to a local folder
- Contains all tables, measures, relationships, calculated columns
- Does NOT contain actual data rows or credentials

**Step 17 — Push to GitHub via Git Bash**
```bash
git init
git add .
git commit -m "describe what changed"
git push
```

---

### Phase 5 — Reopening (every new session)

**Step 18 — Reopen the .pbix File**
- Power BI assigns a new random port each time — this is normal
- The MCP tool finds it automatically

**Step 19 — Tell Claude the File is Open**
- Claude runs `ListLocalInstances` → finds new port automatically
- Claude runs `Connect` → connected in seconds
- Continue working where you left off

---

## 🔑 Key Things to Remember

| Phase | Frequency |
|-------|-----------|
| Phase 1 (Install & Configure) | One time only |
| Phase 2 (Load Data & Save) | Once per new dataset |
| Phase 3 (Model Building) | Each session as needed |
| Phase 4 (TMDL Export & GitHub Push) | After every meaningful set of changes |
| Phase 5 (Reopen & Reconnect) | Every single session — just 2 steps |

> **Data source doesn't matter** — CSV, PostgreSQL, Snowflake, Excel — Phase 3 onwards is identical every time. Once data is loaded into Power BI, Claude only sees tables and columns regardless of where they came from.

---

## 💡 What Claude Can and Cannot Do

| ✅ Claude CAN | ❌ Claude CANNOT |
|---------------|-----------------|
| Build relationships | Import CSV/create new .pbix |
| Create DAX measures | Design report visuals |
| Add calculated columns | Drag fields onto canvas |
| Hide/show tables | Set colors or formatting |
| Export TMDL | Access data credentials |
| Read model structure | Modify Power Query steps |

---

## 🛠️ Tech Stack

- **Claude AI** (Claude Desktop or Claude.ai)
- **Power BI Desktop**
- **Power BI Modeling MCP** (Microsoft VS Code Extension)
- **Git + GitHub** (version control)
- **TMDL** (Tabular Model Definition Language)

---

## 📊 Models in This Repository

### Adventure Works (CSV Source)
- 10 tables loaded via CSV and Folder connector
- 3 years of Sales Data combined into one table via Folder connector
- 13 DAX measures across 4 display folders
- 15 calculated columns across 4 tables
- Full date intelligence via Calendar Lookup

### DVD Rental / PostgreSQL (Database Source)
- 15 tables from PostgreSQL Sakila database
- 15 DAX measures across 5 display folders
- 6 calculated columns
- All relationships auto-mapped from PostgreSQL schema

---

*Built with Claude AI + Power BI Desktop + MCP*
