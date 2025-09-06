# Project Requirements Document (PRD)

## 1. Project Overview

**brain2train1.1** is envisioned as a user-friendly cognitive training platform that helps people sharpen memory, attention, and problem-solving skills through a variety of short, interactive exercises. By offering exercises like pattern recognition, sequence recall, and logic puzzles in themed modules, the app aims to engage users in daily brain workouts that adapt to their performance. This keeps tasks challenging yet achievable and encourages consistent practice.

We’re building this platform to address the growing demand for accessible brain-fitness tools. Our key objectives are to deliver a smooth, responsive user experience; track and display each user’s progress over time; and maintain user motivation through basic gamification elements (points, badges). Success will be measured by user retention rates, completion streaks, and positive feedback on exercise variety and difficulty adjustments.

## 2. In-Scope vs. Out-of-Scope

### In-Scope (Version 1.0)
- User registration, login, and profile management (email/password + password reset).
- A core set of cognitive training modules (e.g., pattern puzzles, sequence recall, logic tasks).
- Adaptive difficulty engine that adjusts task complexity in real time based on accuracy and response time.
- Progress tracking dashboard showing metrics like accuracy rate, average completion time, and streak count.
- Simple gamification: points for each completed exercise, badges at defined milestones.
- Responsive web interface that works on desktop and tablet screens.

### Out-of-Scope (Future Phases)
- Social features (leaderboards, friend invites, sharing achievements).
- Mobile-native apps (iOS/Android) or offline mode.
- Advanced analytics or AI-driven insights beyond basic difficulty adaptation.
- Third-party integrations (calendar sync, fitness trackers, social media).
- Real-time chat or collaboration between users.

## 3. User Flow

1. A new user navigates to the web app and clicks **Sign Up**. They enter an email, create a password, and confirm their profile details (username, age range, training goals).
2. After verifying their email, they land on the **Dashboard**, which shows welcome text and summary cards: today's recommended exercise, streak count, and total points.
3. From the left sidebar, the user clicks **Training Modules** and selects an exercise category (e.g., Memory, Logic). The exercise screen displays instructions, a timer, and a task.
4. Once the user submits responses, the adaptive engine evaluates performance and assigns a difficulty score for the next exercise. Points earned appear instantly.
5. The user returns to the **Progress** tab to view charts: accuracy over time, average completion speed, and unlocked badges. They can then start a new session or log out.

## 4. Core Features

- **Authentication & Profiles**: Secure sign-up/sign-in, email verification, password reset.
- **Training Modules**: Multiple exercise types (pattern matching, recall, puzzles) organized by category.
- **Adaptive Difficulty**: Real-time adjustment of task complexity based on user performance metrics.
- **Progress Tracking**: Dashboard with charts for accuracy, response time, total points, and streaks.
- **Gamification**: Point system, milestone badges, and streak counters to motivate users.
- **Responsive UI**: Clear navigation (sidebar + main content area) optimized for desktop and tablet.

## 5. Tech Stack & Tools

- **Frontend:** React (Create React App or Next.js), Tailwind CSS or Material-UI for styling.
- **Backend:** Node.js with Express.js (or NestJS), RESTful API endpoints.
- **Database:** PostgreSQL (relational) or MongoDB (document) for storing user data, exercise logs, and badge metadata.
- **Authentication:** JSON Web Tokens (JWT) and bcrypt for password hashing.
- **Adaptive Engine:** Custom Node.js service or lightweight Python module (Flask) for difficulty logic.
- **Hosting/Deployment:** AWS (EC2 or Elastic Beanstalk) or Vercel/Heroku for quick setup.
- **Development Tools:** VS Code with ESLint, Prettier, GitHub Actions for CI/CD.

## 6. Non-Functional Requirements

- **Performance:** Pages should load in under 2 seconds on a modern broadband connection.
- **Scalability:** Backend must handle 1,000+ daily active users without degradation.
- **Security:** All traffic over HTTPS, OWASP Top 10 considerations, GDPR-compliant data handling.
- **Usability:** WCAG 2.1 AA accessibility standards, intuitive UI flows, clear error messages.
- **Reliability:** 99.5% uptime SLA, daily automated backups of the database.

## 7. Constraints & Assumptions

- We assume the repository will be extended with exercise content and UI assets before dev starts.
- The adaptive difficulty logic will be rule-based initially (no machine learning models).
- Internet connectivity is required; there is no offline mode in v1.0.
- Email delivery relies on a third-party service (e.g., SendGrid).
- Browser support: latest two versions of Chrome, Firefox, Safari, and Edge.

## 8. Known Issues & Potential Pitfalls

- **Algorithm Tuning:** Difficulty adaptation rules need real-user testing to avoid too-steep ramps.
- **API Rate Limits:** Watch for database performance bottlenecks under burst traffic; implement caching or queueing if needed.
- **Security Gaps:** Ensure password reset flows can’t be abused; limit login attempts to prevent brute force.
- **Data Privacy:** Collect minimal personal data to reduce compliance complexity; encrypt sensitive fields.
- **Content Gaps:** Without actual exercise definitions, placeholder data will be used—plan for easy content updates.

---

This document lays out a clear framework for the brain2train1.1 platform’s first release. All core components, user journeys, and technical guidelines are described so that subsequent design, frontend, and backend documents can be generated without ambiguity.