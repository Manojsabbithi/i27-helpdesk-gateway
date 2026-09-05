# i27 Helpdesk API Gateway

Node.js and Express API Gateway for the i27 Helpdesk microservices application.

This repository provides the application entry point for protected API traffic in the AWS deployment. It validates JWT authentication, propagates verified user identity to downstream services, and routes requests to the internal microservices running on Amazon EKS.

## Repository Role

The Gateway sits between the public Application Load Balancer and the internal application services.

```text
Browser
   |
   v
Application Load Balancer
   |
   v
API Gateway
   |
   +--> Auth Service
   +--> Ticket Service
   +--> Comment Service
   +--> Attachment Service
   +--> Other internal services
```

The broader AWS infrastructure and platform implementation is documented in:

[i27-helpdesk-aws-infra](https://github.com/Manojsabbithi/i27-helpdesk-aws-infra)

## Security

The Gateway performs authentication and identity propagation for protected routes.

Implemented controls include:

- JWT verification using `jsonwebtoken`
- rejection of missing, invalid, or expired tokens
- verified user identity propagation through downstream request headers
- role context propagation for service-level authorization
- protected routing for ticket, comment, and attachment APIs
- removal of decoded JWT and bearer-token logging from application logs
- removal of obsolete duplicate authentication middleware

Downstream services remain responsible for their own authorization rules, such as administrator access, ticket ownership, agent assignment, and attachment access.

## AWS CI/CD Pipeline

The AWS-specific Jenkins pipeline is defined in `Jenkinsfile.aws`.

The pipeline includes:

```text
Checkout
   |
   v
Node Validation
   |
   v
SonarQube Analysis
   |
   v
Quality Gate
   |
   v
Docker Build
   |
   v
Amazon ECR Push
   |
   v
ECR Image Verification
   |
   v
Amazon EKS Deployment
```

Kubernetes manifests for the AWS environment are stored under:

```text
k8s/aws/
```

The container runs as a non-root user and exposes the Gateway on port `8080`.

## Technology

- Node.js 18
- Express
- Axios
- express-http-proxy
- jsonwebtoken
- Docker
- Jenkins
- SonarQube
- Amazon ECR
- Amazon EKS

## Local Development

Install dependencies:

```bash
npm install
```

Start the service:

```bash
npm start
```

Required runtime configuration such as the JWT secret and downstream service URLs must be supplied through environment variables.

## Project Context

This repository originated from the i27Academy Helpdesk application and is used here as part of an independent AWS DevOps portfolio implementation.

The AWS-focused work includes CI/CD integration, container and EKS deployment, Gateway authentication hardening, identity propagation, routing integration, and operational security improvements.
