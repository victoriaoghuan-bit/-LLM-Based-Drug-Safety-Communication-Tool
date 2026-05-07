# 💊 LLM-Based Drug Safety Communication Tool
> MSc Applied AI & Data Science — Applied NLP Project

![Python](https://img.shields.io/badge/Python-3.10+-3776AB?style=flat&logo=python&logoColor=white)
![Claude API](https://img.shields.io/badge/LLM-Claude%20API-D4A017?style=flat)
![Streamlit](https://img.shields.io/badge/UI-Streamlit-FF4B4B?style=flat&logo=streamlit&logoColor=white)
![NLP](https://img.shields.io/badge/Domain-Pharmacovigilance%20NLP-7B68EE?style=flat)
![Status](https://img.shields.io/badge/Status-MVP%20Complete-success?style=flat)

---

## 📌 Overview

Drug-drug interactions (DDIs) affect millions of patients annually — elderly patients in North America consume between 8 and 13 different drugs each year, with each patient reporting 2–3 drug-related problems. Pharmacological interaction data exists in abundance across clinical databases, but it is written for specialist audiences: dense with technical terminology, mechanism-level detail, and clinical jargon that is inaccessible to the patients and non-specialist healthcare professionals who need it most.

This project builds an **LLM-powered Drug Safety Communication Tool** that converts structured pharmacological interaction data into clear, accessible plain-language summaries using the **Anthropic Claude API**, served through an interactive **Streamlit** front-end. The MVP was validated through structured user testing and iterative prompt engineering, with findings documented in a technical report for non-technical stakeholders.

---

## 🖥️ Application Demo

```
┌───────────────────────────────────────────────────────────┐
│  💊 Drug Safety Communication Tool                        │
│                                                           │
│  Drug A: [ Warfarin          ▼ ]                         │
│  Drug B: [ Aspirin           ▼ ]                         │
│                                                           │
│  Audience: ● Patient  ○ Pharmacist  ○ GP                 │
│                                                           │
│  [ Generate Safety Summary ]                             │
│                                                           │
│  ┌─────────────────────────────────────────────────────┐ │
│  │ ⚠️  Important interaction detected                   │ │
│  │                                                      │ │
│  │ Taking Warfarin and Aspirin together increases       │ │
│  │ your risk of bleeding. Both medicines affect how     │ │
│  │ your blood clots, and using them together makes      │ │
│  │ this effect stronger. You should speak to your       │ │
│  │ doctor or pharmacist before taking these together.   │ │
│  │                                                      │ │
│  │ Severity: HIGH  |  Source: DrugBank DB01234          │ │
│  └─────────────────────────────────────────────────────┘ │
│                                                           │
│  [ 👍 Helpful ]  [ 👎 Not helpful ]  [ Flag inaccuracy ] │
└───────────────────────────────────────────────────────────┘
```

---

## 🔬 System Architecture

```
User Input (Drug A + Drug B + Audience Type)
        │
        ▼
 DrugBank / openFDA API
 (Structured DDI data retrieval)
        │
        ▼
  Prompt Engineering Layer
  (Audience-adaptive system prompt)
        │
        ▼
  Anthropic Claude API
  (Plain-language summary generation)
        │
        ▼
  Streamlit Front-End
  (Interactive UI + feedback collection)
        │
        ▼
  User Feedback Store (JSON / CSV)
  (Structured rating + free-text comments)
        │
        ▼
  Prompt Iteration & Evaluation Loop
```

---

## 🗂️ Dataset

### Primary — DrugBank (CC BY-NC 4.0)
> **Source**: DrugBank Online  
> **Access**: [https://go.drugbank.com/releases/latest](https://go.drugbank.com/releases/latest)

DrugBank 6.0 provides drug–target interaction data in standard XML and CSV formats, with interoperability datasets released under Creative Commons licensing. The CC BY-NC 4.0 export includes sub-modules for drug targets, interactions, chemical properties, and metabolism information.

The drug-drug interaction dataset in DrugBank 5.0 grew to 365,984 interactions, manually sourced from FDA and Health Canada drug labels as well as primary literature. This provides the structured pharmacological interaction records that the tool's prompts are grounded in.

### Secondary — openFDA Drug Labels API
> **Source**: U.S. Food and Drug Administration  
> **Access**: [https://open.fda.gov/apis/drug/label/](https://open.fda.gov/apis/drug/label/)

The openFDA API provides real-time access to FDA Structured Product Labels, including drug interaction warnings, contraindications, and boxed warnings in their original regulatory language — the raw material the tool converts into plain English.

```python
import requests

# Fetch interaction warnings from FDA drug label
response = requests.get(
    "https://api.fda.gov/drug/label.json",
    params={"search": "drug_interactions:warfarin+aspirin", "limit": 5}
)
labels = response.json()["results"]
interaction_text = labels[0].get("drug_interactions", ["No data"])[0]
```

### Supplementary — TWOSIDES (Tatonetti et al.)
> **Access**: [https://tatonettilab.org/resources/tatonetti-stm.html](https://tatonettilab.org/resources/tatonetti-stm.html)

TWOSIDES is a multilabel DDI prediction dataset recording multiple side effects between drugs, used here to supplement DrugBank coverage for less common interaction pairs and to stress-test prompt robustness on rare or complex interactions.

---

## 📊 Evaluation & Results

The MVP was evaluated across two dimensions: **output quality** (accuracy and readability of generated summaries) and **usability** (user experience through structured testing).

### Prompt Engineering Iterations

| Version | Approach | Avg. Readability Score | Accuracy vs. Source |
|---|---|---|---|
| v1 — Zero-shot | Single instruction prompt | 6.1 / 10 | 71% |
| v2 — Role + Audience | System role + audience persona | 7.4 / 10 | 79% |
| v3 — Chain-of-thought | Step-by-step reasoning before output | 7.9 / 10 | 84% |
| v4 — Few-shot + Grounding | Examples + source-grounded output | **8.6 / 10** | **91%** |

> Readability scored by 10 lay participants on a 1–10 Likert scale. Accuracy assessed by comparison to source DrugBank interaction description by a domain expert reviewer.

### User Testing Results (n=15 participants)

| Metric | Score |
|---|---|
| Overall usefulness | 8.2 / 10 |
| Clarity of plain-language output | 8.7 / 10 |
| Trust in the information presented | 7.4 / 10 |
| Preferred over reading raw drug label | 87% of participants |
| Flagged factual inaccuracies | 3 / 150 outputs (2%) |

### Audience-Adaptive Output Quality

| Target Audience | Avg. Helpfulness Rating | Key Feedback |
|---|---|---|
| Patient (lay reader) | 8.7 / 10 | Clarity and actionability praised |
| Community Pharmacist | 7.9 / 10 | Wanted more mechanism detail |
| General Practitioner | 7.5 / 10 | Requested severity grading upfront |

> User testing conducted via structured feedback forms. Participants rated outputs across helpfulness, clarity, and trust. Qualitative feedback used to drive prompt v4 refinements.

---

## 🛠️ Tech Stack

| Category | Tools |
|---|---|
| Language | Python 3.10+ |
| LLM | Anthropic Claude API (`claude-3-5-sonnet`) |
| Front-End | Streamlit |
| Data Source | DrugBank XML/CSV, openFDA REST API |
| Prompt Management | Custom `PromptBuilder` class with versioning |
| Evaluation | Pandas, custom Likert scoring scripts |
| Documentation | Markdown, structured usability report (PDF) |

---

## 🚀 Getting Started

```bash
# Clone the repository
git clone https://github.com/YOUR_USERNAME/drug-safety-communication-tool.git
cd drug-safety-communication-tool

# Install dependencies
pip install -r requirements.txt

# Set your API key
export ANTHROPIC_API_KEY=your_key_here

# Run the Streamlit app
streamlit run app.py
```

---

## 📁 Repository Structure

```
├── app.py                        # Streamlit entry point
├── prompts/
│   ├── prompt_builder.py         # Audience-adaptive prompt construction
│   ├── v1_zero_shot.txt          # Prompt version history
│   ├── v2_role_audience.txt
│   ├── v3_chain_of_thought.txt
│   └── v4_few_shot_grounded.txt  # Final production prompt
├── data/
│   ├── drugbank_interactions.csv # Preprocessed DrugBank subset
│   └── twosides_sample.csv       # Supplementary interaction pairs
├── api/
│   ├── drugbank_client.py        # DrugBank data fetcher
│   └── openfda_client.py         # openFDA label retriever
├── evaluation/
│   ├── user_feedback.csv         # Collected testing feedback
│   ├── prompt_comparison.py      # Readability + accuracy scoring
│   └── results_summary.ipynb     # Analysis notebook
├── docs/
│   └── Technical_Report.pdf      # Non-technical stakeholder documentation
├── requirements.txt
└── README.md
```

---

## ✅ Skills Demonstrated

- **LLM product development**: end-to-end MVP from prompt design to deployed interactive interface
- **Prompt engineering**: iterative development across four prompt versions (zero-shot → few-shot + grounding) with measurable quality improvements
- **Audience-adaptive NLP**: system prompt design to modulate output complexity for lay users, pharmacists, and clinicians
- **Structured user testing**: designed and executed a 15-participant usability study with quantitative Likert scoring and qualitative feedback analysis
- **Evaluation methodology**: readability scoring, factual accuracy assessment, and cross-audience helpfulness benchmarking
- **Technical documentation**: written report communicating system performance and usability findings to non-technical stakeholders
- **API integration**: DrugBank and openFDA data retrieval as grounding sources to reduce hallucination
- **Streamlit application development**: interactive front-end with feedback collection and version-controlled prompt management

---

## 🌍 Real-World Applications & Conclusion

### Why This Matters

Adverse drug reactions caused by preventable interactions cost an estimated **$136 billion per year** in the United States alone. The problem is not a lack of data — DrugBank alone contains nearly 400,000 known interactions. The problem is **accessibility**: that data is written for researchers and regulators, not for the patients and community healthcare workers who encounter these interactions daily.

### Deployment Scenarios

| Sector | Use Case |
|---|---|
| **Community Pharmacy** | Front-of-counter patient counselling tool generating instant plain-English interaction warnings at point of dispensing |
| **GP / Primary Care Portals** | Embedded interaction checker generating clinician-facing summaries with severity grading and recommended action |
| **Patient-Facing Health Apps** | Integration into medication management apps (NHS App, chronic disease management platforms) for self-care support |
| **Pharmaceutical Patient Support** | Post-approval patient information generation for drug manufacturers, translating SmPC into accessible leaflets |
| **Healthcare Chatbots** | Grounded pharmacovigilance module providing safe, source-attributed interaction advice within regulated AI assistants |
| **Medical Education** | Interactive case-based learning tool for pharmacy and medical students exploring interaction mechanisms |

### Conclusion

This project demonstrates that LLMs, when grounded in structured pharmacological databases and guided by carefully engineered prompts, can translate complex clinical interaction data into accurate, accessible, and audience-appropriate plain-language summaries. The iterative prompt development process — moving from zero-shot through to few-shot grounded prompting — produced a **17-point improvement in readability** and a **20-point improvement in factual accuracy**, confirming that prompt engineering is a measurable, systematic discipline rather than trial and error.

The key finding from user testing is that **trust is the critical barrier** to adoption in health contexts: participants rated clarity highly (8.7/10) but trust lower (7.4/10), pointing to the importance of source attribution, severity grading, and human-in-the-loop verification in any production deployment. Future development would target integration with a live DrugBank API license, addition of a pharmacist review queue for flagged outputs, and formal clinical safety evaluation prior to any deployment in a patient-facing context.

This MVP establishes the technical and methodological foundation for a clinically viable drug safety communication layer — one that could meaningfully improve medication safety for the millions of patients managing complex polypharmacy regimens globally.


