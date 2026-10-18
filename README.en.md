# met-mandatory-verify-checker

[![License: MIT](https://img.shields.io/badge/Code%20License-MIT-yellow.svg)](LICENSE)
[![Content: ARR](https://img.shields.io/badge/Content%20License-ARR%20%7C%20citable-blue.svg)](LICENSE-CONTENT)

> **Status: RESERVED (placeholder · open for claim)** — this repository already ships the four-file industry-access skeleton per the platform protocol, and waits for an operator with domain credentials to claim it and fill in real rules.

This is the SynomosAI **domain profile** for mandatory metrological verification of measuring instruments. It decomposes "does this instrument need mandatory verification, has it been verified, is it still in date" into verifiable attributes, so that AI only provides status guidance — never a pass/fail conclusion.

## What this domain governs

Mandatory verification cycle and compliance status of working measuring instruments.

Mandatory working measuring instruments must be verified on a cycle; using an overdue one in trade settlement, safety protection, medical treatment or environmental monitoring has direct consequences. AI can lay out the instrument attributes vs. verification status; pass/fail is decided solely by the verification certificate.

## Domain identity

| Field | Value |
|---|---|
| Domain ID | `met` (globally unique, never reused) |
| Domain name | Metrology · mandatory verification & instrument compliance |
| Profile version | `domain/1.0` |
| Status | `RESERVED` |
| Reserved at | 2026-09-12 |

## Attributes

| Key | Type | Meaning |
|---|---|---|
| `met.instrument_category` | enum | Listed in the mandatory working-instrument catalog and used in trade/safety/medical/environmental scenarios · mandatory/non_mandatory/exempt/undetermined |
| `met.verification_status` | enum | Current verification/calibration status per the certificate or notice · verified/overdue/exempt/none/unknown |
| `met.cycle_validity` | enum | Time remaining to next verification — reminder, not a verdict · within/approaching/expired |
| `met.type_approval` | enum | CPA type-approval status · approved/not_required/none/unknown |
| `met.verification_body` | string | Legal metrology institute or authorized body that performed the verification |

## Red lines (machine-readable, non-negotiable)

1. Never output "metrologically passed / safe to keep using" — the verification certificate is the sole basis.
2. Never state that an overdue mandatory instrument may be used.
3. Never suggest adjusting the instrument, altering usage records, or circumventing mandatory verification.

Each red line has a matching blocking rule in `gate-map.json`; the platform validator fails if coverage is missing.

## Quick Start / 快速开始

This repo is a declarative skeleton — no runtime, no install.

```bash
git clone https://github.com/SynomosAI/met-mandatory-verify-checker.git
cd met-mandatory-verify-checker
python -m json.tool domain.manifest.json    # validate the manifest
cat checker.io.json                         # read the MCP tool contract
```

The checker MCP tool returns only four states — `shown / missing / expired / unverifiable` — and is forbidden from outputting "pass/fail / safe-to-use" conclusions (see `_forbidden_outputs` in `checker.io.json`).

## Repository layout

```text
domain.manifest.json   # declaration: attributes / issuer requirements / red lines / validity
gate-map.json          # friction levels per action class (gate/1.0)
privacy.json           # privacy notice (privacy/1.0) — an admission condition
checker.io.json        # MCP checker I/O contract (show & verify, never judge)
LICENSE                # code & config: MIT
LICENSE-CONTENT        # prose content: All Rights Reserved (citable with attribution)
```

## References

- Metrology Law of the PRC (计量法)
- Implementing Regulations of the Metrology Law (计量法实施细则)
- Catalog of Mandatory Working Measuring Instruments (强制检定的工作计量器具目录)
- Measures for the Administration of New Measuring Instrument Products (计量器具新产品管理办法)
- JJF 1033 Rules for Metrology Standard Assessment (计量标准考核规范)

The above references only explain the origin of the domain's attributes; they are not legal advice.

## Claiming this domain

The domain is open to qualified institutions. To claim:

1. Fork this repo and fill in `operator` and `checker_endpoint`.
2. Calibrate attribute values and red lines per current rules.
3. Run the platform validator self-test (all five criteria must pass).
4. Open a PR with credential proof and rule citations.

## License

Code and configuration are MIT-licensed. Prose content is copyrighted by SynomosAI.

© 2026 SynomosAI. All rights reserved.

Full terms: [LICENSE](LICENSE) (MIT) · [LICENSE-CONTENT](LICENSE-CONTENT) (ARR). Report security issues privately via [SECURITY.md](SECURITY.md).
