## Admin Panel: Frontend Documentation for Backend Integration

**Last Updated:** June 4, 2025

### 1. Overview

This document provides an overview of the Admin Panel frontend, designed to help backend developers understand its structure, data flow, and integration points. The Admin Panel allows administrators to manage users, monitor activity, handle reports (Pireps), oversee revenue, and send messages.

Currently, the frontend operates with **dummy data** hardcoded in `admin_script.js`. The primary goal for backend integration will be to replace this dummy data with live data from backend APIs and to make frontend actions (like creating a user, suspending a post, etc.) persist through API calls.

### 2. File Structure

* **`admin_profile.html`**: The main HTML file containing the structure of the admin panel.
* **`admin_style.css`**: Contains all the CSS rules for styling the admin panel.
* **`admin_script.js`**: Contains all the JavaScript logic for interactivity, data manipulation (currently dummy data), and DOM updates.

### 3. HTML Structure (`admin_profile.html`)

The HTML is structured into a few main parts:

* **Sidebar (`<div class="sidebar">`)**:
    * Contains the logo and primary navigation links (`<nav class="navigation">`).
    * Navigation links have `data-target` attributes (e.g., `data-target="dashboard"`) that correspond to the `id` of content sections in the main area.
* **Main Content (`<div class="main-content">`)**:
    * **Top Header (`<header class="top-header">`)**: Displays the current section title and admin profile/logout.
    * **Content Area (`<main id="content-area">`)**: This is where different sections are loaded.
        * Each major feature has a `<section class="content-section">` with a unique `id` (e.g., `id="dashboard"`, `id="user-cockpit"`). Only one section is `active` (visible) at a time.
        * **Sub-navigation**: Sections like "User Cockpit", "Activity", "Pirep", and "Revenue" have sub-navigation buttons (`<button class="sub-nav-button">`) with `data-subtarget` attributes. These control visibility of sub-content divs (e.g., `<div id="users-view" class="sub-content">`).
        * **Tables**: Data is primarily displayed in tables (`<table class="data-table">`) with `<thead>` and `<tbody>`. The `<tbody>` is dynamically populated by JavaScript.
        * **Forms**: Forms like "Add User" (`<form id="add-user-form">`) and "Add Revenue Manually" (`<form id="add-revenue-form">`) are used for data input.
        * **Filter Containers (`<div class="filter-container">`)**: Present in many sections to allow users to filter the displayed data.
* **Modals**:
    * `userDetailsModal`: For viewing and performing actions on a specific user.
    * `transactionDetailModal`: For viewing details of a specific transaction.
    * Modals are hidden by default and shown by JavaScript.

### 4. CSS Styling (`admin_style.css`)

* **General Approach**: Uses a modern, clean design with a fixed sidebar and a main content area.
* **Key Selectors**:
    * Layout: `.sidebar`, `.main-content`, `.top-header`, `.content-section`, `.sub-nav`, `.sub-content`.
    * Components: `.btn`, `.data-table`, `.stat-card`, `.modal`, `.filter-container`.
    * Navigation: `.navigation ul li a`, `.active-link`, `.sub-nav-button.active`.
    * Status Indicators: Classes like `.status-active`, `.status-suspended`, etc., are used to style status texts.
* The CSS is fairly comprehensive and provides distinct styling for various states and components. Backend developers generally won't need to interact with this directly, but understanding class names can be helpful for debugging or if new UI elements are introduced.

### 5. JavaScript Logic (`admin_script.js`)

This file is central to the frontend's operation.

* **Initialization (`DOMContentLoaded`)**:
    * Sets up event listeners for navigation links (sidebar and sub-navigation).
    * Loads the default section (Dashboard) and populates its initial data.
* **Data Handling (Dummy Data)**:
    * Several arrays at the top of the script define dummy data for various entities:
        * `dummyUsers`: User information.
        * `dummyActivities`: Activity log entries.
        * `dummyCheckrideData`: Checkride schedule and analytics data.
        * `dummyPirepsData`: Pilot reports.
        * `dummyUserReportsData`: User-submitted reports about content/users.
        * `dummySuspendedPostsData`: Information about suspended posts/pireps.
        * `dummySubscriptionsData`: User subscription details.
        * `dummyTransactionsData`: Financial transactions.
    * **Backend Task**: Each of these arrays will need to be replaced with data fetched from backend APIs. The structure of these dummy objects provides a good indication of the expected data fields.
* **Navigation**:
    * Sidebar links: Show/hide main content sections (`<section class="content-section">`) and update the header title.
    * Sub-navigation buttons: Show/hide sub-content divs (`<div class="sub-content">`) within a section and trigger data population for that sub-view.
* **Dynamic Content Population**:
    * Functions like `populateUsersTable()`, `populateNewUsersTable()`, `populateActivityFeed()`, `populateAllPirepsTable()`, `populateSubscriptionsTable()`, `populateTransactionsTable()`, etc., are responsible for rendering data into HTML tables or lists.
    * They iterate over the respective dummy data arrays and create table rows (`<tr>`) or list items (`<li>`).
    * **Backend Task**: These functions will need to be modified to accept data fetched from an API as a parameter, or they will call API-fetching functions internally.
* **Filtering and Searching**:
    * Event listeners on "Apply Filter" buttons or input fields trigger filtering logic.
    * Filters typically iterate over the relevant dummy data array, apply conditions based on selected filter values (role, date, status, search term), and then re-populate the table/list with the filtered results.
    * **Backend Task**: Filtering and searching should ideally be handled by the backend. The frontend would send filter parameters to an API endpoint, and the API would return the filtered dataset.
* **Simulated CRUD Operations & Actions**:
    * The `window.adminPanel` object encapsulates many actions:
        * **User Management**:
            * `adminPanel.viewUserDetails(userId)`: Populates and shows the user details modal.
            * `adminPanel.editUser(userId)`: Placeholder for editing a user. **(Backend Task: Update user API)**
            * `adminPanel.suspendUser(userId, reason, ...)`: Marks a user as suspended in `dummyUsers`. **(Backend Task: Suspend user API)**
            * `adminPanel.unsuspendUser(userId, ...)`: Marks a user as active. **(Backend Task: Unsuspend user API)**
            * `adminPanel.deleteUser(userId)`: Removes a user from `dummyUsers`. **(Backend Task: Delete user API)**
            * `adminPanel.toggleCertificateVerification(userId, isChecked)`: Updates verification status. **(Backend Task: Update user verification API)**
            * `adminPanel.approveNewUser(userId)`: Marks a new user as active. **(Backend Task: Approve user API)**
            * `addUserForm` submit: Creates a new user object and adds it to `dummyUsers`. **(Backend Task: Create user API)**
        * **Activity/Analytics**:
            * `adminPanel.viewActivityDetails(activityId)`: Shows details of an activity.
            * Analytics functions (`calculateAndDisplayAnalytics`, `applyAllAnalyticsFilters`) process `dummyCheckrideData`. **(Backend Task: API for aggregated analytics data based on filters)**
        * **Pirep Management**:
            * `adminPanel.viewPirepDetails(pirepId)`.
            * `adminPanel.suspendPirep(pirepId)`: Marks a pirep as suspended and adds to `dummySuspendedPostsData`. **(Backend Task: Suspend Pirep API)**
            * `adminPanel.restorePirep(pirepId)`: Marks a pirep as active and removes from `dummySuspendedPostsData`. **(Backend Task: Restore Pirep API)**
        * **User Report Management**:
            * `adminPanel.viewReportDetails(reportId)`.
            * `adminPanel.updateReportStatus(reportId, newStatus)`: Updates status in `dummyUserReportsData`. **(Backend Task: Update report status API)**
            * `adminPanel.saveReportAction(reportId)`: Saves admin notes for a report. **(Backend Task: Update report notes API)**
        * **Suspended Post Management**:
            * `adminPanel.viewSuspendedPostDetails(postId)`.
            * `adminPanel.restoreSuspendedPost(postId)`: Removes post from `dummySuspendedPostsData` and potentially restores original content (e.g., a Pirep). **(Backend Task: Restore post API, potentially un-suspend original content API)**
            * `adminPanel.saveSuspendedPostAction(postId)`: Saves admin notes for a suspended post. **(Backend Task: Update suspended post notes API)**
        * **Revenue Management**:
            * `adminPanel.viewSubscriptionDetails(subId)`.
            * `adminPanel.editSubscription(subId)`: Placeholder. **(Backend Task: Update subscription API)**
            * `adminPanel.cancelSubscription(subId)`: Marks subscription as cancelled. **(Backend Task: Cancel subscription API)**
            * `adminPanel.viewTransactionDetails(txId)`: Populates and shows transaction details modal.
            * `adminPanel.markTransactionCompleted(txId)`: Updates transaction status. **(Backend Task: Update transaction status API)**
            * `adminPanel.rejectTransaction(txId)`: Marks transaction as rejected. **(Backend Task: Update transaction status API)**
            * `adminPanel.issueRefund(txId)`: Creates a new refund transaction and updates original. **(Backend Task: Create refund transaction API, update original transaction API)**
            * `addRevenueForm` submit: Creates a new manual revenue entry. **(Backend Task: Create manual transaction API)**
    * **Backend Task**: All these actions currently modify local JavaScript arrays. Each needs to be refactored to make an appropriate API call to the backend. The frontend should then update its display based on the API response (e.g., re-fetch data or update the local data if the API confirms success).
* **Modals**:
    * `adminPanel.showModal(modalId)` and `adminPanel.closeModal(modalId)` control visibility.
    * Content for modals is populated dynamically based on the selected item (user or transaction).
* **Global `adminPanel` Object**:
    * Serves as a namespace for many frontend functions, particularly those triggered by inline `onclick` attributes in the HTML (e.g., for action icons in tables).
* **Dashboard (`populateDashboardStats`)**:
    * Calculates and displays summary statistics based on the dummy data arrays.
    * **Backend Task**: The dashboard will likely require multiple API calls to fetch aggregated counts and stats.
* **Messaging (`updateRecipientCount`, send/preview buttons)**:
    * Simulates sending bulk messages. Calculates recipient count based on `dummyUsers` and `dummySubscriptionsData`.
    * **Backend Task**: Actual message sending (email/in-app) will be a backend responsibility. The frontend might fetch recipient counts for display and then trigger a backend API to send the message.

### 6. Backend Integration Points & API Call Suggestions

This section highlights where backend APIs are crucial.

* **Authentication & Authorization**:
    * Currently no login/logout functionality beyond a placeholder alert.
    * **Backend Task**: Needs to provide login/logout APIs. The frontend will need to store an auth token (e.g., JWT) and send it with subsequent API requests.
    * Permissions for different admin roles (if any) will be enforced by the backend based on the authenticated user. The frontend might conditionally show/hide UI elements based on permissions, but the backend must be the source of truth for authorization.
* **Data Fetching (GET Requests)**:
    * **Users**:
        * `GET /api/admin/users?role=<role>&search=<term>&status=<status>&page=<num>&limit=<count>` (for Users view)
        * `GET /api/admin/users/new?page=<num>&limit=<count>` (for New User view)
        * `GET /api/admin/users/suspended?page=<num>&limit=<count>` (for Suspended Accounts view)
        * `GET /api/admin/users/<userId>` (for user details modal)
    * **Activities**:
        * `GET /api/admin/activities?dateRange=<range>&userType=<type>&activityType=<type>&search=<term>&page=<num>&limit=<count>`
    * **Schedule Analytics**:
        * `GET /api/admin/analytics/checkrides?dateRange=<range>&dpeId=<id>&location=<loc>&certType=<type>&status=<status>` (for stats cards and DPE performance table)
        * `GET /api/admin/analytics/dpes` (to populate DPE filter dropdown)
        * `GET /api/admin/analytics/locations` (to populate location filter dropdown)
    * **Pireps**:
        * `GET /api/admin/pireps?search=<term>&status=<status>&page=<num>&limit=<count>` (for View All Pireps)
        * `GET /api/admin/pireps/<pirepId>`
    * **User Reports**:
        * `GET /api/admin/reports/user?status=<status>&search=<term>&page=<num>&limit=<count>`
        * `GET /api/admin/reports/user/<reportId>`
    * **Suspended Posts**:
        * `GET /api/admin/posts/suspended?search=<term>&page=<num>&limit=<count>`
        * `GET /api/admin/posts/suspended/<postId>`
    * **Revenue - Subscriptions**:
        * `GET /api/admin/subscriptions?planType=<type>&status=<status>&search=<term>&page=<num>&limit=<count>`
        * `GET /api/admin/subscriptions/<subscriptionId>`
    * **Revenue - Transactions**:
        * `GET /api/admin/transactions/pending?type=<type>&search=<term>&page=<num>&limit=<count>`
        * `GET /api/admin/transactions/completed?userType=<type>&txType=<type>&fromDate=<date>&toDate=<date>&search=<term>&page=<num>&limit=<count>`
        * `GET /api/admin/transactions/<transactionId>`
    * **Dashboard Stats**:
        * `GET /api/admin/dashboard/stats` (could return an object with all necessary counts: totalUsers, newUsers, pendingReports, etc.)
    * **Messaging Recipient Counts**:
        * `GET /api/admin/messaging/recipient-count?group=<group_identifier>`
* **Data Creation (POST Requests)**:
    * **Add User**: `POST /api/admin/users` (body: user data from form)
    * **Add Revenue Manually**: `POST /api/admin/transactions/manual` (body: transaction data from form)
* **Data Update (PUT/PATCH Requests)**:
    * **Edit User**: `PUT /api/admin/users/<userId>` (body: updated user data)
    * **Suspend/Unsuspend User**: `PATCH /api/admin/users/<userId>/status` (body: `{ status: "suspended/active", reason: "..." }`)
    * **Toggle Certificate Verification**: `PATCH /api/admin/users/<userId>/verification` (body: `{ isVerified: true/false }`)
    * **Approve New User**: `PATCH /api/admin/users/<userId>/approval` (body: `{ status: "active" }`)
    * **Suspend/Restore Pirep**: `PATCH /api/admin/pireps/<pirepId>/status` (body: `{ status: "suspended/active", reason: "..." }`)
    * **Update User Report Status/Notes**: `PATCH /api/admin/reports/user/<reportId>` (body: `{ status: "...", adminNotes: "..." }`)
    * **Update Suspended Post Notes**: `PATCH /api/admin/posts/suspended/<postId>/notes` (body: `{ adminNotes: "..." }`)
    * **Edit Subscription**: `PUT /api/admin/subscriptions/<subscriptionId>` (body: updated subscription data)
    * **Cancel Subscription**: `PATCH /api/admin/subscriptions/<subscriptionId>/status` (body: `{ status: "cancelled" }`)
    * **Mark Transaction Completed/Rejected**: `PATCH /api/admin/transactions/<transactionId>/status` (body: `{ status: "completed/rejected" }`)
* **Data Deletion (DELETE Requests)**:
    * **Delete User**: `DELETE /api/admin/users/<userId>`
* **Actions (POST Requests for non-CRUD actions)**:
    * **Issue Refund**: `POST /api/admin/transactions/<originalTxId>/refund` (body: refund details if any, or just trigger)
    * **Send Bulk Message**: `POST /api/admin/messaging/send` (body: `{ recipientGroup: "...", messageType: "...", subject: "...", body: "..." }`)

### 7. Key Data Structures (Examples from `admin_script.js`)

These provide a template for the data structures the frontend currently expects. The backend should aim to provide data in a similar format.

* **User (`dummyUsers`)**:
    ```javascript
    {
        id: 1111111,
        username: 'Yash',
        fullName: 'Yash DPE', // Added for consistency, often present
        role: 'DPE', // e.g., DPE, STUDENT, ADMIN, OTHER
        email: 'yash.dpe@example.com',
        status: 'Active', // e.g., Active, Suspended, Pending Verification
        certificateVerified: true,
        phoneNumber: '555-1234',
        certificateNumber: 'CERT98765', // For new users
        suspensionReason: 'Spamming reports', // If suspended
        suspendedDate: '2025-05-01' // If suspended
    }
    ```

* **Activity (`dummyActivities`)**:
    ```javascript
    {
        id: 1111111,
        user: { name: 'Samruddha', type: 'ADMIN' },
        action: 'scheduled a checkride',
        targetUser: { name: 'DPE Yash', type: 'DPE' }, // Optional
        type: 'checkride_scheduled', // e.g., checkride_scheduled, pirep_posted, account_suspended, user_login, profile_updated
        timestamp: '2025-05-10T10:00:00Z',
        details: 'Checkride for PPL at KSQL' // Optional
    }
    ```

* **Checkride Data (`dummyCheckrideData` - for analytics)**:
    ```javascript
    {
        id: 101,
        dpeName: 'YashDPE',
        dpeId: 1, // Link to user ID
        studentName: 'Student A',
        date: '2025-05-01',
        certType: 'PPL', // e.g., PPL, CPL, IR, ME
        status: 'Passed', // e.g., Passed, Failed, Cancelled, Discontinued, Standby
        location: 'KSQL'
    }
    ```

* **Pirep (`dummyPirepsData`)**:
    ```javascript
    {
        id: 201,
        date: '2025-05-08',
        submittedBy: 'GokulStudent', // Username or User ID
        aircraft: 'C172',
        departure: 'KSFO',
        arrival: 'KLAX',
        summary: 'Smooth flight, light turbulence over Tehachapi.',
        status: 'Active' // e.g., Active, Suspended
    }
    ```

* **User Report (`dummyUserReportsData`)**:
    ```javascript
    {
        id: 301,
        reportedBy: 'GokulStudent', // Username or User ID
        reportReason: 'Inappropriate language in comment',
        reportedContentId: 'comment_123', // ID of the reported content (Pirep, comment, etc.)
        reportedUser: 'ThorUser', // Username or User ID of the user who posted the content
        date: '2025-05-09',
        status: 'New', // e.g., New, Action Taken, Dismissed
        adminNotes: ''
    }
    ```

* **Suspended Post (`dummySuspendedPostsData`)**:
    ```javascript
    {
        id: 401,
        originalPoster: 'HulkPilot', // Username or User ID
        postType: 'PIREP', // e.g., PIREP, Comment
        contentId: 'pirep_203', // ID of the original content
        suspensionReason: 'Misleading PIREP information',
        postSnippet: 'PIREP #203: KOAK to KSQL...',
        suspendedDate: '2025-05-08',
        adminNotes: 'Reviewed after report #302.'
    }
    ```

* **Subscription (`dummySubscriptionsData`)**:
    ```javascript
    {
        id: 501,
        userId: 2,
        userName: 'GokulStudent',
        planName: 'Monthly', // e.g., Monthly, 6-Month, Annual
        amount: 29.99,
        startDate: '2025-04-15',
        renewalDate: '2025-05-15', // Or end date if not auto-renewing
        paymentMethod: 'Credit Card ending 1234',
        status: 'Active' // e.g., Active, Expired, Cancelled
    }
    ```

* **Transaction (`dummyTransactionsData`)**:
    ```javascript
    {
        txId: 'ORD-001', // Transaction ID
        userId: 2,
        userName: 'GokulStudent',
        userEmail: 'gokul.student@example.com',
        userType: 'STUDENT', // e.g., STUDENT, DPE, OTHER
        date: '2025-05-01',
        time: '10:00 AM',
        transactionKind: 'CheckridePayment', // e.g., CheckridePayment, SubscriptionFee, ServiceFee, Refund, Manual Adjustment
        amount: 150.00,
        paymentMethod: 'Stripe',
        status: 'Pending', // e.g., Pending, Completed, Rejected, Refunded
        transactionType: 'Credit', // Credit (money in), Debit (money out/refund)
        dpeName: 'YashDPE', // If checkride payment
        checkrideType: 'PPL Initial', // If checkride payment
        primaryAirport: 'KSQL', // If checkride payment
        certNo: 'N/A', // If checkride payment
        messageToAdmin: 'Awaiting DPE confirmation.' // Admin notes or user message
    }
    ```
