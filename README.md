[README (1).md](https://github.com/user-attachments/files/35178263/README.1.md)
# Hospital Management System — Spring Boot Microservices

A Java and Spring Boot microservices project for managing core hospital workflows, including patients, staff, appointments, beds, and notifications.

> **Repository:** [ChunchuTejaswini/MajorProject](https://github.com/ChunchuTejaswini/MajorProject)
>
> **Status:** Development project. Deployment readiness, automated test coverage, and end-to-end behavior have not been independently verified.

## Architecture

The backend is organized into independently configured Spring Boot services. Eureka provides service discovery, and Spring Cloud Gateway routes requests to registered services.

```text
Client / frontend
       |
       v
Spring Cloud Gateway (:8000)
       |
       +-- AppointmentService (:8081)
       +-- BedService         (:9001)
       +-- PatientService     (:9000)
       +-- StaffService       (:9002)
       |
       +-- Eureka discovery  (:8761)

NotificationService (:9005) — separate notification service

MySQL — hospitalManagement database
```

The diagram summarizes the service roles; it does not imply every inter-service integration has been verified.

## Services

| Module | Default port | Responsibility |
| --- | ---: | --- |
| `EurekaService` | 8761 | Service registration and discovery |
| `GatewayService` | 8000 | API gateway and service-based routing |
| `PatientService` | 9000 | Patient-related APIs |
| `BedService` | 9001 | Beds, rooms, and bed-assignment APIs |
| `StaffService` | 9002 | Staff-related APIs and authentication |
| `AppointmentService` | 8081 | Appointment-related APIs |
| `NotificationService` | 9005 | Notification functionality, including SendGrid configuration |

### Configured gateway paths

The gateway configuration currently defines these paths:

| Path | Target service |
| --- | --- |
| `/appointments/**` | AppointmentManagement |
| `/bed/**`, `/rooms/**` | BedManagement |
| `/patients/**` | PatientManagement |
| `/staff/**`, `/doctorSchedule/**` | StaffService |

The notification service is not listed among these explicit gateway routes.

## Technology stack

- Java and Spring Boot
- Spring Cloud Gateway and Netflix Eureka
- Spring Data JPA / Hibernate
- MySQL
- Spring Security and JWT-based authentication
- Maven
- SendGrid configuration for notifications
- Spring AI OpenAI configuration in the appointment service

A frontend may be run separately; this repository's service list describes the backend modules.

## Prerequisites

- A compatible Java Development Kit for the versions configured in each module's `pom.xml`
- Maven or the Maven wrapper, if available
- MySQL
- An IDE such as IntelliJ IDEA
- Appropriate environment variables for the services being run

## Local setup

1. Clone the repository:

   ```bash
   git clone https://github.com/ChunchuTejaswini/MajorProject.git
   cd MajorProject
   ```

2. Start MySQL and prepare the local database named `hospitalManagement`, as referenced by the service configurations.

3. Configure the required environment variables **outside Git**:

   | Variable | Purpose |
   | --- | --- |
   | `DB_USERNAME` | MySQL username for the database-backed services |
   | `DB_PASSWORD` | MySQL password |
   | `JWT_SECRET` | Strong, private signing secret shared by the JWT-issuing and JWT-validating services |
   | `OPENAI_API_KEY` | OpenAI API key if the appointment service's AI functionality is used |
   | `SENDGRID_API_KEY` | SendGrid API key if notification email functionality is used |

   Set variables in your operating system or your IDE's run configuration. Do not commit actual values, `.env` files, or API keys.

4. In IntelliJ, import each service as a Maven project. Resolve dependencies and build the modules.

5. Start `EurekaService`, followed by `GatewayService`, and then the required domain services. Check the Eureka dashboard at <http://localhost:8761> for service registration.

6. Access the configured backend routes through the gateway at <http://localhost:8000>. Use valid test credentials and test data when exercising protected endpoints.

   **Note:** External providers, database credentials, and local service availability affect which features can run. This README does not guarantee that every endpoint works without additional setup.

## Configuration and security notes

- JWT signing secrets are referenced through `${JWT_SECRET}` in the current gateway and staff configuration files.
- Database credentials are read from environment variables in the database-backed services.
- Avoid enabling verbose security, HTTP request, or SQL parameter logging with real personal data.
- Never commit production credentials, real patient records, or confidential client information.
- Previously committed credentials must be rotated even after the current files are updated; Git history retains earlier commits unless separately cleaned.
- Review CORS origins and database schema management before deploying to a non-local environment.

## Development workflow

Each service is maintained in its own module folder with its own Maven configuration. For example:

```bash
cd PatientService
mvn clean package
```

Repeat the build for each required service and address any test or compilation failures. Build success and automated test coverage have not been verified as part of this README.

## Repository structure

```text
MajorProject/
├── AppointmentService/
├── BedService/
├── EurekaService/
├── GatewayService/
├── NotificationService/
├── PatientService/
├── StaffService/
└── README.md
```

## Project scope

This repository demonstrates a microservice-based backend structure for hospital administration, with API routing, discovery, persistence, authentication, and notification-related components. The source code should be reviewed and tested before treating it as production-ready.
