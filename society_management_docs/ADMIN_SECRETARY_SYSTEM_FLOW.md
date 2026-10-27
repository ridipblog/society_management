# Society Admin & Secretary System — System Workflow & Feature Specification

---

## 1. Executive Overview & System Scope

The **Society Admin & Secretary System** provides centralized governance and administrative controls for the Executive Committee (specifically the **Hon. Secretary**, **President**, and authorized Committee Administrators). It governs resident onboarding, financial ledger tracking, maintenance demand generation, grievance dispatch, perimeter gate oversight, amenity reservations, and emergency crisis management.

### Core Operational Principles
* **Direct Admin Persona**: Direct session access into the Admin / Secretary console without intermediary role selection modals.
* **Streamlined Authentication**: Mobile-first authentication using Registered Phone Number verified via Password or 6-digit SMS OTP.
* **View-Only Payment Accounting**: Real-time financial ledger tracking and automatic payment reconciliation without manual payment approvals.
* **Automated Auditability**: Every administrative action (member onboarding, fee recalculation, ticket escalation, blacklisting, emergency sirens) creates an immutable audit log.
* **Collaborative Helpdesk Dispatch**: Multi-tier grievance routing enabling delegation to committee leads, staff assignment, and presidential escalation.

---

## 2. End-to-End Administrative Workflow Pipeline

```
┌────────────────────────────────────────────────────────────────────────────────────────┐
│                   ADMIN & SECRETARY OPERATIONAL WORKFLOW PIPELINE                      │
└────────────────────────────────────────────────────────────────────────────────────────┘
                                            │
                                  [Admin Authentication]
                               • Registered Mobile Number (+91)
                               • Password or 6-Digit SMS OTP
                               • Direct Session Initialization
                                            │
                                            ▼
                             [Executive Overview & Analytics]
                               • Realized Collections vs Billed Demand
                               • Occupancy Statistics (Owners, Tenants, Vacant)
                               • Critical Open Grievances Queue
                                            │
       ┌────────────────────┬───────────────┴───────────────┬────────────────────┐
       ▼                    ▼                               ▼                    ▼
[Member Management]   [Financial Ledger]           [Helpdesk Dispatch]    [Gate & Amenities]
 • Admin-Only Creation • Automated Demand Invoicing • Ticket Intake        • Live Gate Log Audit
 • Unit & Flat Mapping • Dynamic Late Fee Run       • 4 Routing Actions    • Person/Plate Blacklist
 • Vehicle Registration• View-Only Payment Records  • Staff Work Order     • Amenity Anti-Collision
 • Invite Dispatch     • Defaulter Reminders        • Resident Verification• Gate SOS Siren Trigger
```

---

## 3. Module 1: Admin Authentication & Access Control

### 3.1. Authentication Workflow
1. **Identifier Entry**: Admin enters registered mobile number (`+91` format).
2. **Credential Verification**:
   * **Mode A (Password)**: Secure alphanumeric password verification.
   * **Mode B (SMS OTP)**: System generates a time-sensitive 6-digit SMS OTP to the registered mobile number.
3. **Session Initialization**:
   * System verifies that the user holds active `Secretary`, `President`, or `Admin` privileges.
   * Direct login into administrative session—no role-selection modal or prompt.
4. **Security Controls**:
   * Inactivity timeout and automatic session expiration.
   * Re-authentication prompt required prior to executing critical actions (e.g., perimeter SOS siren activation).

---

## 4. Module 2: Executive Dashboard & Operational Analytics

### 4.1. Core Metrics & Key Performance Indicators (KPIs)
* **Collection Efficiency**:
  * Realized monthly collection amount (₹).
  * Pending dues / unpaid balances (₹).
  * Realized collection percentage against total billed demand.
* **Occupancy & Roster Tracking**:
  * Total residential units in society.
  * Count of Owner-occupied units.
  * Count of Tenant-occupied units.
  * Count of Vacant / Unsold units.
* **Complaint Resolution Rate**:
  * Total active open tickets.
  * Tickets categorized by severity: Urgent / High Priority vs Standard.
  * Escalated tickets awaiting executive intervention.

### 4.2. Financial Trend Analysis
* **Historical Month-over-Month (MoM) Demand vs Collection**:
  * Tracks realized inflow against total billed demand across rolling 6-month historical cycles.
  * Automated variance calculation indicating collection health against society benchmark (e.g., 90% target threshold).
  * Monthly drill-down metrics: Billed Demand, Collected Amount, Recovery Rate (%), and Defaulter Count.

### 4.3. Urgent Grievances Queue
* Real-time stream of high-priority tickets requiring immediate executive dispatch.
* Ticket tracking parameters:
  * Unique Ticket Reference ID.
  * Reporting Flat Number and Resident Name.
  * Issue Category (e.g., Lift Breakdown, Main Line Water Leak, Security Breach).
  * Status Lifecycle: `Pending` ➔ `In Progress` ➔ `Closed`.

---

## 5. Module 3: Member & Account Provisioning

### 5.1. Admin-Only Account Creation
Self-registration by general public or residents is restricted to maintain perimeter security and verified residency. Accounts are provisioned exclusively by the Admin, Hon. Secretary, or President.

### 5.2. Account Creation Workflow
```
[Admin Inputs Member Data]
  • Role: Owner / Tenant / Committee / Guard / Staff
  • Full Name, Mobile Number (Unique Key), Email
  • Wing / Tower & Flat Number
  • Authorized Vehicle Plate & Assigned Parking Bay
            │
            ▼
[Validation Engine]
  • Validates mobile uniqueness in database
  • Checks parking slot availability & conflicts
            │
            ▼
[Account Provisioned]
  • Creates profile and links to unit ledger
  • Generates secure onboarding token
            │
            ▼
[Automated Invitation Dispatched]
  • Sends SMS / WhatsApp onboarding message with temporary credentials
  • Resident logs into Resident App and manages secondary family details
```

### 5.3. Resident Directory & Communication Features
* **Directory Search**: Instant lookup by resident name, flat number, or contact number.
* **Role Categorization**: Filter by Owners, Tenants, Committee Members, Security Guards, and Maintenance Staff.
* **Direct Communication Triggers**: Direct phone dialer trigger and direct messaging integration for administrative notices.

---

## 6. Module 4: Maintenance Billing, Late Fees & Payment Ledger

### 6.1. Maintenance Demands Generation
* Monthly demands generated automatically per flat based on predefined society billing rules (fixed maintenance, per-sq-ft charges, water charges, sinking fund).
* Demands scheduled and distributed with predefined payment due dates (e.g., 15th of each month).

### 6.2. Dynamic Late Fee Calculation & Lock
* **Recalculation Trigger**: Automated nightly batch job or on-demand manual trigger executed by Admin.
* **Calculation Engine**:
  * Identifies all accounts with outstanding balances past the grace period.
  * Calculates dynamic late fee formulas (e.g., fixed penalty or compounding monthly percentage).
  * Updates active balance on the flat's ledger.
  * Dispatches revised bill notification to resident.

### 6.3. View-Only Payment Records & Transaction Ledger
* **No Manual Payment Approvals**:
  * All payments made through digital channels (Payment Gateway, UPI, or Direct NEFT/RTGS Bank Transfers with UTR) are automatically recorded and reconciled.
  * The Admin does not approve or reject payments; the system acts as a transparent, view-only accounting ledger.
* **Ledger Record Parameters**:
  * Unit / Flat Number and Payer Name.
  * Amount Paid (₹) and Outstanding Balance.
  * Payment Status: `Paid`, `Pending`, or `Overdue`.
  * Date and Exact Payment Timestamp.
  * Mode of Payment (Online Gateway, UPI, Bank Transfer).
  * Payment Reference Number: Gateway Transaction ID or Bank UTR Reference.
* **Receipt & Invoice Access**:
  * Admin can inspect payment confirmation details and download official digital receipts featuring verification QR codes.

### 6.4. Defaulter Recovery Workflow
* Automated aging analysis (30 days, 60 days, 90+ days overdue).
* Batch or individual automated payment reminder notifications dispatched via WhatsApp and SMS containing direct payment links.

---

## 7. Module 5: Helpdesk & Grievance Routing

### 7.1. Collaborative Resolution Workflow
All resident complaints enter a centralized committee inbox and are tracked through a defined lifecycle:
`Unassigned / Urgent` ➔ `In Progress` ➔ `Resolved` ➔ `Verified & Closed`.

### 7.2. The 4 Administrative Routing Actions
For any reported grievance, the Admin or Secretary can trigger one of four resolution workflows:

1. **Forward to Committee Lead**:
   * Delegates ticket ownership to a specific functional committee member (e.g., Civil Works Lead, Electrical Lead, Gardening Lead).
   * Lead receives task notification and assumes resolution tracking.
2. **Escalate to President**:
   * Directly escalates high-impact issues, legal disputes, or major capital expense repairs to the President's priority queue.
3. **Assign Staff / Issue Work Order**:
   * Dispatches the issue directly to an authorized society maintenance technician (e.g., Society Plumber, Electrician, Lift AMC vendor).
   * System dispatches an automated work order via SMS / WhatsApp containing flat details, resident contact info, and issue description.
4. **Self-Assign**:
   * Admin / Secretary takes direct personal ownership of the ticket.

### 7.3. Resolution Verification & Rating
* When work is completed, Admin marks the ticket as `Resolved`.
* System triggers an automated verification prompt to the resident requesting confirmation and a 1-to-5 star service rating.

---

## 8. Module 6: Gate Security Oversight & Perimeter Control

### 8.1. Real-Time Gate Activity Monitoring
* Live tracking of active visitors, delivery personnel, cab drivers, and service vendors currently inside society premises.
* Searchable audit log filtering by visitor name, mobile number, flat visited, entry gate, and duration of stay.

### 8.2. Perimeter Blacklisting
* **Blacklist Enforcement**: Admin can flag specific individuals, contractors, or vehicle registration numbers as blacklisted.
* **Instant Sync to Security Gates**:
  * Blacklisted records propagate immediately to all guard gate consoles.
  * If a blacklisted person or vehicle attempts entry, the guard console triggers a red warning and alerts the Admin immediately.

---

## 9. Module 7: Amenity Scheduling & Anti-Collision Management

### 9.1. Amenity Booking Requests
* Residents submit booking requests for shared facilities (Clubhouse Banquet Hall, Party Lawn, Tennis Court, Guest Rooms).
* System validates resident eligibility (e.g., checks for zero outstanding maintenance dues).

### 9.2. Anti-Collision Verification
* Automated slot validation ensures no two bookings overlap on the same date, time slot, and facility.
* Maintenance and cleaning buffers are automatically enforced between consecutive bookings.

### 9.3. Administrative Decision
* Admin reviews requested date, time, flat, and stated event purpose.
* Actions: `Approve` or `Decline` (with mandatory reason).
* Approval generates a digital amenity pass for the resident and registers the booking on the society calendar.

---

## 10. Module 8: Society Circulars & Emergency Controls

### 10.1. Notice & Circular Broadcasting
* Admin drafts notices categorized by topic: `General Announcement`, `Maintenance Shutdown`, `AGM Notice`, `Emergency Regulation`.
* Supports attachment of formal documents (PDF circulars or image notices).
* Dispatches multi-channel announcements: instant push notification to resident devices and SMS broadcast.

### 10.2. Emergency Gate SOS Siren Trigger
* **Instant Siren Trigger**: Admin can activate the physical perimeter / gate siren during severe emergencies (Fire, Security Breach, Disaster).
* **Emergency Broadcast**:
  * Broadcasts a high-priority emergency banner across all resident and guard applications.
  * Provides one-tap access to emergency speed-dial: Community Doctor, Fire Station, Local Police Station, and Gate Intercom.

---

## 11. Module 9: System Notification Triggers

The system automatically triggers alerts to keep the Admin and Secretary updated on operational events:

| Event Trigger | Recipient | Delivery Channel | System Action |
| :--- | :--- | :--- | :--- |
| **Urgent Grievance Logged** | Admin / Secretary | Push Notification & In-App Alert | Adds ticket to Urgent Queue with dispatch action buttons |
| **Maintenance Payment Received** | Admin / Secretary | Push Notification & Ledger Update | Automatically updates flat ledger and logs transaction reference |
| **Gate SOS Siren Triggered** | Admin, Committee, Guards | High-Priority Siren Alert | Displays emergency location banner and speed-dial emergency contacts |
| **Amenity Booking Submitted** | Admin / Secretary | In-App Alert | Queues request for anti-collision review and approval |
| **Blacklisted Visitor Attempt** | Admin & Security Guards | Urgent Warning Alert | Blocks entry pass issuance and notifies security supervisor |
| **Maintenance Overdue Threshold** | Resident & Admin | WhatsApp / SMS Reminder | Generates overdue notice with dynamic late fee and payment link |
