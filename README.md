# EventBookingSystem

* High-Level (Context) Diagram


```mermaid

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

```


* Component Diagram (Internal User Service)

```mermaid

graph TD
  subgraph user-service
    UC[UserController]
    USvc[UserService]
    Auth[AuthController / OAuth2 Handler]
    LoginSvc[LoginService (strategy factory)]
    EmailLogin[EmailLoginStrategy]
    GoogleLogin[GoogleLoginStrategy]
    Repo[UserRepository (JPA)]
    Mapper[UserMapper (MapStruct)]
    Jwt[JwtTokenProvider]
    MsgPub[EventPublisher (interface)]
    KafkaPub[KafkaEventPublisher]
    RedisCache[Redis (cache)]
    Flyway[Flyway migrations]
    Metrics[Micrometer]
  end

  UC --> USvc
  UC --> LoginSvc
  LoginSvc --> EmailLogin
  LoginSvc --> GoogleLogin
  USvc --> Repo
  USvc --> Mapper
  USvc --> Jwt
  USvc --> MsgPub
  MsgPub --> KafkaPub
  USvc --> RedisCache
  user-service --> Flyway
  user-service --> Metrics


```



* Booking/Signup/Login Sequence (Mermaid) — includes Google OAuth

``` mermaid

sequenceDiagram
  participant C as Client
  participant GW as API Gateway
  participant US as User Service
  participant GP as Google OAuth2
  participant DB as Postgres
  participant MQ as Kafka

  C->>GP: Click "Login with Google" -> redirect to Google consent
  GP-->>C: Google redirects back with code
  C->>GW: /auth/oauth2/callback?code=...
  GW->>US: POST /auth/oauth2/callback (code)
  US->>GP: Exchange code for access token (server side)
  GP-->>US: returns user profile (email, name, picture)
  US->>DB: find user by email
  alt user exists
    US->>US: issue JWT
  else
    US->>DB: create user (role=USER)
    US->>MQ: publish USER_REGISTERED event
    US->>US: issue JWT
  end
  US-->>GW: JWT
  GW-->>C: JWT (or redirect with token)


```



