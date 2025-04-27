# Microservices Patient Management System

## Table of Contents

1. [Introduction](#introduction)
2. [Project Structure](#project-structure)
3. [Service Configurations](#service-configurations)
4. [Microservices](#microservices)
   - [Patient Service](#patient-service)
   - [Billing Service](#billing-service)
   - [Auth Service](#auth-service)
   - [Analytics Service](#analytics-service)
5. [Technologies Used](#technologies-used)
6. [Installation](#installation)
7. [Running the Application](#running-the-application)
8. [API Documentation](#api-documentation)
9. [Inter-Service Communication](#inter-service-communication)
10. [Project Sources](#project-sources)

## Introduction

This project is a production-ready patient management system developed using a microservices architecture with Java Spring Boot. It is designed to efficiently handle patient data management, billing, authentication, and analytics. The system is deployed on Amazon Web Services (AWS) and integrates advanced technologies such as gRPC for inter-service communication, Kafka for event-driven architecture, and an API Gateway for external access.

## Project Structure

The project is organized into several microservices, each handling a specific domain of functionality. This modular approach promotes loose coupling and independent scalability. Below is the directory structure of the project:

```
microservices-patient-management/
├── analytics-service/
│   └── (Analytics Service implementation files)
├── api-gateway/
│   └── api-requests/
│       └── (API request files, e.g., HTTP requests for API Gateway)
├── auth-service/
│   ├── api-requests/
│   │   ├── login.http
│   │   └── validate.http
│   └── (Auth Service implementation files)
├── patient-service/
│   ├── api-requests/
│   │   ├── create-patient.http
│   │   ├── delete-patient.http
│   │   ├── get-patients.http
│   │   └── update-patient.http
│   └── (Patient Service implementation files)
├── billing-service/
│   ├── grpc-requests/
│   │   └── (gRPC request definitions)
│   ├── api-requests/
│   │   └── create-billing-account.http
│   └── (Billing Service implementation files)
├── infrastructure/
│   ├── cdk.out/
│   │   └── (AWS CDK output files)
│   ├── src/
│   │   └── (Infrastructure source files)
│   └── target/
│       └── localstack-deploy.sh
├── integration-tests/
│   ├── src/
│   │   └── (Integration test source files)
│   └── pom.xml
├── patient-service/
│   ├── src/
│   │   └── (Patient Service source files)
│   └── pom.xml
├── pom.xml
└── README.markdown
```

### Main Components:
- **Microservices**:
  - **Patient Service**: Manages patient data and operations.
  - **Billing Service**: Handles billing and payment processing.
  - **Auth Service**: Provides authentication and authorization.
  - **Analytics Service**: Processes and analyzes patient data for insights.
- **API Gateway**: Routes external requests to the appropriate microservices.
- **Databases**: Each microservice uses its own PostgreSQL database for data isolation.
- **Message Broker**: Kafka facilitates asynchronous event-driven communication.
- **Containerization**: Docker is used for containerizing services, with Docker Compose for local development and AWS ECS/EKS for production.

The project follows a Maven-based structure, with each microservice having its own `pom.xml` file defining specific configurations.

## Service Configurations

This section lists the configurations for each service, including binding ports and environment variables required for running the application locally using Docker.

- **Analytics Service**:
  - **Bind Ports**: `4002:4002`
  - **Environment Variables**:
    ```plaintext
    SPRING_KAFKA_BOOTSTRAP_SERVERS=kafka:9092
    ```

- **Billing Service**:
  - **Bind Ports**: `4001:4001`, `9001:9001`

- **Auth Service**:
  - **Environment Variables**:
    ```plaintext
    JWT_SECRET=your_jwt_secret
    SPRING_DATASOURCE_PASSWORD=your_postgres_password
    SPRING_DATASOURCE_URL=jdbc:postgresql://auth-service-db:5432/your_auth_db
    SPRING_DATASOURCE_USERNAME=your_postgres_username
    SPRING_JPA_HIBERNATE_DDL_AUTO=update
    SPRING_SQL_INIT_MODE=always
    ```

- **API Gateway**:
  - **Bind Ports**: `4004:4004`
  - **Environment Variables**:
    ```plaintext
    AUTH_SERVICE_URL=http://auth-service:4005
    ```

- **Kafka**:
  - **Bind Ports**: `9092:9092`, `9094:9094`
  - **Environment Variables**:
    ```plaintext
    KAFKA_CFG_ADVERTISED_LISTENERS=PLAINTEXT://kafka:9092,EXTERNAL://localhost:9094
    KAFKA_CFG_CONTROLLER_LISTENER_NAMES=CONTROLLER
    KAFKA_CFG_CONTROLLER_QUORUM_VOTERS=0@kafka:9093
    KAFKA_CFG_LISTENER_SECURITY_PROTOCOL_MAP=CONTROLLER:PLAINTEXT,EXTERNAL:PLAINTEXT,PLAINTEXT:PLAINTEXT
    KAFKA_CFG_NODE_ID=0
    KAFKA_CFG_PROCESS_ROLES=controller,broker
    ```

- **Auth Service Database (auth-service-db)**:
  - **Bind Ports**: `5001:5432`
  - **Environment Variables**:
    ```plaintext
    POSTGRES_DB=your_auth_db
    POSTGRES_PASSWORD=your_postgres_password
    POSTGRES_USER=your_postgres_username
    ```

- **Patient Service**:
  - **Environment Variables**:
    ```plaintext
    BILLING_SERVICE_ADDRESS=billing-service
    BILLING_SERVICE_GRPC_PORT=9001
    SPRING_DATASOURCE_PASSWORD=your_postgres_password
    SPRING_DATASOURCE_URL=jdbc:postgresql://patient-service-db:5432/your_patient_db
    SPRING_DATASOURCE_USERNAME=your_postgres_username
    SPRING_JPA_HIBERNATE_DDL_AUTO=update
    SPRING_KAFKA_BOOTSTRAP_SERVERS=kafka:9092
    SPRING_SQL_INIT_MODE=always
    ```

- **Patient Service Database (patient-service-db)**:
  - **Bind Ports**: `5000:5432`
  - **Environment Variables**:
    ```plaintext
    POSTGRES_DB=your_patient_db
    POSTGRES_PASSWORD=your_postgres_password
    POSTGRES_USER=your_postgres_username
    ```

## Microservices

### Patient Service

- **Description**: The Patient Service manages all patient-related data, including creating, reading, updating, and deleting patient records. It interacts with other services, such as the Billing Service, to handle billing operations triggered by patient activities.
- **Technologies**: Java Spring Boot, Spring Data JPA, PostgreSQL, gRPC, Kafka

### Billing Service

- **Description**: The Billing Service is responsible for creating invoices, processing payments, and integrating with external payment gateways. It communicates with other services via gRPC.
- **Technologies**: Java Spring Boot, gRPC

### Auth Service

- **Description**: The Auth Service handles user authentication and authorization, issuing JSON Web Tokens (JWT) for secure access to other services.
- **Technologies**: Java Spring Boot, Spring Security, JWT, PostgreSQL

### Analytics Service

- **Description**: The Analytics Service processes and analyzes patient data to provide insights, such as trends in patient visits or billing patterns. It subscribes to events from other services to generate reports.
- **Technologies**: Java Spring Boot, Kafka

## Technologies Used

The project incorporates a robust stack of technologies to ensure scalability, performance, and maintainability:

| Technology            | Purpose                                      |
|-----------------------|----------------------------------------------|
| Java                  | Programming language                         |
| Spring Boot           | Framework for building microservices         |
| PostgreSQL            | Relational database for data storage         |
| Kafka                 | Message broker for event-driven architecture |
| gRPC                  | High-performance inter-service communication |
| Spring Cloud Gateway  | API Gateway for routing external requests    |
| Docker                | Containerization of services                 |
| Docker Compose        | Orchestration for local development          |
| AWS ECS/EKS           | Orchestration for production deployment      |
| IntelliJ IDEA         | Integrated Development Environment (IDE)     |

## Installation

Follow these steps to clone the repository, configure environment variables, and set up the project locally:

1. **Clone the Repository**:
   Clone the project from [GitHub](https://github.com/ersinisgor/microservices-patient-management):
   ```bash
   git clone https://github.com/ersinisgor/microservices-patient-management.git
   ```

2. **Navigate to the Project Directory**:
   ```bash
   cd microservices-patient-management
   ```

3. **Configure Environment Variables**:
   The microservices require specific environment variables to connect to PostgreSQL databases, Kafka, and other services. You can set these variables in a `.env` file or directly in your environment. Create a `.env` file in the project root directory with the following content, replacing placeholders with your own values:

  

   Replace the following placeholders with your own values:
   - `your_jwt_secret`: A secure secret key for JWT authentication.
   - `your_postgres_password`: Your PostgreSQL database password.
   - `your_postgres_username`: Your PostgreSQL database username.
   - `your_patient_db`: The name of the database for the Patient Service.
   - `your_auth_db`: The name of the database for the Auth Service.

   Alternatively, you can set these environment variables manually in your terminal before running Docker Compose. For example:
   ```bash
   export PATIENT_SERVICE_DATASOURCE_PASSWORD=your_postgres_password
   export PATIENT_SERVICE_DATASOURCE_URL=jdbc:postgresql://patient-service-db:5432/your_patient_db
   ```

   Ensure the PostgreSQL and Kafka services are accessible via the Docker network (e.g., `patient-service-db:5432`, `auth-service-db:5432`, and `kafka:9092`).

4. **Build the Docker Images**:
   ```bash
   docker-compose build
   ```

5. **Start the Services**:
   ```bash
   docker-compose up
   ```

This will launch all microservices, PostgreSQL databases, and Kafka in Docker containers.

## Running the Application

Once the services are running, you can access the API Gateway at `http://localhost:4004`. Use a tool like Postman to test the APIs.

- **Authentication**: Access the `/auth/login` endpoint to obtain a JWT token, which is required for interacting with protected endpoints.
- **Service Interaction**: External requests are routed through the API Gateway, which directs them to the appropriate microservice.

## API Documentation

API documentation is available via Swagger UI at `http://localhost:4004/swagger-ui.html`. This interface provides detailed information about available endpoints, request/response formats, and authentication requirements.

## Inter-Service Communication

The microservices communicate using two primary mechanisms:

- **gRPC**: Enables synchronous, high-performance communication between services. For example, the Patient Service may use gRPC to call the Billing Service when generating a bill for a new patient.
- **Kafka**: Facilitates asynchronous, event-driven communication. For instance, when a patient is created, the Patient Service publishes a `PatientCreated` event to Kafka, which the Analytics Service subscribes to and processes for generating insights.

## Project Sources

The following resources provide additional context and inspiration for this project:

- **YouTube Video**: This project uses a YouTube video as a source for building the Java Spring Boot microservices system, available at [Java Spring Microservices Course](https://www.youtube.com/watch?v=tseqdcFfTUY&t=35514s).
- **Original GitHub Repository**: The foundational project that inspired this implementation, available at [chrisblakely01/java-spring-microservices](https://github.com/chrisblakely01/java-spring-microservices).