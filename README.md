# Patient Management System

A modern REST API for managing patient records with comprehensive CRUD operations, built with Spring Boot 3.5 and Java 21.

[![Java](https://img.shields.io/badge/Java-21-orange.svg)](https://www.oracle.com/java/)
[![Spring Boot](https://img.shields.io/badge/Spring%20Boot-3.5.10-brightgreen.svg)](https://spring.io/projects/spring-boot)
[![H2 Database](https://img.shields.io/badge/H2-In--Memory-blue.svg)](https://www.h2database.com/)
[![License](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)

## Features

- **Complete CRUD Operations** - Create, read, update, and delete patient records
- **Email Uniqueness** - Enforced at both database and service layers
- **Smart Validation** - Validation groups for create vs. update operations
- **UUID Primary Keys** - Auto-generated unique identifiers for all patients
- **Layered Architecture** - Clean separation of concerns (Controller → Service → Repository)
- **In-Memory Database** - H2 database with web console for development/testing
- **DTO Pattern** - Request/Response separation with validation constraints
- **Global Exception Handling** - Centralized error handling with custom exceptions
- **Sample Data** - Pre-loaded test data via SQL scripts

## Technology Stack

| Category | Technology |
|----------|-----------|
| **Language** | Java 21 (LTS) |
| **Framework** | Spring Boot 3.5.10 |
| **Database** | H2 in-memory |
| **ORM** | Spring Data JPA + Hibernate |
| **Build Tool** | Maven |
| **Validation** | Jakarta Bean Validation |
| **Utilities** | Custom Mappers, Validation Groups |

## Quick Start

### Prerequisites

- **Java 21** or higher ([Download](https://www.oracle.com/java/technologies/downloads/#java21))
- **Maven 3.x** (or use included wrapper)

### Installation

1. **Clone the repository**
   ```bash
   git clone <repository-url>
   cd patient-management
   ```

2. **Build the project**
   ```bash
   cd patient-service
   ./mvnw clean install
   ```

3. **Run the application**
   ```bash
   ./mvnw spring-boot:run
   ```

The service will start on `http://localhost:4000`.

### Accessing H2 Console

Visit `http://localhost:4000/h2-console` with these credentials:
- **JDBC URL:** `jdbc:h2:mem:testdb`
- **Username:** `admin_viewer`
- **Password:** `password`

## API Documentation

### Get All Patients

**Endpoint:** `GET /patients`

**Response (200 OK):**
```json
[
  {
    "id": "550e8400-e29b-41d4-a716-446655440000",
    "name": "John Doe",
    "email": "john.doe@example.com",
    "address": "123 Main St",
    "dateOfBirth": "1990-05-15"
  }
]
```

**Example:**
```bash
curl http://localhost:4000/patients
```

### Create a Patient

**Endpoint:** `POST /patients`

**Request Body:**
```json
{
  "name": "Jane Smith",
  "email": "jane.smith@example.com",
  "address": "456 Oak Avenue",
  "dateOfBirth": "1985-03-20",
  "registeredDate": "2024-02-08"
}
```

**Response (200 OK):**
```json
{
  "id": "7c9e6679-7425-40de-944b-e07fc1f90ae7",
  "name": "Jane Smith",
  "email": "jane.smith@example.com",
  "address": "456 Oak Avenue",
  "dateOfBirth": "1985-03-20"
}
```

**Example:**
```bash
curl -X POST http://localhost:4000/patients \
  -H "Content-Type: application/json" \
  -d '{
    "name": "Jane Smith",
    "email": "jane.smith@example.com",
    "address": "456 Oak Avenue",
    "dateOfBirth": "1985-03-20",
    "registeredDate": "2024-02-08"
  }'
```

### Update a Patient

**Endpoint:** `PUT /patients/{id}`

**Request Body:**
```json
{
  "name": "Jane Smith Updated",
  "email": "jane.updated@example.com",
  "address": "789 New Street",
  "dateOfBirth": "1985-03-20"
}
```

**Note:** `registeredDate` is not required for updates.

**Response (200 OK):**
```json
{
  "id": "7c9e6679-7425-40de-944b-e07fc1f90ae7",
  "name": "Jane Smith Updated",
  "email": "jane.updated@example.com",
  "address": "789 New Street",
  "dateOfBirth": "1985-03-20"
}
```

**Example:**
```bash
curl -X PUT http://localhost:4000/patients/7c9e6679-7425-40de-944b-e07fc1f90ae7 \
  -H "Content-Type: application/json" \
  -d '{
    "name": "Jane Smith Updated",
    "email": "jane.updated@example.com",
    "address": "789 New Street",
    "dateOfBirth": "1985-03-20"
  }'
```

### Delete a Patient

**Endpoint:** `DELETE /patients/{id}`

**Response:** `204 No Content` (empty body)

**Example:**
```bash
curl -X DELETE http://localhost:4000/patients/7c9e6679-7425-40de-944b-e07fc1f90ae7
```

### Error Handling

**Patient Not Found (400 Bad Request):**
```json
{
  "message": "Patient not found"
}
```

**Email Already Exists (400 Bad Request):**
```json
{
  "message": "Email address already exists"
}
```

**Validation Error (400 Bad Request):**
```json
{
  "email": "Email should be valid",
  "name": "Name is required"
}
```

## Architecture

```
┌─────────────────────────────────────────────────┐
│           Controller Layer                      │
│  (REST endpoints, validation annotations)       │
└──────────────────┬──────────────────────────────┘
                   │
┌──────────────────▼──────────────────────────────┐
│           Service Layer                          │
│  (Business logic, email uniqueness checks)      │
└──────────────────┬──────────────────────────────┘
                   │
┌──────────────────▼──────────────────────────────┐
│           Repository Layer                       │
│  (Spring Data JPA, custom query methods)        │
└──────────────────┬──────────────────────────────┘
                   │
┌──────────────────▼──────────────────────────────┐
│           H2 Database                            │
│  (In-memory storage)                            │
└─────────────────────────────────────────────────┘
```

### Package Structure

```
dev.toganbayev.patientservice/
├── controller/     # REST endpoints (@RestController)
├── service/        # Business logic (@Service)
├── repository/     # Data access (@Repository)
├── model/          # JPA entities (@Entity)
├── dto/            # Request/Response DTOs
│   └── validators/ # Custom validation groups
├── mapper/         # Entity-DTO conversion utilities
└── exception/      # Custom exceptions and global handler
```

## Testing

### Run Tests

```bash
# Run all tests
./mvnw test

# Run specific test class
./mvnw test -Dtest=PatientServiceApplicationTests

# Package without running tests
./mvnw clean package -DskipTests
```

## Development

### Build Commands

```bash
# Clean build
./mvnw clean install

# Skip tests
./mvnw clean package -DskipTests

# Run application
./mvnw spring-boot:run

# View dependency tree
./mvnw dependency:tree

# Check for updates
./mvnw versions:display-dependency-updates
```

### Database Configuration

**H2 Console Access:**
- URL: `http://localhost:4000/h2-console`
- JDBC URL: `jdbc:h2:mem:testdb`
- Username: `admin_viewer`
- Password: `password`

**Data Initialization:**
- Sample data loaded via `src/main/resources/data.sql`
- Schema auto-generated by Hibernate (`spring.jpa.hibernate.ddl-auto=update`)
- Data initialization mode: `spring.sql.init.mode=always`

### Code Style

- **Indentation:** 4 spaces
- **Validation Groups:** `CreatePatientValidationGroup` for create operations
- **DTOs:** Separate Request/Response DTOs with validation constraints
- **Exception Handling:** Centralized via `GlobalExceptionHandler` with `@ControllerAdvice`

### Key Components

**PatientMapper** (`mapper/PatientMapper.java`)
- Static utility methods for entity-DTO conversion
- Handles date string parsing (LocalDate ↔ String)

**GlobalExceptionHandler** (`exception/GlobalExceptionHandler.java`)
- Centralized error handling with `@ControllerAdvice`
- Custom exceptions: `EmailAlreadyExistsException`, `PatientNotFoundException`
- Returns structured JSON error responses

**Validation Groups** (`dto/validators/CreatePatientValidationGroup.java`)
- Distinguishes between create (requires `registeredDate`) and update operations
- Applied via `@Validated` annotation on controller methods

**Email Uniqueness**
- Database constraint on `Patient` entity
- Service-level checks via `existsByEmail()` and `existsByEmailAndIdNot()`

## API Testing

Sample HTTP requests are available in `api-requests/patient-service/`:
- `create-patient.http` - Create new patient
- `get-patients.http` - Get all patients
- `update-patient.http` - Update existing patient
- `delete-patient.http` - Delete patient

Use these with IntelliJ IDEA HTTP Client or similar tools.

## Roadmap

- [ ] Add pagination and sorting for patient listing
- [ ] Implement search functionality (by name, email)
- [ ] Add patient medical history tracking
- [ ] Implement authentication and authorization
- [ ] Add comprehensive unit and integration tests
- [ ] Migrate to production database (PostgreSQL/MySQL)
- [ ] Add API documentation (Swagger/OpenAPI)
- [ ] Implement audit logging
- [ ] Add metrics and monitoring

## Contributing

Contributions are welcome! Please follow these guidelines:

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/amazing-feature`)
3. Write tests for new functionality
4. Ensure all tests pass (`./mvnw test`)
5. Commit your changes (`git commit -m 'Add amazing feature'`)
6. Push to the branch (`git push origin feature/amazing-feature`)
7. Open a Pull Request

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## Contact

Toganbayev - [@toganbayev](https://github.com/toganbayev)

Project Link: [https://github.com/toganbayev/patient-management](https://github.com/toganbayev/patient-management)

## Acknowledgments

- [Spring Boot](https://spring.io/projects/spring-boot) - Application framework
- [Spring Data JPA](https://spring.io/projects/spring-data-jpa) - Data persistence
- [H2 Database](https://www.h2database.com/) - In-memory database
- [Jakarta Bean Validation](https://beanvalidation.org/) - Validation framework
