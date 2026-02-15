# JanSetu: AI-Powered Eligibility Intelligence Engine
## Requirements Document

---

## 1. Project Overview

### The Problem

India has thousands of government welfare schemes, but citizens face critical barriers:

- **Discovery Gap**: Citizens cannot find relevant schemes among thousands of programs
- **Hidden Rules**: Eligibility criteria are buried in lengthy PDF documents
- **Manual Confusion**: Verification processes are unclear and time-consuming
- **Low Awareness**: Lack of structured information leads to poor scheme utilization
- **Access Inequality**: Complex documentation excludes those who need help most

### The Solution

JanSetu is an AI-powered eligibility intelligence engine that:

1. Ingests government scheme PDF documents
2. Extracts eligibility rules using AI (OCR + LLM)
3. Converts unstructured text into machine-readable rules
4. Calculates explainable eligibility scores for citizens
5. Provides analytics dashboards for administrators

**JanSetu is NOT a chatbot.** It is a structured AI eligibility intelligence system that converts government scheme documents into machine-readable rules and produces explainable scoring for citizens and administrators.

---

## 2. Objectives

- Simplify scheme discovery for citizens through intelligent matching
- Automate eligibility rule extraction from PDF documents
- Provide explainable scoring with clear reasoning
- Enable administrators to upload and manage schemes easily
- Deliver analytics for government and NGO decision-makers
- Improve access to welfare programs across India

---

## 3. Demo Flow

**Step-by-step demonstration for judges:**

1. **Admin Upload**: Administrator uploads a government scheme PDF (e.g., PM-KISAN)
2. **AI Extraction**: System uses OCR + LLM to extract eligibility criteria from document
3. **Rule Structuring**: Extracted rules are converted to structured format and stored
4. **Citizen Input**: Citizen enters their profile (age, income, location, occupation, etc.)
5. **Score Calculation**: Eligibility engine calculates match score (0-100%) for each scheme
6. **Explainability**: Results panel shows why citizen qualifies or doesn't qualify
7. **Analytics Dashboard**: Admin views scheme utilization, eligibility trends, and insights

---

## 4. Users

### Citizen
End-users seeking government schemes. They input their profile and receive personalized eligibility scores with explanations.

### Administrator
Government officials or NGO staff who upload scheme documents and manage the system.

### Government/NGO Analyst
Decision-makers who use analytics dashboards to understand scheme reach, gaps, and utilization patterns.

---

## 5. Functional Requirements

### FR1: Document Upload System
- Admin can upload PDF documents of government schemes
- System validates file format and size
- Documents stored securely in cloud storage

### FR2: AI Eligibility Extraction
- OCR extracts text from PDF documents
- LLM identifies and extracts eligibility criteria
- System handles multi-page documents and tables

### FR3: Rule Structuring Engine
- Converts extracted text into structured rule format
- Stores rules with conditions (age, income, location, occupation, etc.)
- Maintains scheme metadata (name, department, benefits, deadlines)

### FR4: Citizen Profile Input
- Simple form for citizens to enter personal information
- Fields: age, gender, income, location, occupation, family size, etc.
- Profile saved for future scheme matching

### FR5: Eligibility Scoring Engine
- Matches citizen profile against structured scheme rules
- Calculates eligibility score (0-100%) for each scheme
- Ranks schemes by relevance to citizen

### FR6: Explainable Results Panel
- Shows why citizen qualifies or doesn't qualify
- Highlights matching criteria and missing requirements
- Provides actionable next steps

### FR7: Admin Analytics Dashboard
- Displays scheme upload history
- Shows eligibility trends and user demographics
- Provides insights on scheme reach and gaps

---

## 6. Non-Functional Requirements

### Usability
- Intuitive interface for non-technical users
- Mobile-responsive design
- Support for English (Hindi for future scope)

### Scalability
- Handle 100+ schemes in MVP
- Support concurrent users during demo

### Reliability
- 95%+ uptime during hackathon demo
- Graceful error handling for failed extractions

### Performance
- PDF processing within 30-60 seconds
- Eligibility scoring in under 2 seconds
- Dashboard loads within 3 seconds

### Security
- Secure document storage
- Basic authentication for admin panel
- No PII exposure in logs

### Explainability
- Clear reasoning for every eligibility decision
- Transparent rule display
- Audit trail for extracted rules

---

## 7. AI Justification

AI is essential for JanSetu because:

### Document Understanding
- **OCR**: Extracts text from scanned PDFs and images
- **LLM**: Understands context and identifies eligibility criteria from unstructured text
- **Structured Conversion**: Transforms natural language rules into machine-readable format

### Intelligent Extraction
- Handles variations in document formats across departments
- Identifies implicit eligibility conditions
- Extracts tables, lists, and nested criteria

### Explainable Scoring
- AI-powered reasoning explains eligibility decisions
- Generates human-readable explanations from structured rules

**AI is used for document understanding and rule extraction, NOT for chat or conversation.**

---

## 8. AWS Stack (Hackathon-Friendly)

### Architecture Components

- **Amazon S3**: Document storage for uploaded PDFs
- **AWS Textract**: OCR for text extraction from PDFs
- **Amazon Bedrock**: LLM (Claude/Titan) for eligibility rule extraction
- **AWS Lambda**: Serverless processing for extraction pipeline
- **Amazon DynamoDB**: NoSQL storage for structured rules and profiles
- **API Gateway**: REST API backend for frontend
- **Simple Frontend**: React/HTML dashboard for demo

### Data Flow
1. Admin uploads PDF → S3
2. Lambda triggers Textract → extracts text
3. Lambda calls Bedrock → LLM extracts rules
4. Structured rules → DynamoDB
5. Citizen profile → scoring engine → results
6. Analytics queries → DynamoDB → dashboard

---

## 9. Hackathon MVP Scope

**For the hackathon prototype, JanSetu will include:**

- **5 Sample Schemes**: Pre-loaded schemes (PM-KISAN, Ayushman Bharat, etc.)
- **Automated Extraction**: Full pipeline from PDF to structured rules
- **Scoring Engine**: Working eligibility calculator with explainability
- **Explainability Panel**: Clear reasoning for eligibility decisions
- **Admin Upload Panel**: Interface to upload and process new schemes
- **Simple Analytics Dashboard**: Basic metrics on schemes and users

**Out of Scope for MVP:**
- Mobile app
- Multi-language support
- Real-time government API integration
- Advanced analytics (ML predictions)

---

## 10. Future Scope

### Post-Hackathon Enhancements

- **Multilingual Support**: Hindi, Tamil, Bengali, and other regional languages
- **Mobile Application**: Native iOS/Android apps for wider reach
- **Government Integration**: Direct API connections to scheme databases
- **SMS/WhatsApp Notifications**: Alerts for new matching schemes
- **Advanced Analytics**: ML-based predictions for scheme utilization

---

## Summary

JanSetu addresses a critical gap in government welfare access by using AI to convert unstructured scheme documents into structured, explainable eligibility intelligence. The system is designed as a practical, buildable MVP for the hackathon that demonstrates real value for citizens and administrators.
