# EventBookingSystem



flowchart LR
  Client["Client (Web / Mobile)"]
  API["API Gateway"]
  US["User Service\n(Spring Boot)"]
  ES["Event Service"]
  BS["Booking Service"]
  PS["Payment Service"]
  NS["Notification Service"]
  DB["PostgreSQL"]
  CACHE["Redis"]
  MQ["Kafka (or pluggable broker)"]
  AuthProvider["Google OAuth2"]

  Client -->|HTTPS / OAuth2| AuthProvider
  Client -->|HTTPS / Bearer JWT| API
  API --> US
  API --> ES
  API --> BS

  US --> DB
  US --> CACHE
  US -->|publish user events| MQ
  BS --> MQ
  PS --> MQ
  NS --> MQ
