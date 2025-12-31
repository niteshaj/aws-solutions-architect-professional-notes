# Regional and Global AWS Architecture

- There are 3 main type of architectures:
    - Small scale architectures: one region/one country
    - Small architecture with DR: one region + backup region for disaster recovery
    - Multiple region based systems
- Architectural components at global level:
    - Global Service Location and Discovery
    - Content Delivery (CDN) and optimization
    - Global health checks and Failover
- Regional components:
    - Regional entry point
    - Scaling and resilience
    - Application services and components


## 1. Core AWS Geography

### 1.1 Region

* **Definition**: A **physical geographic area** with multiple **isolated Availability Zones (AZs)**.
* **Purpose**:

  * Fault isolation
  * Data residency & compliance
  * Latency optimization
* **Examples**: `us-east-1`, `eu-west-1`, `ap-south-1`

**SAP-C02 Insight**

> Multi-AZ = **high availability**, Multi-Region = **disaster recovery**

---

### 1.2 Availability Zone (AZ)

* **Definition**: One or more **data centers** with **independent power, cooling, networking**.
* **Characteristics**:

  * Low-latency (< 1–2 ms) between AZs
  * Designed to avoid correlated failures
    
* **Best Practice**:

  * Always deploy **production workloads across ≥2 AZs**

---

### 1.3 Edge Locations (Global)

* Used by:

  * **CloudFront**
  * **Route 53**
  * **AWS Shield**
    
* **Purpose**:

  * Reduce latency
  * DDoS protection
  * Global content delivery

---

## 2. Global vs Regional Services 

### 2.1 Global Services

These **do not belong to a single region**:

| Service               | Why Global                            |
| --------------------- | ------------------------------------- |
| **IAM**               | Identity must work across all regions |
| **Route 53**          | Global DNS                            |
| **CloudFront**        | CDN via Edge Locations                |
| **AWS Organizations** | Account governance                    |
| **WAF (global mode)** | Protects CloudFront                   |
| **Shield**            | Global DDoS protection                |

**Exam Trap**  
❌ *“IAM is regional”* → **WRONG**

---

### 2.2 Regional Services

Bound to a **single region**:

| Category   | Examples              |
| ---------- | --------------------- |
| Compute    | EC2, ECS, EKS, Lambda |
| Storage    | EBS, EFS, S3          |
| Databases  | RDS, Aurora, DynamoDB |
| Networking | VPC, ALB, NLB         |
| Security   | KMS, Secrets Manager  |

**Key Note**

* **S3 is regional but globally accessible**
* **DynamoDB Global Tables** = Multi-Region

---
## 3. Data Scope & Replication (Architect Focus)

### 3.1 Storage Behavior

| Service | Scope        | Replication         |
| ------- | ------------ | ------------------- |
| S3      | Regional     | Multi-AZ by default |
| S3 CRR  | Multi-Region | Async               |
| EBS     | AZ-level     | No native cross-AZ  |
| EFS     | Regional     | Multi-AZ            |
| FSx     | Regional     | Depends on config   |

---

### 3.2 Database Replication Patterns

| Service     | Pattern                       |
| ----------- | ----------------------------- |
| RDS         | Multi-AZ (sync)               |
| Aurora      | 6 copies across 3 AZs         |
| DynamoDB    | Global Tables (active-active) |
| ElastiCache | AZ-based replication          |

**SAP-C02 Rule**

> **Multi-AZ ≠ Multi-Region**


## 4. Networking Architecture (VERY IMPORTANT)

### 4.1 VPC Scope

* **VPC is regional**
* Subnets are **AZ-specific**
* Cannot span regions

---

### 4.2 Inter-Region Connectivity

| Option          | Use Case                    |
| --------------- | --------------------------- |
| VPC Peering     | Simple, non-transitive      |
| Transit Gateway | Hub-and-spoke               |
| VPN             | Encrypted, low cost         |
| Direct Connect  | High bandwidth, low latency |

---

## 5. Global Traffic Management (Exam Favorite)

### 5.1 Route 53 Routing Policies

| Policy        | Use Case               |
| ------------- | ---------------------- |
| Latency-based | Nearest region         |
| Geolocation   | Country-based routing  |
| Failover      | Active-passive DR      |
| Weighted      | Blue/green deployments |
| Multi-value   | Simple health checks   |

---

### 5.2 Global Accelerator vs CloudFront

| Feature   | Global Accelerator | CloudFront  |
| --------- | ------------------ | ----------- |
| Protocol  | TCP/UDP            | HTTP/HTTPS  |
| Static IP | Yes                | No          |
| Use Case  | APIs, gaming       | Web content |
| Caching   | ❌                  | ✅           |

**Exam Tip**

> APIs → **Global Accelerator**
> Static content → **CloudFront**

---

## 6. Disaster Recovery by Region (SAP-C02 GOLD)

| Strategy         | RTO       | RPO       |
| ---------------- | --------- | --------- |
| Backup & Restore | High      | High      |
| Pilot Light      | Medium    | Medium    |
| Warm Standby     | Low       | Low       |
| Active-Active    | Near-zero | Near-zero |

**Rule**

> Higher availability = higher cost + complexity

