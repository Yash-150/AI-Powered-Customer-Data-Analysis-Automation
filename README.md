# AI-Powered Customer Data Analysis & Automated Email Reporting

![n8n](https://img.shields.io/badge/n8n-Workflow%20Automation-EA4B71?logo=n8n&logoColor=white)
![OpenAI](https://img.shields.io/badge/OpenAI-GPT--5--mini-412991?logo=openai&logoColor=white)
![Google Sheets](https://img.shields.io/badge/Google%20Sheets-Data%20Source-34A853?logo=googlesheets&logoColor=white)
![Gmail](https://img.shields.io/badge/Gmail-Email%20Reports-EA4335?logo=gmail&logoColor=white)

A beginner-friendly, no-code project that uses an AI Agent in **n8n** to read customer records from **Google Sheets**, count the **unique customers in each city**, and send the findings by **Gmail**.

**Main outcome:** from one chat message, the workflow reported **29 unique customers across 24 cities** in the sample dataset and emailed the result as a table.

![Workflow overview](screenshots/01-workflow-overview.png)

---

## Table of Contents

1. [Introduction](#introduction)
2. [Project Overview](#project-overview)
3. [Project Highlights](#project-highlights)
4. [Tools and Technologies](#tools-and-technologies)
5. [How the Project Works](#how-the-project-works)
6. [Workflow Architecture](#workflow-architecture)
7. [Dataset Information](#dataset-information)
8. [Analysis Methodology](#analysis-methodology)
9. [Project Results](#project-results)
10. [Practical Screenshots](#practical-screenshots)
11. [How to Set Up the Project](#how-to-set-up-the-project)
12. [Sample User Request](#sample-user-request)
13. [Skills Demonstrated](#skills-demonstrated)
14. [Learning Outcomes](#learning-outcomes)
15. [Limitations and Future Improvements](#limitations-and-future-improvements)
16. [Project Documentation](#project-documentation)
17. [About the Creator](#about-the-creator)

---

## Introduction

Customer data is often stored as order records, so the same customer can appear in many rows. Counting how many *different* customers are in each city by hand means scanning the sheet, removing repeats, tallying the numbers, writing a summary and emailing it.

I built this project to practise reducing those repeated steps. A user asks for the analysis in a chat window. An AI Agent reads the data from Google Sheets, removes repeated customers, counts unique customers per city and sends the result by email. The AI model does the reading and summarizing, and n8n connects the model to the spreadsheet and to Gmail.

This is a learning and portfolio project, not a production system.

## Project Overview

1. The user types a request in the n8n chat interface.
2. The AI Agent reads all customer rows from a Google Sheet.
3. It identifies unique customers and groups them by city.
4. It writes an HTML summary with a city-wise table.
5. It sends that summary through Gmail and confirms in the chat.

## Project Highlights

- AI-powered customer data analysis
- Google Sheets integration
- Unique customer identification
- City-wise customer counting
- Automated email reporting through Gmail
- No-code workflow automation in n8n
- Conversational interaction through a chat interface

## Tools and Technologies

| Technology | Purpose |
|---|---|
| n8n | Workflow automation |
| OpenAI GPT-5-mini | AI-powered analysis and task handling |
| Google Sheets | Customer data source |
| Gmail | Automated email reporting |
| Excel | Sample dataset |
| Simple Memory | Recent conversation context (last 10 messages) |

## How the Project Works

**Flow:** Chat Message Trigger → AI Agent → Google Sheets Data Retrieval → Customer Analysis → Gmail Email Report

| Step | Component | What happens |
|---|---|---|
| 1 | **When chat message received** | Starts the workflow when the user sends a message in the chat window. |
| 2 | **AI Agent** | Receives the message and follows its written instructions (the system prompt). |
| 3 | **Google Sheets tool** | The agent calls it to read all customer rows from Sheet1. |
| 4 | **Customer analysis** | The agent removes repeated customers and counts unique customers per city. |
| 5 | **Gmail tool (`send_email`)** | The agent sends the HTML table and summary. The recipient and subject are set in the node. |
| 6 | **Chat reply** | The agent confirms in the chat that the analysis was done and emailed. |

Two components are attached to the AI Agent and support it throughout:

- **OpenAI Chat Model (GPT-5-mini)** is the "brain" the agent uses to understand the request and do the analysis.
- **Simple Memory** keeps the last 10 messages so the agent has recent conversation context.

## Workflow Architecture

![Workflow architecture diagram](documentation/workflow-architecture-diagram.png)

The actual n8n canvas:

![n8n workflow canvas](screenshots/04-successful-execution.png)

## Dataset Information

The sample dataset is in [`dataset/sales_data_sample.xlsx`](dataset/sales_data_sample.xlsx).

| Item | Detail |
|---|---|
| Records | 43 sample records (33 distinct order numbers) |
| Columns | 25 |
| Customer fields | `CUSTOMERNAME` (plus contact and phone columns, masked in this copy) |
| Location fields | `CITY`, `STATE`, `POSTALCODE`, `COUNTRY`, `TERRITORY` (plus address columns, masked in this copy) |
| Order fields | `ORDERNUMBER`, `ORDERLINENUMBER`, `ORDERDATE`, `STATUS`, `QUANTITYORDERED`, `PRICEEACH`, `SALES`, `DEALSIZE` |
| Product fields | `PRODUCTLINE`, `PRODUCTCODE`, `MSRP` |
| Coverage | 10 countries, 24 cities, orders from January 2003 to May 2005 |

**Why repeated customers matter:** this is *order-level* data, so the same customer can appear in several records. For example, two customers each appear in three rows. Counting rows would overstate the number of customers, so the analysis counts each customer once.

**Privacy note:** in this public copy, the contact name, phone and address-line columns are replaced with `REDACTED`. The columns are kept so the file still has 25 columns, and the customer and city values used in the analysis are unchanged. The counts are identical to the original file.

## Analysis Methodology

1. **Retrieve** the records from Google Sheets.
2. **Check** which customer identification fields are available.
3. **Remove duplicate** customer entries, for counting purposes only.
4. **Group** the unique customers by city.
5. **Calculate** the customer total for each city.
6. **Prepare** the summary and email it.

> **Important: which field identifies a customer?**
> The exported workflow tells the agent to use **Customer ID**, or **Email / Name** if Customer ID is missing. The supplied dataset has **no Customer ID and no Email column**; it has `CUSTOMERNAME`. In the demonstrated run, the agent therefore used **`CUSTOMERNAME`** to remove duplicates, and the email states this. This works for the sample data, but it depends on each customer name being spelled the same way every time.

## Project Results

| Metric | Result |
|---|---|
| Total Unique Customers | 29 |
| Distinct Cities | 24 |
| NYC | 3 |
| Madrid | 2 |
| Paris | 2 |
| San Francisco | 2 |

These figures come from the demonstrated workflow execution and the uploaded sample dataset. I re-checked them by counting distinct `CUSTOMERNAME` values per `CITY` in the Excel file, and they match. The other 20 cities have one unique customer each, and the city counts add up to 29.

## Practical Screenshots

### 1. Workflow Overview

![Workflow overview](screenshots/01-workflow-overview.png)

The overall n8n workflow: the chat trigger, the AI Agent, and the connected OpenAI model, memory, Google Sheets tool and Gmail tool. The chat panel shows the request and the execution log.

### 2. Initial Chat Test

![Initial chat test](screenshots/02-initial-chat-test.png)

The first test, a simple "hi". The agent replied and the run succeeded, which confirmed the chat trigger, model and memory worked before any tools were added.

### 3. AI Agent Analysis

![AI agent analysis](screenshots/03-ai-agent-analysis.png)

The data retrieval test. The OpenAI Chat Model output lists unique customers grouped by city, and the chat summary shows 24 distinct cities and 29 distinct customers.

### 4. Successful Execution

![Successful execution](screenshots/04-successful-execution.png)

The full workflow run for the request "analyze unique customers in every city and email me the analysis". Every node shows a green tick, and the execution log shows the Google Sheets tool followed by the Gmail `send_email` tool. The chat reply confirms the analysis was emailed.

### 5. Email Report

![Email report](screenshots/05-email-report.png)

The email received in Gmail with the subject "analysis findings". It contains a summary, the total unique customers (29), total cities (24) and a city-wise table.

> Screenshots were captured during testing. Personal identifiers such as the email address, browser address bar and spreadsheet link are covered with grey boxes.

## How to Set Up the Project

You need accounts for n8n, OpenAI, Google and Gmail.

1. **Create or install n8n.** Sign up for n8n Cloud, or [self-host n8n](https://docs.n8n.io/hosting/).
2. **Import the workflow.** In n8n choose *Create workflow → Import from file* and select [`workflow/n8n_workflow.json`](workflow/n8n_workflow.json). The file was exported from n8n Cloud; if n8n shows a version warning, update to a recent n8n version.
3. **Configure OpenAI credentials.** Open the *OpenAI Chat Model* node and add your own OpenAI API key.
4. **Connect a Google account.** Open the *Google Sheets* node and sign in with your own Google account.
5. **Connect a Gmail account.** Open the *send_email* node and sign in with your own Gmail account.
6. **Upload the sample spreadsheet.** Upload [`dataset/sales_data_sample.xlsx`](dataset/sales_data_sample.xlsx) to your own Google Drive and open it as a Google Sheet.
7. **Update the Google Sheets node.** Replace the `YOUR_GOOGLE_SHEET_URL` placeholder with the link to your sheet and select *Sheet1*.
8. **Review the AI Agent instructions.** Open the *AI Agent* node and read the system message. See [`documentation/system-prompt.md`](documentation/system-prompt.md) for an explanation. Because the sample data has no Customer ID or Email, the agent will fall back to customer name.
9. **Set the recipient and subject.** In the *send_email* node, replace `YOUR_EMAIL@example.com` with your own address. You can also change the subject.
10. **Test the workflow.** Click *Open chat* in n8n and send the sample request below.

The workflow file contains no credentials. You must supply your own accounts, and never share your API keys or sheet links publicly.

## Sample User Request

> Can you analyze unique customers in every city and send me this analysis over email?

**Expected output**

- The agent reads the sheet and removes repeated customers.
- You receive an email with the subject you configured. It includes a short summary and a table of unique customers per city.
- For the sample dataset it should report **29 unique customers across 24 cities**, with NYC highest at 3.
- The chat confirms the email was sent.

Because the analysis is written by an AI model, wording and layout can differ slightly between runs. Always check the numbers against the data.

## Skills Demonstrated

- Data Analysis
- Customer Deduplication
- Data Understanding
- Generative AI
- Prompt Writing
- No-Code Automation
- Google Sheets Integration
- Email Automation
- Workflow Testing
- Technical Documentation

## Learning Outcomes

- Learned how n8n nodes connect: the AI Agent sits in the centre, and the model, memory and tools attach to it.
- Practised writing clear system instructions and saw how they change what an AI Agent does.
- Connected Google Sheets and Gmail to a workflow.
- Understood the difference between order-level data and customer-level data, and why counting rows gives the wrong number of customers.
- Learned that the best identifier (Customer ID) was not in my data, and worked with `CUSTOMERNAME` while understanding its limits.
- Tested step by step (chat, then sheet, then email) and used the execution log to see what each part did.
- Saw how AI can support a routine data-analysis task, while still needing its output checked against the source data.

## Limitations and Future Improvements

### Current limitations

- The workflow is built around one customer-analysis task.
- Deduplication depends on the identifying fields available. With names only, spelling differences could cause errors.
- The analysis depends on the connected spreadsheet.
- The email recipient and subject are fixed in the Gmail node.
- It was tested on a small sample (43 records), not a large dataset.
- The result is AI-generated, so it can vary and should be verified.

### Future ideas (not implemented)

- More advanced customer analysis
- Sales and revenue reporting
- Data cleaning and validation
- Automated PDF reporting
- Data visualization
- Multiple reporting options
- Better error handling

## Project Documentation

| Document | Link |
|---|---|
| Detailed project report (PDF) | [documentation/project-report.pdf](documentation/project-report.pdf) |
| Detailed project report (editable DOCX) | [documentation/project-report.docx](documentation/project-report.docx) |
| n8n workflow (sanitized) | [workflow/n8n_workflow.json](workflow/n8n_workflow.json) |
| Sample dataset | [dataset/sales_data_sample.xlsx](dataset/sales_data_sample.xlsx) |
| System prompt | [documentation/system-prompt.md](documentation/system-prompt.md) |

## About the Creator

**Yash Verma**
BCA Graduate | Data Analytics | Business Intelligence | AI & No-Code Automation

Interested in solving practical business problems using data analytics, AI tools and automation.

- **LinkedIn:** _add your LinkedIn profile URL here_
- **GitHub:** _add your GitHub profile URL here_

## License

The original materials in this repository are released under the [MIT License](LICENSE). The sample dataset is not covered by this license and remains subject to the terms of its original source.
