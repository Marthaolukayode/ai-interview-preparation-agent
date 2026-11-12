# Development Log — AI Job Interview Research & Personalised Preparation Agent

**Project:** Ironhack Project 3 — Autonomous Company Research & Report Generation Agent  
**Industry / use case:** HR Technology — personalised interview preparation for job seekers  
**Primary stack:** n8n  
**Development period:** 9–12 November 2026  
**Presentation:** Friday, 13 November 2026  
**Status as of 12 November:** Functional MVP built and tested, including Microsoft and SAP RAG, Siemens fallback, seven-section reports, HTML/PDF delivery and Notion integration. Final GitHub submission, documentation review and selected resilience checks remain.

> I record completed work and verified tests separately from outstanding items. I do not include API keys, real CV contents or other personal information in this log.

## 1. Project objective and scope

I set out to build an autonomous n8n workflow that helps job seekers prepare for interviews. An applicant submits a company name, CV in PDF format, pasted job description, optional job URL and mandatory privacy acknowledgement. The workflow extracts CV evidence, researches the target employer, retrieves official company documents where indexed, and produces a personalised seven-section interview preparation report. The report is displayed in a browser, can be saved as a PDF and can be delivered to Notion.

**Target user:** Individual job seekers preparing for interviews.  
**Problem:** Company research, understanding job requirements and identifying relevant CV evidence take considerable time.  
**Value:** Repeatable, evidence-based preparation tailored to a candidate's experience rather than a generic company summary.

### Scope decisions

- I used a single n8n form rather than complex dynamic form fields.
- I made pasted job-description text the main input; the job URL is optional and must not block report generation.
- I limited the initial Pinecone knowledge base to **Microsoft and SAP** and used live research for other companies.
- I used synthetic or personally authorised CVs for development and testing.
- I deferred job-advertisement PDF upload and more complex agent frameworks to keep the MVP achievable within four days.

## 2. Day 1 — Monday, 9 November 2026

**Focus: Project approval, scope, architecture and planning**

### What I completed

- [x] Received instructor approval to begin building after clarifying personalisation, differentiation, privacy and RAG coverage.
- [x] Defined the user problem, value proposition and MVP boundaries.
- [x] Created the GitHub repository and initial documentation structure.
- [x] Designed the seven-section interview preparation report template.
- [x] Documented a nine-stage logical workflow architecture in `docs/workflow_architecture.md`.
- [x] Selected n8n as the primary orchestration tool and documented the decision not to use LangGraph for this MVP.
- [x] Planned SAP/Microsoft document retrieval, API integrations and three future go-to-market (GTM) experiments.
- [x] Defined privacy requirements, including a mandatory acknowledgement and reduced execution-data retention.

### Decisions and rationale

I chose **n8n** because its visual workflow builder supports form inputs, API calls, branching, retries and automated delivery within the project timeline. I considered LangGraph but did not adopt it because a code-first state-management approach would add unnecessary complexity for this MVP.

I defined the following report sections:

1. Company overview and sourced developments
2. Job requirements extracted from the advertisement
3. CV-to-job matching based on documented evidence
4. Skills and experience gaps **not demonstrated in the CV**
5. Personalised interview questions
6. Suggested STAR examples grounded in CV evidence
7. Preparation recommendations and questions for the interviewer

### Instructor guidance incorporated

I kept the form simple, prioritised pasted job text over URL extraction, planned PDF extraction tests, required privacy-setting checks before CV processing, and designed separate tests for indexed companies and the non-indexed fallback.

### End-of-day outcome

I finished Day 1 with an approved project scope, report template, documented architecture, GitHub repository and implementation plan. Technical integration and full workflow testing continued on Day 2.

---

## 3. Day 2 — Tuesday, 10 November 2026

**Focus: Research APIs, Pinecone, CV extraction and initial report generation**

### What I completed 

- [x] Configured and tested **Tavily** company research through an n8n HTTP Request node.
- [x] Configured and tested **NewsAPI** for company-related news.
- [x] Configured the native **OpenAI Message a Model** node using `gpt-4o-mini`.
- [x] Created and connected the Pinecone index `interview-prep-knowledge` with OpenAI embeddings.
- [x] Tested retrieval of company information from Pinecone, including Microsoft annual-report passages with source metadata.
- [x] Implemented the n8n applicant form and extracted text from a synthetic PDF CV.
- [x] Connected applicant input, research and AI generation into an initial seven-section report.
- [x] Added automatic non-identifying report ID generation.
- [x] Verified the n8n workflow execution-saving settings.
- [x] Documented three proposed GTM experiments in `gtm_future_sprints.md` .

### Integration and test details

| Integration | Implementation and test evidence | Result |
|---|---|---|
| Tavily | POST request to `https://api.tavily.com/search`; returned titles, URLs and research passages for SAP | Successful |
| NewsAPI | GET request to `https://newsapi.org/v2/everything`; tested SAP news with `sortBy=publishedAt` and `pageSize=3` | Successful |
| OpenAI | `Message a Model` with `gpt-4o-mini`; first tested interview-question generation, then report generation | Successful |
| Pinecone | Index `interview-prep-knowledge`, 1536 dimensions, cosine similarity, OpenAI `text-embedding-3-small`; insertion and retrieval nodes | Successful initial retrieval |
| PDF extraction | n8n `Extract From File` on a synthetic text-based CV PDF | Successful |
| Report generation | Seven-section personalised output and generated report identifier | Successful |

During initial Pinecone setup, I inserted a synthetic SAP company document using the Default Data Loader and retrieved it using a natural-language query (similarity score approximately **0.648**). I also retrieved Microsoft company information with source metadata. Full company-specific Microsoft and SAP RAG tests were completed on Day 3.

### Privacy measures configured

I reviewed the workflow execution settings before continuing CV-based testing:

| n8n setting | Configured value |
|---|---|
| Save successful production executions | Do not save |
| Save failed production executions | Do not save |
| Save manual executions | Do not save |
| Save execution progress | Do not save |

These settings reduce retention of CVs, extracted text and personal information in n8n execution history. I also planned the mandatory applicant privacy acknowledgement and restrictions on using real applicant data.

### Issues and resolutions

- **Tavily HTTP 405:** I corrected an incorrect API endpoint and retested successfully.
- **Multiple Pinecone results:** I used an `Aggregate` node to combine retrieved document chunks before passing them to OpenAI.
- **Report identification:** I updated the OpenAI prompt to include the automatically generated report ID.

### End-of-day outcome

I had working connections to Tavily, NewsAPI, OpenAI and Pinecone, a functioning form/PDF extraction path, and an initial personalised report. I carried forward production report presentation, complete company-specific RAG routing and fallback tests to Day 3.

---

## 4. Day 3 — Wednesday, 11 November 2026

**Focus: Complete the autonomous workflow, company-specific RAG and report delivery**

### What I completed

- [x] Implemented and tested form submission through the production workflow.
- [x] Integrated extracted CV text, job-description text, Tavily, NewsAPI and Pinecone into report generation.
- [x] **Completed and tested Microsoft-specific Pinecone RAG retrieval.**
- [x] **Completed and tested SAP-specific Pinecone RAG retrieval.**
- [x] Added an `IF` node to route Microsoft and SAP requests to Pinecone and other companies to a live-research fallback branch.
- [x] Configured `Edit Fields1` to provide a fallback message when indexed documents are not applicable.
- [x] Connected both branches to OpenAI and updated the prompt to handle absent company documents without substituting unrelated evidence.
- [x] Successfully generated a Siemens report through the fallback branch.
- [x] Configured `Form Ending` to display reports through the production URL.
- [x] Added `Format Report as HTML` JavaScript conversion for professional browser presentation.
- [x] Added structured headings, lists, hyperlinks, styling and report identifiers.
- [x] Implemented **Print / Save as PDF** and print-specific CSS.
- [x] Verified that the print button is hidden in the PDF preview.

### Verified test evidence

| Test | Evidence | Status |
|---|---|---|
| Microsoft RAG | Microsoft official-document retrieval and successful production report, `RPT-20261111-3527` | **Completed** |
| SAP RAG | SAP-specific Pinecone retrieval tested | **Completed** |
| Siemens fallback | Seven-section report using live research without unrelated SAP/Microsoft documents, `RPT-20261111-3549` | **Completed** |
| Browser delivery | HTML report displayed through the production form | **Completed** |
| PDF export | Three-page A4 preview; print button excluded from print output | **Completed** |

### Issues and resolutions

- **Raw Markdown in Form Ending:** I added JavaScript conversion to HTML.
- **JavaScript syntax error:** I corrected the print-button implementation.
- **Print button appeared in PDF:** I added `@media print` CSS to hide it.
- **Risk of unrelated company evidence:** I used conditional routing and a fallback message to keep SAP/Microsoft documents out of Siemens reports.

### Instructor feedback

My instructor confirmed that the workflow, RAG implementation and product differentiation met the project requirements. Additional AI agent functionality was optional. Professional presentation and Notion/Slack integration were suggested as useful improvements.

### End-of-day outcome

I completed the working Microsoft and SAP RAG paths, Siemens fallback, browser report delivery and PDF export. I reserved Day 4 for validation, resilience testing, Notion delivery, privacy review and submission preparation.

---

## 5. Day 4 — Thursday, 12 November 2026

**Focus: Validation, error handling, Notion integration and final documentation**

### What I completed

- [x] Added and tested required form-field validation and privacy acknowledgement handling.
- [x] Tested handling of unsupported or scanned/image-only PDF extraction scenarios, with user-facing error messaging.
- [x] Simulated a **Tavily failure** by temporarily changing the endpoint; confirmed the workflow's research fallback behaviour and restored `https://api.tavily.com/search`.
- [x] Simulated a **NewsAPI failure** by temporarily changing the endpoint; confirmed fallback behaviour and research-availability disclosure, then restored `https://newsapi.org/v2/everything`.
- [x] Simulated an **OpenAI model failure** using an invalid model ID; configured retries (up to three attempts with a 2,000 ms wait), error-output routing and a user-friendly `Report Generation Unavailable` form response; restored `gpt-4o-mini`.
- [x] Integrated **Notion** for automatic report delivery.
- [x] Verified Notion child-page creation, dynamic page titles and structured report content insertion.
- [x] Confirmed that the Notion report contained all seven sections; one successful run inserted **49 content blocks**.
- [x] Continued refining employer identification so the hiring company is distinguished from the job-advertising platform.
- [x] Tested Microsoft Pinecone retrieval again and received four Microsoft annual-report document chunks.
- [x] Documented implemented privacy controls, mandatory applicant acknowledgement and remaining privacy review tasks.

### Notion delivery evidence

I successfully generated and delivered a report for **RiVOLUTiON GmbH**, distinguishing the hiring company from the advertising platform **TrustJobs**. The report ID was `RPT-20261112-3709`. Notion created the report page and appended structured content, including all seven sections.

### Error-handling test record

| Scenario | What I tested | Outcome |
|---|---|---|
| Missing/invalid applicant input | Required fields and privacy acknowledgement | Validation implemented and tested |
| Unsupported/scanned CV PDF | PDF extraction error path | User-facing handling tested |
| Tavily unavailable | Temporary invalid endpoint | Fallback tested; endpoint restored |
| NewsAPI unavailable | Temporary invalid endpoint | Fallback/disclosure tested; endpoint restored |
| OpenAI unavailable | Invalid model ID and retry/error branch | Friendly failure response tested; model restored |
| Pinecone retrieval success | Microsoft documents retrieved again (`RPT-20261112-3687`) | Successful |
| Pinecone API outage / empty-result resilience | Separate failure simulation | **Not fully verified** |

I added a `Pinecone Retrieval Results` node with a `pinecone_status` field and configured aggregation of `document` into `company_documents`. The status field's final appearance in the Aggregate JSON output was not conclusively verified during the latest inspection. This is a **resilience-check limitation**, not an unfinished Microsoft or SAP RAG implementation.

### Privacy and applicant consent

I included a **mandatory privacy acknowledgement** in the applicant form so the applicant must acknowledge data processing before using the agent. I documented the purpose of processing and the use of external AI/research services. I continued using synthetic or personally authorised CVs for testing and avoided placing CV contents or API credentials in repository documentation.

I had previously configured n8n to avoid saving successful, failed and manual execution data and execution progress. **Final re-verification of these settings after testing remains outstanding.** I do not claim that a checkbox or n8n execution settings alone establish full GDPR compliance; external-provider retention, Notion access and production legal/privacy requirements still need review.

### Remaining work at the end of Day 4

- [ ] Verify the final Pinecone failure/empty-result status propagation and API-outage behaviour.
- [ ] Perform a final manual review of source citations and STAR examples against synthetic CV evidence.
- [ ] Export and sanitise the n8n workflow JSON for GitHub.
- [ ] Save two clearly labelled final sample reports using synthetic or authorised CV data.
- [ ] Finalise README, architecture and documentation files in the repository.
- [ ] Recheck execution privacy settings and Notion sharing/access before submission.
- [ ] Commit and push the final deliverables to GitHub.
- [ ] Prepare and rehearse the presentation.

### End-of-day outcome

I finished the main MVP features and key error-handling tests, including automatic Notion delivery. The outstanding items concern final quality checks, privacy review and packaging/submission rather than the completed Microsoft/SAP RAG or Siemens fallback functionality.

---

## 6. Architecture and technology decisions

```text
Applicant form
  Company name + CV PDF + job description + optional URL + privacy acknowledgement
       |
       v
Validate inputs and extract CV text
       |
       v
Identify hiring company and gather live research
  Tavily + NewsAPI
       |
       v
Company-specific routing (IF)
  |-- Microsoft / SAP --> Pinecone Vector Store --> Retrieval Results --> Aggregate --|
  |-- Other companies --> Edit Fields1 (live-research fallback) ---------------------|
                                                                                   v
                                                                     OpenAI (gpt-4o-mini)
                                                                                   |
                                                                                   v
                                                                    Seven-section report
                                                                        /          \
                                                                       v            v
                                                            HTML / Form Ending    Notion
                                                            Print / Save PDF      Report page
```

| Technology | Why I used it |
|---|---|
| n8n | Visual orchestration, form submission, routing and delivery |
| Tavily | Current company research with source links |
| NewsAPI | Recent company news when available |
| Pinecone | Company-specific RAG for Microsoft and SAP documents |
| OpenAI | CV-to-role analysis and personalised report generation |
| Notion | Automated delivery of structured reports |
| JavaScript in n8n | HTML formatting, print presentation and supporting transformations |

## 7. Report quality and limitations

I designed the report to distinguish **CV evidence** from inferred strengths, identify requirements **not demonstrated in the CV** rather than asserting a candidate lacks skills, and avoid invented STAR achievements. Company-specific claims should have relevant source links. Research APIs may return incomplete or irrelevant results, so final citation review is important.

The initial knowledge base covers Microsoft and SAP; other employers use live research. The workflow supports text-based PDF extraction but does not provide full OCR for scanned CVs. An API failure may reduce report quality or prevent report generation, in which case the workflow should communicate the limitation rather than fabricate evidence.

## 8. Future go-to-market (GTM) sprints

These are **proposed future experiments**, not completed sprints.

| Sprint | Goal | Audience | Channel | Deliverable | Success measure |
|---|---|---|---|---|---|
| 1 — Job-seeker validation | Test whether reports are useful | Small group of job seekers | Direct outreach | Feedback sessions and sample reports | Feedback count and usefulness rating |
| 2 — Career-coach pilot | Test repeat usage | Career coaches | Targeted outreach | Pilot workflow and feedback form | Participation and repeat use |
| 3 — Improve onboarding | Reduce applicant friction | New applicants | Landing page/demo | Clearer instructions and examples | Completion rate and time to report |

Further details belong in `gtm_future_sprints.md`.

## 9. Final deliverables and submission checklist

| Deliverable | Status as of 12 November |
|---|---|
| Working n8n MVP | **Completed and tested** |
| Tavily, NewsAPI, OpenAI and Pinecone integrations | **Completed and tested** |
| Microsoft and SAP RAG | **Completed and tested 11 November** |
| Siemens non-indexed fallback | **Completed and tested 11 November** |
| Seven-section report and HTML/PDF delivery | **Completed and tested** |
| Notion automatic report delivery | **Completed and tested 12 November** |
| Mandatory privacy acknowledgement | **Implemented and tested** |
| Core API and PDF error handling | **Implemented and tested; Pinecone outage check remains** |
| Two final synthetic sample reports saved for repository | Pending final packaging |
| Sanitised n8n workflow JSON export | Pending |
| Final README, architecture and development log in GitHub | Pending final repository update |
| Final privacy/Notion access review | Pending |
| Presentation and rehearsal | Pending |

**GitHub repository URL:** To be added after final submission.  
**Presentation date:** Friday, 13 November 2026.

## 10. Presentation plan — Friday, 13 November 2026

- [ ] Explain the user problem and how the agent adds value.
- [ ] Show the n8n architecture and the role of each integration.
- [ ] Demonstrate a personalised seven-section report.
- [ ] Explain Microsoft/SAP RAG and the Siemens live-research fallback.
- [ ] Show Notion delivery and HTML/PDF output.
- [ ] Explain mandatory consent, data privacy measures, error handling and known limitations.
- [ ] Present the proposed GTM sprints and next improvements.
- [ ] Deliver the 5–7 minute presentation.

---

*I will update this log only when additional work has been completed or verified. All API keys and personal CV contents remain outside project documentation.*
