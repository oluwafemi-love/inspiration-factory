# Inspiration Factory

A mobile app for turning ideas into actionable projects.

## 1. Project Overview

**Inspiration Factory** helps people discover problems worth solving and create projects around ideas they care about.

For this school MVP, users can browse a predefined collection of public ideas and create, view, update and delete their own projects linked to those ideas.

The application is being developed as a React Native and Expo project for the final two weeks of the course, with a presentation scheduled for **November 3, 2026**.

### Target Users

People who want to explore ideas and organize projects around SDG-related, research or community problems.

### Core User Flow

1. Register or log in.
2. Browse the available ideas.
3. Select an idea and create a project.
4. View and manage personal projects.
5. Update a project's details, status or visibility.
6. Delete a project when it is no longer needed.
7. Log out and log back in to verify that data persists.

## 2. MVP Scope

The priority is a complete, reliable end-to-end user flow rather than a large collection of features.

### Included in the MVP

- User registration, login and logout.
- Secure password hashing and backend authentication.
- A predefined collection of public ideas.
- Project creation linked to an existing idea.
- Project listing and detail views.
- Project editing and deletion.
- Project ownership and authorization.
- Public and private project visibility.
- Project statuses: `planned`, `in_progress` and `completed`.
- Filtering projects by status.
- Input validation and appropriate HTTP status codes.
- Loading, empty, success and error states.
- Responsive, keyboard-friendly mobile screens.
- Persistent database storage.
- Dockerized backend deployment over HTTPS.
- Automated backend tests and GitHub Actions CI.



## 3. Application Screens

The app will use Expo Router for navigation.

| Screen | Responsibility |
|---|---|
| Registration | Create a user account. |
| Login | Authenticate a user and display understandable errors. |
| Ideas | Browse predefined public ideas. |
| My Projects | List the signed-in user's projects and filter by status. |
| Project Details | View a project's information and available actions. |
| Create/Edit Project | Create or update a project using a validated form. |

The create and edit flows may share the same screen and reusable form components.

## 4. Data Model

The MVP will use a relational PostgreSQL database with three main tables.

### Users

| Field | Description |
|---|---|
| `id` | Unique user identifier |
| `name` | User's display name |
| `email` | Unique email address |
| `password_hash` | Securely hashed password |
| `created_at` | Account creation timestamp |

### Ideas

| Field | Description |
|---|---|
| `id` | Unique idea identifier |
| `title` | Idea title |
| `description` | Description of the problem or opportunity |
| `category` | Idea category, such as an SDG or community topic |
| `location` | Optional descriptive location |

Ideas will initially be seeded manually. Idea creation and editing are outside the MVP scope.

### Projects

| Field | Description |
|---|---|
| `id` | Unique project identifier |
| `idea_id` | Reference to the idea being addressed |
| `owner_id` | Reference to the project's designated owner |
| `title` | Project title |
| `description` | Project description |
| `status` | `planned`, `in_progress` or `completed` |
| `visibility` | `public` or `private` |
| `created_at` | Creation timestamp |
| `updated_at` | Last update timestamp |

### Data and Permission Rules

- Every project belongs to one existing idea and has exactly one designated owner.
- Projects default to public visibility.
- Only the owner can update or delete their project in this MVP.
- Private project details are accessible only to their owner.
- Other users must not discover private project titles, descriptions or activity through API responses.
- Public project details may be visible to other authenticated users.
- Public idea project counts may include private projects, provided the count does not reveal private project details or identities.
- Passwords and password hashes must never be returned in API responses.
- Project ownership and visibility must be enforced by the backend, not just the mobile interface.

## 5. Technology Stack

| Area | Technology | Purpose |
|---|---|---|
| Mobile | React Native | Build the mobile interface |
| App framework | Expo | Develop and test the mobile app |
| Navigation | Expo Router | Manage screens and routes |
| Language | TypeScript | Improve type safety |
| Backend | Node.js and Express | Provide the REST API |
| Authentication | JWT and bcrypt | Authenticate users and hash passwords |
| Database | PostgreSQL | Persist users, ideas and projects |
| API security | Server-side validation and authorization | Protect data and enforce permissions |
| Containerization | Docker | Package the backend consistently |
| CI | GitHub Actions | Run automated tests on pushes and pull requests |
| Backend hosting | Render | Deploy the API over HTTPS |
| Database hosting | Neon | Host the persistent PostgreSQL database |
| Version control | Git and GitHub | Collaborate and review changes |

Hosting providers and plans will be verified before deployment. The mobile app will use a configurable API base URL, pointing to the deployed backend for the final presentation.

## 6. Backend API

The backend will expose endpoints similar to the following. Exact route naming may be adjusted during implementation.

| Method | Endpoint | Purpose |
|---|---|---|
| `POST` | `/auth/register` | Register a user |
| `POST` | `/auth/login` | Authenticate a user |
| `POST` | `/auth/logout` | Log out or invalidate a session if server-side sessions are implemented |
| `GET` | `/ideas` | List public ideas |
| `GET` | `/ideas/:id` | Read an idea and its permitted public metadata |
| `GET` | `/projects` | List projects visible to the signed-in user |
| `POST` | `/projects` | Create a project |
| `GET` | `/projects/:id` | Read an authorized project |
| `PATCH` | `/projects/:id` | Update an owned project |
| `DELETE` | `/projects/:id` | Delete an owned project |

All project operations will go through the custom backend. The mobile app will not connect directly to PostgreSQL.

Protected routes will require valid authentication. The backend will validate incoming data, enforce ownership and visibility rules, and return appropriate HTTP status codes and understandable error messages.

If JWT authentication is used without server-side session storage, logout will clear the client-side token. Token expiry and secure token storage will be handled appropriately for the mobile environment.

## 7. Deployment and CI/CD

The backend and database must remain available when team members' computers are turned off.

### Deployment

- Containerize the Express backend using Docker.
- Deploy the backend to Render with a public HTTPS URL.
- Host PostgreSQL on Neon using persistent managed storage.
- Configure database credentials, JWT secrets and other sensitive values as backend environment variables.
- Keep `.env` files and secrets out of Git.
- Use the deployed API in the mobile app during the final presentation.
- Verify that records survive app restarts, new logins and backend restarts or redeployments.

### Continuous Integration

GitHub Actions will run automated backend tests on pushes and pull requests.

Tests will cover:

- Successful project creation, reading, updating and deletion.
- Invalid or missing input.
- Requests without authentication.
- Attempts to access or modify another user's private data.
- Unauthorized attempts to modify or delete another user's project.
- Appropriate HTTP status codes and error responses.

The latest required workflow run must pass before submission.

## 8. Additional Feature for VG

The proposed additional feature is **Project Status Tracking**.

Users can assign one of three statuses to their projects:

- Planned
- In Progress
- Completed

The My Projects screen will allow users to filter projects by status.

This feature will be implemented and demonstrated only after the basic authentication, CRUD, database, deployment and testing requirements work reliably. Its acceptance as the additional VG feature will be confirmed with the teacher.

## 9. Team Workflow

The project will use a shared GitHub repository.

Suggested structure:

```text
inspiration-factory/
├── mobile/
│   ├── app/
│   ├── components/
│   └── ...
├── backend/
│   ├── src/
│   ├── tests/
│   ├── Dockerfile
│   └── ...
├── .github/
│   └── workflows/
├── README.md
└── .gitignore
```

The exact structure may evolve as the project develops.

### Collaboration Rules

- Maintain a task board with `To Do`, `In Progress` and `Done` columns.
- Use feature branches and pull requests.


## 10. Definition of Done

The MVP is ready for submission when:

- [ ] Users can register, log in and log out.
- [ ] Failed logins display understandable error messages.
- [ ] Users can browse predefined public ideas.
- [ ] Users can create, read, update and delete their own projects.
- [ ] Project ownership and privacy rules are enforced by the backend.
- [ ] Project status tracking and filtering work.
- [ ] Data persists across app restarts, new logins and backend redeployments.
- [ ] Loading, empty, validation, success and error states are handled.
- [ ] The app remains usable when the mobile keyboard is open.
- [ ] The core flow has been tested on an Android or iOS device or emulator.
- [ ] The backend is deployed over public HTTPS.
- [ ] The backend runs in Docker and the database is independently hosted.
- [ ] GitHub Actions runs the required automated tests successfully.
- [ ] The final presentation uses the deployed backend.
- [ ] The README explains the architecture, setup, environment variables and deployment.

## 11. Future Direction

Inspiration Factory may eventually grow into a platform for discovering problems, developing solutions, and collaborating on meaningful projects.

That broader vision is intentionally outside this school MVP. The immediate objective is to establish a secure, tested and deployable foundation that can be extended after the course.

---