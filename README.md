# FirstOneSite Task
## User Profile Management Feature Breakdown

Here are the requested deliverables:

### 1. Task Breakdown

Tasks are grouped logically (Front-end, Back-end, Integration/General) and include dependencies where applicable. Priorities are marked (High, Medium, Low).

#### Front-end (FE) Tasks (Ionic/React/TypeScript)

| Task ID | Description | Priority | Dependencies |
|---|---|---|---|
| FE-1 | Set up routing for the profile page (`/profile`). | High | - |
| FE-2 | Create Profile View component: Display static layout for name, email, bio, picture. | High | FE-1 |
| FE-3 | Integrate GraphQL query to fetch and display user profile data in Profile View. Handle loading/error states. | High | FE-2, BE-3 |
| FE-4 | Implement responsive design for Profile View (desktop/mobile). | High | FE-3 |
| FE-5 | Create Recent Activities component: Display static list/card layout. | Medium | FE-1 |
| FE-6 | Integrate GraphQL query to fetch and display user activities, sorted by date. | Medium | FE-5, BE-4 |
| FE-7 | Implement responsive design for Recent Activities component. | Medium | FE-6 |
| FE-8 | Add "Edit Profile" button to Profile View, linking to edit mode/page. | High | FE-3 |
| FE-9 | Create Edit Profile Form component: Include fields for name, email, bio, and file input for profile picture. | High | FE-8 |
| FE-10 | Implement client-side validation for Edit Profile form (required fields, email format, potentially image type/size). | High | FE-9 |
| FE-11 | Implement profile picture preview logic on file selection (optional but good UX). | Medium | FE-9 |
| FE-12 | Integrate GraphQL mutation to submit updated profile data (including picture upload). Handle success/error responses and display messages. | High | FE-10, BE-6 |
| FE-13 | Ensure profile view updates immediately after successful edit submission (state management). | High | FE-3, FE-12 |
| FE-14 | Add "Delete Account" button. | Medium | FE-3 |
| FE-15 | Create reusable Confirmation Modal component (for deletion). | Medium | FE-14 |
| FE-16 | Implement logic to show confirmation modal on "Delete Account" click. | Medium | FE-14, FE-15 |
| FE-17 | Integrate GraphQL mutation for account deletion on confirmation. Handle redirect on success. | Medium | FE-16, BE-7 |
| FE-18 | Set up i18n library (e.g., `react-i18next`) and structure for translation files. | Medium | - |
| FE-19 | Extract all user-facing strings into translation files (e.g., `en.json`). | Medium | FE-18, All UI components |
| FE-20 | Implement language switching mechanism (if not already present). | Low | FE-18 |

#### Back-end (BE) Tasks (Node.js/GraphQL/PostgreSQL)

| Task ID | Description | Priority | Dependencies |
|---|---|---|---|
| BE-1 | Design/Update PostgreSQL `users` table schema (add bio, profile_picture_url). | High | - |
| BE-2 | Design/Create PostgreSQL `activities` table schema (user_id, action, timestamp, details). Add indexes. | Medium | - |
| BE-3 | Define GraphQL schema: `User` type, `Activity` type, `getUserProfile` query. | High | BE-1 |
| BE-4 | Define GraphQL schema: `getUserActivities` query (with sorting, potentially pagination). | Medium | BE-2 |
| BE-5 | Define GraphQL schema: `updateUserProfile` mutation (input types for name, email, bio, picture). | High | BE-1 |
| BE-6 | Define GraphQL schema: `deleteUserAccount` mutation. | Medium | BE-1 |
| BE-7 | Implement resolver for `getUserProfile`: Fetch data for the authenticated user. Enforce authentication. | High | BE-3 |
| BE-8 | Implement resolver for `getUserActivities`: Fetch activities for the authenticated user, sort by date. Enforce authentication. | Medium | BE-4 |
| BE-9 | Implement resolver for `updateUserProfile`: Validate input data (required, formats, sanitization). Handle profile picture upload (see Technical Approach). Update user record. Enforce authentication/authorization. | High | BE-5 |
| BE-10 | Implement resolver for `deleteUserAccount`: Verify user, perform permanent deletion of user and associated data (define scope). Enforce authentication/authorization. | Medium | BE-6 |
| BE-11 | Implement robust server-side validation and sanitization for all input fields. | High | BE-9, BE-10 |
| BE-12 | Set up file storage mechanism for profile pictures (e.g., S3 bucket or similar). | High | BE-9 |
| BE-13 | Implement activity logging logic (hook into relevant mutations/actions to create records in `activities` table). | Medium | BE-2 |
| BE-14 | Ensure all relevant resolvers enforce authorization (user can only modify/delete their own profile). | High | BE-7, BE-8, BE-9, BE-10 |

#### Integration/General Tasks

| Task ID | Description | Priority | Dependencies |
|---|---|---|---|
| INT-1 | Integrate FE GraphQL client (e.g., Apollo Client) with BE GraphQL endpoint. | High | FE-3, BE-7 |
| INT-2 | End-to-end testing for profile view flow. | High | FE-4, BE-7 |
| INT-3 | End-to-end testing for profile edit flow (including picture upload). | High | FE-13, BE-9 |
| INT-4 | End-to-end testing for account deletion flow. | Medium | FE-17, BE-10 |
| INT-5 | Unit/Integration tests for FE components and BE resolvers. | Medium | All FE/BE tasks |
| INT-6 | Code reviews for all implemented tasks. | High | All FE/BE tasks |
| INT-7 | Documentation updates (if necessary). | Low | - |

### 2. Technical Approach

* **Framework/Language:** Ionic with React and TypeScript for the front-end. Node.js (likely with Apollo Server) for the GraphQL back-end.
* **Database:** PostgreSQL.
* **API:** GraphQL.
* **State Management (FE):** Utilize React Context API or a lightweight state management library to manage profile data and ensure immediate UI updates after edits. Apollo Client's cache can also handle some of this automatically.
* **Styling/Responsiveness:** Leverage Ionic's built-in components and grid system (`IonGrid`, `IonRow`, `IonCol`) along with CSS media queries for responsiveness across desktop and mobile.
* **i18n:** Use `react-i18next` library on the front-end. Store translations in JSON files (`public/locales/en/translation.json`, `public/locales/es/translation.json`, etc.). Wrap the application or relevant sections with the `I18nextProvider` and use the `useTranslation` hook in components.
* **Profile Picture Upload:**
    * **Front-end:** Use a standard `<input type="file" accept="image/*">`. Perform basic client-side validation (file type, size limit) for quick feedback. Optionally display an image preview.
    * **Transmission:** Use a library like `apollo-upload-client` (front-end) and `graphql-upload` (back-end) to handle multipart/form-data requests needed for file uploads via GraphQL.
    * **Back-end:** The `updateUserProfile` resolver will receive the file stream. Perform mandatory server-side validation (file type, size, potentially virus scan).
    * **Storage:** Upload the validated image file to a dedicated object storage service (e.g., AWS S3, Google Cloud Storage). This is preferred over storing blobs in the database for scalability and performance. Configure the storage bucket for public read access or use signed URLs if access needs to be restricted.
    * **Database:** Store only the URL or a unique identifier of the uploaded image in the `profile_picture_url` column of the `users` table in PostgreSQL.
*   **Activity Data:**
    * **Storage:** Use the `activities` table in PostgreSQL (`id` SERIAL PRIMARY KEY, `user_id` INTEGER REFERENCES users(id) ON DELETE CASCADE, `action_type` VARCHAR(50) NOT NULL, `details` JSONB, `created_at` TIMESTAMPTZ DEFAULT NOW()). The `ON DELETE CASCADE` ensures activities are removed if the user is deleted. Index `user_id` and `created_at`.
    * **Logging:** Implement a reusable logging service or function within the back-end that relevant mutations (`updateUserProfile`, etc.) can call to insert records into the `activities` table.
    * **Retrieval:** The `getUserActivities` resolver will query this table, filtering by the authenticated `user_id` and ordering by `created_at DESC`. Implement pagination (e.g., using cursor-based pagination with GraphQL) to handle potentially large numbers of activities efficiently.
* **Security:**
  * **Authentication:** Assume an existing authentication mechanism (e.g., JWT, sessions) provides the authenticated user's ID to the GraphQL context. All resolvers accessing or modifying user-specific data must check for a valid authenticated user.
  * **Authorization:** Resolvers must verify that the authenticated user is authorized to perform the requested action (e.g., user A cannot edit user B's profile). This usually involves comparing the authenticated user ID with the ID associated with the data being accessed/modified.
  * **Validation/Sanitization:** Use libraries like `class-validator` or `joi` for input validation in the back-end resolvers. Sanitize inputs (especially strings like bio) to prevent XSS attacks (e.g., using `dompurify` if rendering user-generated content). Use parameterized queries or an ORM (like TypeORM or Prisma) to prevent SQL injection.

### 3. Effort Estimation

These are rough estimates and depend on developer familiarity with the stack and the complexity of existing systems (like auth).

| Category | Task Group | Estimated Days |
|---|---|---|
| Front-end | Core View/Edit Components (FE-1 to FE-4, FE-8 to FE-13) | 4 - 6 days |
| Front-end | Activities View (FE-5 to FE-7) | 1 - 2 days |
| Front-end | Deletion Flow (FE-14 to FE-17) | 1 - 1.5 days |
| Front-end | i18n (FE-18 to FE-20) | 1 - 2 days |
| Back-end | Schema & GraphQL Setup (BE-1 to BE-6) | 1 - 2 days |
| Back-end | Core Resolvers (Profile View/Edit) (BE-7, BE-9, BE-11, BE-12, BE-14) | 3 - 5 days |
| Back-end | Activities Resolver & Logging (BE-8, BE-13) | 1.5 - 2.5 days |
| Back-end | Deletion Resolver (BE-10) | 0.5 - 1 day |
| Integration/Testing | Setup & E2E/Integration Tests (INT-1 to INT-5) | 3 - 5 days |
| **Overall** | **Total Estimated Effort** | **16 - 27 days** |

### 4. Questions or Clarifications

1. **Activity Tracking:**
    * What specific user actions should be logged as "recently completed activities"? Is this limited to profile changes, or does it include other application actions?
    * Is there an existing system/service for logging user activities, or does this need to be built from scratch as part of this feature?
    * How many recent activities should be displayed? Is pagination required for the activity list?
2. **i18n:**
    * Which specific languages need to be supported initially?
    * Who is responsible for providing the translated text strings?
    * Is there an existing i18n implementation or preferred library within the application?
3. **Design & UX:**
    * Are there detailed UI mockups or wireframes available for both mobile and desktop views, including the edit form and activity list/card format?
    * Is there an existing component library or design system that should be adhered to?
    * What is the desired behavior for the profile picture? (e.g., Aspect ratio enforcement? Cropping tool required? Default avatar?)
    * What content should the "farewell or goodbye page" display after account deletion?
4. **Profile Editing:**
    * Is the user's email address intended to be editable via this form? If so, does changing it require an email verification step? (Often email is tied to login and handled separately).
5. **Account Deletion:**
    * What is the exact scope of "all associated data" to be deleted? Does this include data in other tables potentially linked to the user but not explicitly mentioned (e.g., posts, comments)?
    * Is a "soft delete" (marking the account inactive instead of permanent deletion) acceptable or preferred, at least initially?
6. **Technical Context:**
    * Is there an existing authentication system in place? What information does it make available about the logged in user (e.g., user ID in context)?
    * Are there existing base components, utility functions, or API clients (like Apollo Client) already configured?
7. **Error Handling:**
    * Are there specific formats or locations required for displaying validation errors or server error messages?
