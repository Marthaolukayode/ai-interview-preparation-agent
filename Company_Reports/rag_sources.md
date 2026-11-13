# RAG Knowledge Sources – AI Interview Preparation Agent

## 1. Purpose

The AI Interview Preparation Agent uses Retrieval-Augmented Generation (RAG) to provide company-specific information during interview preparation.

Official company annual reports were selected as knowledge sources to help ground the AI-generated interview preparation reports in relevant company information.

## 2. Knowledge Sources

| Company | Source Document | Purpose |
|---|---|---|
| Microsoft | 2025 Annual Report | Company background, business strategy, products, financial performance and strategic priorities |
| SAP | SAP Integrated Report 2025 | Company background, enterprise software solutions, business strategy, financial performance and strategic priorities |

**Original source websites:**

- Microsoft Investor Relations: https://www.microsoft.com/en-us/Investor/
- SAP Investor Relations: https://www.sap.com/integrated-reports/

The PDFs were obtained for use as reference material in the project's RAG knowledge base.

## 3. Pinecone RAG Implementation

The Microsoft and SAP company reports were used as source documents for the Pinecone vector knowledge base.

The RAG workflow supports the following process:

1. Company reports are prepared and ingested into the knowledge base.
2. Relevant document content is stored and indexed in Pinecone for semantic retrieval.
3. When a user selects Microsoft or SAP, the n8n workflow follows the company-specific RAG retrieval path.
4. Retrieved company information is supplied as context to the AI model.
5. The AI uses this context, together with the candidate's CV, job description and research results, to generate a personalised interview preparation report.

## 4. Retrieval and Fallback

The workflow supports two company-research approaches:

- **Microsoft and SAP:** Pinecone RAG retrieval using the indexed company documents.
- **Other companies:** Live company research using the configured research tools, without requiring a pre-indexed company report.

This allows the agent to provide interview preparation for both indexed and non-indexed companies.

## 5. Testing

The Microsoft and SAP RAG paths were implemented and tested as part of the project.

The workflow also supports a fallback research path for companies without indexed documents.

## 6. Copyright and Reproducibility

The original annual-report PDFs are not included in the public GitHub repository.

Readers can access the reports through the official company investor-relations websites listed above. The original documents remain the property of their respective publishers.

This file documents the knowledge sources used to build the RAG component of the AI Interview Preparation Agent.