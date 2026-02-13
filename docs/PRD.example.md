# PRD

## MW-PRD Runtime Control
ContinuousMode: ON

## 1. System Goal

### Verbatim Input (Block 1)

The core value provided by the system is the validation of weblinks following the HTTP Web Linking specification RFC 8288 after users have provided a URL. Primary users are persons with a computational and technical background that are interested veryfing their web resources provide RFC compliant web linking in the HTTP response header. The system shall use Linksmith internally, a Java library for parsing and validating web links and use the output of the library to  determine validation results. The system must not perform web link validation with any other mechanism.

### Structured Draft (Block 1)

- Primary users or actors:
  - Technically proficient users who want to verify their web resources provide RFC 8288-compliant Web Linking in HTTP response headers.
- Core value provided by the system:
  - Validate HTTP `Link` header web links for RFC 8288 compliance after a user provides a URL.
- System boundary (what the system does and does not do):
  - Does:
    - Accept a user-provided URL.
    - Provide a Web UI and an HTTP API for users to submit URLs.
    - Validate the web links present in the HTTP response header(s) against RFC 8288.
    - Use Linksmith (Java) internally to parse and validate web links and derive validation results from Linksmith output.
  - Does not:
    - Perform web link validation using any mechanism other than Linksmith.

## 2. Core Entities

### Verbatim Input (Block 2)

The core entities are WebLinks (from Linksmith) and ValidationResult (also from Linksmith). The entities are described in detail in the JavaDocs of the Linksmith library.

### Structured Draft (Block 2)

- Entity: `ValidationRequest` (system input contract)
  - Required fields:
    - `mode` (`url` | `rawHeaders`)
    - `input` (string)
  - Optional fields:
    - None defined yet.
  - Relationships:
    - One `ValidationRequest` produces one `ValidationReport`.
    - `mode=url` means `input` is a URL.
    - `mode=rawHeaders` means `input` is a full HTTP response header block including the status line and without folded/multi-line header lines.
- Entity: `ValidationReport` (system output contract for one request)
  - Required fields:
    - `results` (array of `LinkValidationResult`)
    - `summary` (`ValidationSummary`)
  - Optional fields:
    - Human-readable rendering of the same validation outcome.
  - Relationships:
    - One `ValidationReport` corresponds to one `ValidationRequest`.
    - `ValidationReport.results[*]` contains one `LinkValidationResult` per discovered `Link` header value in scope.
- Entity: `LinkValidationResult` (per discovered web link)
  - Required fields:
    - `link` (string)
    - `validations` (array)
    - `status` (`pass` | `warning` | `error`)
  - Optional fields:
    - None defined yet.
  - Relationships:
    - Many `LinkValidationResult` entries belong to one `ValidationReport`.
    - Each entry contributes to `ValidationSummary` aggregate counts.
- Entity: `ValidationSummary` (aggregate output statistics)
  - Required fields:
    - `totalValidatedLinks` (number)
    - `passedLinks` (number)
    - `linksWithErrors` (number)
    - `linksWithWarnings` (number)
  - Optional fields:
    - None defined yet.
  - Relationships:
    - One `ValidationSummary` belongs to one `ValidationReport`.
    - Derived from all `LinkValidationResult` items in the same report.

## 3. Required Capabilities

### Verbatim Input (Block 3)

Input & Execution Capabilities

Users must be able to:
	•	Submit a URL for validation
	•	Submit raw HTTP response headers for validation
	•	Trigger validation execution
	•	Cancel validation execution

Result Inspection Capabilities

Users must be able to:
	•	View validation results for submitted input
	•	See validation results per discovered web link
	•	View warnings and errors highlighted
	•	View a summary of validation results

Output & Integration Capabilities

Users must be able to:
	•	Export validation results as JSON
	•	Retrieve validation results via HTTP API
	•	Copy validation results

History & Management (Optional but common)

Users must be able to:
	•	View previously executed validations
	•	Re-run a previous validation request
	•	Delete validation history entries

### Structured Draft (Block 3)

- Input and execution capabilities:
  - Submit a URL for validation.
  - Submit raw HTTP response headers for validation.
  - Trigger validation execution.
  - Cancel validation execution.
- Result inspection capabilities:
  - View validation results for submitted input.
  - View validation results per discovered web link.
  - View warnings and errors highlighted.
  - View a summary of validation results.
- Output and integration capabilities:
  - Export validation results as JSON.
  - Retrieve validation results via HTTP API.
  - Copy validation results.
- History and management capabilities (required for MVP):
  - View previously executed validations.
  - Re-run a previous validation request.
  - Delete validation history entries.

## 4. Rules & Constraints

### Verbatim Input (Block 4)

The system must enforce the following rules:

- Validation requests must contain either a URL or raw HTTP response headers.
- Raw header input must include the HTTP status line.
- Raw header input must not contain folded header lines.
- Only HTTP Link header fields are in validation scope.
- All Link header occurrences must be validated.
- The system must follow redirects up to 3 levels when fetching URLs.
- Authentication must not be supported during URL fetching.
- Validation output must include per-link validation results.
- Validation output must include severity levels (pass, warning, error).
- Validation results must include summary statistics.

### Structured Draft (Block 4)

- Input validation rules:
  - A validation request must include exactly one input mode: URL input or raw HTTP response header input.
  - Raw header input must include the HTTP status line.
  - Raw header input must reject folded/multi-line header lines.
- Validation scope rules:
  - Only HTTP `Link` header field(s) are in scope.
  - All `Link` header field occurrences must be validated.
- Fetch behavior rules:
  - URL fetch mode must follow redirects up to a maximum of 3.
  - URL fetch mode must not support authentication.
- Output rules:
  - Validation output must include per-link validation results.
  - Validation output must include per-result severity values: `pass`, `warning`, `error`.
  - Validation output must include summary statistics.

## 5. Completion Criteria

### Verbatim Input (Block 5)

1. URL mode success (valid Link header)
	2.	URL mode no Link header (empty results + summary)
	3.	Raw headers success (status line + Link)
	4.	Raw headers invalid (missing status line OR folded headers rejected)
	5.	Per-link severity logic works (pass/warning/error)
	6.	API JSON schema matches fields + summary consistency

### Structured Draft (Block 5)

- The system is complete when all of the following acceptance checks pass:
  - URL mode: a request with valid `Link` header input returns successful per-link validation results.
  - URL mode without `Link` header: returns empty `results` and a valid `summary` with consistent zero/derived counts.
  - Raw headers mode success: full raw HTTP response headers including status line and valid `Link` header are accepted and validated.
  - Raw headers mode invalid input: request is rejected when the status line is missing or folded/multi-line headers are present.
  - Per-link severity logic: per-link status and validation item statuses correctly use `pass`, `warning`, or `error`.
  - API JSON contract: response schema fields match the defined contract and summary counts are consistent with per-link results.

## 6. Technical Constraints

### Verbatim Input (Block 6)

- Language/runtime: Java 21 (LTS)
	- Backend framework: Spring Boot (with Spring MVC)
	- Frontend: Server-rendered UI using Spring MVC + Thymeleaf (minimal JavaScript)
	- Link validation engine: Must use Linksmith internally for parsing/validation; must not validate web links via any other mechanism
	- Linksmith version: 1.0.0 (pinned exact version)
	- Validation failure behavior: If Linksmith cannot parse/validate input, return strict failure with explicit error reporting (no lenient/partial fallback)
	- Hosting/deployment: Self-hosted on a bare-metal Linux server (internal infrastructure)
	- API output: HTTP API must return machine-readable JSON output per the provisional schema defined in Block 3/4
	- Transport: URL fetch mode must support HTTP and HTTPS (no other schemes)

### Structured Draft (Block 6)

- Runtime and language:
  - Runtime must be Java 21 (LTS).
- Framework constraints:
  - Backend must use Spring Boot with Spring MVC.
  - Frontend must be server-rendered using Spring MVC + Thymeleaf with minimal JavaScript.
- Validation engine constraints:
  - Link validation must use Linksmith internally.
  - The system must not use any alternative validation mechanism.
  - Linksmith version must be pinned exactly to `1.0.0`.
  - If Linksmith cannot parse/validate input, the system must return strict failure with explicit error reporting (no lenient/partial fallback).
- Deployment constraints:
  - Deployment target is self-hosted bare-metal Linux in internal infrastructure.
- API/output constraints:
  - HTTP API responses must return machine-readable JSON conforming to the provisional schema defined in Blocks 3 and 4.
- Transport constraints:
  - URL fetch mode must support only `http` and `https` schemes.

## 7. Open Questions

- [x] OQ-B3-001 (Block=3 Required Capabilities) [Topic=Interface] What is the primary way users provide a URL to the system?
  - Answer: Web UI and HTTP API.
- [x] OQ-B3B4-002 (Block=3 Required Capabilities; 4 Rules & Constraints) [Topic=Fetch] Does the system fetch the URL over HTTP(S) itself to obtain response headers, or does the user provide raw HTTP response headers for validation?
  - Answer: Both (URL fetch mode + raw-headers mode).
- [x] OQ-B3B4-003 (Block=3 Required Capabilities; 4 Rules & Constraints) [Topic=Scope-Headers] Which HTTP response headers are in scope (only the `Link` header, or also others that may contain web links)?
  - Answer: Only `Link` headers are in scope; all `Link` header field occurrences must be considered (HTTP allows multiple `Link` header entries).
- [x] OQ-B3B4-004 (Block=3 Required Capabilities; 4 Rules & Constraints) [Topic=RFC-Scope] Which RFC 8288 features must be supported/validated (e.g., multiple `Link` headers, comma-separated values, parameters like `rel`, `anchor`, `hreflang`, `media`, `title`, `title*`, `type`, quoting/encoding edge cases)?
  - Answer: Correction: The system must not consider individual header web linking (RFC 8288) features.
- [x] OQ-B3B4-005 (Block=3 Required Capabilities; 4 Rules & Constraints) [Topic=Redirects] How should redirects be handled (follow, limit, report)?
  - Answer: Follow redirects up to a limit of 3.
- [x] OQ-B3-006 (Block=3 Required Capabilities) [Topic=Auth] Are authenticated endpoints in scope (custom headers, cookies, OAuth), or only public URLs?
  - Answer: Only public URLs without authentication.
- [x] OQ-B3-007 (Block=3 Required Capabilities) [Topic=Output] What should “validation results” look like (pass/fail, warnings, per-link details, error codes, machine-readable JSON)?
  - Answer: Both human-readable and JSON output; JSON is especially required for HTTP API responses. Output must list every web link and its validation result, highlight warnings and errors, provide an overview of links that passed validation, and include a total summary of all validation results.
- [x] OQ-B3B6-008 (Block=3 Required Capabilities; 6 Technical Constraints) [Topic=Dependency] Which Linksmith version is required, and what should happen if Linksmith cannot parse/validate certain inputs (strict vs lenient reporting)?
  - Answer: Pin an exact Linksmith version and use strict failure behavior when parsing/validation cannot be completed.
- [x] OQ-B3B4-009 (Block=3 Required Capabilities; 4 Rules & Constraints) [Topic=Input-Headers] In raw-headers mode, what input format is required (copy/paste full header block, JSON list of headers, allow multi-line folding), and is the HTTP status line included or excluded?
  - Answer: Use copy/paste full HTTP response header block with status line included (option 2); multi-line/folded headers are not allowed.
- [x] OQ-B3B4-010 (Block=3 Required Capabilities; 4 Rules & Constraints) [Topic=Output-Schema] What is the required JSON response schema for API output (field names, nesting, summary fields, and warning/error representation)?
  - Answer: Include a top-level array of validation results. Each item must include the validated web link and an array of validation results for that link. The top-level result object must include summary information for total validated web links, number of passed web links, number of web links with errors, and number of web links with warnings.
- [x] OQ-B6-011 (Block=6 Technical Constraints) [Topic=Dependency-Version] What exact Linksmith version must be pinned?
  - Answer: `1.0.0`.
- [x] OQ-B3B4-012 (Block=3 Required Capabilities; 4 Rules & Constraints) [Topic=Output-Schema-Fields] What are the exact JSON field names and the exact per-link validation-item shape/status values for the API response schema?
  - Answer: Define a provisional own schema based on current PRD requirements for the prototype; this may be adjusted in a later iteration.
- [x] OQ-B2-013 (Block=2 Core Entities) [Topic=Entity-Fields] For `WebLink` and `ValidationResult`, what exact required fields, optional fields, and relationships should this PRD treat as in-scope from Linksmith JavaDocs?
  - Answer: Correction applied: Linksmith is an internal implementation dependency only. The PRD must not model Linksmith internal entities and may only describe system inputs, outputs, and behavioral guarantees.
- [x] OQ-B3-014 (Block=3 Required Capabilities) [Topic=MVP-Scope] Are history management capabilities (`view previous validations`, `re-run`, `delete history`) optional or required for MVP?
  - Answer: Required for MVP.

## 8. PRD Changelog

- 2026-02-12: Initialized PRD and captured MW-PRD v1 Block 1 (System Goal) verbatim; added initial structured draft and open questions.
- 2026-02-12: Confirmed Block 1 “no changes”; added MW-PRD Status checklist and marked Block 1 complete.
- 2026-02-12: Resolved interface question (`OQ-B3-001`) as Web UI + HTTP API; converted Open Questions to MW-PRD checkbox+ID format; added MW-PRD Status as Section 9.
- 2026-02-12: Resolved OQ-B3B4-002 (both URL fetch mode + raw-headers mode) and updated Block 3.
- 2026-02-12: Resolved OQ-B3B4-003 (only `Link` headers; consider multiple header entries).
- 2026-02-12: Resolved OQ-B3B4-004 (no per-feature RFC 8288 scoping; delegate to Linksmith).
- 2026-02-12: Resolved OQ-B3B4-005 (follow redirects up to 3) and updated Block 3.
- 2026-02-13: Resolved OQ-B3-006 (public URLs only; no authentication) and updated Block 3.
- 2026-02-13: Resolved OQ-B3-007 (human-readable + JSON output, per-link results, warnings/errors highlighting, pass overview, total summary) and updated Block 3; added OQ-B3B4-010 for exact API JSON schema.
- 2026-02-13: Resolved OQ-B3B6-008 (pin exact Linksmith version + strict failure behavior) and updated Blocks 3 and 6; added OQ-B6-011 for the specific pinned version value.
- 2026-02-13: Resolved OQ-B3B4-009 (raw headers input must be full HTTP response header block including status line; multi-line/folded headers disallowed) and updated Blocks 3 and 4.
- 2026-02-13: Resolved OQ-B3B4-010 (top-level JSON results array + per-link validated link and validation-results array + required top-level summary counts); updated Blocks 3 and 4; added OQ-B3B4-012 for exact field names and per-item status/value shape.
- 2026-02-13: Resolved OQ-B6-011 by pinning Linksmith to version `1.0.0`; updated Blocks 3 and 6.
- 2026-02-13: Resolved OQ-B3B4-012 by defining a provisional prototype API JSON schema (field names, per-link shape, and status enums) based on existing PRD requirements.
- 2026-02-13: Captured Block 2 verbatim input and structured draft for entities `WebLink` and `ValidationResult`; added OQ-B2-013 for exact in-scope fields and relationships from Linksmith JavaDocs.
- 2026-02-13: Resolved OQ-B2-013 with requirement correction to keep Linksmith internal; rewrote Block 2 to system-facing entities (`ValidationRequest`, `ValidationReport`) only.
- 2026-02-13: Captured Block 3 verbatim input and updated structured Required Capabilities (input/execution, result inspection, output/integration, and optional history management).
- 2026-02-13: Completed Block 3 (structured content present and no unresolved Block 3 open questions).
- 2026-02-13: Captured Block 4 verbatim input and updated structured Rules & Constraints (input, scope, fetch behavior, and output requirements).
- 2026-02-13: Completed Block 4 (structured content present and no unresolved Block 4 open questions).
- 2026-02-13: Refined Block 2 core entities to reflect finalized rules/constraints (mode-specific input contract, per-link result entity, and summary entity) while keeping Linksmith internals out of PRD scope.
- 2026-02-13: Captured Block 5 completion criteria and converted them into measurable acceptance checks.
- 2026-02-13: Completed Block 5 (structured content present and no unresolved Block 5 open questions).
- 2026-02-13: Captured Block 6 technical constraints (runtime/framework, Linksmith dependency and failure behavior, deployment, transport, and API output contract).
- 2026-02-13: Completed Block 6 (structured content present and no unresolved Block 6 open questions).
- 2026-02-13: Resolved OQ-B3-014 and updated Block 3 to make history management required for MVP.

## 9. MW-PRD Status
- [x] Block 1 – System Goal
- [x] Block 2 – Core Entities
- [x] Block 3 – Required Capabilities
- [x] Block 4 – Rules & Constraints
- [x] Block 5 – Completion Criteria
- [x] Block 6 – Technical Constraints
