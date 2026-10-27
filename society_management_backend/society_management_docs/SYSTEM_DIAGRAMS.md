# Residential Society Management Platform - Visual System Diagrams

This document contains all the standalone **Mermaid Sequence Diagrams & Architectural Workflows** for the Residential Society Management Platform.

---

## 1. High-Level Platform Architecture

```mermaid
graph TD
    A[Society President / Admin] -->|1. Registers Self + Society Details + Reg Docs| B(Society & User in Pending State)
    C[Platform Super Admin Backend] -->|2. Verifies Submitted Docs & Approves| D[Approved President Account]
    D -->|3. President Selects SaaS Subscription Plan & Pays| E[Active Subscribed Society]
    E -->|4. President Configures Towers, Wings & Flats| F(Society Physical Hierarchy Ready)
    
    E -->|5. President / Admin Registers Accounts & Maps Flats| H[Created Accounts: Exe. Members, Owners, Tenants, Guards]
    H -->|6. President / Admin Adds Family Members & Details| I[Active User Accounts with Family & Flat Details]
    
    I -->|7. Daily Operations via Mobile App| J[Core Functional Modules]
    J --> J1[Gate Security & Visitors]
    J --> J2[Billing & Maintenance Ledger]
    J --> J3[Complaints & Work Orders]
    J --> J4[Amenity Bookings]
    J --> J5[Notices, Polls & Emergency SOS]
```

---

## 2. Phase 1: Society & President Onboarding Sequence

```mermaid
sequenceDiagram
    autonumber
    actor President as Society President / Admin
    participant API as Public API Endpoint
    participant DB as Database
    actor SuperAdmin as Platform Super Admin (Backend)
    participant Gateway as Payment Gateway (Razorpay/Stripe)

    President->>API: Submit Society Registration Form<br/>(Personal Details + Society Info + Reg Documents)
    API->>DB: Atomic Transaction:<br/>1. Create User (status: 'pending')<br/>2. Create Society (status: 'pending_verification')<br/>3. Map user_societies (role: 'society-admin', status: 'pending')
    API-->>President: Response: "Registration submitted! Under verification by platform team."
    
    Note over President: Cannot access dashboard yet (status = pending)

    SuperAdmin->>API: Fetch Pending Registrations List
    API->>DB: Fetch pending societies with uploaded docs & president details
    DB-->>SuperAdmin: Display pending list
    
    alt Super Admin Approves
        SuperAdmin->>API: Approve Society & President
        API->>DB: Update society.status = 'pending_subscription'<br/>Update user.status = 'active'<br/>Update user_societies.status = 'approved'<br/>Assign Spatie Role: 'society-admin'
        API->>President: Send Approval Email / SMS ("Your society is verified! Complete subscription to activate.")
        
        President->>API: Login with Credentials
        API-->>President: Token + Redirect to Subscription Checkout
        
        President->>API: Browse Subscription Plans (Basic / Standard / Premium)
        President->>API: Select Plan & Initiate Payment (Checkout)
        API->>Gateway: Create Subscription Order
        Gateway-->>President: Payment Modal / UPI Intent
        President->>Gateway: Completes Payment (or selects 14-Day Free Trial)
        Gateway-->>API: Webhook: Payment Verified
        API->>DB: Create society_subscriptions record<br/>Update society.status = 'active'
        API-->>President: "Subscription Active! Full Society Dashboard Unlocked."
    else Super Admin Rejects
        SuperAdmin->>API: Reject Registration (with rejection reason)
        API->>DB: Update society.status = 'rejected'
        API->>President: Send Rejection Email with correction guidance
    end
```

---

## 3. Phase 2: Centralized User Registration & Account Setup Authority Matrix

```mermaid
graph TD
    subgraph "Target Account Types"
        Acc1[Executive Committee Member<br/>Secretary / Treasurer]
        Acc2[Flat Owner Account]
        Acc3[Tenant Account]
        Acc4[Security Guard & Maintenance Staff]
    end

    subgraph "Creation Authority"
        Pres[President Only]
        AdminExec[President OR Authorized Admin / Executive Member]
    end

    Acc1 -->|Strict Rule: Only President Can Register| Pres
    Acc2 -->|Can Register & Map Flat| AdminExec
    Acc3 -->|Can Register & Map Flat| AdminExec
    Acc4 -->|Can Register & Assign Gate| AdminExec

    Pres -->|Creates Account| Act1[Created User Account]
    AdminExec -->|Creates Account| Act1

    Act1 -->|Step 2: Add Details| Setup[Add Family Members, Vehicles & Helpers]
    Setup -->|Step 3: Dispatch| User[User Receives Credentials & Logs In]
```

---

## 4. Phase 2: Login & Multi-Role / Multi-Society Selection Popup

```mermaid
sequenceDiagram
    autonumber
    actor User as User (Phone Number)
    participant App as Mobile App
    participant API as Laravel Backend
    participant DB as Database (Multi-Tenant)

    User->>App: Enter Phone Number + Password / OTP
    App->>API: POST /login (phone, password/otp)
    API->>DB: Query user by phone & fetch all approved memberships in user_societies
    DB-->>API: Return User + array of available_profiles[] (roles, societies, flats)
    API-->>App: Return Bearer Token + available_profiles[] + requires_role_selection

    alt Case 1: Phone Number has Multiple Roles / Flats (count > 1)
        App->>User: Display Popup Modal: "Select Role & Profile to Continue"<br/>(e.g., [Secretary - Green Valley] vs [Owner - Flat A-102] vs [Tenant - Flat C-304])
        User->>App: Taps Selected Role / Profile Card
    else Case 2: Single Role (count == 1)
        App->>App: Auto-select single linked profile
    end

    App->>API: GET /dashboard (Headers: X-Society-Id, X-Active-Role, X-Flat-Id)
    API->>DB: Scoped query for active society, role & flat
    DB-->>API: Return customized profile dashboard
    API-->>App: Render Role-Specific Dashboard (Admin vs Owner vs Tenant)

    opt Switch Role Later
        User->>App: Taps "Switch Role / Flat" in Account Hub
        App->>User: Reopens Role Selection Popup Modal
        User->>App: Selects alternate role -> App updates active context headers
    end
```

---

## 5. Phase 3: Gate Security & Visitor Operations

```mermaid
sequenceDiagram
    autonumber
    actor Visitor as Visitor / Maintenance Worker
    actor Guard as Security Guard (Guard App)
    participant API as Laravel Backend
    actor Resident as Resident (Owner / Tenant App)

    alt Category 1: Flat-Specific Visitor (Guest, Delivery, Cab)
        Visitor->>Guard: Arrives at Gate (Visits Flat #204)
        Guard->>API: Submit Visitor Entry (Photo + Target Flat # + Name + Phone + Vehicle #)
        API->>DB: Save visitor log with check-in timestamp
        API->>Resident: Auto Push Notification: "Your visitor [Name] has arrived at the gate" (With Photo)
        API-->>Guard: Entry recorded -> Visitor enters
    else Category 2: Maintenance Staff & Contractors (Multi-Flat Workers)
        Visitor->>Guard: Arrives at Gate (Plumber, Electrician, Lift AMC)
        Guard->>API: Submit Service Entry (Name + Phone + Service/Trade Type: e.g., 'Electrician')<br/>*NO Photo required | NO single Flat # required (Multi-flat access)*
        API->>DB: Save service entry log with check-in timestamp
        API-->>Guard: Service entry logged -> Worker enters society
    end

    opt Departure
        Visitor->>Guard: Leaves Society
        Guard->>API: Log Exit Timestamp
        API->>DB: Record check-out timestamp
    end
```

---

## 6. Phase 4: Financial Accounting & In-App Maintenance Cost Collection

```mermaid
sequenceDiagram
    autonumber
    actor Admin as Accountant / Committee
    participant Cron as Automated Billing Cron
    participant API as Laravel Backend
    actor Resident as Resident (Owner / Tenant App)
    participant Gateway as Payment Gateway / Bank

    Cron->>API: 1st of Month: Generate Itemized Invoices (Base + Water + Parking + Sinking Fund)
    API->>Resident: Push Alert & WhatsApp: "Oct Maintenance Bill Generated (Due: 15th)"
    
    Resident->>API: View Itemized Invoices & Breakdown (In-App)
    
    alt Mode A: Instant Online Payment (Razorpay / Stripe / UPI)
        Resident->>API: Initiate Online Payment
        API->>Gateway: Create Payment Order
        Resident->>Gateway: Pays via GPay / PhonePe / Card / NetBanking
        Gateway-->>API: Webhook: Payment Verified
        API->>API: Auto-mark Invoice as 'PAID' & Post Credit to Flat Ledger
        API->>Resident: Instant Push Notification + Official Digital Receipt with QR
    else Mode B: Direct Bank Transfer (NEFT / IMPS / UPI with Zero Fee)
        Resident->>Resident: Copies Society Bank A/c or scans Society UPI QR in App
        Resident->>Gateway: Transfers funds via Bank / UPI App
        Resident->>API: Submit Payment Proof (Enter UTR Number + Upload Receipt Screenshot)
        API->>Admin: Alert: "New Payment Proof Submitted for Flat #102"
        Admin->>API: Review Pending Payments (UTR & Receipt Image)
        Admin->>API: Approve Payment
        API->>API: Mark 'PAID', Post Credit to Flat Ledger & Update Society Bank Balance
        API->>Resident: Push Alert: "Payment Approved! Download Official Receipt"
    end
```

---

## 7. Phase 5: Grievance Routing & Committee Forwarding

```mermaid
graph TD
    A[Complaint Lodged by Resident / Guard / Exe. Member] -->|Real-Time Broadcast| B{Shared Society Admin Queue}
    
    B --> C[Visible in President Dashboard]
    B --> D[Visible in All Executive Members Dashboards]
    
    subgraph "Action Buttons in Admin / Exe. Login"
        E[Forward to Specific Executive Member<br/>e.g., Water Lead, Treasurer]
        F[Forward to President<br/>for escalation / policy decision]
        G[Assign to Maintenance Staff<br/>Electrician, Plumber, Lift Agency]
        H[Self-Assign / Take Ownership]
    end
    
    C --> E & F & G & H
    D --> E & F & G & H
    
    E & F & G & H --> I[Ticket Status: 'Assigned / In-Progress']
    I --> J[Assigned Person Performs Work & Uploads Completion Photo]
    J --> K[Status: 'Resolved']
    K --> L[Resident Receives Push Alert -> 1 to 5 Star Rating -> Closed]
```

---

## 8. Phase 7: Emergency SOS & Community Safety

```mermaid
graph LR
    A[Resident / Guard Taps SOS Button] -->|High-Priority Payload| B[Laravel Emergency Service]
    B -->|Priority Push Notification| C[Gate Security Guard Alert: Loud Siren]
    B -->|SMS / WhatsApp Alert| D[Society Emergency Contacts]
    B -->|Local Broadcast| E[Medical Emergency Network: Doctors & CPR Residents]
    B -->|Incident Logged| F[Emergency Audit Trail with GPS / Flat #]
```
