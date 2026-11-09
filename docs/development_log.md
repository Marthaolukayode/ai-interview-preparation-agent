# Development Log — AI Job Interview Research & Personalised Preparation Agent

**Project:** Ironhack Project 3 — Autonomous Company Research & Report Generation Agent  
**Industry / use case:** HR Technology — personalised interview preparation for job seekers  
**Primary stack:** n8n  
**Development dates:** 9–12 November 2026  
**Presentation:** Friday, 13 November 2026  
**Status:** Approved for development; architecture planned; implementation not yet verified

> This is a living record. Update the morning plan and end-of-day outcomes daily. Mark a task complete only after it has been carried out and verified. Do not put API keys, CV contents, or personal information in this file.

## 1. Project objective

Build an autonomous n8n workflow that accepts an applicant's company name, CV (PDF), pasted job advertisement text and optional job URL; researches the company; retrieves relevant official company documents when available; compares job requirements with evidence in the applicant's CV; and generates a personalised, source-supported interview preparation report.

### Target user and value

- **User:** Individual job seekers preparing for interviews.
- **Problem:** Company research, job-advertisement analysis and matching personal experience to a role are time-consuming.
- **Value:** Repeatable, evidence-based and CV-personalised preparation, rather than a generic company summary.

## 2. Instructor approval and scope decisions

**9 November 2026 — Approved to start building.** The instructor confirmed that the revised proposal answered the questions about personalisation, differentiation, privacy and RAG coverage.

Instructor implementation guidance incorporated into the MVP:

1. Use **one simple n8n form**: company name, CV upload, job description text and optional job URL. Avoid dynamic show/hide form fields.
2. **Pasted job description text is the main path.** Job URL extraction is a bonus and must not block the workflow if a site restricts access.
3. Test n8n **Extract From File** with **2–3 different PDF CVs on Day 2**. Scanned/image-only PDFs are not supported by the basic extraction path; provide a clear error message.
4. Before testing with CVs, review **Workflow → Settings** in n8n and disable saving successful executions; review failed-execution saving and any other retention options. The shared n8n instance may expose saved execution data to administrators. Verify actual settings rather than assuming privacy.
5. Test both retrieval paths: **SAP/Microsoft in Pinecone** and **a company outside the collection (e.g. Siemens)** using live research.
6. Use **only my own CV or synthetic CVs** for tests and sample reports.
7. Defer job-advertisement PDF upload unless time remains on Day 4.

## 3. Day 1 decisions and progress — Monday, 9 November

| Task | Status | Evidence / next action |
|---|---|---|
| Finalise project scope and obtain instructor approval | **Complete** | Instructor explicitly approved development. |
| Set up GitHub repository | **Complete** | Record repository URL below. |
| Define seven-section report template | **Complete** | Sections listed in §4. |
| Design logical workflow architecture | **Complete** | Nine-stage workflow architecture documented in docs/workflow_architecture.md. Implementation scheduled for Days 2–3. |
| Document n8n stack selection | **Complate** | Rationale in §6; add to README. |
| Define data privacy controls | **Patially Complete** | Checklist in §8; settings not yet verified. |
| Plan SAP/Microsoft RAG sources | **Complete** | Identify exact official documents and URLs. |
| Set up and test API access | **Pending** | Test each provider separately; never commit keys. |
| Outline GTM sprints | **Pending** | Define goals, audiences, channels, deliverables and metrics. |

**GitHub repository URL:** _Add the actual URL_  
**End-of-day summary:** _Complete at the end of Day 1_  
**Blockers / decisions:** _Record any issues and next steps_

## 4. Approved report template

Every generated report should contain these seven sections:

1. **Company overview:** Company background, products/services, recent developments and source links.
2. **Job requirements:** Responsibilities, skills and qualifications extracted from the submitted advertisement.
3. **CV-to-job matching:** Requirement-by-requirement evidence from the applicant's CV.
4. **Skills and experience gaps:** Requirements **not demonstrated in the CV**, without claiming the applicant lacks the skills.
5. **Personalised interview questions:** Questions tailored to the company, role and candidate's experience.
6. **Suggested STAR examples:** Relevant CV experiences framed using Situation, Task, Action, Result; do not invent achievements.
7. **Preparation recommendations:** Areas to research, practise and questions to ask the interviewer.

**Report quality requirements:** Cite company claims, separate sourced facts from suggestions, acknowledge missing evidence, and avoid fabricated CV details.

## 5. Logical workflow architecture (planned)

```text
[1. n8n Form Trigger]
    Company name + CV PDF + pasted job text + optional URL + privacy acknowledgement
             |
             v
[2. Validate Inputs]
    Required fields, acknowledgement, PDF type, basic limits
             |
             v
[3. Extract CV and Resolve Job Text]
    Extract From File; use pasted text; optionally try URL without blocking fallback
             |
             v
[4. Research Company]
    Tavily + NewsAPI, collect source URLs and relevant passages
             |
             v
[5. Retrieve Company Evidence]
    SAP/Microsoft -> Pinecone retrieval filtered by company
    Other company/no matching documents -> live-research-only fallback
             |
             v
[6. Analyse Candidate Fit]
    OpenAI compares job requirements with CV evidence
             |
             v
[7. Generate Seven-Section Report]
    OpenAI produces personalised, sourced report
             |
             v
[8. Validate Output]
    Required sections, source links, unsupported claims, error handling
             |
             v
[9. Deliver Report]
    Return output via a tested delivery method; minimise retained personal data
```

**Proposed n8n node families:** Form Trigger, IF, Edit Fields, Extract From File, HTTP Request, Pinecone Vector Store / Pinecone API, OpenAI, Code or IF for output checks, and an appropriate response/delivery node. The final node list and delivery mechanism must be verified during implementation.

**Error paths to build:** Missing CV/job text, unsupported or scanned PDF, unavailable URL, API timeout/rate limit, empty Pinecone retrieval, missing sources and invalid report output.

## 6. Stack and integration decisions

**Primary stack — n8n:** I chose n8n because its visual workflow builder supports form input, API calls, branching, retries and demonstration within a four-day MVP. It allows me to integrate document processing and company research without implementing an entire orchestration framework in code. I am not choosing LangGraph for this MVP because its code-first graph/state design would add implementation complexity beyond the time available. LangGraph may be useful later if the agent requires sophisticated stateful, multi-agent behaviour.

| Service | Purpose | Test status |
|---|---|---|
| Tavily | Live company research and sources | Tested successfully |
| NewsAPI | Recent company news, where available | Tested successfully |
| OpenAI API | CV-to-role analysis and report generation |Tested successfully|
| Pinecone | Retrieval from official company documents (RAG) | Tested successfully |

**Security:** Store credentials only in n8n credential storage or local untracked environment settings. Commit `.env.example` with placeholders, not `.env` or real keys.

### 6.1 Tavily API — Implementation and Test Record

**Implementation:** Configured an n8n HTTP Request node using POST, Header Authentication and the Tavily Search API endpoint (`https://api.tavily.com/search`).

**Test:** Submitted a company research query for SAP. Tavily successfully returned structured results containing source URLs, titles and content.

**Issue and resolution:** Initially encountered an HTTP 405 error due to an incorrect endpoint. Corrected the URL and successfully executed the request.

**Next improvement:** Prioritise official company sources and replace the static SAP query with dynamic company input during workflow development.

**Status:** Successfully tested.

### 6.2 NewsAPI — Implementation and Testing

**Implementation:** Configured an n8n HTTP Request node using GET, Header Authentication and the NewsAPI endpoint (`https://newsapi.org/v2/everything`).

**Test:** Submitted a news search query for SAP using `sortBy=publishedAt` and `pageSize=3`. NewsAPI successfully returned structured articles containing titles, descriptions, publication information and source URLs.

**Improvement identified:** Refine search queries to prioritise company-specific news and filter irrelevant articles.

**Status:** Successfully tested.

### 6.3 OpenAI API — Implementation and Testing

**Implementation:** Configured the native OpenAI node in n8n using the `Message a Model` operation and GPT-4o-mini.

**Test:** Submitted a prompt requesting three interview questions for a Product Manager position at SAP. OpenAI successfully generated relevant interview questions.

**Next improvement:** Replace the static prompt with dynamic inputs from the candidate's CV, job description and company research.

**Status:** Successfully tested.

### 6.4 Pinecone API — Integration and Test Record

**Purpose:** Store and retrieve company knowledge to support retrieval-augmented generation (RAG).

**Implementation:**
- Created the Pinecone index `interview-prep-knowledge` with 1536 dimensions and cosine similarity.
- Connected Pinecone to n8n using API credentials.
- Configured OpenAI `text-embedding-3-small` for document embeddings.
- Created separate nodes for document insertion and retrieval.
- Inserted a synthetic SAP company information document using the Default Data Loader.

**Test Result:** Successful. Pinecone retrieved the inserted SAP document in response to a natural-language query about SAP's products and business priorities.

**Similarity Score:** Approximately 0.648.

**Next Steps:** Ingest official SAP and Microsoft company documents, implement company-specific retrieval and connect retrieved evidence to the interview preparation report.

**Status:** Pinecone integration and retrieval test completed. Official company knowledge base ingestion pending.


## 7. RAG plan and fallback

**Initial indexed companies:** SAP and Microsoft.  
**Documents to identify and verify:** Official company annual reports and official company/about or careers information. Record exact titles, years and source URLs before ingestion.

**Indexing approach:** Extract source text, split into manageable chunks, generate embeddings, and store chunks in Pinecone with metadata such as `company`, `document_title`, `year`, `source_url` and page/section where available.

**Retrieval:** Filter by the exact target company. If the company is not indexed, or no useful matching passages are found, proceed with live Tavily/NewsAPI research. Do **not** substitute SAP or Microsoft documents for Siemens or another employer. The report should disclose when no indexed company documents were used.

**Test cases:** (a) SAP or Microsoft with relevant indexed passages; (b) Siemens or another non-indexed company using live research only.

## 8. Data privacy and CV-handling plan

- Display a clear privacy notice before CV submission, explaining purpose and providers, including that CV text is sent to OpenAI for report generation.
- Include an acknowledgement checkbox in the form; a checkbox alone does not establish full GDPR compliance.
- Do not upload CVs to Pinecone or commit CVs/sample personal data to GitHub.
- Before using CVs, open **n8n Workflow → Settings**; disable saving successful executions and inspect failed executions, manual executions, binary-data storage, logs and other retention settings as available.
- Use only synthetic or personally authorised CVs for tests; redact identifying details from public screenshots and reports.
- Document actual provider/hosting retention limits and avoid promising immediate deletion unless verified.
- For production use, obtain appropriate legal/privacy review covering GDPR legal basis, transparency, processors, transfers, retention and rights.

### 8.1 Data Privacy — Day 1 Implementation Record

**Date:** 9 November 2026

**Activity:** n8n Workflow Execution Settings Configuration

I configured the execution settings for the AI Interview Preparation Agent to minimise the retention of applicants' personal data.

**Settings configured:**

- Save successful production executions: **Do not save**
- Save failed production executions: **Do not save**
- Save manual executions: **Do not save**
- Save execution progress: **Do not save**

**Purpose:** These settings reduce the retention of uploaded CVs, extracted text and other personal information in n8n execution history.

**Remaining privacy tasks:**
- Add the privacy notice and acknowledgement to the applicant form.
- Verify CV data minimisation and handling.
- Review external API providers' data-retention policies.
- Test the workflow using synthetic CVs.

**Status:** Settings configured; save verification pending.

## 9. Daily implementation and evidence log

### Day 2 — Tuesday, 10 November: Grounding + tools

**Morning plan**
- [ ] Identify and download official SAP/Microsoft RAG documents; record exact source URLs.
- [ ] Configure Pinecone index, chunking, embeddings and company metadata.
- [ ] Test SAP/Microsoft retrieval and non-indexed-company fallback.
- [ ] Independently test Tavily, NewsAPI and OpenAI API connections.
- [ ] Test Extract From File with 2–3 synthetic/own CV PDFs, including a scanned-PDF failure case if available.
- [ ] Create the n8n workflow skeleton.
- [ ] Verify n8n execution-saving privacy settings before uploading CVs.

**End-of-day completed:** _Fill in after work_  
**Evidence / screenshots / test results:** _Fill in_  
**Blockers and fixes:** _Fill in_  
**Carry-over to Day 3:** _Fill in_

### Day 3 — Wednesday, 11 November: Autonomous core

**Morning plan**
- [ ] Implement form submission and input validation.
- [ ] Extract CV text and process pasted job description; add optional URL fallback if feasible.
- [ ] Connect Tavily/NewsAPI research and company-filtered Pinecone retrieval.
- [ ] Implement CV-to-job comparison and seven-section report generation.
- [ ] Run end-to-end test and save **first** synthetic/own-CV sample report.

**End-of-day completed:** _Fill in after work_  
**Evidence / screenshots / test results:** _Fill in_  
**Blockers and fixes:** _Fill in_  
**Carry-over to Day 4:** _Fill in_

### Day 4 — Thursday, 12 November: Harden + GTM + submission

**Morning plan**
- [ ] Add validation, retry handling and clear errors for unsupported CV PDFs or failed API requests.
- [ ] Test the SAP/Microsoft RAG path and the Siemens/non-indexed fallback path.
- [ ] Generate **second** sample report using a different target company and synthetic/own CV.
- [ ] Finalise `gtm_future_sprints.md` (2–3 sprints; each has goal, audience, channel, deliverable and metric).
- [ ] Finalise README, architecture diagram, sample reports and workflow JSON export.
- [ ] Check GitHub for secrets or personal data; verify privacy settings and documented limitations.
- [ ] Prepare slides and rehearse the 5–7 minute presentation.

**End-of-day completed:** _Fill in after work_  
**Evidence / screenshots / test results:** _Fill in_  
**Blockers and fixes:** _Fill in_  
**Submission repository link:** _Fill in_

### Day 5 — Friday, 13 November: Presentation only

- [ ] Explain the user problem and value proposition.
- [ ] Show n8n architecture, integrations and grounding.
- [ ] Demonstrate a generated personalised report and RAG/fallback behaviour.
- [ ] Explain privacy settings, limitations and GTM sprints.
- [ ] Deliver the 5–7 minute presentation (instructor notes Friday availability only until 11:00).

## 10. Future GTM sprint outline (to finalise on Day 4)

| Sprint | Working title | Goal | Audience | Channel | Deliverable | Metric |
|---|---|---|---|---|---|---|
| 1 | Job-seeker validation | Test report usefulness | Small group of job seekers | Direct outreach | Feedback sessions + sample reports | Useful-report rating / feedback count |
| 2 | Career-coach pilot | Test repeatable usage | Career coaches | Targeted outreach | Pilot workflow + feedback form | Pilot participation / repeat use |
| 3 | Improve onboarding | Reduce friction | New applicants | Landing page / demo | Simplified instructions and examples | Completion rate / time to report |

These are **proposals**, not completed sprints. Define measurable targets and assumptions in `gtm_future_sprints.md`.

## 11. Final deliverables checklist

- [ ] Working autonomous n8n workflow (exported JSON)
- [ ] At least three tested external API/tool integrations
- [ ] Grounded company research with Pinecone RAG and fallback
- [ ] Two generated sample reports with synthetic/own CVs
- [ ] Documented stack decision and architecture diagram
- [ ] README with setup, limitations and privacy section
- [ ] `gtm_future_sprints.md`
- [ ] GitHub repository link and files checked
- [ ] Presentation slides and rehearsed demo

## 12. Issue and decision log template

| Date | Issue or decision | What I tried / rationale | Outcome | Next action |
|---|---|---|---|---|
| 2026-11-09 | Instructor requested simpler input form | Use one n8n form; pasted text primary, URL optional | Scope updated | Implement on Day 3 |
| 2026-11-09 | CV privacy on shared n8n instance | Plan to disable saved executions and verify retention settings | Pending verification | Review settings before CV tests |

---

**Update rule:** After each working session, add the actual changes made, exact n8n nodes tested, sample output paths, errors and fixes. Keep the distinction between **planned**, **tested** and **completed** explicit.
