🧩 Core Development Setup


Component	Recommended Version / Tool	Notes
Java	17+	(LTS and compatible with Spring Boot 3.x)
Spring Boot CLI or Initializr	spring.io	Easiest way to generate projects
IDE	IntelliJ IDEA (Community or Ultimate)	or Eclipse / VS Code with Spring Extensions
Build Tool	Maven or Gradle	(Pick one — Maven simpler for start)
PostgreSQL	Latest stable	For persistent data
Redis	Optional (for caching, seat locking)	Install locally via Docker or brew
RabbitMQ or Kafka	For async eventing	Start with RabbitMQ (lighter)
Docker Desktop	For containerization	Will be useful for deployment
Git + GitHub	Version control	Push all code + docs
Postman / Insomnia	For API testing	Verify endpoints easily
Swagger / OpenAPI	Springdoc OpenAPI	To document APIs automatically


Optional (for production or advanced setups)

Render / Railway / AWS Free Tier – to deploy backend
Supabase / Neon.tech – free cloud PostgreSQL
Vercel – for frontend hosting
Prometheus + Grafana – for metrics (optional)
