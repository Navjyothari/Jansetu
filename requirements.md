# JanSetu – Public Scheme Access Intelligence Engine

## Requirements Document v1.0

---

## 1. Project Overview

**JanSetu** is an AI-powered eligibility intelligence system designed to bridge the gap between citizens and government welfare schemes. The system converts unstructured government scheme documents into structured, machine-readable eligibility logic and matches citizens to relevant schemes using explainable scoring mechanisms.

Unlike traditional chatbot interfaces, JanSetu is a structured intelligence platform featuring dashboards, analytics, and transparent decision-making processes that help citizens, NGOs, and government agencies efficiently identify and access applicable welfare programs.

---

## 2. Objectives

- **Automate Scheme Discovery**: Eliminate manual searching through hundreds of government schemes
- **Democratize Access**: Enable citizens, especially from underserved communities, to discover eligible schemes
- **Ensure Transparency**: Provide explainable AI-driven recommendations with clear eligibility reasoning
- **Support Decision Makers**: Equip NGOs, social workers, and policymakers with analytics and insights
- **Reduce Administrative Burden**: Streamline scheme awareness and application processes

---

## 3. Scope

### In Scope
- Document ingestion and processing (PDFs, web pages, scheme documents)
- OCR and text extraction from scanned documents
- AI-based extraction of eligibility criteria
- Structured rule engine for scheme logic
- Citizen profile matching and ranking
- Explainable scoring system ("Why am I eligible?")
- Analytics dashboard with scheme statistics
- Admin panel for scheme management
- Cloud-native deployment on AWS

### Out of Scope
- Direct application submission to government portals
- Payment processing or financial transactions
- Real-time government database integration
- Mobile native applications (Phase 1)
- Multi-language support (Phase 1)
- Chatbot or conversational interface

---

## 4. Target Users

### Primary Users
1. **Citizens**: Individuals seeking government welfare schemes they qualify for
2. **NGOs and Social Workers**: Organizations helping communities access benefits
3. **Government Agencies**: Departments monitoring scheme reach and effectiveness

### Secondary Users
4. **Policy Analysts**: Researchers studying scheme coverage and gaps
5. **System Administrators**: Technical staff managing the platform

---

## 5. Core Features

### 5.1 Document Intelligence
- Upload scheme documents (PDF, DOCX, web URLs)
- OCR processing for scanned documents
- Text extraction and preprocessing
- AI-powered eligibility criteria extraction

### 5.2 Eligibility Logic Engine
- Convert natural language criteria into structured JSON rules
- Support for multiple criteria types:
  - Demographics (age, gender, caste, religion)
  - Economic (income, BPL status, occupation)
  - Geographic (state, district, urban/rural)
  - Special categories (disability, widow, farmer, student)
- Rule validation and conflict detection

### 5.3 Citizen Matching System
- Profile input interface for citizens
- Multi-criteria matching algorithm
- Explainable scoring (0-100 scale)
- Ranked scheme recommendations
- Eligibility explanation generator

### 5.4 Analytics Dashboard
- Total schemes processed
- User engagement metrics
- Scheme category distribution
- Geographic coverage analysis
- Eligibility trends and insights

### 5.5 Administration Panel
- Scheme upload and management
- Document processing status tracking
- User management
- System health monitoring
- Audit logs

---

## 6. Functional Requirements

### FR1: Document Ingestion
- **FR1.1**: System shall accept PDF, DOCX, and web URL inputs
- **FR1.2**: System shall perform OCR on scanned documents with >90% accuracy
- **FR1.3**: System shall extract text and metadata from documents
- **FR1.4**: System shall store original documents in cloud storage

### FR2: Eligibility Extraction
- **FR2.1**: System shall use LLM to identify eligibility criteria from text
- **FR2.2**: System shall extract structured fields: age range, income limits, location, categories
- **FR2.3**: System shall convert criteria into JSON rule format
- **FR2.4**: System shall validate extracted rules for completeness

### FR3: Scheme Storage
- **FR3.1**: System shall store schemes with metadata (name, department, benefits, deadlines)
- **FR3.2**: System shall maintain version history of scheme rules
- **FR3.3**: System shall support scheme categorization (education, health, agriculture, etc.)
- **FR3.4**: System shall enable search and filtering of schemes

### FR4: Citizen Profile Matching
- **FR4.1**: System shall accept citizen profile input (age, income, location, category, etc.)
- **FR4.2**: System shall match profile against all active schemes
- **FR4.3**: System shall calculate eligibility score (0-100) for each scheme
- **FR4.4**: System shall rank schemes by relevance and eligibility
- **FR4.5**: System shall complete matching within 5 seconds

### FR5: Explainability
- **FR5.1**: System shall generate human-readable explanations for each match
- **FR5.2**: System shall highlight which criteria were met/not met
- **FR5.3**: System shall show confidence scores for AI-extracted rules
- **FR5.4**: System shall provide "Why not eligible" explanations for near-misses

### FR6: Dashboard and Analytics
- **FR6.1**: System shall display total schemes, users, and matches
- **FR6.2**: System shall visualize scheme distribution by category and geography
- **FR6.3**: System shall show trending schemes and popular searches
- **FR6.4**: System shall generate downloadable reports

### FR7: Admin Functions
- **FR7.1**: System shall provide secure admin authentication
- **FR7.2**: System shall allow bulk scheme uploads
- **FR7.3**: System shall display document processing status
- **FR7.4**: System shall enable manual rule editing and override
- **FR7.5**: System shall maintain audit logs of all admin actions

---

## 7. Non-Functional Requirements

### NFR1: Performance
- **NFR1.1**: Scheme matching shall complete within 5 seconds for typical profiles
- **NFR1.2**: Document processing shall handle files up to 50MB
- **NFR1.3**: System shall support 1000 concurrent users
- **NFR1.4**: Dashboard shall load within 2 seconds

### NFR2: Scalability
- **NFR2.1**: Architecture shall support horizontal scaling
- **NFR2.2**: System shall handle 10,000+ schemes in database
- **NFR2.3**: Storage shall scale automatically with demand

### NFR3: Reliability
- **NFR3.1**: System uptime shall be 99.5% or higher
- **NFR3.2**: Data shall be backed up daily
- **NFR3.3**: Failed document processing shall retry automatically

### NFR4: Security
- **NFR4.1**: All data transmission shall use HTTPS/TLS
- **NFR4.2**: User data shall be encrypted at rest
- **NFR4.3**: Admin access shall require multi-factor authentication
- **NFR4.4**: System shall comply with data privacy regulations
- **NFR4.5**: PII shall be anonymized in analytics

### NFR5: Usability
- **NFR5.1**: UI shall be intuitive for users with basic digital literacy
- **NFR5.2**: Forms shall provide clear validation messages
- **NFR5.3**: System shall be accessible on desktop and tablet devices
- **NFR5.4**: Interface shall follow WCAG 2.1 Level AA guidelines

### NFR6: Maintainability
- **NFR6.1**: Code shall follow modular microservices architecture
- **NFR6.2**: APIs shall be versioned and documented
- **NFR6.3**: System shall include comprehensive logging
- **NFR6.4**: Infrastructure shall be defined as code (IaC)

### NFR7: Explainability and Transparency
- **NFR7.1**: AI decisions shall be traceable and auditable
- **NFR7.2**: Confidence scores shall be displayed for AI-extracted rules
- **NFR7.3**: System shall provide clear reasoning for all recommendations

---

## 8. Technology Stack

### Frontend
- **Framework**: React 18+
- **UI Library**: Material-UI / Tailwind CSS
- **State Management**: Redux / Context API
- **Visualization**: Chart.js / Recharts

### Backend
- **API Layer**: Node.js (Express) / Python (FastAPI)
- **Rule Engine**: Custom JSON-based logic processor
- **Authentication**: JWT / AWS Cognito

### AI/ML
- **LLM**: AWS Bedrock (Claude/Titan) / OpenAI API
- **OCR**: AWS Textract
- **Text Processing**: LangChain / Custom NLP pipelines

### Data Storage
- **Primary Database**: PostgreSQL (structured scheme data)
- **Document Store**: AWS S3
- **Cache**: Redis (optional for performance)
- **Alternative**: DynamoDB for NoSQL approach

### Cloud Infrastructure (AWS)
- **Compute**: AWS Lambda (serverless functions) / ECS (containers)
- **Storage**: S3 (documents), RDS (PostgreSQL)
- **AI Services**: Bedrock, Textract
- **API Gateway**: AWS API Gateway
- **Monitoring**: CloudWatch
- **IaC**: AWS CDK / Terraform

### DevOps
- **CI/CD**: GitHub Actions / AWS CodePipeline
- **Containerization**: Docker
- **Orchestration**: AWS ECS / Fargate

---

## 9. Assumptions and Constraints

### Assumptions
- Government scheme documents are publicly available
- Eligibility criteria are clearly stated in source documents
- Users have basic digital literacy and internet access
- AWS services are available and within budget
- LLM APIs provide sufficient accuracy for criteria extraction

### Constraints
- Budget limitations for cloud services and API usage
- Time constraint for hackathon/prototype development
- Limited access to real-time government databases
- No direct integration with government application portals
- English-only support in initial version

---

## 10. Future Scope

### Phase 2 Enhancements
- **Multi-language Support**: Hindi, regional languages
- **Mobile Applications**: Native iOS and Android apps
- **Advanced AI**: Fine-tuned models for scheme-specific extraction
- **Government Integration**: Direct API connections to official portals
- **Application Assistance**: Guided form filling and document preparation
- **Notification System**: Alerts for new schemes and deadlines
- **Community Features**: User reviews, success stories, Q&A forums
- **Offline Mode**: Progressive Web App with offline capabilities
- **Voice Interface**: Voice-based profile input for accessibility
- **Blockchain**: Immutable audit trail for scheme eligibility verification

### Long-term Vision
- Expand to other countries and welfare systems
- Partner with government agencies for official adoption
- Integrate with Aadhaar and DigiLocker for automated profile creation
- Develop predictive analytics for scheme impact assessment
- Create API marketplace for third-party integrations

---

## Document Control

| Version | Date | Author | Changes |
|---------|------|--------|---------|
| 1.0 | 2026-02-14 | Project Team | Initial requirements document |

---

**Document Status**: Draft for Review  
**Next Review Date**: TBD  
**Approval Required From**: Project Stakeholders, Technical Lead

---

*This document serves as the foundation for the JanSetu project and should be reviewed and updated as the project evolves.*
