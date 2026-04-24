Experiment No 7 - Secure Authentication using Spring Security with JWT This project demonstrates how to implement secure authentication and Role-Based Access Control (RBAC) in a Spring Boot application using Spring Security and JSON Web Tokens (JWT).

Features Implemented To Meet Criteria The application meets all the requirements of Experiment 7:

Criteria A (Login & JWT Generation): The /auth/login endpoint authenticates a user using data fetched from an H2 in-memory database. Upon successful authentication, it generates and returns a signed JWT token using the io.jsonwebtoken library. Registration is also fully functional. Criteria B (Security Filters & Restrictions): The custom JwtAuthFilter intercepts incoming HTTP requests, validates the signature and expiration time of the JWT, and constructs a SecurityContext. Public routes (/auth/login, /auth/register) are configured as open in SecurityConfig, whereas other sensitive routes require a token. Criteria C (RBAC with @PreAuthorize): Contains dedicated testing endpoints for specific roles. Uses @PreAuthorize("hasAuthority('ROLE_USER')") for the User profile and @PreAuthorize("hasAuthority('ROLE_ADMIN')") for the Admin dashboard to enforce role-based segregation. Project Architecture JwtService: Responsible for generating, decoding, and validating the tokens. JwtAuthFilter: Executes before the routing to validate the token per request. SecurityConfig: Contains the primary security configurations, enabling method security, configuring open and protected routes, and supplying BCrypt standard password encoding. UserInfoUserDetailsService: Replaces the default Spring framework user discovery by fetching users securely from our H2 table and translating their data into Spring UserDetails representations. H2 Database: Uses temporary memory configurations (jdbc:h2:mem:testdb) to allow dynamic addition of Admins and Users at runtime. Running the Application Ensure you have Java 17+ installed.

Navigate to the root directory containing the pom.xml. Run the Spring Boot application using the wrapper: .\mvnw.cmd spring-boot:run The server will launch on port 8080. Testing the Endpoints You can verify the security setup using cURL or Postman:

Verify Public Route (Open)

curl http://localhost:8080/auth/welcome Register a User (Dynamically add them)

curl -X POST http://localhost:8080/auth/register -H "Content-Type: application/json" -d "{"username":"user1", "password":"mypassword", "roles":"ROLE_USER"}" Register an Admin

curl -X POST http://localhost:8080/auth/register -H "Content-Type: application/json" -d "{"username":"superadmin", "password":"mypassword", "roles":"ROLE_ADMIN"}" Login (Returns the JWT)

curl -X POST http://localhost:8080/auth/login -H "Content-Type: application/json" -d "{"username":"user1", "password":"mypassword"}" Copy the string it responds with.

Test Role Constraints

Access the user endpoint by replacing <jwt_token> below: curl http://localhost:8080/api/user/profile -H "Authorization: Bearer <jwt_token>" If you access the admin endpoint (/api/admin/dashboard) using the basic user token, you will correctly receive a 403 Forbidden response. "# exp7_fsd"
