Experiment 7 – Secure Authentication using Spring Security with JWT
Overview

This project demonstrates secure user authentication and Role-Based Access Control (RBAC) using Spring Boot, Spring Security, and JSON Web Tokens (JWT).

The application supports:

User registration
Login authentication
JWT token generation
Token validation using filters
Role-based endpoint protection
H2 in-memory database integration

This experiment satisfies all required evaluation criteria for Experiment 7.

Features Implemented
Criteria A – Login & JWT Generation

The application provides authentication through:

POST /auth/login

Functionality includes:

Validates credentials stored in the H2 database
Generates signed JWT token upon successful login
Uses io.jsonwebtoken (JJWT) library
Supports dynamic user registration

Registration endpoint:

POST /auth/register

Allows runtime creation of:

ROLE_USER
ROLE_ADMIN
Criteria B – Security Filters & Endpoint Protection

A custom filter:

JwtAuthFilter

handles request interception and token validation.

Responsibilities:

Extract JWT from Authorization header
Validate signature and expiration
Build Spring SecurityContext
Authenticate protected routes

Public endpoints:

/auth/login
/auth/register
/auth/welcome

Protected endpoints:

/api/user/**
/api/admin/**

require valid JWT authentication.

Criteria C – Role-Based Access Control (RBAC)

RBAC is implemented using:

@PreAuthorize

Examples:

User profile endpoint:

@PreAuthorize("hasAuthority('ROLE_USER')")

Admin dashboard endpoint:

@PreAuthorize("hasAuthority('ROLE_ADMIN')")

Access rules:

Role	Accessible Endpoint
USER	/api/user/profile
ADMIN	/api/admin/dashboard

Unauthorized access returns:

403 Forbidden
Project Architecture
JwtService

Handles:

Token generation
Token parsing
Token validation
JwtAuthFilter

Executes before controller routing to:

Extract token from request header
Validate token
Set authentication context
SecurityConfig

Contains:

Endpoint authorization rules
Stateless session configuration
Password encoding (BCrypt)
JWT filter integration
Method-level security enablement
UserInfoUserDetailsService

Loads users from:

H2 in-memory database

Converts database records into:

UserDetails

objects required by Spring Security.

H2 Database

Configuration:

jdbc:h2:mem:testdb

Used for:

Runtime user storage
Dynamic role assignment
Lightweight testing environment
Running the Application
Prerequisites
Java 17+
Maven installed
Start the Server

Navigate to project root directory:

mvn spring-boot:run

Server runs on:

http://localhost:8080
Testing the Endpoints

You can test using Postman or cURL.

1. Public Endpoint Test
GET /auth/welcome

Example:

curl http://localhost:8080/auth/welcome
2. Register a User
curl -X POST http://localhost:8080/auth/register \
-H "Content-Type: application/json" \
-d "{\"username\":\"user1\",\"password\":\"mypassword\",\"roles\":\"ROLE_USER\"}"
3. Register an Admin
curl -X POST http://localhost:8080/auth/register \
-H "Content-Type: application/json" \
-d "{\"username\":\"admin1\",\"password\":\"mypassword\",\"roles\":\"ROLE_ADMIN\"}"
4. Login and Generate JWT
curl -X POST http://localhost:8080/auth/login \
-H "Content-Type: application/json" \
-d "{\"username\":\"user1\",\"password\":\"mypassword\"}"

Response:

JWT Token

Copy this token for protected requests.

5. Access Protected User Endpoint
curl http://localhost:8080/api/user/profile \
-H "Authorization: Bearer <jwt_token>"
6. Access Admin Endpoint
curl http://localhost:8080/api/admin/dashboard \
-H "Authorization: Bearer <jwt_token>"

If a USER token is used here:

403 Forbidden

will be returned correctly.

Technology Stack
Component	Technology
Framework	Spring Boot
Security	Spring Security
Authentication	JWT (JJWT)
Database	H2 (In-memory)
Build Tool	Maven
Java Version	Java 17
Security Features Implemented
Stateless authentication
JWT token validation filter
BCrypt password encryption
Role-based endpoint protection
Method-level authorization
Secure login flow
Public vs protected route separation
Learning Outcomes

This experiment demonstrates:

JWT-based authentication workflow
Spring Security filter chain usage
Role-based authorization using annotations
Secure REST API development
H2 database integration with Spring Security