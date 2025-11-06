```mermaid

flowchart TB
    subgraph Clients["Devices / Codebases"]
        Web["Web App (React / Next.js)"]
        Mobile["Mobile App (React Native / Expo)"]
        WorkersApp["Job Workers (TS)"]
    end

    subgraph FrontDoor["Front Door (Edge Layer)"]
        CDN["CDN / Caching\n(Cloudflare / Fastly / CloudFront)"]
        WAF["WAF + Rate Limiting"]
        Routing["Routing: Web / API / Webhooks"]
    end

    subgraph API["Core API (Stateless Containers)"]
        APIService["REST / GraphQL API\n(Fargate / Cloud Run / Container Apps / Kubernetes)"]
    end

    subgraph Integration["PoS Integration Service"]
        VendorConnect["OAuth + Webhook Handler + Polling"]
    end

    subgraph Async["Async Jobs & Schedulers"]
        JobWorkers["Job Runner Containers"]
        Cron["Scheduled Tasks (daily/weekly)"]
    end

    subgraph Data["State & Storage"]
        DB["Postgres (RDS / Cloud SQL / Azure PG / Neon)"]
        Storage["Object Storage (S3 / Blob / GCS / R2)\nSigned URLs + Video Pipeline (Mux optional)"]
        Queue["Queue / Event Bus\n(SQS / Service Bus / PubSub / RabbitMQ / Kafka)"]
    end

    subgraph Platform["Platform Services"]
        Auth["Identity & Auth\n(Auth0 / Firebase Auth / Ory / Entra B2C)"]
        Notify["Notifications\n(FCM/APNs + SendGrid/Mailgun/SES)"]
        Analytics["Analytics Warehouse\n(BigQuery / Redshift / Snowflake / Synapse)"]
    end

    subgraph External["External Systems"]
        PoS["PoS Vendors\n(Square / Toast / Clover / etc.)"]
    end

    %% Connections
    Web --> FrontDoor
    Mobile --> FrontDoor
    WorkersApp --> Async

    FrontDoor --> APIService
    FrontDoor --> VendorConnect
    FrontDoor --> JobWorkers

    APIService --> DB
    APIService --> Storage
    APIService --> Queue

    VendorConnect --> DB
    VendorConnect --> Storage
    VendorConnect --> Queue
    PoS --> VendorConnect

    JobWorkers --> DB
    JobWorkers --> Queue
    JobWorkers --> Storage

    Queue --> Analytics
    DB --> Analytics

    Auth --> APIService
    Auth --> FrontDoor

    Notify --> APIService
    Notify --> JobWorkers
