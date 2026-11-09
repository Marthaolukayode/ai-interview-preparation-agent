# ai-interview-preparation-agent
An autonomous AI-powered job interview preparation agent built with n8n, OpenAI, Pinecone RAG, Tavily and NewsAPI. Generates personalised company research, CV-to-job matching and interview preparation reports.

## Project Overview

The AI Interview Preparation Agent is an autonomous workflow designed to help job seekers prepare for interviews through personalised, evidence-based research.

The agent will analyse a job advertisement, research the target company and compare the position's requirements with the applicant's professional experience. It will then generate a structured interview preparation report highlighting relevant strengths, potential evidence gaps, personalised interview questions and suggested STAR examples.

## Technology Stack

**Primary Stack: n8n**

I selected n8n because its visual workflow builder supports form input, API integrations, conditional routing, error handling and automation within the four-day project timeline. It allows me to integrate document processing and company research without implementing an entire orchestration framework in code.

I chose not to use LangGraph because its code-first graph and state-management approach would introduce additional implementation complexity for this MVP. LangGraph may be useful in the future if the agent requires more sophisticated stateful or multi-agent behaviour.

### Planned Integrations

| Technology | Purpose |
|---|---|
| n8n | Workflow orchestration and applicant form |
| Tavily API | Live company research |
| NewsAPI | Recent company news |
| OpenAI API | CV-to-job matching and personalised report generation |
| Pinecone | RAG retrieval from official company documents |

## MVP Features

- One applicant form containing company name, CV PDF upload, job description text, optional job URL and privacy acknowledgement.
- Automated company research using external sources.
- Pinecone RAG for official SAP and Microsoft documents.
- Live-research fallback for companies outside the Pinecone knowledge base.
- Personalised CV-to-job analysis and interview preparation recommendations.
- Seven-section report with a non-identifying Report ID.
- Input validation, error handling and privacy-conscious processing.

## Project Documentation

- [Development Log](docs/development_log.md)
- [Report Template](docs/report_template.md)
- [Workflow Architecture](docs/workflow_architecture.md)

## Development Status

**Day 1 — 9 November 2026**

- Instructor approval received.
- GitHub repository created.
- Report template defined.
- Workflow architecture documented.
- n8n selected as the primary stack.
- Initial execution-data privacy settings configured.

**Next:** SAP/Microsoft RAG document planning and API setup.

*This project is currently under development. The features above describe the planned MVP, not yet completed functionality.*