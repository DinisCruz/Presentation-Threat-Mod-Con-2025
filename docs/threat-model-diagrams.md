# Threat Modeling Knowledge Graph Diagrams

## 1. High-Level Business to Infrastructure Flow

```mermaid
graph TB
    subgraph "Business Layer"
        BF1[Customer Payment Processing]
        BF2[User Authentication & Access]
        BF3[Order Fulfillment]
        BF4[Customer Data Management]
    end
    
    subgraph "Application Layer"
        APP1[PaymentService API]
        APP2[AuthService]
        APP3[OrderManagementSystem]
        APP4[CustomerPortal]
    end
    
    subgraph "Infrastructure Stack"
        ENV1[Python 3.11.5]
        ENV2[Node.js 20.9.0]
        RT1[AWS Lambda]
        RT2[Kubernetes Pod]
        COMP1[AWS us-east-1]
        COMP2[GKE Cluster prod-auth]
    end
    
    BF1 --> APP1
    BF2 --> APP2
    BF3 --> APP3
    BF4 --> APP4
    
    APP1 -.-> ENV1
    APP2 -.-> ENV2
    ENV1 -.-> RT1
    ENV2 -.-> RT2
    RT1 -.-> COMP1
    RT2 -.-> COMP2
    
    classDef business fill:#3498db,stroke:#2c3e50,stroke-width:2px
    classDef app fill:#2ecc71,stroke:#27ae60,stroke-width:2px
    classDef infra fill:#e74c3c,stroke:#c0392b,stroke-width:2px
    
    class BF1,BF2,BF3,BF4 business
    class APP1,APP2,APP3,APP4 app
    class ENV1,ENV2,RT1,RT2,COMP1,COMP2 infra
```

## 2. Payment Service Deep Dive

```mermaid
graph TD
    subgraph "PaymentService API"
        COMP1[Payment Gateway Handler]
        COMP2[Transaction Validator]
        COMP3[Payment Audit Logger]
        COMP4[Currency Converter]
        COMP5[Tokenization Service]
    end
    
    subgraph "Packages in Payment Gateway Handler"
        PKG1[stripe-python 5.1.0]
        PKG2[cryptography 41.0.5]
        PKG3[payment-core]
        PKG4[requests 2.31.0]
        PKG5[pydantic 2.4.2]
    end
    
    subgraph "Classes in payment-core"
        CLS1[PaymentProcessor]
        CLS2[CardValidator]
        CLS3[FraudDetector]
    end
    
    COMP1 --> PKG1
    COMP1 --> PKG2
    COMP1 --> PKG3
    COMP1 --> PKG4
    COMP1 --> PKG5
    
    PKG3 --> CLS1
    PKG3 --> CLS2
    PKG3 --> CLS3
    
    classDef component fill:#e74c3c,stroke:#c0392b,stroke-width:2px
    classDef package fill:#f39c12,stroke:#d68910,stroke-width:2px
    classDef _class fill:#9b59b6,stroke:#7d3c98,stroke-width:2px
    
    class COMP1,COMP2,COMP3,COMP4,COMP5 component
    class PKG1,PKG2,PKG3,PKG4,PKG5 package
    class CLS1,CLS2,CLS3 _class
```

## 3. Code Execution Flow

```mermaid
graph LR
    subgraph "PaymentProcessor Class"
        M1[process_payment]
        M2[validate_card_details]
        M3[check_fraud_score]
        M4[encrypt_sensitive_data]
    end
    
    subgraph "Code Lines"
        L1[Line 142: tokenize card]
        L2[Line 145: calculate fraud score]
        L3[Line 148: check threshold]
        L4[Line 152: encrypt payload]
        L5[Line 156: call gateway]
    end
    
    subgraph "Execution Environment"
        ENV[Python 3.11.5]
        RT[AWS Lambda]
        COMP[AWS us-east-1]
    end
    
    M1 --> L1
    M1 --> L2
    M1 --> L3
    M1 --> L4
    M1 --> L5
    
    L1 --> ENV
    L2 --> ENV
    L3 --> ENV
    L4 --> ENV
    L5 --> ENV
    
    ENV --> RT
    RT --> COMP
    
    classDef method fill:#1abc9c,stroke:#16a085,stroke-width:2px
    classDef code fill:#34495e,stroke:#2c3e50,stroke-width:2px
    classDef env fill:#16a085,stroke:#138d75,stroke-width:2px
    
    class M1,M2,M3,M4 method
    class L1,L2,L3,L4,L5 code
    class ENV,RT,COMP env
```

## 4. Threat Propagation Example

```mermaid
graph BT
    subgraph "Impact Chain"
        CVE[CVE-2023-12345 in stripe-python]
        PKG[stripe-python 5.1.0]
        COMP[Payment Gateway Handler]
        APP[PaymentService API]
        BF[Customer Payment Processing]
        IMPACT[Business Impact: Payment Data Exposure]
    end
    
    CVE -->|affects| PKG
    PKG -->|used by| COMP
    COMP -->|part of| APP
    APP -->|implements| BF
    BF -->|results in| IMPACT
    
    style CVE fill:#e74c3c,stroke:#c0392b,stroke-width:3px
    style IMPACT fill:#c0392b,stroke:#a93226,stroke-width:3px,color:#fff
```

## 5. Cross-Layer Dependencies

```mermaid
graph TB
    subgraph "Business Functions"
        BF1[Payment Processing]
        BF2[User Authentication]
    end
    
    subgraph "Shared Infrastructure"
        DB[(PostgreSQL)]
        CACHE[(Redis)]
        MQ[RabbitMQ]
    end
    
    subgraph "Runtime Environments"
        RT1[Lambda Functions]
        RT2[K8s Pods]
        RT3[Container Instances]
    end
    
    BF1 --> DB
    BF2 --> DB
    BF1 --> CACHE
    BF2 --> CACHE
    BF1 --> MQ
    
    DB --> RT2
    CACHE --> RT2
    MQ --> RT1
    
    classDef business fill:#3498db,stroke:#2c3e50,stroke-width:2px
    classDef shared fill:#95a5a6,stroke:#7f8c8d,stroke-width:2px
    classDef runtime fill:#e67e22,stroke:#d35400,stroke-width:2px
    
    class BF1,BF2 business
    class DB,CACHE,MQ shared
    class RT1,RT2,RT3 runtime
```

## 6. Security Zones and Trust Boundaries

```mermaid
graph TB
    subgraph "External Zone"
        EXT1[Public Internet]
        EXT2[3rd Party APIs]
    end
    
    subgraph "DMZ"
        WAF[Web Application Firewall]
        LB[Load Balancer]
    end
    
    subgraph "Application Zone"
        APP1[PaymentService]
        APP2[AuthService]
        APP3[CustomerPortal]
    end
    
    subgraph "Data Zone"
        DB[(Payment DB)]
        VAULT[Secrets Vault]
    end
    
    EXT1 -->|HTTPS| WAF
    WAF --> LB
    LB --> APP1
    LB --> APP2
    LB --> APP3
    
    APP1 --> DB
    APP1 --> VAULT
    APP1 -.->|API Call| EXT2
    
    style EXT1 fill:#e74c3c,stroke:#c0392b,stroke-width:2px
    style EXT2 fill:#e74c3c,stroke:#c0392b,stroke-width:2px
    style DB fill:#27ae60,stroke:#229954,stroke-width:2px
    style VAULT fill:#27ae60,stroke:#229954,stroke-width:2px
```

## 7. Attack Path Visualization

```mermaid
graph LR
    subgraph "Attack Progression"
        A1[SQL Injection in Code]
        A2[Compromise Database]
        A3[Extract Payment Tokens]
        A4[Access Tokenization Service]
        A5[Decrypt Card Data]
        A6[Exfiltrate PII]
    end
    
    subgraph "Affected Components"
        C1[Line 156: gateway call]
        C2[PostgreSQL Database]
        C3[payment_tokens table]
        C4[Tokenization Service]
        C5[HSM Module]
        C6[Customer Records]
    end
    
    A1 -->|exploits| C1
    A2 -->|targets| C2
    A3 -->|reads| C3
    A4 -->|pivots to| C4
    A5 -->|breaks| C5
    A6 -->|steals| C6
    
    C1 -.-> A2
    C2 -.-> A3
    C3 -.-> A4
    C4 -.-> A5
    C5 -.-> A6
    
    style A1 fill:#e74c3c,stroke:#c0392b,stroke-width:2px
    style A6 fill:#c0392b,stroke:#a93226,stroke-width:3px,color:#fff
```

## 8. Component Dependency Graph

```mermaid
graph TD
    subgraph "Internal Components"
        IC1[payment-core]
        IC2[auth-core]
        IC3[common-utils]
    end
    
    subgraph "Third Party Libraries"
        TP1[stripe-python 5.1.0]
        TP2[requests 2.31.0]
        TP3[cryptography 41.0.5]
        TP4[pydantic 2.4.2]
        TP5[redis-py 5.0.1]
    end
    
    subgraph "Vulnerabilities"
        V1[CVE-2023-12345<br/>CVSS: 8.1]
        V2[CVE-2023-67890<br/>CVSS: 6.5]
    end
    
    IC1 --> TP1
    IC1 --> TP2
    IC1 --> TP3
    IC2 --> TP3
    IC2 --> TP5
    IC3 --> TP4
    
    V1 -.->|affects| TP1
    V2 -.->|affects| TP2
    
    classDef internal fill:#2ecc71,stroke:#27ae60,stroke-width:2px
    classDef thirdparty fill:#f39c12,stroke:#d68910,stroke-width:2px
    classDef vuln fill:#e74c3c,stroke:#c0392b,stroke-width:2px
    
    class IC1,IC2,IC3 internal
    class TP1,TP2,TP3,TP4,TP5 thirdparty
    class V1,V2 vuln
```

## 9. Runtime Environment Mapping

```mermaid
graph TB
    subgraph "Code Artifacts"
        CODE1[payment-service.py]
        CODE2[auth-service.js]
        CODE3[fraud-detector.go]
    end
    
    subgraph "Container Images"
        IMG1[payment:v2.3.1]
        IMG2[auth:v1.8.5]
        IMG3[fraud:v3.0.2]
    end
    
    subgraph "Runtime Instances"
        RT1[Lambda: payment-prod]
        RT2[Pod: auth-deployment-7d9f]
        RT3[CloudRun: fraud-detector]
    end
    
    subgraph "Compute Resources"
        COMP1[AWS Account: 123456]
        COMP2[GKE Cluster: prod-main]
        COMP3[GCP Project: fraud-ml]
    end
    
    CODE1 --> IMG1
    CODE2 --> IMG2
    CODE3 --> IMG3
    
    IMG1 --> RT1
    IMG2 --> RT2
    IMG3 --> RT3
    
    RT1 --> COMP1
    RT2 --> COMP2
    RT3 --> COMP3
    
    classDef code fill:#34495e,stroke:#2c3e50,stroke-width:2px
    classDef image fill:#3498db,stroke:#2980b9,stroke-width:2px
    classDef runtime fill:#e67e22,stroke:#d35400,stroke-width:2px
    classDef compute fill:#c0392b,stroke:#a93226,stroke-width:2px
    
    class CODE1,CODE2,CODE3 code
    class IMG1,IMG2,IMG3 image
    class RT1,RT2,RT3 runtime
    class COMP1,COMP2,COMP3 compute
```

## 10. Data Flow and Security Controls

```mermaid
graph LR
    subgraph "User Input"
        UI[Card Details]
    end
    
    subgraph "Security Controls"
        VAL[Input Validation]
        TOK[Tokenization]
        ENC[Encryption]
        LOG[Audit Logging]
    end
    
    subgraph "Processing"
        PROC[Payment Processing]
        GW[Payment Gateway]
    end
    
    subgraph "Storage"
        DB[(Encrypted DB)]
        LOGS[(Audit Logs)]
    end
    
    UI --> VAL
    VAL -->|Valid| TOK
    VAL -->|Invalid| LOG
    TOK --> ENC
    ENC --> PROC
    PROC --> GW
    PROC --> DB
    PROC --> LOG
    LOG --> LOGS
    
    style UI fill:#e74c3c,stroke:#c0392b,stroke-width:2px
    style DB fill:#27ae60,stroke:#229954,stroke-width:2px
    style LOGS fill:#27ae60,stroke:#229954,stroke-width:2px
```

These diagrams can be rendered using any Mermaid-compatible tool or documentation platform. Each diagram focuses on a different aspect of the threat modeling knowledge graph:

1. **High-Level Flow**: Shows the complete stack from business to infrastructure
2. **Payment Service Deep Dive**: Details one service's component structure
3. **Code Execution Flow**: Traces from methods to infrastructure
4. **Threat Propagation**: Shows how vulnerabilities cascade up
5. **Cross-Layer Dependencies**: Illustrates shared resources
6. **Security Zones**: Maps trust boundaries
7. **Attack Paths**: Visualizes potential attack sequences
8. **Component Dependencies**: Shows library relationships and vulnerabilities
9. **Runtime Mapping**: Connects code to running instances
10. **Data Flow**: Shows security controls in the processing pipeline

Each diagram can be customized further based on your specific threat modeling needs!