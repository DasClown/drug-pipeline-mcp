# 💊 drug-pipeline-mcp

[![CI](https://github.com/DasClown/drug-pipeline-mcp/actions/workflows/ci.yml/badge.svg)](https://github.com/DasClown/drug-pipeline-mcp/actions/workflows/ci.yml)
[![Docker](https://ghcr.io/dasclown/drug-pipeline-mcp/badge.svg?style=flat)](https://ghcr.io/dasclown/drug-pipeline-mcp)
[![Smithery](https://smithery.ai/badge/@crop-mcp/drug-pipeline)](https://smithery.ai/servers/crop-mcp/drug-pipeline)
[![Python 3.10+](https://img.shields.io/badge/python-3.10+-blue.svg)](https://www.python.org/downloads/)
[![License: MIT](https://img.shields.io/badge/license-MIT-green.svg)](LICENSE)
[![PyPI](https://img.shields.io/pypi/v/drug-pipeline-mcp?label=PyPI)](https://pypi.org/project/drug-pipeline-mcp/)
[![GitHub stars](https://img.shields.io/github/stars/DasClown/drug-pipeline-mcp?style=flat)](https://github.com/DasClown/drug-pipeline-mcp/stargazers)
[![Discussions](https://img.shields.io/github/discussions/DasClown/drug-pipeline-mcp?style=flat&label=Discussions&color=informational)](https://github.com/DasClown/drug-pipeline-mcp/discussions)

**Pharmaceutical R&D Pipeline Intelligence for AI Agents** — Clinical trials, FDA/EMA approvals, safety data, drug interactions, drug labels, recalls, patents & publications in one MCP server.

No hallucination. Every output traces to a source NCT ID, FDA application number, or PMID.

## Quick Start

```bash
pip install git+https://github.com/DasClown/drug-pipeline-mcp.git
# or try it on Smithery: https://smithery.ai/servers/crop-mcp/drug-pipeline

# Start MCP server (stdio)
drug-pipeline

# Or HTTP mode for remote access
pip install drug-pipeline-mcp[http]
drug-pipeline --http --port 8081
```

## Tools (17)

| Tool | What it does | Data Source |
|------|-------------|-------------|
| `search_trials` | Search clinical trials by condition, phase, status, sponsor | ClinicalTrials.gov |
| `get_trial_detail` | Full protocol for a specific NCT (eligibility, outcomes, locations) | ClinicalTrials.gov |
| `lookup_drug` | Drug info: active ingredients, strength, ATC classification, NDC | openFDA + RxNorm |
| `get_approvals` | FDA approval history with submission dates and status | openFDA |
| `get_eu_approvals` | **EU/EMA authorization status** — brand names, ATC, status, orphan/biosimilar flags | EMA Daily XLSX |
| `get_safety_data` | **FAERS adverse event reports** — top reactions, serious outcomes, total count | openFDA FAERS |
| `approved_for_condition` | **Find drugs by indication** — which drugs are EU-approved for a condition | EMA Daily XLSX |
| `get_trial_results` | **Trial results** — outcome measures, adverse events, baseline, participant flow | ClinicalTrials.gov |
| `list_orphan_drugs` | **EU orphan drug designations** — filter by therapeutic area | EMA Daily XLSX |
| `company_pipeline` | **Company R&D pipeline** — all trials grouped by phase + EU approval status | ClinicalTrials.gov + EMA |
| `search_publications` | PubMed search for drug/trial publications | PubMed / NCBI |
| `get_drug_label` | **FDA drug label** — indications, boxed warnings, contraindications, dosing | openFDA Drug Labeling |
| `get_recalls` | **FDA drug recalls** — Class I/II/III, reasons, dates, recalling firms | openFDA Enforcement |
| `detect_safety_signals` | **PRR safety signal detection** — disproportionate adverse event analysis | openFDA FAERS |
| `get_patent_expiry` | **Patent & exclusivity info** — approval dates, market exclusivity estimates | openFDA |
| `get_drug_interactions` | **Drug-drug interactions** — FDA label interactions + FAERS co-reported drugs | openFDA Labeling + FAERS |
| `drug_pipeline` | **Composite** — drug info + FDA + EU + safety + label + signals + recalls + interactions + trials + pubs + patent | All sources |

## Example Agent Queries

> *"What's in the pipeline for GLP-1 agonists?"*
→ `drug_pipeline(drug_name="semaglutide")` → ATC class, FDA status, 10+ trials, publications

> *"Which companies have Phase 3 trials for non-small cell lung cancer?"*
→ `search_trials(condition="non-small cell lung cancer", phase="PHASE3", status="RECRUITING")`

> *"Is pembrolizumab approved in the EU vs US?"*
→ `get_approvals(drug_name="Keytruda")` → FDA submission history with dates
→ `get_eu_approvals(drug_name="Keytruda")` → EU authorization status

> *"What are the safety signals for semaglutide?"*
→ `get_safety_data(drug_name="semaglutide")` → 6,027 FAERS reports, top reactions: Nausea (862), Vomiting (750)
→ `detect_safety_signals(drug_name="semaglutide")` → PRR analysis, disproportionate signals

> *"What does the label say for Keytruda?"*
→ `get_drug_label(drug_name="Keytruda")` → indications, boxed warnings, contraindications, dosing

> *"Are there recalls for Tylenol?"*
→ `get_recalls(drug_name="Tylenol")` → recall dates, reasons, classification, firms

> *"When does the patent for Keytruda expire?"*
→ `get_patent_expiry(drug_name="Keytruda")` → approval dates, exclusivity information

> *"What drugs are approved for non-small cell lung cancer?"*
→ `approved_for_condition(condition="non-small cell lung cancer")` → 82 drugs (Keytruda, Tagrisso, Opdivo, Tecentriq, ...)

> *"What are the eligibility criteria for NCT03178617?"*
→ `get_trial_detail(nct_id="NCT03178617")`

## Tool-by-Tool Query Examples

| Tool | Example query | Expected answer |
|------|---------------|-----------------|
| `search_trials` | Find recruiting Phase 3 trials for non-small cell lung cancer | Matching trials with NCT IDs, phases, sponsors, and statuses |
| `get_trial_detail` | Show the protocol for NCT03178617 | Eligibility criteria, outcomes, locations, and intervention details |
| `lookup_drug` | Look up semaglutide | Active ingredients, strengths, RxNorm identifiers, and ATC class |
| `get_approvals` | Show FDA approvals for Keytruda | FDA application numbers, submissions, dates, and status history |
| `get_eu_approvals` | Is Keytruda authorized in the EU? | EMA authorization status, brand names, ATC, and flags |
| `get_safety_data` | What adverse events are reported for semaglutide? | FAERS report counts, top reactions, and serious outcome summary |
| `approved_for_condition` | Which drugs are approved for non-small cell lung cancer? | EU-approved medicines matching the condition and therapeutic area |
| `get_trial_results` | Summarize results for NCT03178617 | Outcome measures, adverse events, baseline data, and participant flow |
| `list_orphan_drugs` | List orphan drugs in oncology | Orphan drug designations filtered by therapeutic area |
| `company_pipeline` | Show AstraZeneca's oncology pipeline | Trials grouped by phase plus EU approval context when available |
| `search_publications` | Find PubMed publications for tirzepatide obesity trials | PMID-linked publication matches with titles and metadata |
| `get_drug_label` | What does the FDA label say for Keytruda? | Indications, warnings, contraindications, dosing, and label sections |
| `get_recalls` | Are there recalls for Tylenol? | Recall class, reason, dates, and recalling firm details |
| `detect_safety_signals` | Detect safety signals for semaglutide | PRR-style disproportionate adverse event signals from FAERS |
| `get_patent_expiry` | When may Keytruda exclusivity expire? | Approval dates and estimated patent or exclusivity information |
| `get_drug_interactions` | Check interactions for warfarin | FDA label interaction text and FAERS co-reported drugs |
| `drug_pipeline` | Give me the full pipeline view for semaglutide | Composite drug info, approvals, safety, trials, publications, labels, and sources |

## Example Output (drug_pipeline)

```json
{
  "status": "ok",
  "query": {"drug_name": "semaglutide"},
  "drug_info": {
    "atc_classification": {"code": "A10BJ", "name": "GLP-1 analogues"},
    "rxcui": "1991302",
    "products": [{"brand_name": "Ozempic", "generic_name": "semaglutide", "labeler": "Novo Nordisk"}]
  },
  "clinical_trials": { "results": [ ... ] },
  "publications": { "total_count": 846, "returned_count": 5 },
  "data_sources": ["openFDA", "RxNorm", "PubMed", "clinicaltrials.gov"]
}
```

## Architecture

```
drug-pipeline-mcp/
├── drug_pipeline/
│   ├── __init__.py        # Version
│   ├── server.py          # MCP server (17 tools)
│   └── sources.py         # Data source fetchers
├── drug_pipeline_cli.py   # CLI entry point
├── pyproject.toml
└── README.md
```

No machine learning. No predictions. Only structured synthesis of verified primary sources.

## Data Sources

| Source | Data | Free |
|--------|------|------|
| ClinicalTrials.gov | 500K+ studies, phases, status, eligibility, **results** | ✅ Always free |
| openFDA Drug Approvals | FDA approvals, NDC directory, submissions | ✅ Always free |
| openFDA FAERS | Adverse event reports, reactions, outcomes | ✅ Always free |
| RxNorm / RxNav | Drug identifiers, ATC classification | ✅ Always free |
| PubMed / NCBI | Scientific publications | ✅ Always free |
| EMA Medicines Register | EU authorization status, ATC, orphan/biosimilar flags, **therapeutic areas** | ✅ Always free |

## Anti-Hallucination

Every result includes:
- **NCT ID** → `https://clinicaltrials.gov/study/NCT...`
- **FDA Application Number** → `https://www.accessdata.fda.gov/...`
- **PMID** → `https://pubmed.ncbi.nlm.nih.gov/PMID...`

No calculated fields, no predictions, no "ungefähre" estimates.

---

## Client Integration

### Claude Desktop

```json
{
  "mcpServers": {
    "drug-pipeline": {
      "command": "python3",
      "args": ["-m", "drug_pipeline.server"]
    }
  }
}
```

### Cursor / VS Code

Add to your `.cursor/mcp.json` or VS Code MCP config:

```json
{
  "mcpServers": {
    "drug-pipeline": {
      "command": "uvx",
      "args": ["drug-pipeline-mcp"]
    }
  }
}
```

### HTTP / SSE (Remote)

```bash
pip install drug-pipeline-mcp[http]
drug-pipeline --http --port 8081
```

Connect via SSE at `http://your-server:8081/sse`.

### Smithery

[![Smithery](https://smithery.ai/badge/@crop-mcp/drug-pipeline)](https://smithery.ai/servers/crop-mcp/drug-pipeline)

One-click deploy on Smithery. No config needed.

---

## 🤝 Getting Help & Contributing

| Channel | Purpose |
|---------|---------|
| **[💬 GitHub Discussions](https://github.com/DasClown/drug-pipeline-mcp/discussions)** | Questions before coding, feature ideas, community chat |
| **[🐛 GitHub Issues](https://github.com/DasClown/drug-pipeline-mcp/issues)** | Bug reports, confirmed feature requests |
| **[📖 CONTRIBUTING.md](CONTRIBUTING.md)** | Development setup, code style, testing |

New contributors welcome! See [CONTRIBUTING.md](CONTRIBUTING.md) for setup instructions, branch naming, and release process.

---

## Language

All output is in **English** (JSON field names, descriptions, results). The server can be configured via the `language` parameter on Smithery for future localization support.

## License

MIT
