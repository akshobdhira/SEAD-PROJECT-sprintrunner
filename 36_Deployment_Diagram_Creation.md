# Deployment Diagram Creation
## Sprint Retrospective Analyzer - Cloud Infrastructure

**Document Version:** 1.0  
**Date:** January 27, 2026  
**Problem Statement:** 36 - Deployment Diagram Creation

---

## 1. Introduction

This document presents the deployment diagram for the Sprint Retrospective Analyzer, showing the physical architecture, hardware, software, and network configuration for the production environment.

---

## 2. Deployment Architecture

### 2.1 Diagram

![Cloud Deployment Diagram](images/deployment_diagram_cloud_1769530443053.png)

### 2.2 Architecture Overview

**Deployment Model:** Cloud-based Three-Tier Architecture  
**Cloud Provider:** AWS (Primary) or Azure (Alternative)  
**Region:** us-east-1 (N. Virginia) for AWS, East US for Azure  
**Availability Zones:** 3 zones for high availability

---

## 3. Deployment Tiers

### 3.1 Client Tier

#### Web Browser (Client Device)

**Node Type:** User device (Laptop, Desktop, Tablet)  
**Artifacts Deployed:**
- React.js Application (JavaScript bundle)
- HTML/CSS/JS static files

**System Requirements:**
- Modern web browser (Chrome 90+, Firefox 88+, Safari 14+, Edge 90+)
- Screen resolution: Min 1280x720, Recommended 1920x1080
- Internet connection: ≥ 1 Mbps

**Protocol:** HTTPS to Load Balancer  
**Port:** 443 (HTTPS)

**Client-Side Technologies:**
- React.js 18+ (UI framework)
- Redux (State management)
- Axios (HTTP client)
- LocalStorage (Draft auto-save)

---

### 3.2 Application Tier (AWS/Azure Cloud)

#### Load Balancer

**Node Type:** AWS Application Load Balancer (ALB) / Azure Load Balancer  
**Purpose:** Distribute incoming traffic across multiple web servers

**Configuration:**
- **Health Check Path:** `/health`
- **Health Check Interval:** 30 seconds
- **Unhealthy Threshold:** 2 consecutive failures
- **Healthy Threshold:** 2 consecutive successes
- **Timeout:** 5 seconds

**Features:**
- SSL/TLS termination (HTTPS → HTTP internal)
- Sticky sessions (cookie-based session affinity)
- Path-based routing (`/api/*` → backend, `/static/*` → CDN)
- Security Groups: Allow inbound HTTPS (443) from 0.0.0.0/0

**Performance:**
- Can handle 500+ concurrent connections
- Auto-scaling as traffic increases

---

#### Web Server (Node.js) x3

**Node Type:** AWS EC2 t3.medium instance / Azure B2ms VM

**Hardware Specifications:**
- **vCPU:** 2 cores
- **RAM:** 4 GB
- **Storage:** 30 GB SSD (gp3)
- **Network:** Up to 5 Gbps bandwidth

**Operating System:** Ubuntu 20.04 LTS

**Installed Software:**
- Node.js 18 LTS
- PM2 (Process manager for Node.js)
- Nginx (Reverse proxy, optional)
- Docker 20+ (Containerization)

**Artifacts Deployed:**
- Backend API application (Node.js + Express.js)
- API Gateway
- Authentication Service
- Retrospective Service
- Analytics Service

**Configuration:**
```yaml
# Application configuration
PORT: 3000
NODE_ENV: production
DATABASE_URL: postgresql://user:pass@db-primary.amazonaws.com:5432/sra
JWT_SECRET: <securely stored in AWS Secrets Manager>
S3_BUCKET: sra-storage-production
EMAIL_API_KEY: <SendGrid API key>
```

**Auto-Scaling:**
- **Minimum Instances:** 2
- **Maximum Instances:** 10
- **Scaling Policy:** CPU utilization > 70% → Add instance
- **Cooldown Period:** 300 seconds

**Deployment Method:**
- Docker containers orchestrated by ECS (AWS) or AKS (Azure)
- Blue-Green deployment for zero-downtime updates
- Rolling updates (update 1 instance at a time)

---

### 3.3 Database Tier

#### Primary Database (PostgreSQL)

**Node Type:** AWS RDS db.t3.large / Azure Database for PostgreSQL  
**Purpose:** Main transactional database

**Hardware Specifications:**
- **vCPU:** 2 cores
- **RAM:** 8 GB
- **Storage:** 500 GB SSD (gp3), auto-scaling up to 1 TB
- **IOPS:** 3,000 baseline, burstable to 12,000

**Database Configuration:**
- **Engine:** PostgreSQL 14.7
- **Instance Class:** db.t3.large
- **Multi-AZ:** Yes (for high availability)
- **Automated Backups:** Daily, 30-day retention
- **Backup Window:** 03:00-04:00 UTC (low traffic period)
- **Maintenance Window:** Sunday 04:00-05:00 UTC

**Security:**
- **Encryption at Rest:** AES-256
- **Encryption in Transit:** SSL/TLS connections enforced
- **Network:** Private subnet, no public IP
- **Security Group:** Allow inbound PostgreSQL (5432) only from web servers
- **Authentication:** Username/password + IAM authentication

**Performance Tuning:**
- **Connection Pooling:** Max 50 connections
- **Shared Buffers:** 2 GB (25% of RAM)
- **Work Memory:** 64 MB
- **Maintenance Work Memory:** 512 MB

---

#### Read Replica (PostgreSQL)

**Node Type:** AWS RDS db.t3.medium / Azure Database replica  
**Purpose:** Offload SELECT queries for reporting and analytics

**Hardware Specifications:**
- **vCPU:** 2 cores
- **RAM:** 4 GB
- **Storage:** Same as primary (synchronized)

**Replication:**
- **Method:** Asynchronous streaming replication
- **Lag:** Typically < 1 second
- **Failover:** Can be promoted to primary if needed

**Usage:**
- Report generation queries
- Dashboard data fetching
- Analytics service queries
- Historical data analysis

**Read-Write Split:**
- Application automatically routes:
  - INSERT, UPDATE, DELETE → Primary Database
  - SELECT (reports, dashboards) → Read Replica

---

### 3.4 External Services

#### Email Server (SendGrid / AWS SES)

**Node Type:** External SaaS Service  
**Purpose:** Send transactional emails

**Protocol:** SMTP (TLS encrypted)  
**Port:** 587 (TLS) or 465 (SSL)

**Integration:**
- API-based or SMTP integration
- Authenticated via API key (stored in AWS Secrets Manager)
- Email templates stored in service

**Email Volume:**
- **Expected:** ~5,000 emails/month (Phase 1)
- **Limit:** 50,000 emails/month (SendGrid Essentials plan)

**Deliverability:**
- SPF record configured: `v=spf1 include:sendgrid.net ~all`
- DKIM signing enabled
- Custom domain: `noreply@yourcompany.com`

---

#### File Storage (AWS S3 / Azure Blob Storage)

**Node Type:** Cloud Object Storage  
**Purpose:** Store file attachments and generated reports

**Bucket Configuration:**
- **Bucket Name:** `sra-storage-production`
- **Region:** us-east-1 (same as application)
- **Access:** Private (no public access)
- **Versioning:** Enabled
- **Lifecycle Policy:** Delete files > 90 days old (for temp reports)

**Security:**
- **Server-Side Encryption:** AES-256
- **Access Control:** IAM roles for web servers
- **Pre-Signed URLs:** For secure file downloads (expiry: 15 minutes)

**Usage:**
- User-uploaded feedback attachments
- Generated PDF reports
- Exported CSV data files
- System backups

---

## 4. Network Architecture

### 4.1 VPC Configuration (AWS)

**Virtual Private Cloud (VPC):**
- **CIDR Block:** 10.0.0.0/16
- **Availability Zones:** 3 (us-east-1a, us-east-1b, us-east-1c)

**Subnets:**
| **Subnet** | **Type** | **CIDR** | **Purpose** |
|------------|----------|----------|-------------|
| Public Subnet 1a | Public | 10.0.1.0/24 | Load Balancer, NAT Gateway |
| Public Subnet 1b | Public | 10.0.2.0/24 | Load Balancer, NAT Gateway |
| Private Subnet 1a | Private | 10.0.11.0/24 | Web Servers |
| Private Subnet 1b | Private | 10.0.12.0/24 | Web Servers |
| Private Subnet 1c | Private | 10.0.13.0/24 | Web Servers |
| Database Subnet 1a | Private | 10.0.21.0/24 | Primary Database |
| Database Subnet 1b | Private | 10.0.22.0/24 | Read Replica  |

**Internet Gateway:** Attached to VPC for public internet access

**NAT Gateway:** In public subnets for outbound internet from private subnets

**Route Tables:**
- **Public Route Table:** 0.0.0.0/0 → Internet Gateway
- **Private Route Table:** 0.0.0.0/0 → NAT Gateway

---

### 4.2 Security Groups

**Load Balancer Security Group:**
- Inbound: Allow TCP 443 (HTTPS) from 0.0.0.0/0
- Outbound: Allow TCP 3000 to Web Server Security Group

**Web Server Security Group:**
- Inbound: Allow TCP 3000 from Load Balancer Security Group
- Outbound: Allow TCP 5432 to Database Security Group
- Outbound: Allow TCP 443 to 0.0.0.0/0 (for external API calls)

**Database Security Group:**
- Inbound: Allow TCP 5432 from Web Server Security Group only
- Outbound: None

---

## 5. Deployment Workflow

### 5.1 CI/CD Pipeline

**Source Control:** GitHub  
**CI/CD Tool:** GitHub Actions / AWS CodePipeline

**Pipeline Stages:**
1. **Source:** Code pushed to `main` branch triggers pipeline
2. **Build:** 
   - Run unit tests (`npm test`)
   - Build Docker image
   - Push to AWS ECR (Elastic Container Registry)
3. **Deploy to Staging:**
   - Deploy to staging environment
   - Run integration tests
   - Manual approval required
4. **Deploy to Production:**
   - Blue-Green deployment (zero downtime)
   - Route 10% traffic to new version (canary)
   - Monitor for 10 minutes
   - Route 100% traffic if no issues
   - Rollback if errors detected

### 5.2 Deployment Commands

**Build Docker Image:**
```bash
docker build -t sra-backend:v1.2.0 .
docker tag sra-backend:v1.2.0 <account-id>.dkr.ecr.us-east-1.amazonaws.com/sra-backend:v1.2.0
docker push <account-id>.dkr.ecr.us-east-1.amazonaws.com/sra-backend:v1.2.0
```

**Deploy to ECS:**
```bash
aws ecs update-service --cluster sra-cluster --service sra-backend --force-new-deployment
```

---

## 6. Monitoring and Logging

### 6.1 Monitoring Tools

**Application Monitoring:** AWS CloudWatch / Azure Monitor

**Metrics Collected:**
- CPU utilization (per EC2 instance)
- Memory utilization
- Network in/out
- Disk I/O
- Database connections
- API response times
- Error rates

**Alarms:**
| **Metric** | **Threshold** | **Action** |
|------------|---------------|------------|
| CPU > 80% | 2 consecutive periods (10 min) | Trigger auto-scaling |
| Database connections > 45 | 1 period (5 min) | Alert DevOps team |
| API error rate > 5% | 1 period (5 min) | Page on-call engineer |
| Disk usage > 85% | 1 period (1 day) | Email sysadmin |

### 6.2 Logging

**Log Aggregation:** AWS CloudWatch Logs / ELK Stack

**Log Sources:**
- Application logs (Node.js logger)
- Web server access logs (Nginx)
- Database slow query logs
- Load balancer access logs

**Log Retention:**
- Application logs: 30 days
- Security logs: 1 year
- Audit logs: 7 years (compliance requirement)

---

## 7. Backup and Disaster Recovery

### 7.1 Backup Strategy

**Database Backups:**
- **Automated Daily Backups:** 03:00 UTC, retained for 30 days
- **Manual Snapshots:** Before major deployments, retained indefinitely
- **Transaction Logs:** Continuous, allows point-in-time recovery

**Application Backups:**
- Docker images versioned and stored in ECR (infinite retention)
- Infrastructure as Code (Terraform/CloudFormation) in Git

**File Storage Backups:**
- S3 versioning enabled (files can be restored from previous versions)
- S3 cross-region replication to us-west-2 (disaster recovery)

### 7.2 Disaster Recovery

**RTO (Recovery Time Objective):** < 4 hours  
**RPO (Recovery Point Objective):** < 24 hours

**Disaster Recovery Plan:**
1. **Database Failure:** Promote read replica to primary (< 5 minutes)
2. **Region Failure:** Failover to us-west-2 region (< 4 hours manual process)
3. **Application Failure:** Auto-scaling replaces unhealthy instances (< 10 minutes)

**DR Testing:** Quarterly disaster recovery drills

---

## 8. Cost Estimation

### 8.1 Monthly Infrastructure Cost (AWS)

| **Service** | **Configuration** | **Monthly Cost (USD)** |
|-------------|-------------------|------------------------|
| **Load Balancer** | 1 ALB | $25 |
| **Web Servers** | 3x t3.medium (730 hours) | $90 |
| **Database Primary** | 1x db.t3.large (730 hours) | $140 |
| **Database Replica** | 1x db.t3.medium (730 hours) | $70 |
| **S3 Storage** | 100 GB storage + requests | $5 |
| **Email Service** | SendGrid Essentials | $20 |
| **Data Transfer** | 500 GB/month | $45 |
| **CloudWatch** | Logs + metrics | $15 |
| **Total Estimated Cost** | | **~$410/month** |

**Note:** Costs scale with usage. Auto-scaling can increase costs during peak traffic.

---

## 9. Performance and Scalability

### 9.1 Performance Specifications

| **Metric** | **Target** | **Current Capacity** |
|------------|------------|----------------------|
| **Concurrent Users** | 500 | 500 (3 web servers @ ~165 users each) |
| **API Response Time (p95)** | < 500ms | Measured at load testing |
| **Database Query Time (p95)** | < 200ms | Indexes configured |
| **Page Load Time** | < 2 seconds | CDN for static assets |
| **Uptime SLA** | 99.5% | ~3.7 hours downtime/month allowed |

### 9.2 Scaling Strategy

**Horizontal Scaling (Web Servers):**
- Add more EC2 instances when CPU > 70%
- Max 10 instances for Phase 1

**Vertical Scaling (Database):**
- Upgrade to db.t3.xlarge if CPU consistently > 70%
- Upgrade to db.r6g.large for memory-intensive workloads

**Read Replica Scaling:**
- Add second read replica if query load increases by 100%

---

## 10. Security and Compliance

### 10.1 Security Measures

**Network Security:**
- All traffic encrypted in transit (TLS 1.2+)
- Private subnets for application and database tiers
- Security groups restrict access to minimum necessary

**Data Security:**
- Database encryption at rest (AES-256)
- S3 bucket encryption
- Secrets stored in AWS Secrets Manager (never in code)

**Access Control:**
- IAM roles for EC2 instances (no hardcoded credentials)
- Multi-factor authentication (MFA) for AWS console access
- Principle of least privilege for all IAM policies

### 10.2 Compliance

**GDPR Compliance:**
- Data residency: EU customers can request eu-west-1 deployment
- Right to deletion: Implemented in application logic

**CCPA Compliance:**
- Data export functionality
- Opt-out mechanisms

**SOC 2 (Future):**
- AWS provides SOC 2 Type II attestation
- Application-level controls documented

---

## 11. Summary

**Deployment Model:** Cloud-based three-tier architecture  
**Infrastructure:** AWS EC2, RDS, S3, ALB (or Azure equivalents)  
**Compute:** 3 web servers (t3.medium), auto-scaling 2-10 instances  
**Database:** 1 primary (db.t3.large) + 1 read replica (db.t3.medium)  
**Networking:** VPC with public/private subnets across 3 availability zones  
**Security:** TLS encryption, private subnets, security groups, IAM roles  
**Cost:** ~$410/month for baseline infrastructure  

**Related Documents:** [35_Component_Diagram_Creation.md](file:///C:/Users/AKSHOB%20DHIRA/SEAD-PROJECT-sprintrunner/35_Component_Diagram_Creation.md), [37_Design_Document_Creation.md](file:///C:/Users/AKSHOB%20DHIRA/SEAD-PROJECT-sprintrunner/37_Design_Document_Creation.md)

---

**Document Control:**
- **Created By:** System Design Team
- **Last Updated:** January 27, 2026
- **Status:** Final
- **Version:** 1.0
