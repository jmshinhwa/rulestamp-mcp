# Rulestamp Compliance Linters — 40 MCP servers

> Not affiliated with ReadyStack (readystack.dev). Rulestamp tools are made by the team behind getreadystack.com.


Regulation- and deadline-aware linters (EU CRA / CSAF, PCI DSS 6.4.3, WCAG 2.1 AA, DORA, NIS2, EU AI Act, KSeF FA(3), NF-e, GitHub Actions EOL, TLS certificate lifetime, Kubernetes removed APIs …) that an AI agent calls over the **Model Context Protocol (MCP)**.

Every folder in this repository is one npm package `@readystack/<name>`. The same package runs three ways: as a **stdio MCP server** (`--mcp`), as a **CLI**, and as a **CI step**. No network calls: every rule set ships inside the package and runs offline on the files you point it at.

## Remote MCP server - no install (ChatGPT · Claude · Gemini · Cursor)

`https://mcp.getreadystack.com/mcp` - streamable HTTP, no account, no key. Two tools: `find_checker` (search every standing checker by regulation, deadline, file type or platform, in any language) and `run_checker` (run one checker on pasted text and get each finding with its line and fix). The text you send is checked in memory and never stored. New checkers appear on the server as they are published.

- **Claude** (claude.ai / Desktop): Settings → Connectors → Add custom connector → `https://mcp.getreadystack.com/mcp`
- **ChatGPT** (developer mode): Settings → Apps → Create → MCP server URL `https://mcp.getreadystack.com/mcp`, no authentication
- **Gemini CLI**: `gemini extensions install https://github.com/jmshinhwa/rulestamp-mcp` (this repository ships `gemini-extension.json`)
- **Cursor / VS Code / any client**: `{ "mcpServers": { "readystack": { "url": "https://mcp.getreadystack.com/mcp" } } }`

## Run as an MCP server (Claude Code · Cursor · Windsurf · any MCP client)

```json
{ "mcpServers": { "k8s-removed-api-lint": { "command": "npx", "args": ["-y", "@readystack/k8s-removed-api-lint", "--mcp"] } } }
```

The server speaks MCP over stdio (JSON-RPC 2.0, protocol version 2025-06-18: `initialize`, `tools/list`, `tools/call`). Tools exposed by every server: `check_dir` · `check_file` · `check_text`. `check_text` and `check_file` are free with no account; `check_dir` (the workspace sweep with an exportable report and a CI exit code) needs a licence key from https://getreadystack.com. Each finding comes back with the file, the line number and the fix.

Registry names: `io.github.jmshinhwa/<name>` in the official MCP Registry (registry.modelcontextprotocol.io).

## Run as a CLI or in CI

```bash
npx -y @readystack/k8s-removed-api-lint --file deploy.yaml      # one file
npx -y @readystack/k8s-removed-api-lint --dir . --ci             # whole repo, non-zero exit on findings (licence)
```

## Servers (40)

| npm package | what it checks |
|---|---|
| `@readystack/actions-deprecation-lint-2026` | Node 20 is removed from GitHub-hosted runners on 2026-09-23 and ubuntu-22.04 deprecation opens 2026-09-17. This checks a workflow file against 25 dated GitHub shutdowns and names the date each line st |
| `@readystack/ai-model-retirement-lint` | Finds the LLM model IDs pinned in your code that vendors have already switched off, or have dated to switch off, with the replacement and the date. |
| `@readystack/auditor-ibs-cbs-nfe` | A rejeição por falta de IBS/CBS foi suspensa em 31/07/2026: sua NF-e é autorizada mesmo errada. Este auditor lê o XML no editor e aponta o que a SEFAZ deixou passar. |
| `@readystack/autorenew-signup-lint` | Sixteen statutory checks on the signup page AB 2863 rewrote, in your editor and in the browser |
| `@readystack/base-image-eol-lint` | Marks every base image in your Dockerfiles, compose files and CI workflows whose security patches have already stopped - or stop before 13 November 2026 - and prints the tag that replaces it. |
| `@readystack/cloud-cost-landmine-lint` | Names every line in the file you have open that starts a recurring cloud charge, with the published us-east-1 price and the date it changes by itself. |
| `@readystack/cra-24-72-14-reporting-lint` | Reads your SECURITY.md against the EU Cyber Resilience Act reporting clock that started on 11 September 2026 — 24 hours, 72 hours, 14 days, to ENISA and your coordinating CSIRT. |
| `@readystack/cron-schedule-lint` | Names every schedule line in the file you have open that fires at the wrong hour, twice, or never at all - and prints the next three times each one really fires, in the zone that actually applies. |
| `@readystack/currency-minor-unit-lint` | Finds the money lines that send the wrong amount to a payment API: the x100 that charges 100x in JPY, the toFixed(2) that truncates KWD, and the currency codes that stopped being legal tender. |
| `@readystack/dora-ict-contract-clause-lint` | Twenty-two checks over the ICT vendor contracts in your repository - every missing Article 30 clause named, with the wording to paste in. |
| `@readystack/dora-register-lint` | 24 checks on the register CSV before it reaches the supervisor — LEI check digits, ISO dates and codes, references that resolve |
| `@readystack/eaa-form-lint-wcag22` | Names every WCAG 2.2 AA form failure on its line, with the criterion and the fix |
| `@readystack/einvoice-mandate-lint` | Lints UBL and CII invoice XML against EN 16931 and the national e-invoicing mandates that are live in 2026 |
| `@readystack/email-footer-law-lint` | Fourteen checks on an HTML email footer: CAN-SPAM postal address and 10-business-day opt-out, CASL's 60-day window, and German 5 DDG, the statute that replaced 5 TMG on 2024-05-14. |
| `@readystack/firmware-release-gate` | Finds the build-config lines that ship an ESP-IDF or Zephyr device with secure boot off, a debug port open, unsigned images or plaintext OTA. |
| `@readystack/france-einvoice-reception-lint` | 12 checks for the French mandatory mentions a PDP rejects, on Factur-X, UBL and CII invoices |
| `@readystack/gpsr-listing-lint` | Audits a product feed row by row against Article 19 of the EU General Product Safety Regulation |
| `@readystack/jct-transition-lint-2026` | 2026年9月30日で80%控除が終わります。10月1日からは50%ではなく70%です（令和8年度税制改正）。請求・仕入コードに残った 0.8 と 0.5、明細ごとの端数処理、T+13桁でない登録番号を、行番号と修正案つきで指摘します。 |
| `@readystack/k8s-removed-api-lint` | 15 checks for the apiVersions kubectl no longer serves, on the manifest open in your editor |
| `@readystack/latex-submission-lint` | Seventeen rules on your .tex file: the statements the editorial office looks for, and the source that breaks the publisher's build. |
| `@readystack/license-flip-audit` | Finds the dependencies whose licence changed under you - Terraform 1.6+ BUSL, Redis 7.4+ SSPL, Bitnami's August 2025 move, Elastic 7.11 - and names the exact version where each flip happened. |
| `@readystack/log-pii-telemetry-lint` | 22 rules that name the lines where a Node or TypeScript service writes personal data into logs, crash reports and analytics — with the article it touches and the one-line fix. |
| `@readystack/mcp-2026-migration-lint` | The 2026-07-28 MCP revision removed the initialize handshake, sessions, ping and 4 more RPCs. 27 rules find them in your mcp.json and server code, with the replacement on each line. |
| `@readystack/mcp-config-guard` | Reads .mcp.json / .vscode/mcp.json / claude_desktop_config.json while you edit it and marks the lines that hand an AI agent more than you meant - unpinned servers, literal credentials, plaintext trans |
| `@readystack/model-card-lint-gpai` | Lints a Hugging Face model card against the Hub metadata spec and the EU AI Act general-purpose AI documentation duties, line by line. |
| `@readystack/optout-signal-lint` | Finds the ad and analytics code that keeps firing after a visitor’s browser has already sent Global Privacy Control. |
| `@readystack/pci-payment-page-script-audit` | PCI DSS 4.0.1 requirements 6.4.3 and 11.6.1 have been mandatory since 2025-03-31, and the PCI SSC revised FAQ 1331 on 2026-08-04 so a QSA agreement alone no longer marks them not applicable. This read |
| `@readystack/personal-data-map-dsar-audit` | Reads a migration, Prisma schema, Django model or TypeORM entity and marks every column a subject access request has to reach - with the GDPR article each finding hangs on. 13 rules, line numbers, no |
| `@readystack/play-release-blocker-lint` | Finds the lines that make Google Play reject your release - target API below 36, Billing Library below 8, 4 KB-only native libs - and prints the date each gate closed. |
| `@readystack/pqc-deprecation-lint` | Dates every quantum-vulnerable algorithm in your code against the NIST IR 8547 clock: deprecated after 2030-12-31, disallowed after 2035-12-31. |
| `@readystack/privacy-manifest-lint` | Checks PrivacyInfo.xcprivacy and your C#, Dart, JS, Swift and Kotlin source for Apple required-reason APIs, and decodes all 17 reason codes back to the category they belong to. |
| `@readystack/pyproject-release-gate` | 19 date-aware checks on the [project] metadata that decides whether your next release uploads |
| `@readystack/robots-txt-ai-crawler-audit` | Checks robots.txt line by line and says what each AI crawler token actually controls - training, AI-search citation, or user fetch - and which lines silently do nothing. |
| `@readystack/security-headers-csp-lint` | Reads security headers and CSP line by line in your config file and names the lines that silently do nothing: retired headers, keywords missing their quotes, directives the browser throws away. |
| `@readystack/sql-card-data-lint` | Names every column, index, view and seed row in a .sql migration that stores card data, with its PCI DSS Requirement 3 rule id |
| `@readystack/tls-cert-lifetime-lint` | Finds the 12 settings that break when public TLS certificates fall to 100 days on 2027-03-15: openssl -days, Terraform, cert-manager, late expiry alerts, HPKP, TLS 1.0/1.1, SHA-1, RSA-1024. |
| `@readystack/vsix-publish-lint` | Find the package.json lines that stop vsce before you run the release build |
| `@readystack/wcag21-aa-legal-baseline-audit` | Audits HTML, JSX, Vue, Twig, Blade, ERB and Razor markup against the 24 WCAG 2.1 Level AA checks that 28 CFR 35.200 and EN 301 549 actually name - not WCAG 2.2. |
| `@readystack/wcag22-css-lint` | Reads your stylesheet and names the success criterion each rule breaks, including the three WCAG 2.2 added that WCAG 2.1 linters never learned. |
| `@readystack/wordpress-privacy-lint` | Finds the 12 author-side privacy duties your plugin PHP breaks, with the article and the fix on every line. |

## Free vs licence

Free: checking the text or file you pass in, with every rule and every fix — no account, no telemetry. Licence key ($29 one-time per tool, or the yearly rule-update plan): the workspace sweep (`check_dir` / `--dir`), the exportable report and the CI exit code. Keys are verified against Polar; the free part never expires and never asks for the key.

## Licence

[LICENSE](LICENSE) — source-available: read, audit and use freely (commercial use included); paid features require a valid licence key; the key check may not be removed or circumvented. Each package also ships a short `LICENSE.txt` with the same two rules.

## Related

- Free web versions of every tool + the licence store: https://getreadystack.com
- VS Code / Open VSX extensions built from the same engines: https://github.com/jmshinhwa/readystack-themes
