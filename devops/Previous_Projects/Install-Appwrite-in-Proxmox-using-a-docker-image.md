---
title: "Install Appwrite in Proxmox Using a Docker Image"
date: 2025-02-22T07:02:22Z
draft: false
---
## Websites
- **Official Website:** [https://appwrite.io/](https://appwrite.io/)

- **GitHub Repository:** [https://github.com/appwrite](https://github.com/appwrite)

- **Documentation:** [https://appwrite.io/docs](https://appwrite.io/docs)

- **Self-Hosting Guide:** [https://appwrite.io/docs/advanced/self-hosting](https://appwrite.io/docs/advanced/self-hosting)

- **Awesome Appwrite (Community Resources):** [https://github.com/appwrite/awesome-appwrite](https://github.com/appwrite/awesome-appwrite)
---
## What is Appwrite
- **🛠 Backend-as-a-Service (BaaS):**  
  - Open-source platform to simplify backend development for web, mobile, and Flutter apps.  

- **🔒 Authentication & Authorization:**  
  - Built-in user management with support for email/password, OAuth (Google, Facebook, etc.), anonymous login, and JWT.  
  - Role-Based Access Control (RBAC) for fine-grained permissions.  

- **🗃 Database as a Service:**  
  - NoSQL-style database with collections and documents.  
  - Real-time data updates with WebSocket subscriptions.  

- **📁 Secure File Storage:**  
  - Manage file uploads with built-in storage service.  
  - Supports permissions, previews, and CDN access.  

- **⚡ Serverless Functions:**  
  - Run custom backend code in response to events (e.g., user signup, file upload).  
  - Supports multiple runtimes like Node.js, Python, PHP, etc.  

- **📡 Real-Time API:**  
  - WebSocket support for real-time features like chat apps or live updates.  

- **🔑 Security First:**  
  - Data encryption, SSL/TLS, and security rules for every resource.  
  - Protects against common vulnerabilities (e.g., SQL Injection, XSS).  

- **🛳 Self-Hostable with Docker:**  
  - Fully containerized and easy to deploy using Docker or Kubernetes.  

- **📦 Multi-Platform SDKs:**  
  - Provides SDKs for Web, Flutter, iOS, Android, Node.js, and more.  

- **🔗 Extensible via APIs:**  
  - REST, GraphQL, and WebSocket APIs for seamless integration.  
---
## Uses of Appwrite
1. **🛡 User Authentication & Authorization:**  
   - Implement secure user login with email/password, OAuth (Google, GitHub, etc.), or anonymous login.  
   - Manage sessions, JWT tokens, and role-based access control (RBAC).  

2. **🗃 Backend Database Management:**  
   - Build and manage NoSQL-style collections and documents.  
   - Enable real-time data syncing with WebSockets for live updates in apps.  

3. **📁 File Storage & Management:**  
   - Handle file uploads, storage, and access control with built-in CDN support.  
   - Useful for media-heavy apps like social networks or file-sharing platforms.  

4. **⚡ Serverless Functions:**  
   - Run backend code on-demand or in response to events (e.g., user registration, file upload).  
   - Supports custom APIs, cron jobs, and data processing.  

5. **📡 Real-Time Applications:**  
   - Build apps like chat platforms, collaborative tools, or live dashboards using WebSockets.  

6. **🔔 Notifications & Triggers:**  
   - Set up event-driven workflows (e.g., send welcome emails on user signup) using functions.  

7. **📊 Analytics & Insights:**  
   - Track user behavior, file uploads, and database activity through Appwrite's built-in stats.  

8. **💾 Multi-Platform Backend Integration:**  
   - Build and connect backend services for web, mobile (iOS, Android), and Flutter apps with official SDKs.  

9. **🌐 API-First Development:**  
   - Expose REST, GraphQL, and WebSocket APIs for custom frontend or microservices integration.  

10. **🛳 Self-Hosting & Customization:**  
   - Deploy Appwrite on your own servers, Docker, Kubernetes, or cloud platforms for full control.  
---
## Features of Appwrite
**Appwrite** is an open-source **Backend-as-a-Service (BaaS)** platform that helps developers build secure and scalable applications faster by providing ready-to-use APIs and services. It abstracts common backend tasks, letting developers focus more on the frontend and business logic.

### Core Features of Appwrite
1. **User Authentication & Management:**  
   - Supports email/password, OAuth (Google, Facebook, etc.), anonymous login, JWT, and more.  
   - Role-based access control (RBAC) and sessions management.

2. **Database as a Service:**  
   - NoSQL database with structured collections and documents.  
   - Real-time data updates using subscriptions.

3. **Storage:**  
   - Secure file storage with permissions, previews, and CDN support.  
   - Direct uploads from clients with access control.

4. **Functions (Serverless):**  
   - Run backend code in response to events (like uploads or user registration).  
   - Supports multiple runtimes (Node.js, Python, PHP, etc.).

5. **Real-time & WebSockets:**  
   - Real-time updates for databases, users, and storage using WebSockets.

6. **Security First:**  
   - Built-in data encryption, SSL/TLS support, and permissions on every resource.  
   - Prevents common vulnerabilities (like SQL Injection, XSS).

7. **Self-hostable:**  
   - You can deploy Appwrite on your own infrastructure using **Docker**.  

8. **API & SDKs:**  
   - REST, GraphQL, and WebSocket APIs.  
   - SDKs for Web, Flutter, iOS, Android, Node.js, and more.

---
### Use Cases for Appwrite
- Building web and mobile applications without managing a backend.  
- Managing users, databases, and storage in one place.  
- Rapid prototyping with a full-featured backend.  

### 🐳 **Self-Hosting Appwrite with Docker:**  
Appwrite is designed with Docker in mind and can be spun up using `docker-compose` easily.
---
## Install Appwrite using Docker

1. **🛠 Set Up LXC Container (if not already done):**  
   - **OS Template:** Use **Ubuntu 22.04** or **Debian 12** for the LXC container.  
   - **Privileged LXC:** Appwrite needs some privileged access, so create a **privileged LXC** or adjust permissions.  
   - **Resources:** Allocate at least **2 CPU cores**, **4GB RAM**, and **20GB+ disk space**.

2. ** Install Docker & Docker Compose:**  
   Inside the LXC container:  
```bash
nano compose.yaml   
```

3. ** Create Docker Compose File:**  
Put this in the file

```yaml
version: '3.8'

services:
  appwrite:
    image: appwrite/appwrite:latest
    container_name: appwrite
    ports:
      - "${APPWRITE_PORT_HTTP}:80"
      - "${APPWRITE_PORT_HTTPS}:443"
    environment:
      - _APP_ENV=${APPWRITE_ENV}
      - _APP_OPENSSL_KEY_V1=${APPWRITE_SSL_KEY}
      - _APP_DOMAIN=${APPWRITE_DOMAIN}
      - _APP_DOMAIN_TARGET=${APPWRITE_DOMAIN_TARGET}
      - _APP_STORAGE_LIMIT=${APPWRITE_STORAGE_LIMIT}
    volumes:
      - ./appwrite-data:/storage:rw
    restart: unless-stopped

```
4. **Create .env file**
``` bash
nano .env
```
Put this in the file:
```
# Appwrite Environment
APPWRITE_ENV=production

# Ports
APPWRITE_PORT_HTTP=80
APPWRITE_PORT_HTTPS=443

# Domain Configuration
APPWRITE_DOMAIN=your-domain.com
APPWRITE_DOMAIN_TARGET=your-domain.com

# SSL Key (Generate using: openssl rand -base64 32)
APPWRITE_SSL_KEY=your-random-generated-key

# Storage Limit (in bytes)
APPWRITE_STORAGE_LIMIT=10000000

```

   **Replace:**  
   - `your-random-key` → Generate with `openssl rand -base64 32`.  
   - `your-domain.com` → Your domain or Proxmox local IP.

5. **Start Appwrite:**  
```bash
docker-compose up -d
```

6. **Access Appwrite:**  
   - Navigate to `http://<Proxmox-IP>` or `https://your-domain.com`.  
   - Follow the initial setup wizard.

7. **(Optional) Setup SSL/TLS:**  
   - Use **NGINX Proxy Manager** or **Caddy** for auto-SSL if you're exposing it externally.  
   - Or use **Let's Encrypt** within the Appwrite setup.
---

##  Benefits of Running Appwrite on Proxmox
- Full control over the environment.  
- Easy snapshots and backups using Proxmox tools.  
- Low overhead with **LXC** compared to VMs.  

---
## Recommended Solutions for Logs in Docker/Kubernetes for Spring Cloud:

1. **ELK Stack (Elasticsearch, Logstash, Kibana):**  
   - Collect logs with **Filebeat** or **Logstash**.  
   - Store in **Elasticsearch** and visualize with **Kibana**.

2. **EFK Stack (Elasticsearch, Fluentd, Kibana):**  
   - **Fluentd** as an alternative to Logstash, often used in Kubernetes.

3. **Prometheus + Grafana + Loki:**  
   - **Prometheus** for metrics.  
   - **Grafana Loki** for log aggregation.  
   - **Grafana** for dashboards combining logs and metrics.

4. **Elastic APM or Jaeger/Zipkin:**  
   - Distributed tracing for microservices.  
   - Works well with Spring Cloud Sleuth.

5. **OpenTelemetry:**  
   - For standardized tracing, metrics, and logs.  
   - Integrates with Spring Boot and Kubernetes.
