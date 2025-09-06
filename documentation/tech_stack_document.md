# Tech Stack Document

This document explains the technologies chosen for the **brain2train1.1** project in simple, everyday terms. We’ll cover the tools for the user interface (frontend), the behind-the-scenes logic and data (backend), how everything is hosted and delivered, any extra services we connect to, and the steps taken to keep the app safe and fast.

## 1. Frontend Technologies

The frontend is everything the user sees and interacts with in their browser. For this project, we are using:

- **React (Create React App)**
  - A popular JavaScript library for building fast, interactive user interfaces.
  - Lets us break the UI into small, reusable pieces (components) like buttons, cards, and charts.
- **Tailwind CSS**
  - A utility-first CSS framework that provides ready-made style classes (e.g., `bg-blue-500`, `p-4`).
  - Helps keep our styles organized and consistent without writing a lot of custom CSS.
- **Recharts (or Chart.js)**
  - A simple library for drawing charts (line graphs, bar charts) used to display user progress and streaks.
- **ESLint & Prettier**
  - Tools that automatically check and format our code, ensuring it stays clean and consistent across the team.

How these choices enhance the user experience:
- **Speed & Responsiveness:** React updates only what’s needed on the screen, so pages feel snappy.
- **Consistency:** Tailwind’s utility classes enforce a uniform look and feel.
- **Clarity:** Prebuilt chart libraries make data easy to read at a glance.

## 2. Backend Technologies

The backend powers the app’s logic, data storage, and rules for adaptive difficulty. We have:

- **Node.js & Express.js**
  - A JavaScript runtime (Node.js) and a lightweight web framework (Express) that handle incoming requests from the frontend.
  - Organizes our features into RESTful API endpoints (e.g., `/api/login`, `/api/exercises`).
- **PostgreSQL (or MongoDB)**
  - A reliable database to store user profiles, exercise logs, points, and badges.
  - PostgreSQL gives us structured tables; MongoDB offers a flexible document model. We can choose based on future needs.
- **Authentication with JWT & bcrypt**
  - `bcrypt` securely hashes user passwords before saving them in the database.
  - JSON Web Tokens (JWT) keep users logged in without storing sensitive details on the frontend.
- **Adaptive Difficulty Service**
  - A small Node.js module (or lightweight Python/Flask service) that adjusts exercise complexity in real time based on accuracy and response time.
  - Keeps training challenging but not frustrating.

How they work together:
1. The frontend calls an Express endpoint (for example, to log in or fetch exercises).
2. Express checks the user’s JWT, retrieves data from PostgreSQL, and returns it as JSON.
3. When a user finishes an exercise, the adaptive service computes the next difficulty level and updates the database.

## 3. Infrastructure and Deployment

To keep the app reliable, easy to update, and ready for growth, we use:

- **Version Control: Git & GitHub**
  - All code is stored in a GitHub repository, allowing multiple developers to work in parallel and track every change.
- **Continuous Integration/Continuous Deployment (CI/CD) with GitHub Actions**
  - Automatically runs tests, checks code style, and deploys to our hosting platforms when we merge changes.
- **Hosting Platforms**
  - **Frontend:** Vercel or Netlify for instant, global delivery of static assets (HTML, CSS, JavaScript).
  - **Backend:** AWS Elastic Beanstalk (or Heroku) to run our Node.js/Express server.
- **Automated Backups & Monitoring**
  - Daily database backups ensure we don’t lose user progress.
  - Basic uptime monitoring alerts us if the service goes down.

These choices ensure:
- **Reliability:** Automated tests and deployments reduce human error.
- **Scalability:** We can add more server instances or upgrade database capacity as user demand grows.
- **Speed of Updates:** New features and fixes go live within minutes of merging code.

## 4. Third-Party Integrations

We connect to a few external services to round out the user experience:

- **SendGrid (Email Delivery)**
  - Sends account verification and password reset emails securely and reliably.
- **Google Analytics (optional)**
  - Tracks broad usage patterns—how many users return daily, how long they spend on exercises.

Benefits:
- **Trustworthy Email:** Users get timely confirmation and reset links without building our own mail server.
- **Actionable Insights:** Analytics data helps us understand which exercises are most popular and where users drop off.

## 5. Security and Performance Considerations

Keeping user data safe and the app running smoothly is a top priority. Here’s what we do:

Security Measures:
- **HTTPS Everywhere:** All traffic is encrypted with SSL/TLS.
- **Password Hashing:** `bcrypt` prevents attackers from reading stored passwords.
- **JWT Access Control:** Verifies each request to protected routes.
- **OWASP Best Practices:** We guard against common web vulnerabilities (injection, cross-site scripting).
- **Rate Limiting:** Limits login attempts to prevent brute-force attacks.

Performance Optimizations:
- **Code Splitting & Lazy Loading:** Only load the JavaScript needed for the current page.
- **Database Indexing:** Speeds up queries for frequent lookups (user profiles, recent exercises).
- **Caching Static Assets:** Uses a CDN (Content Delivery Network) to deliver images, styles, and scripts from servers close to the user.
- **Lightweight Charts:** Chooses efficient chart libraries to keep page weight low.

## 6. Conclusion and Overall Tech Stack Summary

Our choices for **brain2train1.1** are guided by three main goals:

1. **User Delight:** React and Tailwind deliver a smooth, attractive interface.
2. **Developer Speed & Reliability:** Node.js, Express, PostgreSQL, and GitHub Actions let us build, test, and deploy quickly and safely.
3. **Scalability & Security:** Hosting on Vercel/AWS with SSL, JWT, and backup strategies ensures the platform can grow without compromising user data.

Unique Aspects:
- A dedicated adaptive difficulty service that tailors exercises in real time.
- Utility-first styling with Tailwind for rapid UI development.
- End-to-end JavaScript (and an optional lightweight Python service) for a streamlined, full-stack experience.

With this foundation, **brain2train1.1** is well equipped to deliver a reliable, engaging cognitive training experience to users—and to evolve smoothly as we add new features in the future.