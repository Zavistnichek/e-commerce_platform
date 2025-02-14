# e-commerce_platform

```mermaid
flowchart TD
subgraph Infrastructure
Docker[Docker / Docker Compose]
Nginx[Nginx: Reverse Proxy & Load Balancer]
CI[CI/CD: GitHub Actions]
end
subgraph Core[Django Core Service]
direction TB
Auth[Auth Service: JWT]
Products[Product Management: CRUD]
Orders[Order Management: Status Tracking]
Cart[Cart Service: Session-based]
Admin[Admin Dashboard: Django Admin]
DRF[Django REST Framework: API]
end
subgraph Integrations[FastAPI Integration Services]
direction TB
Kaspi[Kaspi API Adapter: Sync Products/Orders]
Ozon[Ozon API Adapter: Sync Products/Orders]
Notifications[Notification Service: WhatsApp/Telegram]
CeleryWorker[Celery Worker: Async Tasks]
end
Redis[(Redis\nTask Queue & Cache)]
subgraph Monitoring
Prometheus[Prometheus: Metrics]
Grafana[Grafana: Dashboards]
Sentry[Sentry: Error Tracking]
Logstash[Logstash: Log Processing]
Elasticsearch[(Elasticsearch)]
Kibana[Kibana: Visualization]
end
subgraph ExternalSystems[External Systems]
Payment[Payment Gateways: Kaspi, Stripe]
WhatsApp[(WhatsApp Business API)]
Telegram[(Telegram Bot API)]
KaspiAPI[(Kaspi API)]
OzonAPI[(Ozon API)]
end
%% Interactions
User <-->|HTTPS| Nginx
Nginx --> Core
Nginx --> Integrations
Core -->|HTTP| Integrations
Core -->|PostgreSQL| DB[(PostgreSQL)]
Integrations -->|Celery| Redis
Integrations -->|Async Calls| ExternalSystems
Kaspi -->|REST API| KaspiAPI
Ozon -->|REST API| OzonAPI
Notifications --> WhatsApp
Notifications --> Telegram
Prometheus -->|Metrics| Core
Prometheus -->|Metrics| Integrations
Grafana --> Prometheus
Sentry -->|Errors| Core
Sentry -->|Errors| Integrations
Core -->|Logs| Logstash
Integrations -->|Logs| Logstash
Logstash -->|Processed Logs| Elasticsearch
Kibana -->|Visualize| Elasticsearch
Docker --> Core
Docker --> Integrations
Docker --> DB
Docker --> Redis
Docker --> Nginx
Docker --> Logstash
Docker --> Elasticsearch
Docker --> Kibana
CI -->|Deploy| Docker
classDef box fill:#f9f9f9,stroke:#333,stroke-width:2px;
classDef service fill:#e1f5fe,stroke:#039be5;
classDef external fill:#ffebee,stroke:#ff5252;
classDef db fill:#f0f4c3,stroke:#afb42b;
class Core,Integrations,Monitoring service
class ExternalSystems,Payment,WhatsApp,Telegram,KaspiAPI,OzonAPI external
class DB,Redis,Elasticsearch db
class Infrastructure,CI box