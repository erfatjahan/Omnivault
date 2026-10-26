# Agent Context & Instructions

## Project Overview
- **Name**: Omnivault (Multi-Vendor E-Commerce Platform)
- **Primary Owner / Lead**:Most.Erfat jahan ekra
- **Role**: Full-stack Software Engineer and system architect.

## Standing Instructions & Rules
1. **Role Alignment**: - Work as a practical software engineering partner for Omnivault.
   - Read relevant code and follow existing patterns before changing behavior.
   - Prefer focused, maintainable implementation over unrelated refactors. Consider connected apps and downstream effects before concluding a task.
2. **Version Documentation**:
   - Maintain `version_documentation.md` for confirmed releases and milestones.
   - Record exact version numbers, dates, changes, and architecture decisions when known.
   - Do not invent release history. Ask before a major version bump after a prolonged gap.
3. **Agent Context**:
   -  Keep this `agent_context.md` updated with all specific instructions,preferences,and design decisions given by Erfat throughout the project lifecycle.
4. **README**:
   - Maintain the root `README.md` as clear,human-readable GitHub documentation for Omnivault.
   - Document the product,verified setup requirements, and how to run the backend,storefront,and dashboard.
   - Update it when a change materially affects setup,      configuration,or user-facing behavior. Do not invent undocumented setup details.
5. **Gitignore**:
	  - Maintain `.gitignore` rigorously,ensuring environment secrets (`.env`), build artifacts,node_modules`,upload folders, and temporary files are excluded.
6. **Frontend Design Standard**:
	- Build responsive, accessible storefront and admin experiences that fit Omnivault's existing visual language.
	- Reuse established Tailwind conventions, design tokens, theme behavior, and components. Avoid arbitrary one-off colors and styles when an existing pattern can be extended.
	- Preserve dark/light mode behavior and provide clear interaction feedback.
	- Cover relevant loading, empty, error, and success states for user-facing flows.
7. **Tech Stack & Architecture Decisions (Confirmed by  erfat)**: 
 -  **Customer Frontend**: React, Vite, Redux Toolkit, and Tailwind CSS in `frontend/`.
-  **Admin Dashboard**: Separate React, Vite, Redux Toolkit, and Tailwind CSS application in `dashboard/`.
-  **Backend**: Express 5 with JavaScript ES modules in `backend/`.
-  **Database**: PostgreSQL using `pg`. Table definitions are in `backend/models/`; table setup is coordinated in `backend/utils/createTables.js`.
-  **Backend organization**: Routes are in `backend/router/`, request handlers in `backend/controllers/`, and middleware in `backend/middlewares/`.
-  **API clients**: Each frontend application has an Axios client under `src/lib/axios.js`.Preserve API base URL, credential, and request conventions across client changes.
  - **Repository Structure**: Three distinct directories at root: `frontend/`,`backend/,dashboard/`.
9. **Commerce Flow & API Consistency**:
	- For changes involving products,users,orders,shipping,or payments, trace the relevant route, middleware,controller, database operations,and affected frontend or dashboard clients.
	- Preserve existing request/response contracts, authentication and authorization checks,payment behavior,and order state transitions unless the task explicitly changes them.
	- Validate related edge cases and keep customer-facing and administrative behavior consistent.
10. **Third-Party Integrations**:
	- Dependencies include SSLCommerz,Cloudinary,Nodemailer, and Google Generative AI.
	- Verify the existing implementation before extending an integration.Keep API keys,credentials,and environment-specific values in environment configuration.
	- Handle integration failures explicitly and do not report an operation as successful if it failed.
11. **Validation & Delivery**:
    - Run checks from the directory of the application being changed.
    - For `frontend/` and `dashboard/`, use `npm run lint` and/or `npm run build` as appropriate.
    - The backend's current `npm test` script is a placeholder that exits with an error; do not treat it as a working test suite. Use focused checks appropriate to the change.
    - Do not install dependencies unless a manifest changes or a validation command reports a missing dependency.
	 - Summarize what changed, the checks run, and any known limitations.
