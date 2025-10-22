# Project Proposal: RealPage Natural Language Interface (NLI)

## Team

* **Team Lead:** [Team Lead Name]
* **Team Members:**
  * [Member 1 Name]
  * [Member 2 Name]
  * [Member 3 Name]
  * [Member 4 Name]

## Project Goal

We are building an intelligent data access platform that enables users to interact with RealPage operational data through natural language. The system combines a Text-to-SQL model with multimodal AI capabilities (speech-to-text, text-to-speech, RAG-based assistance, and virtual avatar interface) to provide an accessible, ChatGPT-like experience for querying complex business data. Users can speak or type questions in plain English and receive immediate, accurate results from their RealPage database without needing SQL knowledge.

## User Stories

### End User (Property Manager/Analyst)
**As a property manager**, I want to query operational data using natural language so that I can quickly access insights without learning SQL or waiting for IT support.

* I can log into the web interface using my credentials
* I can type or speak questions like "Show me all units with overdue maintenance in Building A"
* I receive results in a clear table format that I can export to Excel or PDF
* I can ask follow-up questions using the RAG-powered assistant for clarification
* I can interact with a virtual avatar that guides me through complex queries
* I receive spoken responses through text-to-speech for accessibility

### Admin User (System Administrator)
**As a system administrator**, I want to manage users and monitor system performance so that I can ensure data security and optimal operations.

* I can create, modify, and deactivate user accounts
* I can assign role-based permissions (Admin vs. User)
* I can view system logs and query history
* I can monitor API usage and database performance
* I can schedule and manage canned reports
* I can configure STT/TTS settings and avatar preferences
* I can access data quality metrics from ETL processes

### Data Analyst
**As a data analyst**, I want to explore historical trends and generate custom reports so that I can provide strategic insights to leadership.

* I can query data spanning from 2022 to present
* I can combine data from multiple tables using natural language
* I can export query results in multiple formats (CSV, Excel, PDF)
* I can save frequently-used queries for quick access
* I can use RAG assistance to understand available data schemas

## UI Design

### Main Interface Components

#### 1. Landing/Login Page
* Clean, professional login form
* Email/password authentication
* "Remember me" functionality
* Password reset link
* Responsive design for mobile and desktop

#### 2. Chat Interface (Primary User View)
**Layout:**
* Left sidebar (collapsible):
  * New conversation button
  * Recent queries history
  * Saved queries
  * User settings
* Center panel:
  * Virtual avatar (toggle on/off)
  * Message history display
  * Input area with microphone button for STT
  * Text input field with send button
  * Export options (CSV, Excel, PDF)
* Right sidebar (contextual):
  * Query suggestions
  * RAG-powered help documentation
  * Available tables/schemas reference

**Interaction Flow:**
1. User enters query via text or speech (microphone icon)
2. System displays "Processing..." with loading indicator
3. Avatar provides spoken confirmation (TTS) if enabled
4. Results appear in formatted table
5. User can export, modify query, or ask follow-up questions
6. RAG assistant offers related queries or clarifications

#### 3. Admin Dashboard
**Tabs:**
* **User Management:**
  * User list table (name, email, role, status, last login)
  * Add/Edit/Deactivate user buttons
  * Role assignment dropdown
* **System Monitoring:**
  * Real-time metrics (queries/hour, active users, API health)
  * Performance graphs (response time, query success rate)
  * Error logs viewer
* **Reports Management:**
  * Schedule canned reports
  * View/download generated reports
  * Configure report parameters
* **ETL Status:**
  * Last sync timestamp
  * Data quality metrics
  * Sync history and error logs

#### 4. Settings Page
* User profile settings
* Avatar customization (enable/disable, voice selection)
* STT/TTS preferences (language, speed, voice)
* Export format defaults
* Notification preferences

### Design Specifications
* **Framework:** React with Material-UI or Tailwind CSS
* **Color Scheme:** Professional blue/gray palette for business context
* **Accessibility:** WCAG 2.1 AA compliant, keyboard navigation support
* **Responsive:** Mobile-first design, breakpoints at 768px, 1024px, 1440px

## Project Requirements

### Back End

**Technology Stack:**
* **Runtime:** Node.js with Express.js framework
* **API Gateway:** AWS API Gateway for RESTful endpoints
* **Authentication:** AWS Cognito for user management and JWT-based auth
* **Database:** AWS RDS (PostgreSQL) for structured data
* **Document Storage:** AWS S3 for documents and canned reports
* **Model Hosting:** AWS SageMaker for Text-to-SQL model deployment
* **ETL Processing:** AWS Glue for data extraction and transformation
* **Caching:** AWS ElastiCache (Redis) for query result caching

**Core Services:**
1. **Authentication Service:** User login, token management, role verification
2. **Query Processing Service:** 
   * Natural language input → Text-to-SQL model → SQL execution → result formatting
   * Query validation and security (SQL injection prevention)
3. **ETL Service:** 
   * RealPage API integration
   * Monthly delta load scheduling
   * Data cleaning and transformation
4. **Multimodal Services:**
   * STT integration (AWS Transcribe or Assembly AI)
   * TTS integration (AWS Polly or ElevenLabs)
   * RAG service using vector database (Pinecone/AWS OpenSearch)
   * Avatar rendering service (D-ID or Synthesia integration)
5. **Report Generation Service:** Scheduled canned reports, export functionality
6. **Logging & Monitoring Service:** CloudWatch integration, audit trails

### Web API

**RESTful API Endpoints:**

```
Authentication:
POST   /api/auth/login
POST   /api/auth/logout
POST   /api/auth/refresh
POST   /api/auth/reset-password

Query Processing:
POST   /api/query/text         # Submit text query
POST   /api/query/speech       # Submit audio for STT
GET    /api/query/history      # Get user's query history
GET    /api/query/:id          # Get specific query result
POST   /api/query/export       # Export query results

RAG Assistance:
POST   /api/rag/ask            # Ask RAG assistant
GET    /api/rag/schema         # Get database schema info
GET    /api/rag/suggestions    # Get query suggestions

Multimodal:
POST   /api/tts/synthesize     # Convert text to speech
POST   /api/avatar/generate    # Generate avatar response
GET    /api/avatar/settings    # Get avatar configuration

User Management (Admin):
GET    /api/users              # List all users
POST   /api/users              # Create user
PUT    /api/users/:id          # Update user
DELETE /api/users/:id          # Deactivate user

Reports:
GET    /api/reports            # List available reports
GET    /api/reports/:id        # Download specific report
POST   /api/reports/schedule   # Schedule new report

System Monitoring (Admin):
GET    /api/metrics/system     # System health metrics
GET    /api/logs               # Access system logs
GET    /api/etl/status         # ETL job status
```

**WebSocket Endpoints:**
```
WS     /ws/query               # Real-time query processing updates
WS     /ws/avatar              # Real-time avatar streaming
```

**API Security:**
* JWT authentication required for all endpoints except `/api/auth/login`
* Role-based access control (RBAC) for admin endpoints
* Rate limiting: 100 requests/minute per user
* Input validation and sanitization on all endpoints
* CORS configuration for allowed origins

### Data

**Data Sources:**
1. **RealPage API:** Operational data and documents (historical since 2022)
2. **User-generated data:** Query history, saved queries, user preferences

**Data Storage Architecture:**

**AWS RDS (PostgreSQL):**
```sql
-- Core business tables (from RealPage)
properties
units
tenants
leases
maintenance_requests
financial_transactions
work_orders

-- Application tables
users (id, email, password_hash, role, created_at, last_login)
query_history (id, user_id, query_text, sql_generated, execution_time, timestamp)
saved_queries (id, user_id, name, query_text, created_at)
canned_reports (id, name, schedule, parameters, last_run)
audit_logs (id, user_id, action, details, timestamp)
```

**AWS S3 Buckets:**
* `realpage-documents/` - Documents from RealPage API
* `generated-reports/` - Canned reports (PDF, Excel)
* `query-exports/` - User-exported query results
* `etl-logs/` - ETL process logs

**Vector Database (Pinecone/AWS OpenSearch):**
* Embedded database schema documentation
* RAG knowledge base for query assistance
* Example queries and their SQL translations

**Data Processing Pipeline:**
1. **Initial Load (Time-Zero):** Extract all historical data from RealPage API
2. **Monthly Delta Loads:** 
   * Scheduled on 1st of each month
   * Compare timestamps, extract new/modified records
   * ETL processing: validate, clean, transform, load
3. **Data Quality Checks:** 
   * Completeness validation
   * Consistency checks across related tables
   * Duplicate detection
4. **Backup Strategy:**
   * Daily RDS automated backups (7-day retention)
   * Weekly full database snapshots (30-day retention)
   * S3 versioning enabled for document storage

### User/Admin Views

**User Views (Role: User):**

1. **Query Interface:**
   * Natural language input (text or speech)
   * Real-time query processing feedback
   * Tabular result display with sorting/filtering
   * Export buttons (CSV, Excel, PDF)
   * Query history sidebar
   * Virtual avatar interaction

2. **Profile/Settings:**
   * Personal information management
   * Password change functionality
   * STT/TTS preferences
   * Avatar customization
   * Export format preferences

3. **Help & Documentation:**
   * RAG-powered assistant
   * Example queries library
   * Database schema browser
   * Tutorial videos

**Admin Views (Role: Admin):**

1. **User Management Dashboard:**
   * User list table with search/filter
   * Create new user form
   * Edit user modal (role, status, permissions)
   * Bulk actions (deactivate, export list)
   * User activity summary

2. **System Monitoring:**
   * Real-time metrics dashboard:
     - Active users count
     - Queries per hour/day
     - Average response time
     - Error rate
   * Performance graphs (Chart.js/Recharts)
   * API health status
   * Database connection pool status

3. **ETL Management:**
   * Last sync status and timestamp
   * Schedule next sync
   * View ETL logs and errors
   * Data quality metrics:
     - Records processed
     - Records failed
     - Data completeness score
   * Manual trigger for delta load

4. **Reports Management:**
   * List of scheduled canned reports
   * Configure report parameters
   * Schedule editor (cron-like interface)
   * Download historical reports
   * Report generation logs

5. **Query Analytics:**
   * Most frequently asked queries
   * Query success/failure rates
   * Average execution times
   * Text-to-SQL model accuracy metrics

6. **System Logs Viewer:**
   * Filterable log table (level, timestamp, user, action)
   * Search functionality
   * Export logs
   * Real-time log streaming

**View Access Control:**
* User role: Access to Query Interface, Profile, Help only
* Admin role: Full access to all views
* Session timeout: 30 minutes of inactivity
* Audit logging for all admin actions

## Technical Architecture

### Full-Stack Application: Yes

This is a full-stack application integrating:
* **Frontend:** React SPA with multimodal UI components
* **Backend:** Node.js/Express API layer
* **Database:** PostgreSQL (structured), S3 (documents)
* **AI/ML:** Text-to-SQL model, STT, TTS, RAG, avatar generation
* **Infrastructure:** AWS cloud services

### Authentication & Security

**Authentication Strategy:**
* AWS Cognito user pools for authentication
* JWT tokens for session management
* Refresh token rotation (7-day refresh, 1-hour access tokens)
* Password requirements: min 12 chars, uppercase, lowercase, number, special char
* Multi-factor authentication (MFA) optional for admin accounts

**Security Measures:**
* SQL injection prevention: Parameterized queries only
* Input sanitization on all user inputs
* Rate limiting per user and IP
* HTTPS/TLS 1.3 for all communications
* CORS whitelist configuration
* Encrypted data at rest (AWS KMS)
* Encrypted data in transit (TLS)
* Regular security audits and penetration testing
* Role-based access control (RBAC)
* Query result row limits (max 10,000 rows per query)