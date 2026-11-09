# RAG Implementation Plan — SAP and Microsoft

## 1. Purpose

I will use Retrieval-Augmented Generation (RAG) to improve the accuracy and reliability of company research in my AI Interview Preparation Agent.

The agent will retrieve relevant information from official company documents stored in Pinecone and combine it with current research from Tavily and NewsAPI to generate evidence-based interview preparation reports.

For the initial MVP, the RAG knowledge base will contain selected official documents from SAP and Microsoft. For companies not included in Pinecone, the agent will use live web research as an alternative grounding method.



## 2. Selected Companies and Documents

| Company | Official Document | Year |
|---|---|---|
| SAP | SAP Integrated Report 2025 | 2025 |
| Microsoft | Microsoft Annual Report 2025 | 2025 |

**SAP official source:**

https://www.sap.com/integrated-reports/2025/en.html

**Microsoft official source:**

https://www.microsoft.com/investor/reports/ar25/

**Microsoft download page:**

https://www.microsoft.com/investor/reports/ar25/download-center/

These documents will provide information about company activities, products, business strategies and corporate priorities.

**Status:** Official sources identified. Document preparation and indexing are pending.

## 3. Document Preparation

I plan to:

1. Download the selected official documents or extract relevant sections.
2. Prioritise company background, products, services and strategy.
3. Extract the document text.
4. Split the text into smaller chunks suitable for retrieval.
5. Generate embeddings using an OpenAI embedding model.
6. Upload the embeddings and associated metadata into Pinecone.

I will use the same embedding model and compatible vector dimensions for indexing and retrieval.

## 4. Pinecone Knowledge Base

I will create a Pinecone index to store the embeddings of official company documents.

Each document chunk will contain metadata such as:

| Metadata field | Example |
|---|---|
| company | SAP |
| document_title | SAP Integrated Report 2025 |
| publication_year | 2025 |
| source_url | Official report URL |
| page_or_section | Strategy |
| text | Relevant document passage |

This metadata will allow the agent to retrieve company-specific information and identify its original source.

## 5. RAG Retrieval Process

When an applicant submits a company name, the agent will:

1. Identify the requested company.
2. Check whether company-specific documents are available in Pinecone.
3. Retrieve relevant passages using a company metadata filter.
4. Combine retrieved passages with current research from Tavily and NewsAPI.
5. Pass the supporting evidence to OpenAI for report generation.

For example, when the applicant selects SAP, the agent must retrieve SAP documents rather than Microsoft documents.

## 6. Fallback for Companies Outside Pinecone

The initial Pinecone collection will contain SAP and Microsoft documents only.

If an applicant selects another company, such as Siemens:

1. The agent will identify that no matching company documents are indexed.
2. It will use Tavily and available news sources for company research.
3. It will analyse the job advertisement and applicant's CV as normal.
4. It will generate a personalised report using the available evidence.
5. It will disclose that no preloaded company documents were available.

The agent must never substitute SAP or Microsoft documents for an unrelated company.

If live research also fails, the report must identify the missing evidence rather than inventing company information.

## 7. Data Privacy

The Pinecone knowledge base will contain official company information only.

Applicant CVs, names, addresses, telephone numbers and email addresses will not intentionally be added to the shared Pinecone knowledge base.

CV processing will occur separately within the n8n workflow, with appropriate data minimisation and execution-history settings.

## 8. Testing Plan — Day 2 and Day 3

| Test | Expected Result |
|---|---|
| SAP retrieval | Relevant SAP document passages returned |
| Microsoft retrieval | Relevant Microsoft passages returned |
| Siemens fallback | Live research used without unrelated Pinecone documents |
| Source verification | Retrieved passages include source metadata |
| No retrieval results | Agent reports the limitation and uses available evidence |
| CV privacy | No applicant CV data added to Pinecone |

## 9. Implementation Status

- Official SAP document identified: Complete
- Official Microsoft document identified: Complete
- Document text extraction: Pending
- Pinecone index creation: Pending
- Embedding generation: Pending
- Document upload: Pending
- Company-filtered retrieval: Pending
- Live-research fallback test: Pending

**Planned implementation:** Day 2 — Tuesday, 10 November 2026.