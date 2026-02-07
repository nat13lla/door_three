# Project Requirements Document: Secure Global Intelligence Data Platform (GCP Edition)

## 1. Executive Summary

The goal of this project is to build a scalable, secure, cloud-native data platform on **Google Cloud Platform (GCP)** that ingests, processes, and analyzes high-volume global event data (simulating intelligence feeds). This project is designed primarily for me to capture some of my experience working as a Data Engineer, emphasizing cloud-native solutions, security, data quality, and scalable ETL pipelines.

## 2. Problem Statement

In the context of protecting national infrastructure and cyber assets, organizations require robust systems to ingest disparate data sources (news, social media, logs), cleanse and normalize them, and provide analytical capabilities for threat detection and mission intelligence.
This project will address the challenge of **"Ingesting, processing, and analyzing high-velocity, heterogeneous data at scale with strict security and quality controls."**

## 3. Objectives & Success Criteria

- **Demonstrate Cloud Proficiency:** Build a 100% cloud-native architecture on **GCP** using managed services.
- **Showcase Data Engineering Best Practices:** Implement robust ETL pipelines using modern tools (Apache Beam/Spark, Airflow).
- **Highlight Security & Compliance:** Integrate Cloud IAM, Cloud KMS encryption, and audit logging as first-class citizens.
- **Ensure Data Reliability:** Implement automated data quality checks (Great Expectations) and validation.
- **Enable Scalability:** use Infrastructure as Code (IaC) via **Terraform** for reproducible and scalable environments.

## 4. Technical Architecture

The solution will be built entirely on **GCP** using a "Lakehouse" architecture.

### 4.1. High-Level Components

1.  **Data Source:** Global Database of Events, Language, and Tone (**GDELT**) Project (Simulates high-volume, global intelligence feed).
    - _Why:_ Real-world, massive, structured/unstructured dataset relevant to "mission intelligence".
2.  **Ingestion Layer:**
    - **Batch:** **Cloud Functions (Gen 2)** to fetch GDELT daily updates.
    - **Streaming (Optional/Bonus):** Simulate real-time feed ingestion into **Pub/Sub** or **Confluent Cloud (Kafka)**.
3.  **Storage Layer (Data Lake):**
    - **Raw Zone:** **Google Cloud Storage (GCS)** buckets (Immutable raw data).
    - **Cleaned/Curated Zone:** GCS (Delta Lake / Apache Iceberg format) or BigLake managed tables.
4.  **Processing Layer (ETL):**
    - **Processing:** **Dataflow** (Apache Beam) or **Dataproc Serverless** (Spark).
    - **Transformation:** PySpark/Beam for heavy lifting; **DBT** (Data Build Tool) for BigQuery transformations.
5.  **Data Warehouse / Serving Layer:**
    - **BigQuery**.
    - _Goal:_ Support complex SQL queries for analytics and BI dashboards.
6.  **Orchestration:**
    - **Airflow:** **Cloud Composer** (Managed Airflow 2) to schedule and monitor workflows.
7.  **Data Quality & Governance:**
    - **Great Expectations:** Integrated into the pipeline (Dataflow/Composer) to fail jobs on bad data.
    - **Data Catalog:** **Dataplex** / **DataHub**.

## 5. Functional Requirements

### 5.1. Data Ingestion

- [ ] Automate daily download of GDELT event files (CSV/JSON).
- [ ] Validate file integrity (checksums) before processing.
- [ ] Archive raw files to "Raw" storage zone in GCS with appropriate lifecycle policies (e.g., Coldline after 30 days).

### 5.2. Data Processing (ETL)

- [ ] **Cleanse:** Filter invalid records, handle missing values, normalize date formats using Dataflow/Dataproc.
- [ ] **Enrich:** Join event data with static reference data (e.g., Country Codes, Actor Type mappings).
- [ ] **Transform:** Aggregate events by region/actor for trend analysis.
- [ ] **Format:** Convert CSV to optimized columnar format (Parquet/Delta) stored in GCS.

### 5.3. Data Storage & Management

- [ ] Implement a partitioned directory structure `year=YYYY/month=MM/day=DD` in GCS.
- [ ] Enforce schema evolution rules (backward compatibility).

### 5.4. Analytics & Serving

- [ ] Create BigQuery views/tables for:
  - "Top 10 Active Actors by Region"
  - "Daily Conflict Intensity Score"
  - "Emerging Themes/Topics"

## 6. Non-Functional Requirements (Critical for Role)

### 6.1. Security

- [ ] **Principle of Least Privilege:** Granular **Cloud IAM** roles for every service agent.
- [ ] **Encryption:** **Cloud KMS** (Customer Managed Encryption Keys) for data at rest; TLS for data in transit.
- [ ] **Network Isolation:** Resources deployed in private VPC subnets; use **Private Service Connect** / **VPC Service Controls** to avoid public internet traversal where possible.

### 6.2. DevOps & CI/CD

- [ ] **Infrastructure as Code (IaC):** Use **Terraform** to provision _all_ GCP resources.
- [ ] **CI/CD Pipeline:** **Cloud Build** or **GitHub Actions** to:
  - Lint Python/SQL code (Flake8, SQLFluff).
  - Run Unit Tests (PyTest).
  - Plan/Apply Terraform changes.

### 6.3. Observability

- [ ] **Logging:** Centralized logging (**Cloud Logging**).
- [ ] **Alerting:** **Cloud Monitoring** policies for pipeline failures or data quality breaches.

## 7. Implementation Milestones

### Phase 1: Foundation (Days 1-2)

- [ ] Set up GitHub Repository.
- [ ] Configure GCP Project and IAM Service Accounts.
- [ ] Write Terraform for VPC, GCS Buckets, and basic IAM roles.

### Phase 2: Ingestion & Storage (Days 3-4)

- [ ] Develop Python script for GDELT ingestion.
- [ ] Deploy as **Cloud Function**.
- [ ] Verify raw data landing in GCS.

### Phase 3: Processing & Transformation (Days 5-7)

- [ ] Set up PySpark/Beam job (**Dataflow** or **Dataproc Serverless**) to clean and transform data.
- [ ] Implement Data Quality checks (Great Expectations).
- [ ] Write transformed data to "Curated" zone (GCS).

### Phase 4: Orchestration & Automation (Days 8-9)

- [ ] Set up **Cloud Composer** Environment.
- [ ] Create DAGs to dependency-manage the pipeline.
- [ ] Integrate CI/CD (Cloud Build/GitHub Actions) for automated deployment.

### Phase 5: Documentation & Presentation (Day 10)

- [ ] Finalize `README.md` with architecture diagrams.
- [ ] Prepare a short demo video or walkthrough.
- [ ] Write a blog post or summary explaining design decisions (specifically security/scalability trade-offs).

## 8. Definition of Done

- **Infrastructure:** Deployed via Terraform.
- **Pipeline:** Runs end-to-end without manual intervention.
- **Code:** Committed to Git with meaningful messages.
- **Quality:** Data quality report generated for each run.
- **Security:** No open public access buckets; IAM roles strictly scoped.
