# Scaling Threat Modeling with Semantic Knowledge Graphs: A Multi-Layer Graph-of-Graphs Approach

## Executive Summary

This document presents a comprehensive framework for scaling threat modeling through semantic knowledge graphs, implementing a "graph of graphs" architecture that enables multi-level zoom capabilities across enterprise systems. By creating interconnected threat models at different abstraction levels - from business functions down to individual lines of code and up through infrastructure layers - organizations can achieve unprecedented visibility into security risks and their propagation paths.

## 1. Introduction: The Graph-of-Graphs Concept

Traditional threat modeling often creates isolated models for individual systems or components. This approach fails to capture the complex interdependencies in modern distributed architectures. Our framework introduces a **graph-of-graphs architecture** where:

- Each layer of the system (business, application, code, infrastructure) has its own threat model graph
- These graphs are semantically linked, creating navigable relationships between abstraction levels
- Security teams can "zoom" between levels, tracking threats from technical vulnerabilities to business impact
- The entire structure forms a semantic knowledge graph enabling automated threat analysis

## 2. Architecture Overview

### 2.1 Vertical Layer Structure

The framework consists of 10 interconnected layers, each representing a different abstraction level:

1. **Business Function Layer** - Strategic business capabilities
2. **Application Layer** - Systems implementing business functions  
3. **Component Layer** - Modular parts of applications
4. **Package Layer** - Code libraries (internal and third-party)
5. **Class Layer** - Object-oriented code structures
6. **Method Layer** - Individual functions/procedures
7. **Source Code Layer** - Actual lines of code
8. **Environment Layer** - Execution environments (Python, Node.js, etc.)
9. **Runtime Layer** - Deployment platforms (Lambda, Kubernetes, etc.)
10. **Compute Layer** - Infrastructure providers (AWS, GCP, Azure)

### 2.2 Relationship Types

**Vertical Relationships (Parent-Child):**
- Business Function → Applications (1:many)
- Application → Components (1:many)
- Component → Packages (1:many)
- Package → Classes (1:many)
- Class → Methods (1:many)
- Method → Lines of Code (1:many)

**Horizontal/Cross-cutting Relationships:**
- Lines of Code → Execution Environment (many:1)
- Execution Environment → Runtime (many:1)
- Runtime → Compute Environment (many:1)

**Semantic Relationships:**
- "depends-on" - Package dependencies
- "executes-in" - Code to environment mapping
- "deployed-on" - Runtime to infrastructure mapping
- "processes-data-from" - Data flow between components
- "authenticates-via" - Authentication dependencies
- "exposes-api" - External interface exposure

## 3. Detailed Layer Specifications with Examples

### 3.1 Business Function Layer

**Purpose:** Represents high-level business capabilities that generate value for the organization.

**Example Nodes:**
- **Customer Payment Processing** - Handles all payment-related operations including credit card processing, refunds, and payment reconciliation
- **User Authentication & Access** - Manages user identity, authentication, authorization, and session management
- **Order Fulfillment** - Processes customer orders from placement through delivery
- **Customer Data Management** - Handles personal information, preferences, and account management

**Threat Modeling Considerations:**
- Business impact analysis
- Compliance requirements (PCI-DSS for payments, GDPR for data)
- Revenue risk assessment
- Reputation impact evaluation

### 3.2 Application Layer

**Purpose:** Systems and services that implement business functions.

**Example Nodes:**
- **PaymentService API** 
  - Implements: Customer Payment Processing
  - Type: RESTful API service
  - Criticality: High (revenue-impacting)
  
- **AuthService**
  - Implements: User Authentication & Access
  - Type: Microservice
  - Criticality: High (security boundary)
  
- **OrderManagementSystem**
  - Implements: Order Fulfillment
  - Type: Event-driven service
  - Criticality: Medium
  
- **CustomerPortal**
  - Implements: Customer Data Management
  - Type: Web application
  - Criticality: High (PII handling)

**Threat Modeling Considerations:**
- API attack surface
- Service-to-service authentication
- Data flow between applications
- Deployment architecture risks

### 3.3 Component Layer

**Purpose:** Modular parts within applications that handle specific functionalities.

**Example Nodes (for PaymentService API):**
- **Payment Gateway Handler**
  - Function: Interfaces with external payment providers (Stripe, PayPal)
  - Dependencies: External API connectivity
  - Security: API key management, TLS requirements
  
- **Transaction Validator**
  - Function: Validates payment details and performs fraud checks
  - Dependencies: Fraud detection rules engine
  - Security: Input validation, rate limiting
  
- **Payment Audit Logger**
  - Function: Creates immutable audit trails for compliance
  - Dependencies: Secure log storage
  - Security: Log integrity, access control
  
- **Currency Converter**
  - Function: Real-time currency conversion for international payments
  - Dependencies: External FX rate APIs
  - Security: Rate manipulation risks
  
- **Tokenization Service**
  - Function: Converts sensitive card data to secure tokens
  - Dependencies: HSM/Vault integration
  - Security: Key management, token generation

**Threat Modeling Considerations:**
- Component isolation boundaries
- Inter-component communication security
- Shared resource access
- Component-specific vulnerabilities

### 3.4 Package Layer

**Purpose:** Code libraries and dependencies, both internal and third-party.

**Example Nodes (for Payment Gateway Handler):**
- **stripe-python 5.1.0**
  - Type: Third-party payment processing SDK
  - License: MIT
  - Known vulnerabilities: CVE tracking required
  
- **cryptography 41.0.5**
  - Type: Third-party encryption library
  - License: Apache 2.0
  - Security: Cryptographic implementation correctness
  
- **payment-core**
  - Type: Internal package
  - Version: 2.3.1
  - Security: Business logic vulnerabilities
  
- **requests 2.31.0**
  - Type: Third-party HTTP client
  - License: Apache 2.0
  - Security: SSRF vulnerabilities
  
- **pydantic 2.4.2**
  - Type: Third-party data validation
  - License: MIT
  - Security: Validation bypass risks
  
- **payment-utils**
  - Type: Internal utilities
  - Version: 1.2.0
  - Security: Helper function vulnerabilities

**Threat Modeling Considerations:**
- Supply chain vulnerabilities
- License compliance
- Version currency
- Transitive dependencies
- Known CVEs

### 3.5 Class Layer

**Purpose:** Object-oriented code structures within packages.

**Example Nodes (for payment-core package):**
- **PaymentProcessor**
  - Responsibility: Main payment flow orchestration
  - State management: Transaction state machine
  - Security: State transition validation
  
- **CardValidator**
  - Responsibility: Credit card validation logic
  - Algorithms: Luhn check, BIN validation
  - Security: PCI compliance requirements
  
- **PaymentRequest**
  - Responsibility: Input data model
  - Validation: Schema enforcement
  - Security: Input sanitization
  
- **PaymentResponse**
  - Responsibility: Output data model
  - Filtering: Sensitive data masking
  - Security: Information disclosure
  
- **FraudDetector**
  - Responsibility: Risk scoring algorithms
  - ML models: Anomaly detection
  - Security: Model poisoning risks
  
- **PaymentException**
  - Responsibility: Error handling
  - Information: Error message control
  - Security: Information leakage

**Threat Modeling Considerations:**
- Class responsibility boundaries
- State management security
- Inheritance hierarchy risks
- Access modifier enforcement

### 3.6 Method Layer

**Purpose:** Individual functions and procedures within classes.

**Example Nodes (for PaymentProcessor class):**
- **process_payment()**
  - Parameters: payment_request, user_context
  - Returns: PaymentResponse
  - Security: Main attack surface
  
- **validate_card_details()**
  - Parameters: card_number, cvv, expiry
  - Returns: validation_result
  - Security: PCI data handling
  
- **check_fraud_score()**
  - Parameters: payment_request, user_history
  - Returns: risk_score (0-100)
  - Security: Bypass attempts
  
- **encrypt_sensitive_data()**
  - Parameters: payment_data, encryption_key
  - Returns: encrypted_payload
  - Security: Cryptographic implementation
  
- **call_payment_gateway()**
  - Parameters: gateway_request
  - Returns: gateway_response
  - Security: External API security
  
- **handle_payment_response()**
  - Parameters: raw_response
  - Returns: processed_response
  - Security: Response validation

**Threat Modeling Considerations:**
- Parameter validation
- Return value sanitization
- Exception handling
- Side effects and state changes

### 3.7 Source Code Layer

**Purpose:** Actual lines of code with specific vulnerability points.

**Example Nodes (for process_payment() method):**
- **Line 142:** `card_token = self.tokenization_service.tokenize(card_details)`
  - Risk: Token generation weakness
  - Controls: HSM integration
  
- **Line 145:** `fraud_score = self.fraud_detector.calculate_risk_score(payment_request)`
  - Risk: ML model bypass
  - Controls: Threshold validation
  
- **Line 148:** `if fraud_score > FRAUD_THRESHOLD: raise FraudException()`
  - Risk: Threshold manipulation
  - Controls: Constant protection
  
- **Line 152:** `encrypted_payload = self.encrypt_sensitive_data(payment_data)`
  - Risk: Weak encryption
  - Controls: Algorithm selection
  
- **Line 156:** `response = self.gateway_client.charge(encrypted_payload)`
  - Risk: API injection
  - Controls: Input sanitization
  
- **Line 160:** `audit_log.record(transaction_id, response.status)`
  - Risk: Log injection
  - Controls: Output encoding

**Threat Modeling Considerations:**
- Code-level vulnerabilities (SQLi, XSS, etc.)
- Cryptographic implementation flaws
- Race conditions
- Resource leaks

### 3.8 Environment Layer

**Purpose:** Execution environments and their security configurations.

**Example Nodes:**
- **Python 3.11.5**
  - Used by: Payment services
  - Security: Known interpreter vulnerabilities
  - Configuration: Security settings
  
- **Node.js 20.9.0**
  - Used by: Real-time notification systems
  - Security: Prototype pollution risks
  - Configuration: Security headers
  
- **Java 17**
  - Used by: Legacy transaction systems
  - Security: Deserialization vulnerabilities
  - Configuration: JVM security manager
  
- **Go 1.21**
  - Used by: High-performance validators
  - Security: Memory safety
  - Configuration: Compiler flags

**Threat Modeling Considerations:**
- Language-specific vulnerabilities
- Runtime configuration weaknesses
- Environment variable exposure
- Dependency resolution attacks

### 3.9 Runtime Layer

**Purpose:** Deployment platforms and orchestration systems.

**Example Nodes:**
- **AWS Lambda** 
  - Hosts: PaymentProcessor functions
  - Configuration: 3GB memory, 30s timeout
  - Security: Function isolation, IAM roles
  
- **Kubernetes Pod**
  - Hosts: AuthService
  - Configuration: 2 replicas, health checks
  - Security: Pod security policies, RBAC
  
- **Azure Container Instance**
  - Hosts: FraudDetector
  - Configuration: Isolated network
  - Security: Container scanning, secrets management
  
- **Google Cloud Run**
  - Hosts: NotificationService
  - Configuration: Auto-scaling 0-100
  - Security: Service identity, ingress control

**Threat Modeling Considerations:**
- Container escape vulnerabilities
- Orchestration platform weaknesses
- Secret management
- Network isolation

### 3.10 Compute Layer

**Purpose:** Underlying infrastructure and cloud providers.

**Example Nodes:**
- **AWS us-east-1**
  - Purpose: Primary payment processing region
  - Compliance: PCI-DSS certified
  - Security: VPC isolation, CloudTrail logging
  
- **AWS eu-west-1**
  - Purpose: GDPR-compliant European processing
  - Compliance: Data residency requirements
  - Security: Encryption at rest, network segmentation
  
- **GKE Cluster "prod-auth"**
  - Purpose: Google Kubernetes Engine for authentication
  - Configuration: 10 nodes, auto-scaling
  - Security: Workload identity, binary authorization
  
- **Azure AKS "fraud-cluster"**
  - Purpose: Dedicated fraud detection processing
  - Configuration: GPU-enabled nodes
  - Security: Azure AD integration, network policies

**Threat Modeling Considerations:**
- Cloud provider vulnerabilities
- Multi-tenancy risks
- Geographic compliance
- Infrastructure-level attacks

## 4. Threat Propagation Scenarios

### 4.1 Scenario 1: Third-Party Library Vulnerability

**Threat:** CVE-2023-12345 discovered in stripe-python 5.1.0 allowing authentication bypass

**Propagation Path:**
1. **Package Layer:** stripe-python 5.1.0 (vulnerable)
2. **Component Layer:** Payment Gateway Handler (uses vulnerable package)
3. **Application Layer:** PaymentService API (contains vulnerable component)
4. **Business Layer:** Customer Payment Processing (business function at risk)

**Impact Analysis:**
- All payment transactions potentially compromised
- PCI compliance violation
- Financial loss risk
- Reputation damage

**Mitigation Path (using the graph):**
1. Identify all components using stripe-python 5.1.0
2. Trace to affected applications
3. Determine business impact
4. Prioritize patching based on business criticality

### 4.2 Scenario 2: Runtime Compromise

**Threat:** Kubernetes pod running AuthService compromised via container escape

**Propagation Path:**
1. **Runtime Layer:** Kubernetes Pod (compromised)
2. **Environment Layer:** All Node.js 20.9.0 processes in pod
3. **Code Layer:** All authentication code executing
4. **Method Layer:** Authentication methods exposed
5. **Application Layer:** AuthService fully compromised
6. **Business Layer:** User Authentication & Access function failed

**Impact Analysis:**
- Authentication bypass possible
- Session hijacking risk
- Lateral movement to other pods
- Complete auth system compromise

### 4.3 Scenario 3: Code-Level SQL Injection

**Threat:** SQL injection vulnerability in payment processing code

**Propagation Path:**
1. **Code Layer:** Line 156 contains SQL injection
2. **Method Layer:** call_payment_gateway() method vulnerable
3. **Class Layer:** PaymentProcessor class affected
4. **Package Layer:** payment-core package contains vulnerability
5. **Component Layer:** Payment Gateway Handler at risk
6. **Application Layer:** PaymentService API compromised
7. **Business Layer:** Customer Payment Processing impacted

**Cross-Layer Impact:**
- Database compromise (infrastructure)
- Payment record manipulation (data integrity)
- Compliance violation (regulatory)
- Financial loss (business)

## 5. Benefits of the Graph-of-Graphs Approach

### 5.1 Comprehensive Threat Visibility
- End-to-end threat tracking from infrastructure to business impact
- No blind spots between abstraction layers
- Complete dependency chain visibility

### 5.2 Efficient Risk Prioritization
- Business impact-driven prioritization
- Resource allocation based on threat propagation paths
- Automated risk scoring using graph algorithms

### 5.3 Scalable Threat Modeling
- Modular threat models at each layer
- Reusable component models
- Incremental model building

### 5.4 Automated Threat Analysis
- Graph queries for vulnerability impact
- Automated compliance checking
- Continuous threat model validation

### 5.5 Multi-Team Collaboration
- Business teams understand impact
- Development teams see code-level issues
- Operations teams track infrastructure risks
- Security teams have unified view

## 6. Implementation Considerations

### 6.1 Graph Technologies
- **Neo4j** for property graphs
- **RDF/OWL** for semantic relationships
- **GraphQL** for querying
- **STIX/TAXII** for threat intelligence integration

### 6.2 Data Collection
- SAST/DAST tool integration
- SBOM (Software Bill of Materials) import
- Cloud asset inventory sync
- Code repository mining

### 6.3 Visualization Requirements
- Interactive zoom between layers
- Threat propagation animation
- Risk heat maps at each layer
- Relationship filtering

### 6.4 Query Examples
```cypher
// Find all business functions affected by a CVE
MATCH (cve:CVE {id: 'CVE-2023-12345'})-[:AFFECTS]->(pkg:Package)
      -[:USED_BY*]->(bf:BusinessFunction)
RETURN DISTINCT bf.name

// Calculate blast radius of infrastructure compromise
MATCH (infra:Compute {name: 'AWS us-east-1'})-[:HOSTS]->(runtime:Runtime)
      -[:EXECUTES]->(code:Code)-[:IMPLEMENTS*]->(bf:BusinessFunction)
RETURN bf.name, COUNT(DISTINCT code) as exposed_code_paths
```

## 7. Conclusion

The graph-of-graphs approach to threat modeling provides organizations with unprecedented visibility into their security posture. By connecting threat models across all abstraction layers - from business functions to infrastructure - security teams can:

- Understand the true business impact of technical vulnerabilities
- Prioritize remediation based on actual risk
- Automate threat analysis and impact assessment
- Scale threat modeling across large, complex enterprises

This multi-layer semantic knowledge graph transforms threat modeling from a point-in-time exercise to a living, queryable knowledge base that evolves with the organization's architecture.