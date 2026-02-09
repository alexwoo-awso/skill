# KSeF Legal Status - Details

**Update date:** February 8, 2026
**NOTE:** Information may change. Current regulations and Ministry of Finance communications should be regularly verified.

---

## Implementation Schedule (Planned)

### Phase 1: Large Taxpayers (from February 1, 2026)
**Who is affected:** Turnover >200 million PLN gross in 2024

**Obligations:**
- ✅ Issuing invoices via KSeF
- ✅ Receiving invoices via KSeF

### Phase 2: Other Taxpayers (from April 1, 2026)
**Who is affected:** Turnover ≤200 million PLN

**Obligations:**
- ✅ Receiving invoices via KSeF (from February 1, 2026)
- ✅ Issuing invoices via KSeF (from April 1, 2026)
- ⏳ Until March 31, 2026, may issue invoices outside KSeF

### Phase 3: Microenterprises (from January 1, 2027)
**Who is affected:** Monthly turnover <10,000 PLN

**Obligations:**
- ✅ Receiving: from February 1, 2026
- ✅ Issuing: from January 1, 2027

---

## Transition Period

**NOTE:** Details may change. The information presented is based on currently available communications.

### Planned Grace Period
- **Until December 31, 2026** - expected no penalties for errors
- Applies to: errors in FA(3) structure, sending delays
- Does not apply to: evading invoice issuance

### Offline24 Mode
- **Status:** Planned as a permanent system element
- **Purpose:** Emergency situations (no internet, KSeF outage, disasters)
- **Submission deadline:** 24h from regaining connectivity
- **Legal note:** Right to VAT deduction from KSeF number assignment date (not offline issue date)

### Invoices outside KSeF
- **Until March 31, 2026:** Allowed for companies ≤200 million PLN
- **From April 1, 2026:** Prohibited (except special cases)

---

## System Changes

### KSeF 1.0 → KSeF 2.0
- **KSeF 1.0:** Planned closure January 26, 2026
- **Technical break:** 5 days (January 26-31, 2026)
- **KSeF 2.0:** Planned start February 1, 2026

### FA(2) → FA(3)
- **FA(2):** Planned end of support February 1, 2026
- **FA(3):** Mandatory from February 1, 2026
- **Migration:** All taxpayers must adapt systems

---

## Legal Consequences (Future)

**NOTE:** The following information concerns the planned penalty system after the grace period ends.

### After January 1, 2027 (Planned)
- Missing invoice in KSeF → possible penalty
- Invoice outside KSeF → possible penalty
- Delayed submission → possible penalty

**Penalty amounts:** Consult current VAT Act

---

## Technical Requirements

### Mandatory Structure
- **FA(3) ver. 1-0E**
- Format: XML compliant with schema
- Encoding: UTF-8
- Validation: Automatic upon KSeF receipt

### Certificates (MCU)
- **MCU:** Certificate and Authorization Module
- **Status:** Active from November 1, 2025 according to schedule
- **Purpose:** Alternative authorization method (in addition to tokens)

---

## Information Sources

### Official
- KSeF Portal: https://ksef.podatki.gov.pl
- Ministry of Finance: https://www.gov.pl/web/kas/ksef

### Change Monitoring
- MF Communications: https://ksef.podatki.gov.pl/web/ksef/aktualnosci
- KSeF Latarnia (status): https://github.com/CIRFMF/ksef-latarnia

---

**Last update:** February 8, 2026
**Next verification:** Recommended every 2 weeks

[← Back to main SKILL](https://github.com/alexwoo-awso/skill/blob/main/ksef-accountant-en/SKILL.md)
