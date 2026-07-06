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

**Scenario A:** A dataset of city temperatures where some values are recorded in Celsius and others in Fahrenheit, with no label indicating which is which. A bar chart made from this data will show wildly inconsistent values. Phoenix in July might look colder than Anchorage in January. The chart looks fine, but the insight is wrong.

**Scenario B:** The same dataset, cleaned. All values converted to one unit, missing entries removed, outliers flagged. Now the chart tells the truth.

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
- https://data.cityofpittsburgh.pa.gov — Pittsburgh open data (great for local projects)
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

> Not sure what to pick? We recommend starting with the **Pittsburgh 311 Service Requests** dataset from https://data.wprdc.org/dataset/311-data. It contains civic complaints by neighborhood, type, and date. It is large enough to be interesting and realistic. All examples in this lab use this dataset.

>Note: The Pittsburgh 311 dataset is an archive that covers requests up to February 2025 when the City transitioned to a new system. It is still a great dataset for this lab because the historical data is rich and well documented.

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

Download your dataset as a CSV file. Place it inside the `src/data/` folder of your project.

For the Pittsburgh 311 dataset:
1. Go to https://data.wprdc.org/dataset/311-data
2. Search for "311 Service Requests"
3. Click Export → CSV
4. Save the file as `requests.csv` and place it in `src/data/`

---

### Step 7: Load your data and take inventory

Open `src/index.md` in VS Code. This is your main notebook file. Load your dataset:

```javascript
const raw = await FileAttachment("data/requests.csv").csv({ typed: true });
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

> Note: The column names in your dataset will be different from the examples below. Replace column names like `request_type`, `neighborhood`, and `created_on` with the actual column names from your dataset.

---

### Step 8: Identify data quality problems

Real data almost always has at least some of these issues. Work through each one for your dataset:

**Missing values:**

```javascript
// Replace "request_type" with your actual column name
const missingCount = raw.filter(d => d.request_type == null || d.request_type === "").length;
console.log("Missing values:", missingCount);
```

**Duplicates:**

```javascript
// Replace "request_id" with your unique identifier column
const ids = raw.map(d => d.request_id);
const uniqueIds = new Set(ids);
const duplicates = ids.length - uniqueIds.size;
console.log("Duplicate rows:", duplicates);
```

**Outliers:**

```javascript
import { min, max, mean } from "npm:d3-array";

// Replace "response_time" with your numeric column
const values = raw.map(d => d.response_time).filter(v => v != null);
console.log("Min:", min(values));
console.log("Max:", max(values));
console.log("Mean:", mean(values));
```

An outlier is a value that is statistically unusual. It might be real (an actual extreme event) or it might be an error (a sensor malfunction, a typo). You need to decide which.

**Inconsistent formatting:**

```javascript
// Replace "neighborhood" with your categorical column
const categories = [...new Set(raw.map(d => d.neighborhood))];
console.log(categories);
```

You might see `"Mt. Lebanon"`, `"Mt Lebanon"`, and `"MT LEBANON"` all meaning the same place. That breaks grouping and counts.

**Wrong data types:**

```javascript
// Check if a column that should be numeric is coming in as a string
console.log(typeof raw[0].response_time); // should be "number", not "string"
```

This happens when a column has a stray comma, dollar sign, or percent sign that prevents automatic type detection.

---

### Step 9: Clean your data

Now fix the problems you found. Replace the column names below with your actual column names. Document every decision you make, this is part of your write-up.

**Remove rows with missing values in critical columns:**

```javascript
const cleaned = raw.filter(d =>
  d.request_type != null &&
  d.request_type !== "" &&
  d.neighborhood != null
);
```

**Remove duplicates:**

```javascript
const seen = new Set();
const deduped = raw.filter(d => {
  if (seen.has(d.request_id)) return false;
  seen.add(d.request_id);
  return true;
});
```

**Standardize categorical values:**

```javascript
const cleaned = raw.map(d => ({
  ...d,
  neighborhood: d.neighborhood.trim().toLowerCase()
}));
```

**Convert types:**

```javascript
const cleaned = raw.map(d => ({
  ...d,
  created_on: new Date(d.created_on)
}));
```

**Handle outliers:**

Do not automatically delete outliers. First decide: is this value plausible? If a response time column shows -999, that is a sentinel value used as a placeholder when data was missing. Remove it. If it shows an unusually high but possible value, keep it and note it.

```javascript
// Remove sentinel values — adjust the range to fit your data
const cleaned = raw.filter(d => d.response_time > 0 && d.response_time < 10000);
```

After cleaning, compare row counts:

```javascript
console.log("Raw rows:", raw.length);
console.log("Cleaned rows:", cleaned.length);
console.log("Rows removed:", raw.length - cleaned.length);
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

**Example using Pittsburgh 311 data:**
- Viz 1: "Which neighborhoods generate the most service requests?" → Horizontal bar chart, sorted by count
- Viz 2: "How do request volumes change throughout the year?" → Line chart with month on the x axis

---

## Part 5: Building Your Visualizations

---

### Step 13: Build Visualization 1

Use Observable Plot to build your first chart. Here are the patterns you will use most:

**Bar chart:**

```javascript
import * as Plot from "npm:@observablehq/plot";

Plot.plot({
  marks: [
    Plot.barY(cleaned, {
      x: "neighborhood",
      y: "request_count",
      fill: "steelblue",
      sort: { x: "-y" }
    })
  ],
  x: { label: "Neighborhood", tickRotate: -45 },
  y: { label: "Number of Requests" },
  title: "311 Service Requests by Neighborhood",
  marginBottom: 80
})
```

**Line chart (time series):**

```javascript
Plot.plot({
  marks: [
    Plot.lineY(cleaned, {
      x: "created_on",
      y: "request_count",
      stroke: "steelblue",
      strokeWidth: 2
    }),
    Plot.dot(cleaned, {
      x: "created_on",
      y: "request_count",
      fill: "steelblue",
      r: 3
    })
  ],
  x: { label: "Date", type: "time" },
  y: { label: "Number of Requests" },
  title: "311 Service Requests Over Time"
})
```

**Scatter plot:**

```javascript
Plot.plot({
  marks: [
    Plot.dot(cleaned, {
      x: "response_time",
      y: "request_count",
      fill: "neighborhood",
      r: 5,
      tip: true
    })
  ],
  x: { label: "Average Response Time (days)" },
  y: { label: "Number of Requests" },
  color: { legend: true },
  title: "Response Time vs. Request Volume by Neighborhood"
})
```

After building, ask yourself: does this chart clearly answer the question I defined in Step 12? If not, adjust the chart type, axes, or filters before moving on.

---

### Step 14: Build Visualization 2

Build your second chart using the same process. It should ask a different question than Visualization 1.

If your first chart was a comparison (bar chart), consider making your second one show change over time (line chart) or geographic distribution (map). Variety helps demonstrate that you understand when to use different chart types.

**Choropleth map (if your data has geographic information):**

```javascript
import * as topojson from "npm:topojson-client";

const us = await fetch("https://cdn.jsdelivr.net/npm/us-atlas@3/states-10m.json")
  .then(r => r.json());

const states = topojson.feature(us, us.objects.states);

Plot.plot({
  projection: "albers-usa",
  marks: [
    Plot.geo(states, {
      fill: d => {
        const match = cleaned.find(row => row.state === d.properties.name);
        return match ? match.value : null;
      },
      stroke: "white",
      strokeWidth: 0.5,
      tip: true
    })
  ],
  color: {
    scheme: "YlOrRd",
    legend: true,
    label: "Value"
  },
  title: "Your Title Here"
})
```

---

### Step 15: Add written context to your notebook

A chart without context is incomplete. Underneath each visualization, add a Markdown cell that includes:

1. **The question** this chart answers
2. **The key insight** — what does the chart actually show? Write one sentence that a non-expert could understand.
3. **A caveat** — what limitation should the reader know?

**Example:**

> **What this shows:** Service request volume is highest in neighborhoods with the densest residential population, with Squirrel Hill North and South Side Flats consistently in the top five.
>
> **Caveat:** This reflects requests submitted, not actual need. Neighborhoods with lower digital access may be underrepresented.

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