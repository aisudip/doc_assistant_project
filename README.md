# Document Assistant Project

This project aims to build an AI assistant that can answer questions, summarize documents, and perform calculations on financial and healthcare documents.

This document assistant uses a multi-agent architecture with LangGraph to handle different types of user requests:

**Q&A Agent**: Answers specific questions about document content
**Summarization Agent**: Creates summaries and extracts key points from documents
**Calculation Agent**: Performs mathematical operations on document data

The flow for the assistant is shown in '*langgraph_agent_architecture.png*'.

## Application Invocation 

Upon invocation of the the user is first asked for the user id which is used to keep track of all the conversation with the user. This user id is tagged to session. 

The user than input the request.

## Intent Classifcation
The user request is then classified for intent using the function *'classify_intent'* and categorized as one of the following intents. The classification is done using LLM.
- qa
- summarization 
- calculation

Based on the intent, the request is then routed to one of following the relevant agents using AgentState for data communication.
- qa agent 
- summarization agent
- calculation agent

## Agent Processing
On receipt of the routed request, each of the agent processes the request from the customer.

### qa agent
- Loads the prompt *QA_SYSTEM_PROMPT*
- Loads tools
- Invokes LLM to handle Q&A and record action
- The response is obtained with *AnswerResponse*

### summarization agent
- Loads the prompt *SUMMARIZATION_SYSTEM_PROMPT*
- Loads tools
- Invokes LLM to handle summarization request and record action
- The response is obtained with *SummarizationResponse*

### calculation agent
- Loads the prompt *CALCULATION_SYSTEM_PROMPT*
- Loads tools
- Invokes LLM to handle Q&A and record action
- The response is obtained with *CalculationResponse*

Each of the above agents passes the baton on to *update_memory* function to update conversation memory and record the action

## Workflow

### Nodes
Workflow is defined using Langgraph with the follwoing nodes
- classify_intent
- qa_agent
- summarization_agent
- calculation_agent
- update_memory

### Graph
The structure of the graph is drawn as below - 
classify_intent --> [qa_agent|summarization_agent|calculation_agent] --> update_memory --> END

### Memory Saver
Memory is saved with Langgraph's InMemorySaver

## Application Testing
The application has been tested with different requests -
### Request 1 - Summarize all contracts
**Assistant Response**

🤖 Assistant: ### Contract Summary

**Document ID:** CON-001
**Title:** Service Agreement
**Date:** January 1, 2024
**Parties Involved:**
- **Provider:** DocDacity Solutions Inc.
- **Client:** Healthcare Partners LLC

**Services Provided:**
1. Document Processing Platform Access
2. 24/7 Technical Support
3. Monthly Data Analytics Reports
4. Compliance Monitoring

**Financial Details:**
- **Monthly Fee:** $15,000
- **Total Contract Value:** $180,000

**Duration:** 12 months

**Termination Clause:**
- Either party may terminate the agreement with 60 days written notice.

This contract outlines a comprehensive service agreement between DocDacity Solutions Inc. and Healthcare Partners LLC, focusing on document processing and support services over a year-long period.

INTENT: summarization

TOOLS USED: document_search, document_reader

CONVERSATION SUMMARY: The conversation involved a request to summarize all contracts. The relevant document found was a Service Agreement (ID: CON-001) between DocDacity Solutions Inc. and Healthcare Partners LLC, dated January 1, 2024. The contract includes services such as document processing platform access, 24/7 technical support, monthly data analytics reports, and compliance monitoring. The agreement is valued at $180,000 over 12 months, with a monthly fee of $15,000. It includes a termination clause allowing either party to terminate with 60 days' notice.

### Request 2 - What's the total amount in invoice INV-001?
**Assistant Response**

🤖 Assistant: The total amount in Invoice INV-001 is $22,000. This includes a subtotal of $20,000 for services rendered and a tax of $2,000.

INTENT: calculation

TOOLS USED: document_search, document_reader, document_search, document_reader

CONVERSATION SUMMARY: The total amount in Invoice INV-001 is $22,000. This includes a subtotal of $20,000 for services rendered and a tax of $2,000.

### Request 3 - Calculate the sum of all invoice totals
**Assistant Response**
🤖 Assistant: The sum of all invoice totals is $305,800.

INTENT: calculation

TOOLS USED: document_search, document_reader, document_search, document_reader, document_reader, document_reader, document_reader, calculator

CONVERSATION SUMMARY: The total amount in Invoice INV-001 is $22,000, which includes a subtotal of $20,000 for services rendered and a tax of $2,000. The sum of all invoice totals, including INV-001, INV-002, and INV-003, is $305,800.


### Request 4 - Find documents with amounts over $50,000
**Assistant Response**

🤖 Assistant: The document with an amount over $50,000 is:

- **Document ID:** INV-002
  - **Title:** Invoice #12346
  - **Type:** Invoice
  - **Amount:** $69,300.00
  - **Date:** 2024-02-20
  - **Client:** TechStart Inc.
  - **Details:** Includes products such as an Enterprise License (Annual) and services like Implementation and Training.

INTENT: calculation

TOOLS USED: document_search, document_reader, document_search, document_reader, document_reader, document_reader, document_reader, calculator, document_search

CONVERSATION SUMMARY: The document with an amount over $50,000 is:

- **Document ID:** INV-002
  - **Title:** Invoice #12346
  - **Type:** Invoice
  - **Amount:** $69,300.00
  - **Date:** 2024-02-20
  - **Client:** TechStart Inc.
  - **Details:** Includes products such as an Enterprise License (Annual) and services like Implementation and Training.