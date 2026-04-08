Role: You are a Senior QA Automation Engineer implementing the CANDOR framework protocol for JUnit 5 integration testing.

Context & Environment:

    Framework: Spring Boot 3.x, JUnit 5, AssertJ.

    Database: PostgreSQL (Testcontainers/Local) using TestDatabaseConfig and BaseIntegrationTest.

    Security: Bearer Token Auth via /api/v1/auth/login.

    Existing Data: Refer to the provided setup.sql for IDs (e.g., User 1, Work 1000, Status DRAFT=1).

The Task:
Generate a comprehensive integration test suite for the [INSERT_CONTROLLER_NAME_HERE].

Phase 1: Path Analysis (The Initializer)
Before writing code, analyze the controller/use case and list:

    Happy paths using existing setup.sql data.

    Edge cases (e.g., trying to edit a Work owned by another user).

    Constraint violations (e.g., duplicate label names, invalid UUIDs).

Phase 2: Oracle Generation (The Oracle Fixer)
For every test:

    Do not use assertEquals. Use AssertJ assertThat(...) for better readability.

    Validate the database state after POST/PUT/DELETE operations using JdbcTemplate or the Repository to ensure the "Oracle" (the truth) is persisted.

Phase 3: Implementation Rules

    Inheritance: Extend BaseIntegrationTest.

    Authentication: Use the existing getAuthenticatedHeaders() pattern.

    Annotations: Use @Sql("/test-data/setup.sql") at the class or method level.

    No Hallucinations: Use only the fields present in the provided DTOs/Entities.

Input Artifacts provided for reference:

    TestDatabaseConfig.java (DataSource setup)

    setup.sql (Initial state)

    BaseIntegrationTest.java (Profile and Context setup)

Generate the test suite for: [PASTE YOUR CONTROLLER CODE HERE]