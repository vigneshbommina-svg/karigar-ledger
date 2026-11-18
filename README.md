# karigar-ledger

**A tamper-evident digital ledger for gold given to karigars for job-work.**

Theme: Jeweltech | Problem Statement 2.2: *Gold given to karigars for job-work is tracked on trust, and disputes follow*

Team **souryangas** | Author: BOMMINA VIGNESH, Siddhardha Academy of Higher Education

---

## The problem

Small jewellers hand gold to karigars (artisans) to make ornaments. Today this is tracked in paper notebooks or by word of mouth. When a finished piece comes back lighter than expected, neither side can prove what was given, what was returned, or what wastage is fair. The result is disputes, lost gold and broken trust, with karigars often blamed unfairly.

## The solution

KarigarLedger is a mobile-first app that records every gold issue, stage update and return with weight, purity, a photo of the scale and confirmation from both parties. It calculates wastage against the agreed limit and keeps a tamper-evident history, so disputes are settled with evidence instead of arguments.

## Key features

- **Job orders:** design, purity (e.g. 22K), issued weight and agreed wastage percentage
- **Dual confirmation:** an entry counts only after both jeweller and karigar confirm (OTP or in-app signature)
- **Stage tracking:** weights logged at casting, filing, setting and polishing
- **Wastage alerts:** flags loss above the agreed limit, or gold held too long
- **Tamper-evident audit trail:** timestamped entries, locked after confirmation and hash-chained so edits are detectable
- **Dispute report:** one-tap PDF with the timeline, photos, confirmations and wastage analysis
- **Karigar-friendly UI:** local-language support and minimal typing

## How it works

1. Jeweller creates a job order and issues gold. The karigar confirms.
2. The karigar logs weights as work progresses.
3. On return, both confirm the final weight. The app computes wastage and the balance with the karigar.
4. If within the agreed limit, the job closes. If not, an alert is raised and a dispute report is generated.

## Tamper-evidence

Each entry stores the hash of the previous entry (SHA-256). Changing any old entry changes its hash and breaks every link after it, so verification detects the edit.

```python
import hashlib, json

def entry_hash(entry, prev_hash):
    payload = json.dumps(entry, sort_keys=True) + prev_hash
    return hashlib.sha256(payload.encode()).hexdigest()

def verify_chain(entries):
    prev = "0" * 64
    for e in entries:
        body = {k: v for k, v in e.items() if k not in ("hash", "prev_hash")}
        if e["prev_hash"] != prev or e["hash"] != entry_hash(body, prev):
            return False  # tampering detected at this entry
        prev = e["hash"]
    return True
```

Example: 50.00 g issued and 47.20 g returned means 2.80 g lost, a wastage of 5.6%. Against a 3% limit, the app raises an alert.

## Planned tech stack

| Layer | Choice |
|---|---|
| Frontend | React PWA (works on any phone) |
| Backend | Node.js/Express or Python (FastAPI) |
| Database | PostgreSQL or Firebase |
| Integrity | SHA-256 hash chaining |
| Verification | OTP / e-signature, timestamped photo upload |
| Extras | PDF reports, WhatsApp/SMS alerts, optional OCR of scale photos |

## Repository structure

```
karigar-ledger/
  README.md
  docs/        architecture diagram, wireframes, supporting document
  backend/     API: orders, entries, reconciliation, hash chain
  frontend/    React PWA: issue, stage update, return, report
  tests/       hash-chain and wastage tests
```

## Status

Idea and design stage. The architecture, data model and hash-chain logic are documented in `docs/`; the application code is under development.

## Roadmap

1. Core ledger, dual confirmation, dispute PDF
2. Bluetooth digital scales and OCR of scale photos
3. AI anomaly detection and a karigar trust score
4. Link with HUID hallmarking data, plus credit and insurance for verified jewellers

## References

- Crosby, S. A. and Wallach, D. S., *Efficient Data Structures for Tamper-Evident Logging*, USENIX Security 2009: https://www.usenix.org/conference/usenixsecurity09/technical-sessions/presentation/efficient-data-structures-tamper-evident
- Schneier, B. and Kelsey, J., *Secure Audit Logs to Support Computer Forensics*, ACM TISSEC, 1999
- BIS blog, Hallmarking of Gold Jewellery and Artefacts: https://www.services.bis.gov.in/php/BIS_2.0/BISBlog/three-phase-induction-motors-performance-and-safety-2/
- Vikaspedia, HUID mandatory for sale of hallmarked gold: https://vikaspedia.in/social-welfare/social-awareness/consumer-education/hallmarking-of-gold-jewellery-consumer-education-1/sale-of-hallmarked-gold-jewelry-or-gold-artefacts-without-huid-prohibited-after-31st-march-2023
- Lakshmikumaran and Sridharan, Hallmarking scheme of BIS: https://lakshmisri.com/insights/articles/hallmarking-scheme-of-bis-safeguarding-the-purity-of-precious-metals

## Team

souryangas: BOMMINA VIGNESH, Siddhardha Academy of Higher Education
