# Tool Registry

Central registry of all engineering tools, libraries, and services used across projects.

---

## Meeting & Communication

### Fireflies.ai
**Purpose**: AI-powered meeting transcription and summarization
**Category**: Communication automation
**Use case**: Automatic meeting notes, action items, and insights
**Integration**: API-based, connects to calendar platforms
**Security**: SOC 2 compliant, encrypted storage
**Pricing**: Per-user subscription model

### Zoom AI Companion
**Purpose**: Meeting insights and transcription
**Category**: Video conferencing enhancement
**Use case**: Real-time meeting summaries, action item extraction
**Integration**: Native Zoom feature
**Security**: Zoom's enterprise security framework
**Pricing**: Included with Zoom business/enterprise plans

---

## Documentation

### Mintlify
**Purpose**: Public documentation platform
**Category**: Documentation hosting
**Use case**: Customer-facing docs, API references, guides
**Integration**: Git-based workflow, markdown support
**Security**: Custom domains, SSO available
**Pricing**: Tiered based on usage and features

### Markdown (Cursor)
**Purpose**: Internal engineering documentation
**Category**: Documentation authoring
**Use case**: Technical specs, runbooks, architecture docs
**Integration**: IDE-native, version control friendly
**Security**: Local storage, git-based security
**Pricing**: Included with Cursor IDE

---

## Backend

### Express.js
**Purpose**: Minimalist web framework for Node.js
**Category**: Backend framework
**Use case**: REST APIs, web servers, microservices
**Integration**: npm package, middleware ecosystem
**Security**: Helmet, CORS, rate limiting middleware
**Pricing**: Open source (MIT license)

### NestJS
**Purpose**: Structured enterprise backend framework
**Category**: Backend framework
**Use case**: Enterprise applications, complex APIs, microservices
**Integration**: TypeScript-based, modular architecture
**Security**: Built-in guards, decorators, validation
**Pricing**: Open source (MIT license)

---

## Security

### reCAPTCHA v3
**Purpose**: Invisible bot protection and risk assessment
**Category**: Security service
**Use case**: Form protection, API endpoint security
**Integration**: Frontend token + backend verification
**Security**: Google's anti-fraud system
**Pricing**: Free tier with paid options for high volume

---

## Network & VPN

### Twingate
**Purpose**: Zero-trust network access platform
**Category**: Network security/VPN
**Use case**: Secure remote access, network segmentation, zero-trust architecture
**Integration**: Client agents, SSO integration, network routing
**Security**: Zero-trust principles, end-to-end encryption
**Pricing**: Tiered based on users and resources

### Tailscale
**Purpose**: WireGuard-based VPN and network overlay
**Category**: Network security/VPN
**Use case**: Secure network access, device connectivity, private networking
**Integration**: Cross-platform clients, subnet routing, API access
**Security**: WireGuard encryption, key-based authentication
**Pricing**: Free tier with paid plans for teams

---

## Deployment & Infrastructure

### Docker
**Purpose**: Containerization platform
**Category**: Container runtime
**Use case**: Application packaging, environment consistency
**Integration**: Dockerfiles, docker-compose
**Security**: Image scanning, container isolation
**Pricing**: Free (Docker Desktop), paid enterprise features

### Docker Compose
**Purpose**: Multi-container orchestration
**Category**: Container orchestration
**Use case**: Development environments, simple deployments
**Integration**: YAML configuration, Docker CLI
**Security**: Same as Docker container security
**Pricing**: Included with Docker

---

## Web Servers & Proxy

### Nginx
**Purpose**: High-performance web server and reverse proxy
**Category**: Web server
**Use case**: Reverse proxy, load balancing, static file serving
**Integration**: Configuration files, systemd service
**Security**: SSL termination, access control
**Pricing**: Open source (BSD license)

---

## PDF Generation

### Playwright
**Purpose**: Browser automation and PDF generation
**Category**: Automation tool
**Use case**: PDF from HTML, screenshots, web scraping
**Integration**: Node.js/Python API, browser engines
**Security**: Sandboxed browser environment
**Pricing**: Open source (Apache license)

### pdfkit
**Purpose**: PDF generation from content
**Category**: PDF library
**Use case**: Programmatic PDF creation, reports
**Integration**: Node.js package, stream-based
**Security**: Local processing, no external dependencies
**Pricing**: Open source (MIT license)

---

## Email

### Nodemailer
**Purpose**: Email sending module for Node.js
**Category**: Email library
**Use case**: Transactional emails, notifications
**Integration**: SMTP providers, email service APIs
**Security**: TLS/SSL support, authentication
**Pricing**: Open source (MIT license)

---

## Load Testing

### Artillery
**Purpose**: Load testing and performance benchmarking tool
**Category**: Testing tool
**Use case**: API load testing, performance benchmarking, stress testing
**Integration**: CLI tool, Node.js library, YAML configuration
**Security**: Test data isolation, secure credential handling
**Pricing**: Open source (Apache license)

---

## HTTP Clients

### fetch
**Purpose**: Native HTTP client
**Category**: HTTP client
**Use case**: API calls, data fetching
**Integration**: Browser/Node.js native
**Security**: Same-origin policy, CORS
**Pricing**: Built-in

### axios
**Purpose**: Promise-based HTTP client
**Category**: HTTP client
**Use case**: API calls, request/response interceptors
**Integration**: Node.js/browser package
**Security**: Request/response transformation
**Pricing**: Open source (MIT license)

---

## Security Middleware

### Helmet
**Purpose**: Security header middleware for Express
**Category**: Security middleware
**Use case**: HTTP security headers, XSS protection
**Integration**: Express middleware
**Security**: Sets various security headers
**Pricing**: Open source (MIT license)

### CORS
**Purpose**: Cross-Origin Resource Sharing middleware
**Category**: Security middleware
**Use case**: API access control, cross-origin requests
**Integration**: Express middleware
**Security**: Configurable origin policies
**Pricing**: Open source (MIT license)

---

## Registry Maintenance

### Adding New Tools
1. Verify tool is reusable across projects
2. Add to appropriate category section
3. Include all required fields (purpose, category, use case, integration, security, pricing)
4. Link to detailed tool documentation if available

### Updating Existing Tools
1. Review quarterly for accuracy
2. Update pricing and security information
3. Add new integration patterns as discovered
4. Archive deprecated tools with migration notes

### Tool Categories
- **Meeting & Communication**: Collaboration and communication tools
- **Documentation**: Documentation platforms and authoring tools
- **Backend**: Server-side frameworks and libraries
- **Security**: Security services and middleware
- **Network & VPN**: Network security and VPN solutions
- **Load Testing**: Performance and load testing tools
- **Deployment & Infrastructure**: Containerization and deployment tools
- **Web Servers & Proxy**: Web servers and reverse proxies
- **PDF Generation**: PDF creation and manipulation tools
- **Email**: Email sending and management tools
- **HTTP Clients**: HTTP request libraries and tools
- **Security Middleware**: Security-enhancing middleware
