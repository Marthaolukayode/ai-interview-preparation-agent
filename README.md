# AI Interview Preparation Agent

An AI-powered interview preparation workflow built with **n8n, OpenAI, Pinecone, Tavily, NewsAPI and Notion**. It combines applicant CV evidence, job requirements, company research and retrieval-augmented generation (RAG) to create personalised interview preparation reports.

## Demo Video

[Watch the AI Interview Preparation Agent Demo](https://youtu.be/coY9AXKOJZI)

This video demonstrates the autonomous n8n workflow, Microsoft Pinecone RAG retrieval, personalised interview report generation, and automatic Notion report delivery.

## Project Overview

The AI Interview Preparation Agent helps job seekers prepare for interviews using a structured, evidence-conscious workflow. Applicants submit a CV in PDF format, a target company and a job description. The workflow extracts CV text, validates inputs, researches the hiring company, optionally retrieves official company documents and generates a seven-section interview preparation report.

The workflow distinguishes the **hiring company** from a recruitment agency or job advertiser when that distinction is available. Reports are displayed through n8n and delivered to **Notion** for review .

## Technology Stack

| Technology | Purpose |
|---|---|
| n8n | Form intake, document processing, conditional routing, API integration and automation |
| OpenAI (`gpt-4o-mini`) | CV-to-job analysis and structured report generation |
| Tavily API | Company research |
| NewsAPI | Recent company news |
| Pinecone | RAG retrieval from indexed official company documents |
| Notion | Automated report delivery and storage |
| GitHub | Version control and project documentation |

### Why n8n?

n8n was chosen because its visual workflow builder supports rapid integration of forms, external APIs, document processing, branching and error-handling within the four-day project timeline. LangGraph was not selected for this MVP because its code-first approach would add implementation overhead; it remains an option for more complex future stateful workflows.

## Implemented MVP Features

- Applicant form with company details, CV PDF, job-description input and a required privacy acknowledgement/consent step before processing.
- Form-input validation and handling for unsupported or image-only/scanned PDFs.
- CV extraction and company-specific research through Tavily and NewsAPI.
- Conditional Pinecone retrieval for **Microsoft and SAP**; other companies use live research without  Pinecone.
- OpenAI-generated, seven-section interview preparation reports with non-identifying report IDs.
- CV-to-job matching, interview questions and suggested STAR examples, with instructions to rely on CV evidence.
- Company research availability disclosures and fallback behaviour when Tavily or NewsAPI is unavailable.
- OpenAI retry and user-facing failure handling.
- Automated creation of Notion report pages and insertion of formatted report content.

## Workflow Architecture

```text
Applicant n8n form
       |
Input validation + PDF text extraction
       |
Hiring-company identification + job/CV context
       |
Tavily research + NewsAPI research
       |
       +--> Microsoft / SAP? -- Yes --> Pinecone retrieval --> Aggregate
       |                           
       +--> Other companies -------> Live-research path
                                      |
                           OpenAI report generation
                                      |
                          Report formatting / display
                                      |
                              Notion delivery
```

This is a conceptual overview; consult the exported n8n workflow for exact node connections and execution details.

## Error Handling and Testing

| Area | Implemented / tested outcome | Status |
|---|---|---|
| Form validation | Missing/invalid inputs handled | Tested |
| PDF extraction | Image-only/scanned PDF and extraction problems handled | Tested |
| Tavily failure | Simulated API failure; fallback tested; endpoint restored | Tested |
| NewsAPI failure | Simulated API failure; fallback and research-availability disclosure tested; endpoint restored | Tested |
| OpenAI failure | Invalid model simulated; retry/error-output route and friendly failure response tested; valid model restored | Tested |
| Microsoft RAG | Official-document chunks retrieved from Pinecone on 11 November | **Completed — 11 Nov 2026** |
| SAP RAG | SAP-specific Pinecone retrieval tested on 11 November (project owner confirmed) | **Completed — 11 Nov 2026** |
| Pinecone failure | API-error and empty-result fallback | Pending verification |
| Notion delivery | Automatic page creation and formatted report blocks | Tested |
| Citation and STAR evidence review | Manual source/evidence audit | Pending final review |
| Company-path coverage | Microsoft and SAP RAG and Siemens fallback tested by Day 3; Microsoft reconfirmed Day 4 | Completed for documented paths; regression after latest edits not fully evidenced |

**Important:** Tested failure handling for Tavily, NewsAPI and OpenAI does not establish that every possible failure mode is covered. Pinecone outage handling and regression after the latest status-propagation changes remain limitations; this is distinct from the successful Microsoft/SAP/Siemens company-path tests.

## Example Test Evidence

- **Microsoft:** Day 3 report `RPT-20261111-3527` demonstrated successful report generation; Day 4 report `RPT-20261112-3687` demonstrated retrieval of four official-document chunks from the Microsoft 2025 annual report in Pinecone.
- **SAP:** Company-specific RAG and report testing completed on Day 3 (11 November), as confirmed by the project owner; a specific SAP report ID is not available in the supplied record.
- **Siemens:** Day 3 report `RPT-20261111-3549` verified the non-indexed-company fallback, seven report sections and absence of unrelated Microsoft/SAP document content.
- **HTML/PDF:** Production form report display and three-page A4 print preview tested; print button hidden in PDF via `@media print` CSS.
- **Notion delivery:** report `RPT-20261112-3709` demonstrated creation of a Notion report for a hiring company distinct from the job advertiser, with formatted content blocks.

These identifiers are non-identifying test references, not links to public reports. Only synthetic applicant data should be published in repository samples.

## How to Use the AI Interview Preparation Agent

**Live Application:** [Open the AI Interview Preparation Agent](https://ac-ft-26-10-05.n8n.irn.hk/form/5fa6fa20-6233-4829-8617-5fe1517c0a07)

The AI Interview Preparation Agent can be accessed through its published n8n form. Users do not need to install n8n to use the existing deployed application.

### Using the Live Agent

1. **Open the application:** Access the published interview preparation form using the production form URL.
2. **Enter the hiring company:** Provide the name of the company where you are preparing for an interview (e.g., Microsoft, SAP or Siemens).
3. **Upload your CV:** Select a text-readable PDF containing your professional experience, skills and qualifications.
4. **Provide the job description:** Enter the requirements and responsibilities of the position.
5. **Accept the privacy acknowledgement:** Review and accept the required consent statement before submitting.
6. **Submit the form:** The agent validates the inputs, extracts CV information and conducts company research.
7. **Receive your interview preparation report:** The agent generates a personalised seven-section report using the CV, job description and available company research.
8. **Save your report:** Use the **Print and Save** option to download the generated report as a PDF.

### How Company Research Works

- **Microsoft and SAP:** The workflow retrieves relevant company information from the Pinecone knowledge base alongside live research.
- **Other companies:** The workflow uses live research through Tavily and NewsAPI without requiring pre-indexed documents.

The agent also creates a formatted report in the configured Notion workspace.

### Important Notes

- The uploaded CV must contain extractable text. Image-only or scanned PDFs may not be supported.
- AI-generated interview answers and company information should be reviewed for accuracy.
- Use synthetic applicant information when testing or demonstrating the application publicly.
## Setup and Configuration

1. Import the **sanitised** n8n workflow JSON into an n8n instance.
2. Configure the required credentials for OpenAI, Tavily, NewsAPI, Pinecone and Notion in n8n; **do not commit secrets**.
3. Configure the Pinecone index and populate company documents with appropriate metadata, including company identification.
4. Configure the Notion destination page/database permissions required by the workflow.
5. Review node expressions, credentials, environment-specific URLs and the form trigger before enabling the workflow.
6. Test with a **synthetic CV** and a non-sensitive job description before using real applicant data.

The exported workflow is the source of truth for exact node names, credentials and configuration requirements. This README does not claim the project can be run without those external services and their configuration.

## Privacy and Responsible Use

### Applicant consent and data minimisation

- The applicant-facing form includes a **privacy acknowledgement/consent requirement**. Applicants must acknowledge and agree to the use of their submitted information for generating an interview preparation report before proceeding.
- The form explains that submitted CV and job-description information is processed by the n8n workflow and relevant integrated services, including OpenAI for analysis and Notion for report delivery. The privacy notice should accurately identify the actual service providers, purposes and retention practices before real applicants use the agent.
- Reports use a **non-identifying Report ID** rather than an applicant's name as the primary identifier.
- Only information necessary for the interview-preparation task should be requested; avoid including unnecessary personal details in research queries, report titles or public examples.

### Implemented safeguards and operational checks

- Initial n8n execution-data privacy settings were configured during development to limit unnecessary storage of submitted data. **A final review/restoration of these settings remains pending**; this README does not claim that execution logs have been permanently disabled or deleted.
- Input validation and PDF extraction error handling help prevent unsuitable submissions from being processed further.
- Notion report delivery was tested, but access to the destination workspace/page must be restricted to authorised users; Notion is a storage destination, not a public publication channel.
- The workflow should not be considered GDPR-compliant solely because it has a consent checkbox. A production deployment also requires an appropriate legal basis, transparent privacy notice, retention/deletion procedures, vendor/transfer assessment and access controls.

- Use synthetic CVs and job descriptions for public demos and sample reports.
- Do not publish real CVs, applicant names, execution payloads, credentials, API keys or private Notion URLs.
- Review n8n execution-data retention/privacy settings before processing personal data.
- Treat AI-generated company research, citations and STAR examples as drafts requiring verification. Do not invent applicant achievements or imply that unsupported examples are CV facts.
- Sanitise the workflow export before committing it to GitHub.

## Project Documentation

- [Development Log](docs/development_log.md)
- [Report Template](docs/report_template.md)
- [Workflow Architecture](docs/workflow_architecture.md)
- [RAG Knowledge Sources](Company_Reports/rag_sources.md)
- [GTM Future Sprints](gtm_future_sprints.md)
- [Presentation Slides](Presentation/project3_presentation.pdf)
- [n8n Workflow JSON](workflows/AI%20Interview%20Preparation%20Agent.json)
- [Complete Workflow Screenshot](workflows/screenshots/01_complete_workflow.png)
- [Pinecone RAG Screenshot](workflows/screenshots/02_pinecone_rag.png)
- [Sample Interview Report](workflows/screenshots/03_sample_interview_report.pdf)

## Development Timeline

**Day 1 — 9 November 2026**  
Project scope and instructor approval; GitHub repository; report template; initial workflow architecture; selection of n8n and privacy configuration.

**Days 2–3 — 10–11 November 2026**  
Core n8n workflow construction and integration work: applicant form and PDF extraction; Tavily/NewsAPI research; Microsoft and SAP Pinecone RAG testing (SAP completion confirmed by project owner); Siemens non-indexed-company fallback; seven-section OpenAI reports; production Form Ending display; HTML formatting and print-friendly PDF export. Microsoft report `RPT-20261111-3527` and Siemens report `RPT-20261111-3549` are documented. See the development log for chronological details.

**Day 4 — 12 November 2026**  
Validation and PDF error tests; Tavily, NewsAPI and OpenAI failure simulations and restoration; successful Microsoft Pinecone retrieval; Notion automated delivery and formatted report verification; preparation of documentation and GitHub deliverables.

## Current Status — 13 November 2026

**Functional MVP completed and demonstrated. GitHub submission preparation in progress.**

The AI Interview Preparation Agent successfully processes applicant information, performs company research, generates personalised interview preparation reports and creates formatted Notion reports.

**Verified capabilities:**
- Microsoft and SAP company-specific Pinecone RAG retrieval.
- Live research fallback for non-indexed companies, including Siemens.
- Seven-section interview preparation report generation with HTML display and PDF export.
- Form validation, PDF extraction error handling and tested Tavily, NewsAPI and OpenAI failure scenarios.
- Automated Notion report creation.
- Published production form and accessible recorded demonstration.

**Repository deliverables prepared:**
- Exported n8n workflow JSON.
- Workflow architecture and RAG screenshots.
- Synthetic sample interview preparation report.
- Presentation PDF.
- RAG source documentation.
- Development log and GTM future-sprint plan.
- README instructions for accessing and using the live agent.

**Remaining verification before submission:**
- Final review of workflow JSON for credentials and sensitive information.
- Confirmation of repository links, screenshots and configuration documentation.
- Review of execution-data privacy settings and any remaining consent-flow concerns.
- Final Git status, commit, push and submission of the GitHub repository URL.

**Known limitations:** A forced Pinecone API outage/empty-result test and a complete automated citation/STAR evidence audit have not been verified. These are future improvement areas rather than completed test results.

## Future Improvements

- Expand and validate official-document RAG coverage for more companies.
- Improve source traceability and automated evidence checking.
- Add more robust Pinecone failure/empty-result handling and monitoring.
- Add structured regression tests for multiple synthetic applicant profiles.
- Consider more sophisticated stateful orchestration if the project grows beyond the MVP.
