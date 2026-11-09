
# AI Interview Preparation Agent — Workflow Architecture

## 1. Project Overview
An autonomous n8n workflow that researches a target company, compares a job advertisement with an applicant's CV, and generates a personalised, source-supported interview preparation report.

## 2. Primary Stack
- n8n: Workflow orchestration and applicant form
- Tavily API: Company web research
- NewsAPI: Relevant recent news
- Pinecone: Company-document vector database for RAG
- OpenAI: Embeddings, CV-to-job analysis and report generation

## 3. Applicant Inputs
One n8n form containing:
- Company name (required)
- CV PDF upload (required)
- Job advertisement text (required)
- Job posting URL (optional)
- Privacy acknowledgement (required)

Pasted job text is the primary source. URL extraction is an optional enhancement; failure must not block processing of valid pasted text.

## 4. Workflow Stages
1. Form Trigger — Receive submission and uploaded CV.
2. Input Validation — Check required fields, privacy acknowledgement and file type.
3. Document Extraction — Extract CV text using Extract From File; detect empty or unreadable PDFs.
4. Company Research — Retrieve company information through Tavily and NewsAPI.
5. RAG Retrieval — Query Pinecone using company-specific metadata filters for SAP or Microsoft. For companies without indexed documents, use live-research evidence only.
6. Candidate Analysis — Compare job requirements with relevant CV evidence using OpenAI.
7. Report Generation — Produce the seven-section report in docs/report_template.md.
8. Output Validation — Check report sections, source support and unsupported claims.
9. Delivery — Return the report using a tested n8n delivery method.

## 5. RAG Strategy
- Initial indexed companies: SAP and Microsoft.
- Documents: Official company reports and corporate information, with source URL, title, year and company metadata.
- Search only the requested company's documents.
- If no matching documents exist or retrieval fails, continue with verified live sources and disclose the limitation.
- Never store applicant CVs in Pinecone.

## 6. Report Sections
1. Company Overview
2. Job Requirements Analysis
3. CV-to-Job Matching
4. Skills and Experience Gaps
5. Personalised Interview Questions
6. Suggested STAR Interview Examples
7. Interview Preparation Recommendations

Include a random non-identifying Report ID, research sources and limitations. Do not include the applicant's name or contact information in the report.

## 7. Error Handling
- Missing required input: Stop with a clear message.
- Unreadable/scanned PDF: Explain that a text-based CV PDF is required.
- Inaccessible job URL: Use the supplied job description text.
- NewsAPI returns no results: Continue using other reliable sources.
- Pinecone has no matching company: Use live research only.
- API failure: Apply limited retries; show a clear error if required evidence or processing is unavailable.
- Invalid report: Flag missing sections or unsupported information before delivery.

## 8. Data Privacy
- Display privacy information before submission, including disclosure of OpenAI processing.
- Review n8n Workflow → Settings; disable saving successful executions and assess failed-execution saving.
- Minimise personal data and execution retention.
- Keep CVs out of Pinecone, GitHub and sample reports.
- Use only own or synthetic CVs for testing.
- Do not claim guaranteed deletion until service retention settings are verified.

## 9. Planned Tests
- SAP: Indexed company documents retrieved successfully.
- Microsoft: Indexed company documents retrieved successfully.
- Siemens: No company-specific Pinecone documents; live-research fallback.
- Test 2–3 text-based CV PDFs.
- Test an unreadable/scanned PDF and an inaccessible job URL.
- Generate two sample reports using own or synthetic CVs.

## 10. Implementation Status
Architecture: Designed, not yet implemented.
API integrations: Pending verification.
Pinecone indexing: Pending.
Full workflow: Pending.
Privacy configuration: Pending verification.
  