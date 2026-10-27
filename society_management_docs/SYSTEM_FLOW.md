# Residential Society Management Platform - End-to-End System Workflow

---

## 1. High-Level System Architecture Flow

> 📊 *Visual Architecture Diagram*: See [SYSTEM_DIAGRAMS.md - High-Level Architecture](file:///c:/society_management_backend/SYSTEM_DIAGRAMS.md#1-high-level-platform-architecture).

---

## 2. Module-by-Module Sequential System Flow

```
┌────────────────────────────────────────────────────────────────────────────────────────────────┐
│                                 END-TO-END WORKFLOW PIPELINE                                   │
└────────────────────────────────────────────────────────────────────────────────────────────────┘
  [Phase 1: President & Society Sign-Up]   President registers self + society details + reg docs
                     │
                     ▼
  [Phase 2: Platform Backend Verification]  Super Admin verifies docs ──> Approves Society & President
                     │
                     ▼
  [Phase 3: SaaS Subscription & Payment]   President selects Plan (Basic/Standard/Premium) & Pays
                     │
                     ▼
  [Phase 4: Society Physical Layout Setup]  Subscribed President sets up Towers, Wings & Flats
                     │
                     ▼
  [Phase 5: User Registration by President] President/Admin creates accounts ──> Adds Family & Flat details
                     │
                     ▼
  [Phase 6: Multi-Society Context Switch]   User selects active society (X-Society-Id Header)
                     │
                     ▼
  [Phase 7: Daily Operations & Gate Pass]   Gate Security ── Visitor Logs ── Domestic Staff Tracking
                     │
                     ▼
  [Phase 8: Financial & Maintenance Bills]  Bill Demand Generation ── Payment Upload ── Ledger Posting
                     │
                     ▼
  [Phase 9: Helpdesk & Grievance Tickets]   Complaint Raised ── Staff Assigned ── Resolved ── Rating
                     │
                     ▼
  [Phase 10: Community & Safety SOS]       Notice Broadcasts ── AGM Polls ── SOS Siren
```

---

## Phase 1: Society & President Onboarding & Verification

> 📊 *Visual Sequence Diagram*: See [SYSTEM_DIAGRAMS.md - Phase 1 Onboarding Sequence](file:///c:/society_management_backend/SYSTEM_DIAGRAMS.md#2-phase-1-society--president-onboarding-sequence).

### Step 1.1: Society Self-Registration by President / Secretary
1. **Registration Action**: Submit online registration form.
2. **Payload Collected**:
   * **President Personal Credentials**:
     * Full Name, Mobile Number, Email, Password, Designation (President / Secretary / Chairman).
   * **Society Profile**:
     * Society Name, Address, City, State, Pincode, Latitude / Longitude (Geofence).
     * Registration Number (Cooperative Society / RWA Registration No.).
     * Optional: GST Number, PAN Number.
   * **Verification Documents**:
     * Society Registration Certificate upload (PDF/Image).
     * President ID Proof (Aadhaar / PAN upload).
3. **Initial System State**:
   * `societies.status = 'pending_verification'`
   * `users.status = 'pending'`
   * `user_societies.status = 'pending'`
   * President **cannot** access society management features until verified.

### Step 1.2: Platform Backend Verification (Super Admin)
1. **Super Admin Review**:
   * Super Admin logs in to the **Platform Management Portal**.
   * Views the pending registration queue with uploaded documents and contact details.
2. **Action - Approve**:
   * `societies.status` becomes `'pending_subscription'` (Verified, but awaiting subscription).
   * `users.status` becomes `'active'`.
   * `user_societies.status` becomes `'approved'`.
   * Spatie role `society-admin` is formally assigned.
   * Notification dispatched to President: *"Your society has been verified! Choose your subscription plan to get started."*
3. **Action - Reject**:
   * Status marked as `'rejected'` with an admin reason (e.g., "Registration certificate unclear").
   * Email notification sent back to applicant with instructions to re-upload.

### Step 1.3: SaaS Subscription Plan Selection & Payment Flow
Once verified by the platform team, the President unlocks full society access by subscribing to a SaaS plan:
1. **Browse Plans**:
   * **Basic**: Resident Management, Notices, Billing.
   * **Standard**: Includes Visitor Management, Complaints & Gate App.
   * **Premium**: Includes Accounting, Analytics, Smart Integrations.
   * *(Optional: 14-Day Free Trial activation)*.
2. **Checkout & Payment**:
   * President selects billing cycle (Monthly / Yearly) and initiates payment via Razorpay / Stripe or NetBanking.
3. **Activation & Invoice**:
   * Upon successful payment, system creates an active entry in `society_subscriptions`.
   * `societies.status` transitions from `'pending_subscription'` to `'active'`.
   * Official SaaS Tax Invoice generated for the society records.
   * **Full Society Management Console is now live!**

### Step 1.4: President & Executive Setup of Society Physical Layout
With an active subscription, the President or Executive Members configure the society layout:
* **Blocks / Towers / Wings**: (e.g., Tower A, Tower B, Wing 1).
* **Flats / Units**: (e.g., Flat 101, 102, 201; Type: 2BHK, 3BHK; Sq. Ft.).
* **Optional Unique Flat Identifier (`unique_flat_code` / `flat_uid`)**:
  * Custom unique reference code optionally set per flat (e.g., `SOC-A-101`, meter ID).
* **Society Hierarchy Configured**: Towers, Wings, and Flats are now ready for the President/Admin to register owners, tenants, and committee members!


---

## Phase 2: Centralized User Registration, Family Setup & RBAC (President/Admin Driven)

### Step 2.1: Centralized User Account Creation by President / Admin
In this platform, accounts are **not self-registered by unverified public users**. To guarantee data privacy, safety, and verified society membership, **all user accounts are registered and created directly by the President or authorized Admin / Executive Committee Members**:

1. **Who Can Register / Create Accounts**:
   * **Executive Committee Members (Secretary, Treasurer, VP)**: Registered exclusively by the **President**.
   * **Flat Owners**: Registered by the **President** or **Authorized Admin / Executive Member**.
   * **Tenants**: Registered by the **President** or **Authorized Admin / Executive Member**.
   * **Security Guards**: Registered by the **President** or **Authorized Admin / Executive Member**.
   * **Maintenance Staff**: Registered by the **President** or **Authorized Admin / Executive Member**.

2. **Creation Workflow & Payload**:
   * President / Admin inputs the new user's details:
     * **Personal Credentials**: Full Name, Mobile Number, Email Address, Designation / Role (`executive-committee`, `resident-owner`, `resident-tenant`, `security-guard`, `maintenance-staff`).
     * **Flat / Unit Mapping**: Select Tower/Block + Flat # (for Owners and Tenants).
     * **Occupancy Type**: Marked as `owner_occupied` or `rented`.
   * **Instant Activation & Credentials Dispatch**:
     * User account is created in `users` table and linked in `user_societies` with `status = 'approved'` immediately.
     * Appropriate Spatie role is assigned (`role:president`, `role:secretary`, `role:treasurer`, `role:committee-member`, `role:resident-owner`, `role:resident-tenant`, `role:security-guard`).
     * The system sends an automated SMS & Email invite to the user with login credentials (or a secure one-time password / login link).

### Step 2.1.1: Adding Family Member Details & Flat Configuration (By President/Admin or User)
During or immediately after creating the user account, the President / Admin (or later the resident themselves) adds the complete household and flat details:

1. **Family Members Setup**:
   * President or Admin inputs the resident's family member details:
     * Full Name, Relationship (Spouse, Child, Elderly Parent), Phone Number, and Optional App Access toggle.
   * If App Access is enabled, the family member receives their own login credentials to view society notices, receive visitor alerts, and book amenities.
2. **Vehicle & Helper Details Added**:
   * President / Admin adds the flat's authorized vehicles (2-wheeler, 4-wheeler, license plate #, assigned parking bay).
   * Links recurring domestic helpers (maid, cook, driver) with gate pass clearance.
3. **Owner vs. Tenant Rental Differentiation**:
   * For rented flats, President / Admin creates the Tenant account with `role = 'resident-tenant'` and links them to the flat with Occupancy Type = `tenant`.
   * **Role Separation for the Same Flat**:
     * **Tenant (Daily Living)**: Approves/denies live visitor gate entries, receives domestic helper alerts, logs daily maintenance/repair complaints, and books clubhouse amenities.
     * **Owner (Property Governance)**: Monitors overall flat maintenance dues, tracks tenancy records, and retains society AGM voting rights.

---

### Step 2.2: Centralized User Creation Authority Matrix

> 📊 *Visual Authority Matrix Diagram*: See [SYSTEM_DIAGRAMS.md - User Creation Authority Matrix](file:///c:/society_management_backend/SYSTEM_DIAGRAMS.md#3-phase-2-centralized-user-registration--account-setup-authority-matrix).

#### Authority Breakdown Table:

| Account Type Being Created | Who Has Authority to Register / Create? | Authorized Roles in API Middleware |
| :--- | :--- | :--- |
| **Executive Committee Member** *(Secretary, Treasurer, VP)* | **President ONLY** | `role:president` |
| **Flat Owner Account** | **President** OR **Executive Committee Members** | `role:president\|secretary\|treasurer\|committee-member` |
| **Tenant Account** | **President** OR **Executive Committee Members** | `role:president\|secretary\|treasurer\|committee-member` |
| **Security Guard Account** | **President** OR **Executive Committee Members** | `role:president\|secretary\|treasurer\|committee-member` |
| **Maintenance Staff Account** | **President** OR **Executive Committee Members** | `role:president\|secretary\|treasurer\|committee-member` |

#### Post-Creation Household Setup Actions:
1. **Immediate Family & Vehicle Entry**: President or Admin can input family members and vehicles immediately during account creation or via the Admin Flat Management console.
2. **Resident Self-Service in Account Hub**: Once the Owner/Tenant logs in, they also have full capability in their **User Account Hub (Step 2.4)** to view, add, or update their family members, vehicles, and emergency contacts.

### Step 2.3: Phone Number Login, Role Selection Popup & Multi-Society Context Switching

> 📊 *Visual Sequence Diagram*: See [SYSTEM_DIAGRAMS.md - Login & Multi-Role Selection Popup](file:///c:/society_management_backend/SYSTEM_DIAGRAMS.md#4-phase-2-login--multi-role--multi-society-selection-popup).

#### 1. Multiple Role Condition:
When a user logs in using their registered mobile phone number, the system checks whether the phone number is linked to multiple roles or flats, such as:
* An **Executive Committee Member (e.g. Secretary / Treasurer)** who is also a **Flat Owner**.
* An owner who owns **multiple flats** within the same society or across different societies.
* An individual who is an **Owner** in one society and a **Tenant** in another society.

#### 2. About the Role Selection Popup:
* **Single Role Account**: If the phone number is associated with only one role and flat, the user is logged in directly to their dashboard with no popup.
* **Multiple Role Account**: If the phone number is linked to more than one role or flat:
  * A **"Select Role & Profile" Popup Modal** appears immediately after login.
  * The user is shown their linked roles (e.g. *Secretary*, *Owner - Flat A-102*, *Tenant - Flat C-304*) and selects which role to enter as.
  * Inside the app, a **"Switch Role"** button allows the user to reopen the popup at any time to switch roles without logging out.

### Step 2.4: User Account & Profile Management ("My Account" Hub)
Once authenticated, every user (Owner, Tenant, Executive, or Guard) has access to their personal **User Account** module:

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                       USER ACCOUNT & PROFILE ARCHITECTURE                   │
└──────────────────────────────────────┬──────────────────────────────────────┘
                                       │
      ┌──────────────────┬─────────────┼─────────────┬──────────────────┐
      ▼                  ▼             ▼             ▼                  ▼
[ Profile & Security ] [ Family ] [ Vehicles ] [ Daily Helpers ] [ Flat Account ]
  • Name, Mobile, Email  • Spouse   • 2-Wheeler   • Maid, Cook     • Live Balance
  • Password & Device    • Children • 4-Wheeler   • Driver, Nanny  • Receipts & UTR
  • Push Notifications   • Parents  • Plate Reg   • In/Out Logs    • Passbook/Ledger
```

#### 1. Personal Profile & Security:
* Personal Details: Full Name, Verified Mobile Number, Email Address, Profile Avatar upload.
* Security Settings: Change Password, Biometric / PIN login setup, Active device sessions management.
* Notification Preferences: Toggle Push Notifications, WhatsApp updates, and Email circulars.

#### 2. Family Members Management:
* Pre-populated directly by the President/Admin during account creation, or added/updated by flat owners/tenants for family residing in the same flat (Spouse, Children, Elderly Parents).
* Fields: Name, Relationship, Phone Number, Optional App Login Access.
* Enables family members to independently view notices, book amenities, or receive visitor alerts.

#### 3. Vehicle Registry:
* Residents register their personal vehicles for gate security and parking authorization.
* Fields: Vehicle Type (`2-wheeler`, `4-wheeler`, `EV`), Number Plate (e.g., `MH-12-AB-1234`), Make & Model, Assigned Parking Bay Number.
* Guards at the gate can instantly verify authorized resident vehicles via plate search.

#### 4. Domestic Staff Linked to Account:
* Residents link recurring service helpers to their flat (Maid, Cook, Driver, Babysitter).
* View daily in/out check-in logs and monthly helper attendance sheets.

#### 5. My Flat Financial Account / Statement:
* **Live Balance**: Displays current outstanding dues or prepaid advance balance.
* **Payment History & Passbook**: Itemized ledger of all maintenance invoices, penalty charges, and credits.
* **Instant Receipt Download**: 1-click access to download official signed PDF receipts for any past transaction.

#### 6. Emergency Contacts & Medical Badge:
* Primary emergency contact (Name, Phone number, Relationship).
* **Optional Community Medical Badge**: User can opt-in to mark themselves as a *Doctor*, *Nurse*, or *CPR-trained volunteer* for emergency community SOS alerts.

#### 7. Digital Document Vault:
* Secure storage for flat-related documents (Possession Letter, Sale Deed, Rental Agreement, Police Verification documents).

---

## Phase 3: Gate Security & Visitor Operations Flow

> 📊 *Visual Sequence Diagram*: See [SYSTEM_DIAGRAMS.md - Gate Security & Visitor Operations](file:///c:/society_management_backend/SYSTEM_DIAGRAMS.md#5-phase-3-gate-security--visitor-operations).

---

### Step 3.1: Flat-Specific Visitor Flow (Guests, Deliveries & Cabs)
1. **Visitor Arrival**:
   * A guest, delivery courier, or cab driver arrives at the main gate for a specific flat.
2. **Guard Input**:
   * **Visitor Live Photo**: Guard snaps a quick photo using the Guard Mobile App camera.
   * **Target Flat Number**: Selected from the society's active flat directory (e.g., Tower B - Flat 302).
   * **Visitor Details**: Name, Mobile Number, optional Vehicle Number, and Visitor Type (`guest`, `delivery`, `cab`).
3. **Automated Notification**:
   * The backend immediately triggers an **automated push notification** to the Flat Owner / Tenant's mobile phone:
     > 🔔 *"Your visitor [Name] has arrived at the main gate."* *(Includes visitor photo and vehicle number)*
   * No waiting or manual gate delays—the entry is logged instantly.

---

### Step 3.2: Maintenance Staff & Service Workers Flow (Multi-Flat Access)
Because maintenance staff (plumbers, electricians, lift technicians, painters, carpenters, cleaning teams) frequently service **multiple flats** or common society facilities in a single visit:
1. **No Photo Required**: Quick and frictionless entry to prevent long queues at the gate.
2. **No Single Flat Selection Required**: The worker is not tied to one flat; they have general society-wide access.
3. **Guard Input**:
   * **Worker Details**: Name and Mobile Number.
   * **Service / Trade Category**: Selected from predefined tags (e.g., `Electrician`, `Plumber`, `Lift Maintenance`, `Housekeeping`, `Civil Work`, `Internet Provider`).
   * **Agency / Company Name**: Optional (e.g., *Otis Elevator*, *Urban Company*, *Local Contractor*).
4. **System Action**:
   * Logs entry under society service logbook with timestamp and guard ID.
   * Enables the management committee to track how many outside contractors are inside the society premises at any given time.

---

### Step 3.3: Digital Gate Exit & Premises Tracking
* **Live In-Premises Dashboard**:
  * Guard dashboard displays real-time list of all visitors and service workers currently inside the society premises.
* **1-Tap Check-Out**:
  * When a visitor or maintenance worker exits, the guard taps **"Exit"** to log the exact check-out timestamp, keeping a secure, searchable digital logbook.



---

## Phase 4: Financial Accounting & In-App Maintenance Cost Collection Flow

> 📊 *Visual Sequence Diagram*: See [SYSTEM_DIAGRAMS.md - Financial Accounting & Maintenance Cost Collection](file:///c:/society_management_backend/SYSTEM_DIAGRAMS.md#6-phase-4-financial-accounting--in-app-maintenance-cost-collection).

---

### Step 4.1: Itemized Maintenance Demand Generation
1. **Calculation Rules (Configured per Society)**:
   * **Fixed Monthly Rate** (e.g. ₹3,500/flat) OR **Area-Based Rate** (e.g. 1,250 sqft × ₹3/sqft = ₹3,750).
   * **Itemized Line Items**:
     * Base Maintenance (Cleaning, Security, Garden, Common Electricity).
     * Sinking / Reserve Fund.
     * Water & Sub-meter Electricity reading charges.
     * Reserved Parking Bay Charges.
     * Previous unpaid dues / Late interest penalty carried forward.
2. **Dispatch & Notification**:
   * Auto-generated on the 1st of every month via automated scheduler (`php artisan schedule:run`).
   * Instant Push Notifications, SMS, and WhatsApp alerts sent to all active flat occupants.

---

### Step 4.2: In-App Payment Submission by Every Resident
Every Flat Owner or Tenant opens their mobile app and views their current dues with three payment options:

#### Option A: Instant In-App Online Payment (UPI / Cards / NetBanking)
* Resident taps **"Pay Now"**.
* Supported gateways: **Razorpay / Stripe / PayU**.
* Resident selects **Google Pay, PhonePe, Paytm, Debit/Credit Card, or NetBanking**.
* **Zero Manual Effort**: Payment gateway webhooks automatically confirm the payment $\rightarrow$ Invoice marked `'PAID'` $\rightarrow$ Instant digital receipt issued.

#### Option B: Direct Bank / UPI Transfer with Screenshot Upload (100% Free - Most Popular)
* To avoid 2% gateway transaction charges, the resident can pay directly to the society bank:
  1. The app displays the Society's **Bank Name, Account Number, IFSC Code, and Dynamic UPI QR Code**.
  2. Resident pays via their banking app (IMPS / NEFT / UPI).
  3. Resident returns to the app, inputs the **12-digit Bank UTR / Reference Number**, and uploads the **Payment Screenshot**.
  4. Invoice status updates to `'under_verification'`.

#### Option C: Cash / Cheque Offline Entry
* For elderly or traditional residents, the Accountant can manually log cash or cheque payments (`cheque_no`, `bank_name`, `deposit_date`) directly into the Admin portal.

---

### Step 4.3: 1-Click Verification & Automated Digital Receipting
1. **Accountant Verification Queue**:
   * Shows flat number, submitted amount, payment date, UTR number, and high-resolution receipt image preview.
2. **Action - Approve**:
   * Invoice status changes to **`'PAID'`**.
   * Automatically adds a **Credit Transaction** to the individual `flat_ledgers`.
   * Society Bank/Cash ledger is debited with the collection amount.
   * **Official Digital Receipt Generated**: Includes Society Logo, Tax Reg No, Unique Receipt Number, Flat Number, Transaction UTR, Digital Signature, and Verification QR Code.
   * Resident receives instant push notification: *"Your payment of ₹4,200 for Oct has been approved! Download receipt."*
3. **Action - Reject**:
   * Admin enters rejection reason (e.g. *"UTR not found in bank statement"*, *"Incorrect amount"*).
   * Resident receives an alert and can re-submit valid proof without generating a duplicate bill.

---

### Step 4.4: Dynamic Late Fee Calculation & Accountant Re-Generate Action

1. **Dynamic Late Payment Calculation on Fetch (Resident App)**:
   * **No automated background penalty churn**: Instead of cron jobs constantly writing penalty entries, the late fee is **dynamically calculated in real-time** when the resident fetches their bill to pay in the mobile app.
   * **On-the-Fly Calculation Logic**:
     * If `current_date <= due_date`: Late fee is ₹0.
     * If `current_date > due_date`: Backend calculates late fee on-the-fly based on overdue days (e.g., Fixed penalty of ₹200 OR daily interest).
   * **Resident View**: The mobile app displays the live breakdown at the time of payment:
     $$\text{Total Payable} = \text{Base Maintenance} + \text{Dynamically Calculated Late Fee}$$

2. **"Re-Generate Maintenance Amount" Button (Accountant / Admin Action)**:
   * In the Accountant / Admin Portal, every invoice (or flat ledger) has an explicit action button:
     👉 **`[ Re-Generate / Recalculate Maintenance ]`**
   * **What this button does**:
     * Recalculates the exact late fee and latest dues up to the current date.
     * **Permanently fixes / locks** the updated late fee and final payable amount directly in the database (`invoices.late_fee_amount`, `invoices.total_amount`).
     * Updates the flat ledger with the finalized, audited figure before issuing formal demand notices or exporting accounts.

---

### Step 4.5: Society Expense Tracking & Transparency Ledger
* Accountants log daily operational expenses (Elevator AMC, Security Agency Invoice, Generator Diesel, Gardening supplies).
* Invoices and receipts attached to each entry.
* Committee President/Secretary approves expenses $\rightarrow$ Transparent real-time financial report generated for all residents.

---

## Phase 5: Grievance & Complaint Helpdesk Flow (With Forwarding & Assignment)

### Step 5.1: Complaint Submission (Any Society Member)
* **Who Can Lodge a Complaint**: Any registered and approved user can file a complaint:
  * **Flat Owners & Tenants**: Plumbing, Electrical, Lift, Water Leakage, Noise, Garbage.
  * **Security Guards**: Gate Equipment failure, Boom barrier malfunction, CCTV camera offline.
  * **Executive Committee Members**: Society infrastructure issues, vendor contract default.
* **Payload**: Category, Priority (`low`, `medium`, `high`, `urgent`), Description, Photos/Videos upload.

---

### Step 5.2: Shared Committee Visibility & Action Routing

> 📊 *Visual Routing Diagram*: See [SYSTEM_DIAGRAMS.md - Grievance Routing & Committee Forwarding](file:///c:/society_management_backend/SYSTEM_DIAGRAMS.md#7-phase-5-grievance-routing--committee-forwarding).

---

### Step 5.3: Complaint Actions & Forwarding Controls

1. **Shared Inbox**:
   * **Both the President and all Executive Committee Members** see all active complaints.
   * Filter Tabs: `All`, `Unassigned`, `Assigned to Me`, `In-Progress`, `Resolved`.

2. **The "Forward / Assign" Action Button**:
   In both President and Executive Member logins, every complaint ticket has a **"Forward / Assign"** action:
   * **Option 1: Forward to a Specific Executive Member**:
     * Example: Secretary forwards a water pressure issue directly to the *Water Committee Executive Member*.
   * **Option 2: Forward / Escalate to the President**:
     * Example: Executive member forwards a severe structural/legal violation directly to the *President*.
   * **Option 3: Assign to Maintenance Staff / External Vendor**:
     * Example: Assigns plumbing leak to the society's on-call *Plumber*.
   * **Option 4: Self-Assign**:
     * The logged-in Executive Member or President takes personal responsibility for resolving the ticket.

3. **Audit Trail & Internal Notes**:
   * Every forward action logs an immutable event:
     * *"Forwarded by Secretary John to Executive Member David on Oct 26 at 5:15 PM with note: 'Please inspect the 4th floor pump.' "*
   * The resident sees real-time status updates: *"Your complaint has been assigned to [Name]"*.

4. **Resolution & Feedback**:
   * Assigned person updates status $\rightarrow$ `'Resolved'` with proof photo.
   * Resident verifies work $\rightarrow$ Submits 1-to-5 star rating and feedback. Ticket is marked `'Closed'`.
   * If unsatisfied, the resident can click **"Reopen Ticket"**.

---

## Phase 6: Amenity & Facility Booking Flow

1. **Browse**: Resident views list of bookable amenities (Clubhouse, Tennis Court, Banquet Hall, Party Lawn).
2. **Slot Selection**: Resident selects date and available time slot (prevents double-booking collisions).
3. **Pricing & Rules**:
   * System calculates Hourly/Event fee + refundable Security Deposit.
4. **Confirmation**:
   * Auto-debits or appends booking charge to the resident's monthly flat maintenance ledger.
   * Booking confirmed; gate security notified for guest arrival coordination.

---

## Phase 7: Emergency SOS & Community Safety Flow

> 📊 *Visual Safety Diagram*: See [SYSTEM_DIAGRAMS.md - Emergency SOS & Community Safety](file:///c:/society_management_backend/SYSTEM_DIAGRAMS.md#8-phase-7-emergency-sos--community-safety).

* **SOS Triggers**: Fire, Medical Emergency, Lift Stuck, Security Threat.
* **Medical Network**: Opt-in registry of verified doctors and first responders in the society notified instantly.

