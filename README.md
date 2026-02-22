# healthpal
Welcome to the healthpal wiki!

HealthPal - Digital Healthcare Platform API Documentation

Project Overview
HealthPal is a backend API for a healthcare platform designed to provide Palestinians with access to remote medical support, medicine coordination, and donation-driven treatment sponsorships. The platform's mission is to bridge the gap between patients, doctors, donors, and medical NGOs, especially in situations where local healthcare systems are inaccessible or overwhelmed.

This project was developed for the Advanced Software Engineering course (Fall 2025) under Dr. Amjad AbuHassan. The system provides a secure, scalable, and robust foundation for a full-stack healthcare application.

The Used Architecture, Details, and Justification
We chose a professional and industry-standard architecture to ensure the project is maintainable, scalable, and secure. Architecture Pattern: 3-Tier Monolithic Architecture

Monolith: The entire backend is developed as a single, unified application. For a project of this scope, a monolithic architecture is practical and efficient, simplifying development, testing, and deployment compared to a more complex microservices architecture.

3-Tier Design: We strictly separated the application into three logical layers:

Presentation Tier (Client): For this project, Postman acts as our client, simulating requests from a future web or mobile application.

Logic Tier (Application Server): This is our Node.js/Express.js application. It contains all the business logic, processing requests, enforcing security rules, and coordinating with the database.

Data Tier (Database): This is our MySQL server, which is solely responsible for data storage and retrieval.

Architectural Style: RESTful API

The backend exposes its functionality through a RESTful API. This is the standard for modern web services.

Justification: A RESTful approach is platform-agnostic, meaning any client (web, iOS, Android) can interact with it using standard HTTP protocols. It is stateless, scalable, and its resource-based nature (/users, /consultations) makes the API intuitive and easy for other developers to understand.

Design Principles: SOLID

We adhered to the SOLID principles to ensure a high-quality codebase:

(S) Single Responsibility Principle: Each module has one job. For example, authController.js only handles authentication, while consultationController.js only handles consultation logic. Models like User.js are only responsible for user data structure.

(O) Open/Closed Principle: Our use of middleware like protect and authorize allows us to extend security to new routes without modifying the middleware's source code.

(D) Dependency Inversion Principle: Our controllers depend on an abstraction (the Sequelize ORM) rather than a low-level implementation (raw SQL). This makes our code decoupled from the database; we could switch to another SQL database with minimal changes to our business logic.

The Used DB, and the Schema in Detail
Database Technology: MySQL, as required by the project specifications. It is a robust, reliable, and widely-used relational database.

Interaction Layer: Sequelize ORM (Object-Relational Mapper).

Justification: Using an ORM like Sequelize provides significant advantages:

Abstraction: It allows us to interact with the database using familiar JavaScript objects and methods instead of writing raw SQL, which speeds up development.

Security: It automatically sanitizes inputs, providing strong protection against SQL injection attacks.

Maintainability: It keeps our database schema definitions (models) directly within our codebase, making the project self-documenting and easier to manage.

Database Schema The schema is designed to be normalized and scalable, capturing all the core entities of the HealthPal platform. (A full schema diagram or detailed breakdown of each table would go here. The text-based version is excellent for a wiki).

Code // --- USER & AUTH --- Table users { id, username, email, password_hash, role, ... }

// --- CONSULTATIONS --- Table consultations { id, patient_id, doctor_id, status, mode, ... } Table consultation_slots { id, doctor_id, start_datetime, is_booked, consultation_id } Table messages { id, consultation_id, sender_id, message_text, translated_text } Table mental_health_consultations { id, consultation_id, trauma_type, ... }

// --- SPONSORSHIP --- Table treatment_requests { id, patient_id, doctor_id, goal_amount, raised_amount, ... } Table donations { id, treatment_request_id, donor_id, amount } Table recovery_updates { id, treatment_request_id, content, ... } Table sponsorship_verification { id, treatment_request_id, receipt_url, ... }

// --- MEDICATION & INVENTORY --- Table medicine_requests { request_id, patient_id, item_name_requested, ... } Table inventory_registry { item_id, name, type, quantity_available, ... }

// --- PUBLIC HEALTH --- Table health_guides { id, title, category, language, ... } Table public_health_alerts { id, title, alert_type, severity, ... } Table workshops { id, title, topic, mode, date, ... } Table workshop_registrations { id, workshop_id, user_id }

The URI's Structure
The API endpoints are organized logically around resources. The base URL is http://localhost:5000.

Authentication: /api/auth/{register, login}

Users: /api/users/{me}

Consultations: /api/{doctors, slots, consultations}

Sponsorship: /api/sponsorship/{requests, donations}

Medication & Inventory: /api/medicine, /api/inventory

Public Health: /api/guides, /api/alerts, /api/workshops

News (External): /api/news/health

A full list of all 19+ endpoints, including HTTP method, description, and required roles, is maintained in the project's Postman collection.

The Used External API's
To enhance platform functionality as per the project requirements, we integrated two external, third-party APIs:

Translation API (translate-google package)

Purpose: To provide the "Medical Translation" feature, allowing asynchronous messages between patients and doctors to be translated between Arabic and English in real-time.

Implementation: Within the sendMessage controller, we check the language preference of the sender and receiver. If they differ, we make an asynchronous call to the translation service using the translate-google library and store the result in the translated_text field of the messages table.

Security Note: An npm audit revealed vulnerabilities in this package. For a production environment, we would migrate to a more secure, official API like DeepL or Google Translate. For this academic project, the risk was deemed acceptable to fulfill the feature requirement.

News API (NewsAPI.org)

Purpose: To provide a "Global Health News" feature, enhancing the platform's comprehensiveness with data from an authoritative external source.

Implementation: We created a dedicated endpoint (GET /api/news/health). The controller uses the axios library to make a request to the NewsAPI, fetching the latest top headlines in the health category. This demonstrates another clean example of external API integration.

Describe the Github Repo
Tool: We used Git for version control and GitHub as our central remote repository.

Workflow: We adopted a professional Feature Branch Workflow:

The main branch is kept stable and represents the integrated, working version of the project.

For any new feature or bug fix, a developer creates a new branch from main (e.g., feature/sponsorship-donations).

All work is done on this feature branch.

When the feature is complete and tested locally, the developer pushes the branch to GitHub and opens a Pull Request (PR).

The PR is reviewed by at least one other team member. This code review process is crucial for maintaining code quality, sharing knowledge, and catching bugs early.

Once approved, the feature branch is merged into main.

This workflow prevented conflicts and ensured that the main branch was always in a deployable state.

Describe how we developed our system to handle the problem
Our development process was iterative and focused on collaboration and establishing a consistent architecture.

A key challenge was integrating code from multiple team members who initially used different architectural patterns. One part of the codebase used a Repository and Service layer with raw SQL, while another used the Sequelize ORM directly in the controllers.

We handled this problem by:

Holding a team code review to compare the different architectures.

Making a collective decision to standardize the entire project on the more modern and maintainable Controller -> Sequelize Model pattern. This approach was chosen for its simplicity, consistency, and the security benefits of the ORM.

Collaboratively refactoring the inconsistent code. We systematically replaced all raw SQL (db.execute) and removed the extra repository/service layers, rewriting the logic to use Sequelize methods (User.create, InventoryItem.findAll, etc.).

Resolving integration errors step-by-step, such as fixing inconsistent file paths (/config/db vs /config/database), naming conventions (checkRole vs authorize), and module export styles.

This process of identifying architectural debt and actively refactoring to create a unified system was a critical learning experience and a key to the project's success.
