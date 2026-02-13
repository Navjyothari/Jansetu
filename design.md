# JanSetu – System Design Document

## Design Document v1.0

---

## 1. System Architecture Overview

JanSetu is a cloud-native, event-driven AI intelligence system built on AWS that transforms unstructured government scheme documents into structured eligibility logic and matches citizens with relevant schemes through explainable scoring mechanisms.

### High-Level Architecture

```
┌─────────────────────────────────────────────────────────────────────┐
│                         JanSetu Platform                             │
├─────────────────────────────────────────────────────────────────────┤
│                                                                       │
│  ┌──────────────┐      ┌──────────────┐      ┌──────────────┐      │
│  │   Admin      │      │   Citizen    │      │  Analytics   │      │
│  │   Portal     │      │   Portal     │      │  Dashboard   │      │
│  └──────┬───────┘      └──────┬───────┘      └──────┬───────┘      │
│         │                     │                     │               │
│         └─────────────────────┼─────────────────────┘               │
│                               │                                     │
│                    ┌──────────▼──────────┐                          │
│                    │   API Gateway       │                          │
│                    └──────────┬──────────┘                          │
│                               │                                     │
├───────────────────────────────┼─────────────────────────────────────┤
│                               │                                     │
│  ┌────────────────────────────▼──────────────────────────────┐     │
│  │              Backend Services Layer                        │     │
│  │                                                             │     │
│  │  ┌─────────────┐  ┌─────────────┐  ┌──────────────┐      │     │
│  │  │  Document   │  │  Matching   │  │  Analytics   │      │     │
│  │  │  Ingestion  │  │  Engine     │  │  Service     │      │     │
│  │  └─────────────┘  └─────────────┘  └──────────────┘      │     │
│  │                                                             │     │
│  │  ┌─────────────┐  ┌─────────────┐  ┌──────────────┐      │     │
│  │  │  OCR        │  │  Explain-   │  │  Admin       │      │     │
│  │  │  Service    │  │  ability    │  │  Service     │      │     │
│  │  └─────────────┘  └─────────────┘  └──────────────┘      │     │
│  └─────────────────────────────────────────────────────────┘     │
│                               │                                     │
├───────────────────────────────┼─────────────────────────────────────┤
│                               │                                     │
│  ┌────────────────────────────▼──────────────────────────────┐     │
│  │              AI/ML Processing Layer                        │     │
│  │                                                             │     │
│  │  ┌─────────────┐  ┌─────────────┐  ┌──────────────┐      │     │
│  │  │  AWS        │  │  AWS        │  │  Rule        │      │     │
│  │  │  Textract   │  │  Bedrock    │  │  Structuring │      │     │
│  │  │  (OCR)      │  │  (LLM)      │  │  Engine      │      │     │
│  │  └─────────────┘  └─────────────┘  └──────────────┘      │     │
│  └─────────────────────────────────────────────────────────┘     │
│                               │                                     │
├───────────────────────────────┼─────────────────────────────────────┤
│                               │                                     │
│  ┌────────────────────────────▼──────────────────────────────┐     │
│  │              Data Layer                                    │     │
│  │                                                             │     │
│  │  ┌─────────────┐  ┌─────────────┐  ┌──────────────┐      │     │
│  │  │  PostgreSQL │  │  AWS S3     │  │  Redis       │      │     │
│  │  │  (Schemes)  │  │  (Docs)     │  │  (Cache)     │      │     │
│  │  └─────────────┘  └─────────────┘  └──────────────┘      │     │
│  └─────────────────────────────────────────────────────────┘     │
│                                                                       │
└─────────────────────────────────────────────────────────────────────┘
```

### Architecture Principles
- **Modular**: Independent microservices for each function
- **Event-Driven**: Asynchronous processing using queues
- **Serverless-First**: AWS Lambda for compute efficiency
- **Scalable**: Horizontal scaling with managed services
- **Explainable**: Transparent AI decision-making

---

## 2. Component Architecture


### 2.1 Document Ingestion Service

**Purpose**: Accept and validate scheme documents from admin users

**Technology**: Node.js Lambda + S3

**Responsibilities**:
- Accept PDF, DOCX, and URL inputs
- Validate file format and size (<50MB)
- Upload to S3 with metadata
- Trigger OCR processing pipeline
- Track document processing status

**Inputs**: Document file, metadata (scheme name, department, category)

**Outputs**: S3 object key, processing job ID

---

### 2.2 OCR Processing Service

**Purpose**: Extract text from scanned and digital documents

**Technology**: AWS Textract + Lambda

**Responsibilities**:
- Detect document type (scanned vs digital)
- Perform OCR on scanned documents
- Extract text with layout preservation
- Handle multi-page documents
- Store extracted text in S3

**Inputs**: S3 document reference

**Outputs**: Extracted text (JSON format with structure)

---

### 2.3 Eligibility Extraction AI Service

**Purpose**: Use LLM to identify and extract eligibility criteria from text

**Technology**: AWS Bedrock (Claude/Titan) + Lambda

**Responsibilities**:
- Analyze extracted text using LLM
- Identify eligibility criteria sections
- Extract structured criteria fields
- Classify criteria types (age, income, location, etc.)
- Generate confidence scores

**Inputs**: Extracted text from OCR

**Outputs**: Raw eligibility criteria (structured JSON)

---

### 2.4 Rule Structuring Engine

**Purpose**: Convert natural language criteria into machine-readable rules

**Technology**: Python Lambda + Custom Logic

**Responsibilities**:
- Parse LLM output into standardized format
- Create JSON rule objects
- Validate rule completeness
- Handle edge cases and ambiguities
- Store rules in database

**Inputs**: Raw eligibility criteria

**Outputs**: Structured rule JSON

---

### 2.5 Scheme Database

**Purpose**: Store and manage scheme information and rules

**Technology**: PostgreSQL (RDS) or DynamoDB

**Responsibilities**:
- Store scheme metadata
- Store eligibility rules
- Version control for rule updates
- Enable search and filtering
- Maintain audit logs

**Schema**: See Section 5 (Data Models)

---

### 2.6 Citizen Profile Service

**Purpose**: Accept and validate citizen profile information

**Technology**: Node.js/Python Lambda

**Responsibilities**:
- Accept citizen profile input
- Validate and sanitize data
- Store profiles (optional, with consent)
- Anonymize data for analytics
- Handle profile updates

**Inputs**: Citizen profile form data

**Outputs**: Validated profile object

---

### 2.7 Matching Engine

**Purpose**: Match citizen profiles against scheme eligibility rules

**Technology**: Python Lambda + Custom Algorithm

**Responsibilities**:
- Retrieve all active schemes
- Evaluate profile against each rule
- Calculate eligibility scores (0-100)
- Rank schemes by relevance
- Generate match metadata

**Inputs**: Citizen profile

**Outputs**: Ranked list of schemes with scores

**Performance Target**: <5 seconds for 10,000 schemes

---

### 2.8 Explainability Engine

**Purpose**: Generate human-readable explanations for matches

**Technology**: Python Lambda + Template Engine

**Responsibilities**:
- Analyze which criteria were met/not met
- Generate natural language explanations
- Highlight key matching factors
- Provide "why not eligible" reasoning
- Include confidence indicators

**Inputs**: Match results, rules, profile

**Outputs**: Explanation text for each scheme

---

### 2.9 Analytics Dashboard

**Purpose**: Visualize system metrics and insights

**Technology**: React + Chart.js + Backend API

**Responsibilities**:
- Display scheme statistics
- Show user engagement metrics
- Visualize geographic coverage
- Track processing pipeline health
- Generate downloadable reports

**Data Sources**: PostgreSQL aggregations, CloudWatch metrics

---

### 2.10 Admin Panel

**Purpose**: Manage schemes and monitor system

**Technology**: React + Admin API

**Responsibilities**:
- Upload scheme documents
- View processing status
- Edit extracted rules manually
- Manage user access
- View audit logs
- Monitor system health

**Authentication**: AWS Cognito with MFA

---

## 3. Data Flow

### 3.1 Document Processing Flow

```
Step 1: Admin uploads scheme document
   ↓
Step 2: Document Ingestion Service validates and uploads to S3
   ↓
Step 3: S3 event triggers OCR Processing Service
   ↓
Step 4: AWS Textract extracts text from document
   ↓
Step 5: Extracted text stored in S3, triggers AI Service
   ↓
Step 6: AWS Bedrock LLM analyzes text and extracts criteria
   ↓
Step 7: Rule Structuring Engine converts to JSON rules
   ↓
Step 8: Rules validated and stored in PostgreSQL
   ↓
Step 9: Admin notified of completion (or errors)
```

### 3.2 Citizen Matching Flow

```
Step 1: Citizen enters profile information
   ↓
Step 2: Profile Service validates input
   ↓
Step 3: Matching Engine retrieves all active schemes
   ↓
Step 4: For each scheme, evaluate profile against rules
   ↓
Step 5: Calculate eligibility score (0-100)
   ↓
Step 6: Rank schemes by score
   ↓
Step 7: Explainability Engine generates explanations
   ↓
Step 8: Results returned to frontend
   ↓
Step 9: Display ranked schemes with explanations
```

### 3.3 Analytics Flow

```
Step 1: User actions logged to database
   ↓
Step 2: Scheduled Lambda aggregates data daily
   ↓
Step 3: Aggregations stored in analytics tables
   ↓
Step 4: Dashboard queries aggregated data
   ↓
Step 5: Visualizations rendered in real-time
```

---

## 4. Data Models

### 4.1 Scheme Object

```json
{
  "scheme_id": "uuid",
  "name": "Pradhan Mantri Awas Yojana",
  "department": "Ministry of Housing and Urban Affairs",
  "category": "housing",
  "description": "Housing for all scheme",
  "benefits": "Financial assistance for house construction",
  "application_url": "https://pmaymis.gov.in",
  "deadline": "2026-12-31",
  "status": "active",
  "created_at": "2026-02-14T10:00:00Z",
  "updated_at": "2026-02-14T10:00:00Z",
  "document_url": "s3://jansetu/schemes/pmay.pdf",
  "version": 1
}
```

### 4.2 Eligibility Rule JSON

```json
{
  "rule_id": "uuid",
  "scheme_id": "uuid",
  "rules": {
    "age": {
      "min": 18,
      "max": 70,
      "required": true
    },
    "income": {
      "max_annual": 600000,
      "currency": "INR",
      "required": true
    },
    "location": {
      "states": ["all"],
      "urban_rural": ["urban", "rural"],
      "required": true
    },
    "categories": {
      "allowed": ["EWS", "LIG", "MIG"],
      "required": false
    },
    "gender": {
      "allowed": ["all"],
      "required": false
    },
    "ownership": {
      "must_not_own_house": true,
      "required": true
    }
  },
  "confidence_score": 0.92,
  "extracted_at": "2026-02-14T10:30:00Z",
  "validated": true
}
```

### 4.3 Citizen Profile

```json
{
  "profile_id": "uuid",
  "age": 32,
  "gender": "female",
  "annual_income": 450000,
  "state": "Maharashtra",
  "district": "Mumbai",
  "location_type": "urban",
  "category": "EWS",
  "occupation": "daily_wage_worker",
  "owns_house": false,
  "disability": false,
  "marital_status": "married",
  "dependents": 2,
  "created_at": "2026-02-14T11:00:00Z"
}
```

### 4.4 Match Score Output

```json
{
  "match_id": "uuid",
  "profile_id": "uuid",
  "scheme_id": "uuid",
  "scheme_name": "Pradhan Mantri Awas Yojana",
  "eligibility_score": 95,
  "matched_criteria": [
    "age",
    "income",
    "location",
    "category",
    "ownership"
  ],
  "unmatched_criteria": [],
  "explanation": {
    "summary": "You are highly eligible for this scheme",
    "details": [
      "✓ Age requirement met (18-70 years)",
      "✓ Income within limit (< ₹6,00,000)",
      "✓ Location eligible (Urban Maharashtra)",
      "✓ Category matches (EWS)",
      "✓ Does not own a house"
    ],
    "next_steps": "Visit the application portal to apply"
  },
  "confidence": 0.92,
  "matched_at": "2026-02-14T11:05:00Z"
}
```

---

## 5. AI/ML Design

### 5.1 LLM-Based Eligibility Extraction

**Model**: AWS Bedrock (Claude 3 or Titan)

**Approach**: Few-shot prompting with structured output

**Prompt Structure**:

```
You are an AI assistant that extracts eligibility criteria from government scheme documents.

Given the following scheme document text, extract all eligibility criteria in structured JSON format.

Document Text:
{extracted_text}

Extract the following fields:
- Age requirements (min, max)
- Income limits (annual income in INR)
- Geographic restrictions (states, districts, urban/rural)
- Category requirements (caste, religion, occupation)
- Gender requirements
- Special conditions (disability, widow, farmer, student, etc.)
- Any other eligibility criteria

Output Format:
{
  "age": {"min": X, "max": Y},
  "income": {"max_annual": Z},
  "location": {...},
  ...
}

Be precise and extract only explicitly stated criteria. If a criterion is not mentioned, omit it.
```

### 5.2 Rule Conversion Logic

**Process**:
1. Parse LLM JSON output
2. Validate field types and ranges
3. Normalize values (e.g., "18 years" → 18)
4. Handle ambiguities with default values
5. Flag low-confidence extractions for manual review

### 5.3 Explainable Scoring Algorithm

**Scoring Formula**:

```
Eligibility Score = (Matched Criteria / Total Required Criteria) × 100

Weighted Score = Σ(criterion_weight × match_status)
```

**Match Status**:
- 1.0 = Fully met
- 0.5 = Partially met
- 0.0 = Not met

**Explanation Generation**:
- For each criterion, generate human-readable statement
- Use templates: "✓ Age requirement met (18-70 years)"
- Highlight missing criteria for near-misses
- Provide actionable next steps

### 5.4 Confidence Scoring

**Factors**:
- LLM output consistency
- Presence of explicit criteria in text
- Rule completeness
- Historical validation accuracy

**Threshold**: Rules with confidence <0.7 flagged for manual review

---

## 6. API Design

### 6.1 Scheme Management APIs

**POST /api/schemes/upload**
- Upload scheme document
- Request: multipart/form-data (file + metadata)
- Response: `{ "job_id": "uuid", "status": "processing" }`

**GET /api/schemes**
- List all schemes with filters
- Query params: category, state, status
- Response: Array of scheme objects

**GET /api/schemes/:id**
- Get scheme details
- Response: Scheme object with rules

**PUT /api/schemes/:id/rules**
- Manually edit extracted rules (admin only)
- Request: Updated rule JSON
- Response: Updated scheme object

**GET /api/schemes/:id/status**
- Check document processing status
- Response: `{ "status": "completed", "progress": 100 }`

### 6.2 Matching APIs

**POST /api/match**
- Match citizen profile to schemes
- Request: Citizen profile JSON
- Response: Array of match results with scores

**GET /api/match/:match_id/explanation**
- Get detailed explanation for a match
- Response: Explanation object

### 6.3 Analytics APIs

**GET /api/analytics/dashboard**
- Get dashboard statistics
- Response: Aggregated metrics

**GET /api/analytics/schemes/distribution**
- Get scheme distribution by category
- Response: Chart data

**GET /api/analytics/coverage**
- Get geographic coverage data
- Response: State-wise scheme counts

### 6.4 Admin APIs

**POST /api/admin/login**
- Admin authentication
- Request: Credentials
- Response: JWT token

**GET /api/admin/logs**
- Get audit logs
- Query params: date_range, action_type
- Response: Array of log entries

---

## 7. Database Design

### 7.1 PostgreSQL Schema

**Table: schemes**
```sql
CREATE TABLE schemes (
  scheme_id UUID PRIMARY KEY,
  name VARCHAR(255) NOT NULL,
  department VARCHAR(255),
  category VARCHAR(100),
  description TEXT,
  benefits TEXT,
  application_url VARCHAR(500),
  deadline DATE,
  status VARCHAR(50),
  document_url VARCHAR(500),
  version INTEGER,
  created_at TIMESTAMP,
  updated_at TIMESTAMP
);
```

**Table: eligibility_rules**
```sql
CREATE TABLE eligibility_rules (
  rule_id UUID PRIMARY KEY,
  scheme_id UUID REFERENCES schemes(scheme_id),
  rules JSONB NOT NULL,
  confidence_score DECIMAL(3,2),
  extracted_at TIMESTAMP,
  validated BOOLEAN,
  validator_id UUID
);
```

**Table: citizen_profiles** (optional, with consent)
```sql
CREATE TABLE citizen_profiles (
  profile_id UUID PRIMARY KEY,
  age INTEGER,
  gender VARCHAR(50),
  annual_income INTEGER,
  state VARCHAR(100),
  district VARCHAR(100),
  location_type VARCHAR(50),
  category VARCHAR(100),
  created_at TIMESTAMP,
  anonymized BOOLEAN DEFAULT true
);
```

**Table: matches**
```sql
CREATE TABLE matches (
  match_id UUID PRIMARY KEY,
  profile_id UUID,
  scheme_id UUID REFERENCES schemes(scheme_id),
  eligibility_score INTEGER,
  matched_criteria JSONB,
  explanation JSONB,
  matched_at TIMESTAMP
);
```

**Table: processing_jobs**
```sql
CREATE TABLE processing_jobs (
  job_id UUID PRIMARY KEY,
  scheme_id UUID REFERENCES schemes(scheme_id),
  status VARCHAR(50),
  progress INTEGER,
  error_message TEXT,
  started_at TIMESTAMP,
  completed_at TIMESTAMP
);
```

**Table: audit_logs**
```sql
CREATE TABLE audit_logs (
  log_id UUID PRIMARY KEY,
  user_id UUID,
  action VARCHAR(100),
  resource_type VARCHAR(100),
  resource_id UUID,
  details JSONB,
  timestamp TIMESTAMP
);
```

### 7.2 Indexes

```sql
CREATE INDEX idx_schemes_category ON schemes(category);
CREATE INDEX idx_schemes_status ON schemes(status);
CREATE INDEX idx_rules_scheme ON eligibility_rules(scheme_id);
CREATE INDEX idx_matches_profile ON matches(profile_id);
CREATE INDEX idx_matches_scheme ON matches(scheme_id);
CREATE INDEX idx_logs_timestamp ON audit_logs(timestamp);
```

---

## 8. UI Design Overview

### 8.1 Admin Upload Page

**Layout**:
- Header with navigation
- File upload dropzone (drag & drop)
- Form fields: Scheme name, department, category
- Processing status indicator
- Recent uploads list

**Features**:
- Real-time upload progress
- Validation feedback
- Processing status updates
- Error handling with retry

### 8.2 Citizen Input Form

**Layout**:
- Clean, single-page form
- Grouped sections: Personal, Economic, Location
- Progress indicator
- Help text for each field

**Fields**:
- Age (number input)
- Gender (dropdown)
- Annual Income (number with currency)
- State, District (cascading dropdowns)
- Location Type (urban/rural radio)
- Category (dropdown: General, SC, ST, OBC, EWS)
- Special conditions (checkboxes)

**UX**:
- Auto-save to local storage
- Clear validation messages
- Mobile-responsive design

### 8.3 Results Page

**Layout**:
- Search summary at top
- Filter sidebar (category, score range)
- Scheme cards in grid/list view
- Pagination

**Scheme Card**:
- Scheme name and department
- Eligibility score (visual indicator)
- Key benefits summary
- "View Details" button

**Details Modal**:
- Full scheme description
- Detailed explanation (checkmarks for met criteria)
- Application link
- Deadline information

### 8.4 Analytics Dashboard

**Layout**:
- KPI cards at top (total schemes, users, matches)
- Charts section:
  - Scheme distribution pie chart
  - Geographic coverage map
  - Trending schemes bar chart
  - User engagement timeline
- Filters: Date range, category, state

**Visualizations**:
- Chart.js for interactive charts
- Color-coded categories
- Hover tooltips with details
- Export to PDF/CSV buttons

---

## 9. Scalability Design

### 9.1 Serverless Architecture

**Benefits**:
- Auto-scaling based on demand
- Pay-per-use pricing
- No server management
- High availability

**Implementation**:
- AWS Lambda for all compute
- API Gateway for routing
- S3 for static hosting (React app)
- CloudFront for CDN

### 9.2 Event-Driven Processing

**Queue System**: AWS SQS

**Flow**:
```
Document Upload → SQS Queue → OCR Lambda
OCR Complete → SQS Queue → AI Extraction Lambda
Extraction Complete → SQS Queue → Rule Structuring Lambda
```

**Benefits**:
- Decoupled services
- Retry logic for failures
- Parallel processing
- Load leveling

### 9.3 Parallel Document Processing

**Strategy**:
- Process multiple documents simultaneously
- Each document gets independent Lambda invocation
- No blocking operations
- Progress tracked in database

**Concurrency Limits**:
- Lambda: 1000 concurrent executions (default)
- Textract: 10 concurrent jobs (can be increased)

### 9.4 Caching Strategy

**Redis Cache**:
- Cache scheme list (TTL: 1 hour)
- Cache popular matches (TTL: 30 minutes)
- Cache dashboard aggregations (TTL: 15 minutes)

**Benefits**:
- Reduced database load
- Faster response times
- Cost optimization

### 9.5 Database Optimization

**Strategies**:
- Read replicas for analytics queries
- Connection pooling
- Query optimization with indexes
- Partitioning for large tables (matches, logs)

---

## 10. Security Design

### 10.1 Data Privacy

**Measures**:
- Citizen profiles anonymized by default
- PII encrypted at rest (AES-256)
- No storage of sensitive documents beyond processing
- GDPR-compliant data retention policies
- User consent for profile storage

### 10.2 Role-Based Access Control (RBAC)

**Roles**:
- **Admin**: Full access to all features
- **Analyst**: Read-only access to analytics
- **Citizen**: Access to matching service only

**Implementation**: AWS Cognito user pools with custom attributes

### 10.3 Secure Uploads

**Validation**:
- File type whitelist (PDF, DOCX only)
- File size limit (50MB)
- Virus scanning (AWS S3 + ClamAV)
- Signed URLs for temporary access

### 10.4 API Security

**Measures**:
- HTTPS/TLS 1.3 for all traffic
- JWT authentication for admin APIs
- Rate limiting (100 requests/minute per IP)
- CORS configuration
- Input validation and sanitization
- SQL injection prevention (parameterized queries)

### 10.5 Audit Logging

**Logged Events**:
- All admin actions
- Scheme uploads and edits
- Rule modifications
- Authentication attempts
- API access patterns

**Storage**: CloudWatch Logs with 90-day retention

---

## 11. Technology Stack

### 11.1 Frontend

- **Framework**: React 18+
- **UI Library**: Material-UI (MUI)
- **State Management**: React Context API + useReducer
- **Routing**: React Router v6
- **Charts**: Chart.js with react-chartjs-2
- **HTTP Client**: Axios
- **Form Handling**: React Hook Form
- **Styling**: CSS Modules + MUI theming

### 11.2 Backend

- **Runtime**: Node.js 20 (Lambda)
- **Framework**: Express.js (for API Gateway integration)
- **Alternative**: Python 3.11 with FastAPI
- **Authentication**: AWS Cognito
- **API Documentation**: OpenAPI/Swagger

### 11.3 AI/ML

- **LLM**: AWS Bedrock (Claude 3 Sonnet or Titan)
- **OCR**: AWS Textract
- **Text Processing**: LangChain (optional)
- **Prompt Management**: Custom templates

### 11.4 Data Storage

- **Primary Database**: PostgreSQL 15 (AWS RDS)
- **Document Storage**: AWS S3
- **Cache**: Redis (AWS ElastiCache) - optional
- **Alternative**: DynamoDB for NoSQL approach

### 11.5 Cloud Infrastructure (AWS)

- **Compute**: AWS Lambda
- **API**: API Gateway (REST)
- **Storage**: S3, RDS
- **AI Services**: Bedrock, Textract
- **Queue**: SQS
- **CDN**: CloudFront
- **Monitoring**: CloudWatch
- **Security**: Cognito, IAM, KMS
- **IaC**: AWS CDK (TypeScript)

### 11.6 DevOps

- **Version Control**: Git + GitHub
- **CI/CD**: GitHub Actions
- **Containerization**: Docker (for local dev)
- **Testing**: Jest (backend), React Testing Library (frontend)
- **Linting**: ESLint, Prettier

---

## 12. Deployment Architecture

### 12.1 AWS Services Diagram

```
┌─────────────────────────────────────────────────────────────┐
│                     CloudFront (CDN)                         │
│                  (React App Distribution)                    │
└────────────────────────┬────────────────────────────────────┘
                         │
                         ▼
┌─────────────────────────────────────────────────────────────┐
│                    S3 Bucket (Static)                        │
│                   (React Build Files)                        │
└──────────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────────┐
│                    API Gateway (REST)                        │
│                  (API Endpoint Routing)                      │
└────────────────────────┬────────────────────────────────────┘
                         │
         ┌───────────────┼───────────────┐
         ▼               ▼               ▼
┌──────────────┐ ┌──────────────┐ ┌──────────────┐
│   Lambda     │ │   Lambda     │ │   Lambda     │
│  (Ingestion) │ │  (Matching)  │ │ (Analytics)  │
└──────┬───────┘ └──────┬───────┘ └──────┬───────┘
       │                │                │
       └────────────────┼────────────────┘
                        ▼
        ┌───────────────────────────────┐
        │      SQS Queue (Events)       │
        └───────────────┬───────────────┘
                        ▼
        ┌───────────────────────────────┐
        │   Lambda (OCR Processing)     │
        └───────────────┬───────────────┘
                        ▼
        ┌───────────────────────────────┐
        │      AWS Textract (OCR)       │
        └───────────────┬───────────────┘
                        ▼
        ┌───────────────────────────────┐
        │   Lambda (AI Extraction)      │
        └───────────────┬───────────────┘
                        ▼
        ┌───────────────────────────────┐
        │   AWS Bedrock (LLM)           │
        └───────────────┬───────────────┘
                        ▼
        ┌───────────────────────────────┐
        │   Lambda (Rule Structuring)   │
        └───────────────┬───────────────┘
                        │
         ┌──────────────┼──────────────┐
         ▼              ▼              ▼
┌──────────────┐ ┌──────────────┐ ┌──────────────┐
│  PostgreSQL  │ │   S3 Bucket  │ │    Redis     │
│    (RDS)     │ │  (Documents) │ │ (ElastiCache)│
└──────────────┘ └──────────────┘ └──────────────┘
```

### 12.2 Deployment Pipeline

**Stages**:

1. **Development**
   - Local development with Docker Compose
   - Mock AWS services with LocalStack
   - Unit and integration tests

2. **Build**
   - GitHub Actions triggered on push
   - Run linting and tests
   - Build React app
   - Package Lambda functions

3. **Deploy to Staging**
   - Deploy infrastructure with AWS CDK
   - Upload Lambda code
   - Deploy React app to S3
   - Run smoke tests

4. **Deploy to Production**
   - Manual approval gate
   - Blue-green deployment
   - Health checks
   - Rollback capability

### 12.3 Infrastructure as Code (AWS CDK)

**Stacks**:
- **NetworkStack**: VPC, subnets, security groups
- **DataStack**: RDS, S3 buckets, ElastiCache
- **ComputeStack**: Lambda functions, API Gateway
- **FrontendStack**: S3, CloudFront distribution
- **MonitoringStack**: CloudWatch dashboards, alarms

**Example CDK Code** (TypeScript):

```typescript
const api = new apigateway.RestApi(this, 'JanSetuAPI', {
  restApiName: 'JanSetu Service',
  description: 'API for JanSetu platform'
});

const schemesResource = api.root.addResource('schemes');
schemesResource.addMethod('GET', new apigateway.LambdaIntegration(listSchemesLambda));
schemesResource.addMethod('POST', new apigateway.LambdaIntegration(uploadSchemeLambda));
```

### 12.4 Monitoring and Alerting

**CloudWatch Metrics**:
- Lambda invocation count and duration
- API Gateway 4xx/5xx errors
- Database connection pool usage
- S3 upload success rate

**Alarms**:
- Lambda error rate >5%
- API latency >3 seconds
- Database CPU >80%
- Failed document processing jobs

**Dashboards**:
- Real-time system health
- Processing pipeline status
- User activity metrics

---

## 13. Future Improvements

### Phase 2 Enhancements

**Multilingual Support**:
- Hindi and regional language interfaces
- Multilingual scheme document processing
- Translation API integration

**Mobile Applications**:
- React Native apps for iOS and Android
- Offline mode with local storage
- Push notifications for new schemes

**Advanced Analytics**:
- Predictive analytics for scheme uptake
- Geographic heat maps
- Demographic trend analysis
- Policy impact assessment

**Enhanced AI**:
- Fine-tuned models for scheme-specific extraction
- Active learning from manual corrections
- Multi-modal processing (images, tables)
- Automated scheme categorization

**Integration Features**:
- Government portal API integration
- Aadhaar-based profile auto-fill
- DigiLocker document verification
- SMS/WhatsApp notifications

**Community Features**:
- User reviews and ratings
- Success stories
- Q&A forums
- Scheme comparison tool

### Long-Term Vision

**Policy Insights Engine**:
- Identify underserved demographics
- Scheme coverage gap analysis
- Recommendation engine for policymakers
- Impact measurement dashboard

**Blockchain Integration**:
- Immutable audit trail
- Decentralized identity verification
- Smart contracts for eligibility

**API Marketplace**:
- Public API for third-party integrations
- Developer portal
- Usage-based pricing
- Partner ecosystem

---

## Document Control

| Version | Date | Author | Changes |
|---------|------|--------|---------|
| 1.0 | 2026-02-14 | Project Team | Initial design document |

---

**Document Status**: Draft for Review  
**Next Review Date**: TBD  
**Approval Required From**: Technical Lead, Solution Architect

---

*This design document provides the technical blueprint for implementing the JanSetu platform. It should be used in conjunction with the requirements.md document and updated as the system evolves.*
