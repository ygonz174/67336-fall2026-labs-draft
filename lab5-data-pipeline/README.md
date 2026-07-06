# Lab 5: Data Sourcing, Cleaning & Visualization
**67-336 Data Visualization | Fall 2026**

---

## Overview

This lab ties directly into the AI & Data Cleaning lecture. You will go through the full pipeline that real data analysts use every day: finding a dataset, evaluating its quality, cleaning it, and choosing the right visualization for what the data actually says.

By the end you will have a cleaned dataset and at least two visualizations deployed on Vercel. You will understand why you made the choices you did, not just how.

---

## Before You Start

If you need a refresher on Observable Framework, go back and review the Lab 2 instructions before continuing. If your Lab 2 setup is no longer working, re-run `npm install` in that project folder before continuing here.

---

## Learning Objectives

By the end of this lab, you will be able to:
- Find and evaluate real world datasets from reliable sources
- Identify common data quality problems and fix them
- Make intentional decisions about which chart type fits your data
- Build visualizations in Observable from cleaned data
- Explain the difference between a visualization made from dirty data vs. clean data
- Deploy your finished notebook to Vercel

---

## Why Data Quality Matters

Before writing a single line of code, here is the key idea for this lab.

**A visualization is only as good as the data behind it.**

Consider these two scenarios:

**Scenario A:** A dataset of restaurant inspections where the same restaurant appears under slightly different names ("Ali Baba", "ALI BABA", "Ali Baba Restaurant") and the same municipality is written three different ways. A chart grouping by restaurant name or location will split one business into three, making small restaurants look bigger and big ones look smaller. The chart looks fine, but the insight is wrong.

**Scenario B:** The same dataset, cleaned. Names standardized, duplicates removed, inconsistent categories fixed. Now the chart tells the truth.

This lab is about building the habits that get you from Scenario A to Scenario B!

---

## Part 1: Finding a Dataset

---

### Step 1: What makes a good dataset?

Not all data is created equal. Before you use a dataset, ask yourself these five questions:

**1. Where does it come from?**
Data from government agencies, research institutions, and established nonprofits is generally more trustworthy than data scraped from random websites or crowdsourced without oversight.

**2. When was it last updated?**
A dataset about housing prices from 2015 will mislead you today. Check the last updated date and make sure it fits your question.

**3. How was it collected?**
Survey data has sampling bias. Sensor data has hardware error. Administrative data has reporting inconsistencies. None of this makes data unusable, but you need to know what you are working with.

**4. Are there missing values?**
Every real dataset has gaps. The question is whether the gaps are random (usually okay) or systematic (a problem, e.g. a sensor that only records during business hours).

**5. Is the documentation clear?**
A dataset without a data dictionary, which is a guide explaining what each column means and what units are used, is a red flag. You should never have to guess what a column means.

---

### Step 2: Where to find datasets

Here are reliable sources to use for this lab and your future projects:

**Government and civic data:**
- https://data.gov — U.S. federal open data portal
- https://data.census.gov — U.S. Census Bureau
- https://data.wprdc.org — Western Pennsylvania Regional Data Center (great for local projects)
- https://opendata.cityofnewyork.us — NYC open data
- https://data.worldbank.org — World Bank global development data

**Environmental and climate:**
- https://www.noaa.gov/data — NOAA weather and climate data
- https://earthdata.nasa.gov — NASA Earth observation data
- https://openweathermap.org/api — Weather API (free tier available)

**Health and demographics:**
- https://wonder.cdc.gov — CDC public health data
- https://www.who.int/data — World Health Organization

**General purpose:**
- https://www.kaggle.com/datasets — Community shared datasets (check the source carefully)
- https://github.com/awesomedata/awesome-public-datasets — Curated list of public datasets
- https://datasetsearch.research.google.com — Google's dataset search engine

---

### Step 3: Choose your dataset

For this lab, choose one dataset from the sources above. It must meet all of the following criteria:

- [ ] At least 500 rows
- [ ] At least 5 columns
- [ ] Contains at least one numeric column and one categorical or date column
- [ ] Has clear documentation or column headers you can interpret
- [ ] Is available as a CSV, JSON, or via a public API

> Not sure what to pick? We recommend the **Allegheny County Restaurant and Food Facility Inspection** dataset from https://data.wprdc.org/dataset/allegheny-county-restaurant-food-facility-inspection-violations. It contains inspection records for restaurants and food facilities across Allegheny County including inspection dates, facility types, categories, locations, and permit status. It has real data quality issues to practice cleaning on and is locally relevant to Pittsburgh and CMU students. All examples in this lab use this dataset.

Write down your answers to the five quality questions from Step 1 before moving on. You will reference them in your write-up.

---

## Part 2: Setting Up Your Project

---

### Step 4: Create a new Observable Framework project

Create a new folder for this lab and initialize an Observable Framework project:

```bash
mkdir lab5-data-pipeline
cd lab5-data-pipeline
npm init @observablehq/framework@latest .
```

When prompted, accept the defaults. Then start the dev server:

```bash
npm run dev
```

Open the URL shown in your terminal (usually `http://localhost:3000`) in **Chrome**. You should see the default Observable Framework page.

> Tip: Keep this terminal window running the dev server. Open a second terminal window for any other commands like git.

---

### Step 5: Set up your GitHub repo

1. Go to github.com and sign in.
2. Click the **+** icon in the top right corner and choose "New repository".
3. Name it exactly:
```
67336_Lab5
```
4. Set visibility to **Private**.
5. Leave everything else unchecked.
6. Click "Create repository".

Then add your instructors as collaborators:

7. Go to your new repo → **Settings** → **Collaborators** → **Add people**
8. Add each of the following one at a time:
   - `shihongh`
   - `ygonz174`
   - `lillian-zhao`

Connect your local project to GitHub:

```bash
git init
git remote add origin https://github.com/YOUR-USERNAME/67336_Lab5.git
git branch -M main
git add .
git commit -m "Initial commit"
git push -u origin main
```

---

## Part 3: Loading and Evaluating Your Data

---

### Step 6: Add your dataset

Download your dataset as a CSV file and place it inside the `src/data/` folder of your project.

For the Allegheny County Restaurant Inspections dataset:
1. Go to https://data.wprdc.org/dataset/allegheny-county-restaurant-food-facility-inspection-violations
2. Click the **Download** button and select **CSV**
3. Save the file as `inspections.csv` and place it in `src/data/`

Your dataset has the following columns:

| Column | What it contains |
|---|---|
| `_id` | Unique row identifier |
| `inspection_id` | Inspection identifier |
| `placard_desc` | Inspection result (e.g. "Inspected & Permitted") |
| `facility_name` | Name of the restaurant or food facility |
| `bus_st_date` | Business start date |
| `facility_type` | Type of facility |
| `category` | Category code and description (e.g. "201-Restaurant with Liquor") |
| `nonprofit` | Whether the facility is a nonprofit (Yes or No) |
| `num` | Street number |
| `street` | Street name |
| `city` | City |
| `state` | State |
| `zip_code` | Zip code |
| `municipal` | Municipality name |
| `inspect_dt` | Date of inspection |
| `inspection_purpose` | Comprehensive, Reinspection, or Service Request |
| `reinspection_need` | Whether a reinspection is needed (Yes or No) |
| `permit_status` | Active, About to Expire, etc. |

---

### Step 7: Load your data and take inventory

Open `src/index.md` in VS Code. This is your main notebook file. Load your dataset:

```javascript
const raw = await FileAttachment("data/inspections.csv").csv({ typed: true });
```

> Note: The `{ typed: true }` flag tells Observable to automatically detect column types, numbers as numbers, dates as dates, rather than reading everything as strings. Always include this.

Take a first look at your data by adding these cells:

```javascript
// How many rows?
raw.length
```

```javascript
// What do the first few rows look like?
raw.slice(0, 5)
```

```javascript
// What columns do you have?
Object.keys(raw[0])
```

Write down what you see. How many rows? What are the column names? Do the types look right?

> Note: If you chose a different dataset, replace the column names in the examples below with your actual column names.

---

### Step 8: Identify data quality problems

Real data almost always has at least some of these issues. Work through each one for your dataset:

**Missing values:**

```javascript
// Check for missing facility names
const missingNames = raw.filter(d => d.facility_name == null || d.facility_name === "").length;
console.log("Missing facility names:", missingNames);

// Check for missing inspection dates
const missingDates = raw.filter(d => d.inspect_dt == null || d.inspect_dt === "").length;
console.log("Missing inspection dates:", missingDates);
```

**Duplicates:**

```javascript
// The same facility can appear multiple times — once per inspection.
// Check for duplicate inspection IDs, which would be a true duplicate.
const ids = raw.map(d => d.inspection_id);
const uniqueIds = new Set(ids);
const duplicates = ids.length - uniqueIds.size;
console.log("Duplicate inspection IDs:", duplicates);
```

**Inconsistent formatting:**

```javascript
// Check how municipality names are written
const municipals = [...new Set(raw.map(d => d.municipal))];
console.log(municipals);
```

You will likely see the same municipality written multiple ways, for example `"PITTSBURGH-101"`, `"PITTSBURGH-102"`, and `"CITY OF PITTSBURGH -WARD 4"` all referring to Pittsburgh. That breaks grouping and counts.

```javascript
// Check how categories are written
const categories = [...new Set(raw.map(d => d.category))];
console.log(categories);
```

Notice that categories combine a numeric code and a description, for example `"201-Restaurant with Liquor"`. You may want to split these or standardize them.

**Wrong data types:**

```javascript
// Check if inspection date came in as a string or a Date
console.log(typeof raw[0].inspect_dt);
```

**Outliers:**

```javascript
// Check business start dates — some go back to the early 1900s
import { min, max } from "npm:d3-array";

const dates = raw.map(d => new Date(d.bus_st_date)).filter(d => !isNaN(d));
console.log("Earliest business start:", min(dates));
console.log("Latest business start:", max(dates));
```

A business start date of 1931 is unusual but plausible for an old establishment. A date of 1900-01-01 for many records is likely a sentinel value used when the real date was unknown.

---

### Step 9: Clean your data

Now fix the problems you found. Document every decision you make — this is part of your write-up.

**Remove rows with missing values in critical columns:**

```javascript
const cleaned = raw.filter(d =>
  d.facility_name != null &&
  d.facility_name !== "" &&
  d.inspect_dt != null &&
  d.inspect_dt !== ""
);
```

**Remove duplicate inspections:**

```javascript
const seen = new Set();
const deduped = cleaned.filter(d => {
  if (seen.has(d.inspection_id)) return false;
  seen.add(d.inspection_id);
  return true;
});
```

**Standardize facility names:**

```javascript
const normalized = deduped.map(d => ({
  ...d,
  facility_name: d.facility_name.trim().toLowerCase()
}));
```

**Clean up the category column:**

```javascript
// Extract just the description part after the dash
const normalized = deduped.map(d => ({
  ...d,
  category_clean: d.category.includes("-")
    ? d.category.split("-").slice(1).join("-").trim()
    : d.category.trim()
}));
```

**Convert inspection date to a proper Date object:**

```javascript
const normalized = deduped.map(d => ({
  ...d,
  inspect_date: new Date(d.inspect_dt)
}));
```

**After cleaning, compare row counts:**

```javascript
console.log("Raw rows:", raw.length);
console.log("Cleaned rows:", normalized.length);
console.log("Rows removed:", raw.length - normalized.length);
```

---

### Step 10: Write your data quality summary

Before building any charts, add a Markdown cell to your notebook answering these four questions:

1. What dataset did you choose and where is it from?
2. What quality issues did you find?
3. What did you decide to do about each one, and why?
4. How many rows did you lose during cleaning? Is that acceptable?

This matters because a visualization without documentation of its data quality is incomplete. In professional settings, this summary is often the first thing a reviewer reads.

---

## Part 4: Choosing the Right Visualization

---

### Step 11: Match your question to a chart type

The most common mistake in data visualization is choosing a chart type first and then forcing the data into it. Do it the other way around: start with the question, then find the chart that answers it most clearly.

| If you want to show... | Consider... | Avoid... |
|---|---|---|
| How a value changes over time | Line chart, area chart | Pie chart, bar chart |
| How values compare across categories | Bar chart, dot plot | Line chart |
| How two numeric variables relate | Scatter plot | Bar chart |
| How data is distributed | Histogram, box plot | Line chart |
| Where things are located | Map (choropleth, dot map, heatmap) | Bar chart |
| Part to whole relationships | Stacked bar, treemap, pie (only with few categories) | Line chart |

**The question to ask yourself:** If a person who had never seen this data looked at my chart for ten seconds, would they understand the key insight?

If the answer is no, the chart type is probably wrong, or the data needs more cleaning.

---

### Step 12: Plan your two visualizations

You will build two visualizations from your cleaned dataset. They should answer two different questions about the same data.

Before writing any code, write down the following for each visualization:

**Visualization 1:**
- Question: What am I trying to show?
- Chart type: What type will I use and why?
- X axis: What goes here?
- Y axis: What goes here (if applicable)?
- Color/size: Will I encode a third variable?

**Visualization 2:**
- Same fields as above, for a different question

**Example using the Restaurant Inspections data:**
- Viz 1: "Which facility categories get reinspected most often?" → Bar chart sorted by reinspection count
- Viz 2: "How has the number of inspections changed over time?" → Line chart with inspection date on the x axis

---

## Part 5: Building Your Visualizations

---

### Step 13: Build Visualization 1

Use Observable Plot to build your first chart. Here are the patterns you will use most:

**Bar chart:**

```javascript
import * as Plot from "npm:@observablehq/plot";

// Count reinspections by category
const reinspections = d3.rollups(
  normalized.filter(d => d.reinspection_need === "Yes"),
  v => v.length,
  d => d.category_clean
).map(([category, count]) => ({ category, count }));

Plot.plot({
  marks: [
    Plot.barX(reinspections, {
      y: "category",
      x: "count",
      fill: "steelblue",
      sort: { y: "-x" }
    })
  ],
  y: { label: "Facility Category" },
  x: { label: "Number of Reinspections" },
  title: "Reinspections Needed by Facility Category",
  marginLeft: 200
})
```

**Line chart (time series):**

```javascript
// Count inspections per month
const byMonth = d3.rollups(
  normalized,
  v => v.length,
  d => d3.timeMonth(d.inspect_date)
).map(([date, count]) => ({ date, count }));

Plot.plot({
  marks: [
    Plot.lineY(byMonth, {
      x: "date",
      y: "count",
      stroke: "steelblue",
      strokeWidth: 2
    }),
    Plot.dot(byMonth, {
      x: "date",
      y: "count",
      fill: "steelblue",
      r: 3
    })
  ],
  x: { label: "Month", type: "time" },
  y: { label: "Number of Inspections" },
  title: "Inspections Over Time"
})
```

**Bar chart comparing permit status:**

```javascript
const byStatus = d3.rollups(
  normalized,
  v => v.length,
  d => d.permit_status
).map(([status, count]) => ({ status, count }));

Plot.plot({
  marks: [
    Plot.barY(byStatus, {
      x: "status",
      y: "count",
      fill: "steelblue",
      sort: { x: "-y" }
    })
  ],
  x: { label: "Permit Status" },
  y: { label: "Number of Facilities" },
  title: "Facilities by Permit Status"
})
```

After building, ask yourself: does this chart clearly answer the question I defined in Step 12? If not, adjust the chart type, axes, or filters before moving on.

---

### Step 14: Build Visualization 2

Build your second chart using the same process. It should ask a different question than Visualization 1.

If your first chart was a comparison (bar chart), consider making your second one show change over time (line chart) or distribution (histogram). Variety helps demonstrate that you understand when to use different chart types.

---

### Step 15: Add written context to your notebook

A chart without context is incomplete. Underneath each visualization, add a Markdown cell that includes:

1. **The question** this chart answers
2. **The key insight** — what does the chart actually show? Write one sentence that a non-expert could understand.
3. **A caveat** — what limitation should the reader know?

**Example:**

> **What this shows:** Social clubs and bars have the highest reinspection rates of any facility category, suggesting they receive more violations on initial inspection than restaurants.
>
> **Caveat:** This reflects the number of inspections recorded, not the severity of violations. A facility with many minor violations may appear more often than one with a single critical violation.

---

## Part 6: Reflection

---

### Step 16: Write your reflection

In your notebook, add a final Markdown section called "Reflection" and answer all four questions. Aim for 2 to 3 sentences each.

**1. What was the messiest part of your data?**
Describe the most significant quality issue you found and how you handled it.

**2. Did cleaning change your conclusions?**
Compare what the raw data seemed to show vs. what the cleaned data showed. Did any patterns disappear or appear after cleaning?

**3. What would you do differently with more time?**
What additional cleaning, filtering, or enrichment would improve your analysis?

**4. Why did you choose these two chart types?**
Explain specifically why each chart type fits its question better than the alternatives.

---

## Part 7: Deployment

---

### Step 17: Commit your work

Before deploying, make sure everything is committed:

```bash
git add .
git commit -m "Complete Lab 5 — data pipeline and visualizations"
git push origin main
```

---

### Step 18: Build your project

```bash
npm run build
```

This generates a `dist/` folder with your compiled notebook.

---

### Step 19: Deploy to Vercel

1. Go to https://vercel.com and sign in with your GitHub account.
2. Click **"Add New Project"**.
3. Select your `67336_Lab5` repository.
4. For **Framework Preset**, choose **Other**.
5. Set the following:
   - **Root Directory:** `./` (or leave blank)
   - **Build Command:** `npm run build`
   - **Output Directory:** `dist`
6. Click **Deploy**.

After deployment finishes, you will get a live URL like:

```
https://67336-lab5-yourname.vercel.app
```

Open it and confirm both visualizations render correctly in the browser.

> Tip: If charts look different in the browser than in your dev server, check for any hardcoded file paths. Observable Framework handles file attachments differently in production. Always use `FileAttachment()` rather than raw `fetch()` calls for local files.

---

## Submission

Submit the following on Canvas:

- Your **GitHub repo link** (e.g. `https://github.com/YOUR-USERNAME/67336_Lab5`)
- Your **Vercel live site link** (e.g. `https://67336-lab5-yourname.vercel.app`)

> WARNING: Make sure you have added `shihongh`, `ygonz174`, and `lillian-zhao` as collaborators before submitting.

---

## Submission Checklist

- [ ] Dataset meets all five criteria from Step 3
- [ ] Data quality summary answers all four questions from Step 10
- [ ] Cleaning code runs without errors and shows before and after row counts
- [ ] Visualization 1 is complete, labeled, and has written context
- [ ] Visualization 2 is complete, labeled, and has written context
- [ ] The two visualizations use different chart types and answer different questions
- [ ] Reflection section answers all four questions
- [ ] Project builds with `npm run build` without errors
- [ ] Live Vercel URL loads and both charts render correctly
- [ ] Both links submitted on Canvas

---

## Quick Reference: Data Cleaning Patterns

| Problem | Detection | Fix |
|---|---|---|
| Missing values | `.filter(d => d.col == null)` | Remove row or impute with mean/median |
| Duplicates | `new Set()` comparison | Filter with a `seen` Set |
| Wrong type | `typeof d.col` | `Number()`, `new Date()`, `.toString()` |
| Inconsistent strings | `[...new Set()]` | `.trim().toLowerCase()` |
| Sentinel values | Check `min` and `max` | Filter to plausible range |
| Mixed units | Domain knowledge | Convert to one unit, document it |

---

## Quick Reference: Chart Type Decision Guide

| Question type | Best chart |
|---|---|
| How does X change over time? | Line chart |
| How do categories compare? | Bar chart (sorted) |
| What is the relationship between X and Y? | Scatter plot |
| Where is this happening geographically? | Choropleth map or dot map |
| How is this value distributed? | Histogram or box plot |
| What fraction of the whole is each part? | Stacked bar or treemap |