# Skill: Daily-PubMed-Scout

## 1. Description
This is an advanced medical literature monitoring workflow designed for Hermes. It first retrieves the latest papers on a specified topic by calling the `pubmed-search` skill. It then scores the impact of these papers based on a preset "top journal whitelist," generates a structured briefing with concise abstract summaries, and finally prompts the user to select papers for Zotero import.

## 2. Input Parameters
- `topic`: (Required) The topic or keyword to search for, e.g., "Immunotherapy for NSCLC".
- `days_range`: (Optional) The time range to trace back. Default is the past 2 days.
- `count`: (Optional) The number of papers to retrieve. Default is 20.

## 3. Dependencies
- **Skill:** `pubmed-search` (Must be installed)
- **Target:** Supports calling the Zotero API or generating BibTeX/RIS formats for import (may require a Zotero plugin).

## 4. Workflow Instructions

### Step 1: Retrieval
- Use the provided `topic` to retrieve the latest `count` papers via the `pubmed-search` skill.
- Force the sorting method to: **Latest (most recently published)**.

### Step 2: Scoring Logic
- **Journal Whitelist:**
  - **Tier 0 (Top-tier):** *Nature, Science, Cell, Nature Neusoscience, Cell Stem Cell, Nature Immunology, Immunity, Science Immunology.*
  - **Tier 1 (Field Top-tier):** *Nature Communication, Science Advances, Neuron, Developmental Cell.*
- **Scoring Rules:**
  - If `Journal` matches Tier 0, mark as `🔥 High Impact`.
  - If `Journal` matches Tier 1, mark as `🌟 Essential Reading`.
  - Otherwise, mark as `📄 General Update`.

### Step 3: Output Format
Generate the briefing strictly using the following Markdown format:

---
# 📅 Hermes Daily Research Dispatch - {topic}

### 🔥 Focus (High Impact)
*(List Tier 0 and Tier 1 papers here)*
- **[{Index}] {Title_Original}**
  - **Author:** *{Author Name, which group}*
  - **Journal:** *{Journal Name}*
  - **Core Takeaway:** {Deeply compress the Abstract, summarizing the core clinical conclusions or mechanistic discoveries in 1-2 concise sentences}

### 📄 Supplementary Reading (Other Updates)
*(List the remaining papers from the retrieved set here)*
- **[{Index}] {Title_Original}** ({Journal}) - *One-sentence summary*

---
### 🤖 Next Steps
**Please tell me which papers (e.g., reply with "1, 3, 5") you would like to add to your Zotero library?**

## 5. Error Handling
- If `pubmed-search` returns an empty result, inform the user: *"There are no newly published high-impact papers in the {topic} field today."*
- If a journal is not in the whitelist, default to displaying it in the "Supplementary Reading" section.
