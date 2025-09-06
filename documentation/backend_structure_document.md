# Backend Structure Document

## 1. Backend Architecture

We’ve organized the backend as a set of clear, reusable layers and services to keep it fast, easy to change, and ready to grow:

- **Framework & Style:**
  - Built on Node.js with the Express.js framework (RESTful style).
  - Follows a layered approach (routing → controllers → services → data access).
  - Includes a separate, small service for adaptive difficulty logic (could be its own lightweight microservice).

- **Key Design Patterns:**
  - **MVC-inspired layers:**
    - **Routes** handle incoming HTTP calls.
    - **Controllers** validate requests and call business logic.
    - **Services** implement core features (authentication, exercise evaluation, progress calculations).
    - **Repositories / Data Access** interact with the database.
  - **Single Responsibility:** each module handles one feature (e.g., user management, exercise engine).
  - **Configuration-driven:** environment settings (database URLs, API keys) live in `.env` files.

- **Scalability & Performance:**
  - Stateless controllers let us add more server instances behind a load balancer.
  - The adaptive engine runs separately, so exercise evaluation doesn’t block user-facing routes.
  - Using connection pooling and caching layers (Redis) to keep database calls fast.

- **Maintainability:**
  - Clear folder structure (`routes/`, `controllers/`, `services/`, `models/`).
  - Consistent coding standards enforced by ESLint and Prettier.
  - Automated tests for key services, making it safe to refactor.

## 2. Database Management

We chose a relational database (PostgreSQL) to store structured user and exercise data reliably:

- **Database Type:** SQL (PostgreSQL).
- **ORM / Query Layer:** TypeORM or Sequelize to map JavaScript objects to database tables.
- **Data Storage & Access:**
  - Tables for users, exercises, logs, badges, and system configuration.
  - Indexed common lookup fields (user ID, exercise ID) to speed up queries.
  - Connection pooling so multiple requests can share database connections efficiently.
- **Data Management Practices:**
  - **Migrations:** every schema change is versioned with migration scripts.
  - **Backups:** automated daily dumps to S3 (or equivalent) to prevent data loss.
  - **Health Checks:** periodic tests to verify database connectivity and performance.

## 3. Database Schema

Below is a human-friendly summary of our main tables, followed by SQL definitions.

### Human-Readable Table Descriptions

- **Users**: Stores account details (email, hashed password, profile info, roles).
- **Exercises**: Defines each exercise type (name, category, base difficulty).
- **ExerciseLogs**: Records every time a user completes an exercise (score, response time, difficulty level).
- **Badges**: Lists available badges (name, description, criteria).
- **UserBadges**: Tracks which badges each user has earned.
- **Settings** (optional): System-wide settings like difficulty tuning parameters.

### SQL Schema (PostgreSQL)
```sql
CREATE TABLE Users (
  id SERIAL PRIMARY KEY,
  email VARCHAR(255) UNIQUE NOT NULL,
  password_hash VARCHAR(60) NOT NULL,
  username VARCHAR(100) NOT NULL,
  age_range VARCHAR(50),
  role VARCHAR(20) DEFAULT 'user',
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE TABLE Exercises (
  id SERIAL PRIMARY KEY,
  name VARCHAR(100) NOT NULL,
  category VARCHAR(50) NOT NULL,
  base_difficulty INT NOT NULL,
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE TABLE ExerciseLogs (
  id SERIAL PRIMARY KEY,
  user_id INT REFERENCES Users(id) ON DELETE CASCADE,
  exercise_id INT REFERENCES Exercises(id),
  difficulty_level INT NOT NULL,
  accuracy DECIMAL(5,2),
  response_time_ms INT,
  points_awarded INT,
  completed_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE TABLE Badges (
  id SERIAL PRIMARY KEY,
  name VARCHAR(100) NOT NULL,
  description TEXT,
  criteria JSONB,
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE TABLE UserBadges (
  id SERIAL PRIMARY KEY,
  user_id INT REFERENCES Users(id) ON DELETE CASCADE,
  badge_id INT REFERENCES Badges(id),
  awarded_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

## 4. API Design and Endpoints

We use a RESTful approach so the frontend and backend exchange data over clear URL-based endpoints. All endpoints return JSON and require JWT in the `Authorization` header (except signup/login).

### Authentication & User Management
- `POST /api/auth/register` – Create a new user account.
- `POST /api/auth/login` – Verify credentials and return a JWT.
- `POST /api/auth/forgot-password` – Send password-reset email.
- `POST /api/auth/reset-password` – Update password using a secure token.
- `GET /api/users/me` – Fetch the current user’s profile.
- `PUT /api/users/me` – Update profile information.

### Exercises & Adaptive Engine
- `GET /api/exercises` – List available exercise categories.
- `GET /api/exercises/:id` – Fetch details for one exercise.
- `POST /api/exercises/:id/submit` – Submit results; backend calculates points and next difficulty.

### Progress & Gamification
- `GET /api/progress` – Retrieve user’s performance chart data.
- `GET /api/badges` – List all badges.
- `GET /api/users/me/badges` – List badges the user has earned.

### Administrative (Protected)
- `GET /api/admin/users` – List all users.
- `PUT /api/admin/exercises/:id` – Update exercise definitions.
- `PUT /api/admin/settings` – Tweak system-wide parameters.

## 5. Hosting Solutions

We deploy on managed cloud services for reliability, cost control, and easy scaling:

- **Backend Server:** AWS Elastic Beanstalk (Node.js environment) or Heroku.
  - Automatic OS and runtime patching.
  - Simple scaling via instance count or dyno size.
- **Database:** AWS RDS for PostgreSQL.
  - Built-in backups, multi-AZ for high availability.
- **Environment Configuration:**
  - Use environment variables (in EB or Heroku config) for secrets and endpoints.

**Benefits:**
- Managed platforms remove infrastructure headaches.
- Pay-as-you-go keeps costs aligned with usage.
- Easy to add instances under traffic spikes.

## 6. Infrastructure Components

To deliver fast, resilient service, we layer additional components:

- **Load Balancer (AWS ELB):** Distributes traffic across multiple backend servers.
- **Caching (Redis):**
  - Caches frequent queries (user sessions, exercise templates).
  - Speeds up response times and reduces DB load.
- **CDN (Amazon CloudFront):**
  - Serves static assets (images, JS bundles) from edge locations close to users.
- **Logging & Error Tracking:**
  - Aggregates application logs (AWS CloudWatch or ELK stack).
  - Tracks exceptions and performance issues (Sentry).

**How they work together:**
1. User request hits the CDN (static files) or ELB (API).
2. ELB forwards to one of the backend instances.
3. Backend checks Redis cache before querying the database.
4. Results returned quickly, errors logged centrally, metrics sent to monitoring tools.

## 7. Security Measures

We guard user data and backend endpoints with multiple layers of protection:

- **Encryption in Transit:** HTTPS/TLS for all network traffic.
- **Password Storage:** bcrypt hashes with a secure salt.
- **Token-Based Auth:** JWT tokens manage sessions without server-side cookies.
- **Role-Based Access Control:** Middleware checks user roles for admin routes.
- **OWASP Best Practices:**
  - Input validation to prevent SQL injection.
  - Sanitization to avoid XSS.
- **HTTP Hardening:**
  - Helmet.js sets secure headers.
  - CORS policy allows only trusted origins.
- **Rate Limiting & Brute-Force Protection:**
  - Throttles repeated login attempts.

## 8. Monitoring and Maintenance

To keep everything running smoothly, we use:

- **CI/CD (GitHub Actions):**
  - Runs tests and linting on every pull request.
  - Deploys to staging/production automatically on merge.
- **Performance Monitoring:** AWS CloudWatch (CPU, memory, response times).
- **Error Tracking:** Sentry captures runtime errors and stack traces.
- **Database Health Checks:** Automated scripts monitor connections, slow queries.
- **Scheduled Maintenance:**
  - Weekly dependency updates.
  - Monthly security audits.
  - Quarterly disaster recovery drills using backup snapshots.

## 9. Conclusion and Overall Backend Summary

Our backend is built for reliability, clarity, and growth:

- We adopted a layered Node.js/Express architecture with stateless services and a dedicated adaptive engine.
- PostgreSQL and Redis ensure data is stored safely and fetched quickly.
- A full set of RESTful APIs lets the frontend and mobile clients talk to the server in a consistent way.
- Hosted on AWS (Elastic Beanstalk, RDS, CloudFront) with built-in scaling, backups, and monitoring.
- Multiple security layers (bcrypt, JWT, Helmet, rate limiting) protect user data.
- Automated CI/CD, logging, and maintenance routines keep the service healthy and up to date.

This setup matches our goals to deliver a fast, secure, and maintainable cognitive training platform that can adapt and grow with our users’ needs.