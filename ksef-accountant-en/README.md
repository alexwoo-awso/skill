# KSeF Autonomous Accountant Skill (English)

**Skill for autonomous AI agent supporting Poland's National e-Invoice System (KSeF) operations.**

**🤖 For AI Agents:** See [SKILL.md](https://github.com/alexwoo-awso/skill/blob/main/ksef-accountant-en/SKILL.md) - that's your entry point.

**👤 For Humans:** You're in the right place - this README contains version history, deployment schedule, and documentation overview.

---

## 📋 Description

Comprehensive knowledge and competencies for handling:
- KSeF 2.0 API (FA(3) structure)
- Automatic invoice posting
- Cost classification (AI/ML)
- Payment matching
- Anomaly and fraud detection
- Cash flow prediction
- Integrations (ERP, CRM, Banking)
- Compliance (VAT White List, GDPR)

## 📅 KSeF Deployment Schedule

**NOTE:** The KSeF implementation schedule and regulation details may change.

### Key Dates (planned)
- **February 1, 2026** - KSeF 2.0 production, FA(3) mandatory (for companies >200 million PLN revenue in 2024)
- **April 1, 2026** - mandatory issuance for companies ≤200 million PLN
- **January 1, 2027** - mandatory issuance for microenterprises
- **December 31, 2026** - planned end of grace period (no penalties)

### Technical Environment
```
DEMO:       https://ksef-demo.mf.gov.pl
PRODUCTION: https://ksef.mf.gov.pl
API DOCS:   https://ksef.mf.gov.pl/api/docs
```

**Requirements:**
- Structure: FA(3) ver. 1-0E
- Format: XML compliant with schema
- Validation: automatic upon receipt

**📄 Full legal details:** [ksef-legal-status.md](https://github.com/alexwoo-awso/skill/blob/main/ksef-accountant-en/ksef-legal-status.md)

## 📚 Documentation

Detailed documentation (reference files):
- [Legal status and timeline](https://github.com/alexwoo-awso/skill/blob/main/ksef-accountant-en/ksef-legal-status.md)
- [API Reference](https://github.com/alexwoo-awso/skill/blob/main/ksef-accountant-en/ksef-api-reference.md)
- [FA(3) Examples](https://github.com/alexwoo-awso/skill/blob/main/ksef-accountant-en/ksef-fa3-examples.md)
- [Accounting Workflows](https://github.com/alexwoo-awso/skill/blob/main/ksef-accountant-en/ksef-accounting-workflows.md)
- [AI Features](https://github.com/alexwoo-awso/skill/blob/main/ksef-accountant-en/ksef-ai-features.md)
- [Security & Compliance](https://github.com/alexwoo-awso/skill/blob/main/ksef-accountant-en/ksef-security-compliance.md)
- [Troubleshooting](https://github.com/alexwoo-awso/skill/blob/main/ksef-accountant-en/ksef-troubleshooting.md)

## 📊 Structure

All files in root (flat hierarchy for clawhub.ai):

```
├── SKILL.md                         (main file ~400 lines)
├── ksef-legal-status.md            (legal status, timeline)
├── ksef-api-reference.md           (API endpoints)
├── ksef-fa3-examples.md            (XML examples)
├── ksef-accounting-workflows.md    (accounting flows)
├── ksef-ai-features.md             (AI/ML)
├── ksef-security-compliance.md     (security)
├── ksef-troubleshooting.md         (troubleshooting)
├── SECURITY.md                     (security policy)
└── README.md                       (this file)
```

## 🌍 Language

**This version:** English

**Polish version:** Available at https://clawhub.ai/alexwoo-awso/ksef-accountant-pl

## 📝 Version

**2.2.0** (February 9, 2026) - Structural cleanup

### Version History

**v2.2.0 (February 9, 2026)**
- Separated human-oriented content to README.md
- SKILL.md now clean entrypoint for AI agents
- Moved deployment schedule and version history to README
- Changed {baseDir} references to relative markdown links (./file.md)
- Enhanced security disclaimer for VirusTotal compatibility
- Fixed file reference detection for clawhub.ai import

**v2.1.5 (February 9, 2026)**
- Changed {baseDir} references to relative markdown links
- Enhanced security disclaimer
- Fixed clawhub.ai file detection

**v2.1.4 (February 9, 2026)**
- Changed all relative markdown links to absolute (GitHub)
- Fixed compatibility with clawhub.ai

**v2.1 (February 9, 2026)**
- Refactoring to progressive disclosure structure (main file ~400 lines)
- Extracted details to separate reference documents
- Maintained essence of competencies in main file

**v2.0 (February 8, 2026)**
- Added legal and technical disclaimers
- Softened hard AI/ML declarations
- Marked examples as illustrative

**v1.0 (January 1, 2026)**
- First version of document

## 📜 License

MIT

## 🔗 Resources

- KSeF Portal: https://ksef.podatki.gov.pl
- CIRFMF GitHub: https://github.com/CIRFMF
- clawhub.ai: https://clawhub.ai/alexwoo-awso/ksef-accountant-en

---

**DISCLAIMER:** This document is a specification of AI agent competencies and does not constitute legal or tax advice. Before implementation, consultation with a qualified tax advisor is recommended.
