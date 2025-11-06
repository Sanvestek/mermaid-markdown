flowchart TB
  %% ===== Clients / Code =====
  subgraph Clients["Clients & Codebases"]
    WebCode["Web App Code (React/Next.js)\nSource: GitHub/GitLab/Bitbucket"]
    MobileCode["Mobile App Code (React Native/Expo)\nSource: GitHub/GitLab/Bitbucket"]
    WorkersCode["Workers/Jobs Code (TypeScript)\nSource: GitHub/GitLab/Bitbucket"]
  end

  subgraph CI["Build & Release"]
    CIEngine["CI/CD: GitHub Actions / GitLab CI / CircleCI / Jenkins"]
    Registry["Container Registry: GHCR / Docker Hub / ECR / GCR / ACR"]
    StaticHost["Static Hosting (Web):\nVercel / Netlify / S3+CloudFront / Cloudflare Pages"]
    AppStores["Mobile Distribution:\nApple App Store / Google Play\n(OTA: Expo EAS Updates / CodePush)"]
  end

  %% ===== Edge / Front Door =====
  subgraph Edge["Front Door (Edge Layer)"]
    DNS["DNS + TLS"]
    CDN["CDN & Caching:\nCloudflare / Fastly / CloudFront / Azure Front Door"]
    WAF["WAF + Rate Limiting:\nCloudflare / AWS WAF / Cloud Armor / Azure WAF"]
    Router["Routing → Web / API / Webhooks / Canary"]
  end

  %% ===== Compute / Services =====
  subgraph Services["Application Services (stateless containers)"]
    API["Core API (REST/GraphQL):\nFargate / Cloud Run / Azure Container Apps / Kubernetes"]
    Integrations["PoS Integration Service:\nwebhooks + polling + normalization"]
    Async["Async Workers & Schedulers:\ncontainers + cron"]
  end

  %% ===== Data Platform =====
  subgraph Data["Data Platform & Storage"]
    DB["Relational DB (User & App Data):\nPostgres (RDS / Cloud SQL / Azure PG / Aiven / Neon)\nBackups + PITR + (optional) replicas"]
    Cache["Cache:\nRedis (ElastiCache / Memorystore / Azure Cache / Upstash)"]
    Obj["Object Storage (images, PDFs, exports):\nS3 / GCS / Azure Blob / Cloudflare R2\n(private via Signed URLs)"]
    Media["Video Pipeline (optional):\nMux / AWS MediaConvert / GCP Transcoder / Azure Media Services"]
    Queue["Queue/Event Bus:\nSQS / Service Bus / Pub/Sub / RabbitMQ / Kafka\n(with DLQ)"]
    Secrets["Secrets:\nVault / AWS Secrets Manager / GCP Secret Manager / Azure Key Vault"]
  end

  %% ===== Platform extras =====
  subgraph Platform["Platform Services"]
    Auth["Identity & Auth:\nAuth0 / Firebase Auth / Ory / Entra ID B2C (SSO/MFA)"]
    Notify["Notifications:\nPush = FCM/APNs | Email = SendGrid/Mailgun/SES"]
    Observ["Observability:\nDatadog / New Relic / Grafana / CloudWatch / Cloud Monitoring"]
    Analytics["Analytics Warehouse & BI:\nBigQuery / Redshift / Snowflake / Synapse\nDashboards: Metabase / Power BI / Looker Studio"]
  end

  %% ===== External vendors =====
  subgraph Vendors["External Systems"]
    PoS["PoS Vendors: Square / Toast / Clover / Others"]
  end

  %% ===== Flows =====
  %% Code → CI/CD → Hosting/Registry
  WebCode --> CIEngine
  MobileCode --> CIEngine
  WorkersCode --> CIEngine
  CIEngine --> Registry
  CIEngine --> StaticHost
  CIEngine --> AppStores

  %% Clients hitting edge
  StaticHost --> Edge
  AppStores --> Clients
  Clients --> Edge

  %% Edge to services
  Edge --> API
  Edge --> Integrations
  Edge --> Async

  %% Services to data platform
  API --> DB
  API --> Cache
  API --> Obj
  API --> Queue
  Integrations --> DB
  Integrations --> Obj
  Integrations --> Queue
  Async --> DB
  Async --> Obj
  Async --> Queue

  %% Data flows to analytics
  DB --> Analytics
  Queue --> Analytics

  %% Auth / Notify / Secrets / Observability
  Auth --> API
  Auth --> Edge
  Notify --> API
  Notify --> Async
  Secrets --> API
  Secrets --> Integrations
  Secrets --> Async
  Observ --> API
  Observ --> Integrations
  Observ --> Async

  %% PoS vendors
  PoS --> Integrations
