# Society Resident (Owner & Tenant) System — Workflow & Feature Specification

---

## 1. Executive Overview & User Roles Scope

The **Society Resident System** defines the functional capabilities, workflows, and operational rules governing residents living within a residential society, gated community, or apartment complex. The system caters to two distinct resident personas:

1. **Flat Owner**: Holds legal title to the residential unit. Possesses full administrative rights over the flat, including property document management, tenant onboarding authorization, major structural renovation requests, and statutory maintenance ledger responsibility.
2. **Tenant**: A legal lessee residing in the flat under an active rental agreement. Possesses operational access for daily living: gate visitor approvals, domestic staff management, helpdesk grievance submissions, amenity bookings, and maintenance payments (when delegated by the landlord).

### Core Operational Principles
* **Admin-Provisioned Credentialing**: To guarantee community safety, residents cannot publicly self-register without administrative verification. Accounts are provisioned exclusively by the Society Admin / President.
* **Streamlined Authentication**: Mobile-first login via registered phone number verified through Password or 6-digit SMS OTP.
* **Multi-Flat / Multi-Society Context Switching**: A single mobile number can be linked to multiple units (e.g., an owner owning multiple flats, or an individual who is an owner in one society and a tenant in another) with instant in-app profile switching.
* **Real-Time Gate & Security Synchronization**: Immediate two-way communication with security gate guards for visitor approvals, pre-approved entry passes, and domestic staff tracking.
* **Transparent Accounting & Dynamic Late Fees**: Real-time access to itemized maintenance demand invoices, dynamic late fee calculation, multiple payment options, and instant official digital receipts.

---

## 2. End-to-End Resident User Journey Pipeline

```
┌────────────────────────────────────────────────────────────────────────────────────────┐
│                     RESIDENT USER (OWNER & TENANT) WORKFLOW PIPELINE                   │
└────────────────────────────────────────────────────────────────────────────────────────┘
                                            │
                               [Account Onboarding & Login]
                               • Admin-Provisioned Phone Number
                               • Authentication via Password or SMS OTP
                               • Multi-Flat / Role Switcher (if applicable)
                                            │
                                            ▼
                               [User Account & Household Hub]
                               • Personal Profile & Security Settings
                               • Family Members Management
                               • Vehicle Registration & Parking Bay Mapping
                               • Domestic Helpers Registry & Verification
                               • Digital Document Vault
                                            │
       ┌────────────────────┬───────────────┴───────────────┬────────────────────┐
       ▼                    ▼                               ▼                    ▼
[Gate & Visitors]     [Financial Management]       [Helpdesk Grievance]   [Amenities & Society]
 • Live Approval/Deny  • Itemized Monthly Demands   • Lodge Plumbing/Lift  • Browse & Book Slots
 • Pre-Approved Pass   • Dynamic Late Fee Recalc    • Photo/Video Evidence • Anti-Collision Engine
 • Cab & Parcel Drop   • Gateway / Direct Bank Pay  • Work Order Tracking  • Circulars & Notices
 • Helper Entry Alerts • Real-Time Passbook Ledger  • Verify & Star Rating • Community Directory
 • Visitor History Log • Official Signed Receipts   • Reopen Unresolved    • 1-Tap Emergency SOS
```

---

## 3. Module 1: Authentication, Role Matrix & Context Switching

### 3.1. Authentication Workflow
1. **Registered Mobile Number Entry**: Resident enters registered 10-digit mobile number (`+91` format).
2. **Verification Options**:
   * **Password**: User enters personal alphanumeric password.
   * **SMS OTP**: System sends a secure, time-bound 6-digit OTP to the registered mobile number.
3. **Session Initialization**:
   * Validates active account status (`status = active`).
   * Binds user token to the current mobile device.
   * Prompts biometric or MPIN lock setup for rapid subsequent app access.

### 3.2. Multi-Flat & Multi-Society Context Switching
If a mobile number is associated with more than one profile:
* **Single Role Account**: Directly opens the resident dashboard for the assigned flat.
* **Multiple Role Account**: Prompts the user to select their active session context:
  * *Example: "Owner — Flat A-102 (Green Valley)" vs "Tenant — Flat C-304 (Skyline Towers)"*.
* **In-App Switcher**: User can switch between flats or societies at any time from the account menu without logging out.

### 3.3. Owner vs. Tenant Permission & Capability Matrix

| System Capability | Flat Owner (Resident / Landlord) | Tenant (Resident Lessee) |
| :--- | :---: | :---: |
| **Login & Profile Management** | Yes | Yes |
| **Manage Family Members** | Yes | Yes |
| **Register Household Vehicles** | Yes | Yes (Subject to bay quota) |
| **Manage Domestic Staff (Maid, Cook)** | Yes | Yes |
| **Gate Visitor Approvals (Live & Pre-Approved)** | Yes | Yes |
| **Emergency SOS Trigger** | Yes | Yes |
| **Lodge Maintenance Grievances** | Yes | Yes |
| **Amenity Booking (Clubhouse, Court)** | Yes | Yes |
| **View Society Circulars & Notices** | Yes | Yes |
| **Structural Renovation Applications** | Yes | No (Requires Owner Approval) |
| **Tenant Onboarding / Lease Upload** | Yes | No (Owner Action) |
| **Maintenance Bill Payment** | Yes (Primary Obligation) | Optional (If delegated) |
| **Full Property Document Vault** | Yes (Title Deed, Possession) | Limited (Rent Agreement only) |

---

## 4. Module 2: Resident Account Hub & Household Management ("My Account")

### 4.1. Personal Profile & Security
* **User Profile**: Full Name, Verified Mobile Number, Email Address, Profile Photo.
* **Security Controls**: Password change, biometric authentication toggle, active device sessions viewer with remote logout.
* **Notification Preferences**: Granular toggle for Push Notifications, WhatsApp messages, and SMS alerts.

### 4.2. Family Members Management
* Flat occupants can add family members residing in the same unit (Spouse, Children, Elderly Parents).
* **Parameters**: Full Name, Relationship, Contact Number, Optional Independent App Access.
* Family members with independent app access receive simultaneous gate visitor alerts and can independently book society amenities.

### 4.3. Vehicle Registry & Parking Slot Mapping
* Residents register all household vehicles to ensure authorized gate entry and parking slot compliance.
* **Vehicle Data**:
  * Vehicle Type: `2-Wheeler`, `4-Wheeler`, `Electric Vehicle (EV)`.
  * License Plate Number (e.g., `MH-12-AB-1234`).
  * Make, Model, and Vehicle Color.
  * Assigned Parking Bay / Slot Number (e.g., `Bay #B2-44`).
* **Gate Sync**: Security guards at the gate can instantly verify authorized resident vehicles via license plate lookup.

### 4.4. Domestic Staff Management
* Residents link recurring personal service staff to their flat (Maids, Cooks, Drivers, Nannies, Cleaners).
* **Staff Profile**: Full Name, Mobile Number, Service Role, Photo ID, Police Verification status.


### 4.5. Digital Document Vault
* Secure repository for flat-specific and residency documents:
  * **For Owners**: Possession Letter, Share Certificate, Unit Floor Plan, Property Tax Receipts, Maintenance No-Objection Certificates (NOC).
  * **For Tenants**: Registered Rent Agreement, Police Verification Certificate, Landlord NOC.

### 4.6. Emergency Medical Badge & Volunteer Registry
* Residents can optionally enroll in the society's internal medical first-responder network.
* **Badge Categories**: *Doctor*, *Nurse*, *Paramedic*, *CPR-Trained Volunteer*.
* Enrolled residents receive prioritized high-urgency notifications if a neighbor triggers a Medical Emergency SOS.

---

## 5. Module 3: Gate Security & Visitor Interaction Features

### 5.1. Live Walk-In Visitor Approvals
1. Visitor arrives at the gate and informs the guard of the target flat (e.g., `A-302`).
2. Security guard inputs visitor details and takes a live photo at the gate.
3. System triggers an immediate high-priority push notification to all registered flat occupants:
   * Displays Visitor Name, Mobile Number, Purpose (`Guest`, `Delivery`, `Cab`, `Service`), Vehicle Number, and Live Photo.
4. **Resident Decision**:
   * **Approve**: Gate guard console displays instant green authorization; barrier opens.
   * **Deny**: Gate guard console displays red rejection; entry is prohibited.
   * **Leave at Gate / Security**: Instructs delivery agent to deposit parcel at the gate drop box.

### 5.2. Pre-Approved Visitor Passes
* Residents can generate digital gate passes in advance for expected guests, party attendees, or service contractors.
* **Pass Parameters**: Guest Name, Contact Number, Expected Arrival Date & Time Window, Vehicle Number (optional).
* **Verification Method**: System generates a unique 4-to-6 digit numeric Passcode and QR Code.
* **Gate Entry**: Guest shares the Passcode or displays the QR code at the gate. Guard verifies the code on the guard app for instant, zero-delay entry without calling the resident.

### 5.3. Delivery & Cab Approvals
* **Quick Delivery Pass**: 1-tap pre-authorization for popular delivery services (Amazon, Flipkart, Swiggy, Zomato, Blinkit).
* **Cab Entry Clearance**: Resident inputs cab company and incoming vehicle license plate for automatic gate entry clearance.

### 5.4. Gate Logbook & Audit Trail
* Searchable history of all visitors, deliveries, cabs, and service staff that have entered the resident's flat.
* Log parameters include visitor photo, check-in timestamp, check-out timestamp, and gate number used.

---

## 6. Module 4: Maintenance Demands, Dynamic Late Fees & In-App Payments

### 6.1. Itemized Maintenance Demand Generation
* Automated demand generated on the 1st of every calendar month.
* **Itemized Billing Structure**:
  * Base Maintenance Charges (Common Area Electricity, Security, Housekeeping).
  * Sinking / Reserve Fund contribution.
  * Individual Water Meter / Sub-meter Electricity consumption charges.
  * Assigned Reserved Parking Bay charges.
  * Non-Occupancy Charges (applicable to rented flats as per society bylaws).
  * Previous outstanding arrears.

### 6.2. Real-Time Dynamic Late Fee Calculation
* **On-the-Fly Penalty Computation**: When a resident opens their pending bill after the monthly grace period (e.g., after the 15th of the month), the backend dynamically calculates late fees in real time.
* **Calculation Engine**:
  * If `current_date <= due_date`: Late Fee = ₹0.
  * If `current_date > due_date`: Calculates penalty based on overdue days using society rules (fixed charge or compounding monthly percentage interest).
* **Resident Transparency**: Displays live itemized formula:
  $$\text{Total Payable} = \text{Base Demand} + \text{Dynamically Calculated Late Fee}$$

### 6.3. Payment Execution Methods
Residents can settle dues through two payment pathways:

#### Pathway 1: Instant In-App Online Payment (Gateway)
* Supported modes: UPI (Google Pay, PhonePe, Paytm), NetBanking, Credit Cards, Debit Cards.
* **Automated Processing**: Webhook triggers instant verification $\rightarrow$ bill marked `PAID` $\rightarrow$ credit entry posted to flat ledger $\rightarrow$ instant official digital receipt issued.

#### Pathway 2: Direct Bank Transfer (NEFT / RTGS / IMPS / UPI) with Screenshot
* Display of Society Bank Account details: Bank Name, Beneficiary Name, Account Number, IFSC Code, and Dynamic Society UPI QR Code.
* Resident executes transfer via their external banking application.
* Resident returns to app and inputs:
  * 12-digit Bank Transaction Reference Number (UTR).
  * Transfer Date and Amount Paid.
  * Upload of Payment Confirmation Screenshot / Receipt.
* System marks invoice status as `Under Verification` until reconciled by society administration.

### 6.4. Flat Financial Ledger & Passbook
* Complete statement of account for the flat.
* Historical ledger reflecting all debit entries (monthly demands, penalty charges, amenity fees) and credit entries (payments received).
* Displays live net outstanding balance or advance credit balance.

### 6.5. Official Digital Receipts & Invoices
* Every successful payment generates a digitally signed PDF receipt available for instant download.
* Receipt parameters: Unique Receipt Number, Society Registration Details, Flat Number, Payer Name, Payment Mode, Bank UTR / Gateway ID, Digital Signature, and Verification QR Code.

---

## 7. Module 5: Helpdesk, Grievances & Work Order Tracking

### 7.1. Complaint Lodging Workflow
Residents can report personal flat issues or common society infrastructure defects directly to the management committee.
1. **Category Selection**:
   * Personal Flat: Plumbing, Electrical, Water Seepage, Intercom Malfunction.
   * Common Area: Elevator Breakdown, Hallway Lighting, Garbage Disposal, Stray Animal Issue, Noise Disturbance.
2. **Issue Details**:
   * Descriptive title and detailed explanation.
   * Priority Selection: `Low`, `Medium`, `High`, `Urgent`.
   * Media Evidence: Upload up to 3 photos or short video clips showing the issue.
3. **Preferred Inspection Slot**: Date and time window when the resident is available at home.

### 7.2. Ticket Lifecycle & Real-Time Tracking
* Tickets progress through a transparent lifecycle:
  `Logged / Unassigned` ➔ `Assigned to Staff / Work Order Issued` ➔ `In Progress` ➔ `Resolved` ➔ `Verified & Closed`.
* **Assignment Details**: Resident sees assigned technician name, contact number, and estimated arrival time.
* **Internal Chat & Updates**: Resident and assigned committee member/staff can post real-time updates and notes within the ticket.

### 7.3. Resolution Verification, Rating & Reopening
* When the technician completes the work, the status updates to `Resolved`.
* Resident receives an automated confirmation prompt:
  * **Option A - Verify & Rate**: Resident verifies the repair, submits a 1-to-5 star quality rating with feedback, and the ticket is marked `Closed`.
  * **Option B - Reopen Ticket**: If the repair is incomplete or defective, resident taps `Reopen Ticket` with remarks, escalating the ticket back to the Hon. Secretary.

### 7.4. Flat Renovation Approvals
* Flat Owners must submit structural renovation or interior modification requests before beginning major work.
* **Submission Requirements**: Proposed work description, contractor details, expected start/end dates, debris disposal plan, and architect approval document.
* Committee reviews and issues formal digital approval with renovation guidelines and security gate worker pass.

---

## 8. Module 6: Facility & Amenity Booking Engine

### 8.1. Facility Catalog & Availability
Residents can browse and reserve society amenities:
* Clubhouse Banquet Hall, Party Lawn, Tennis Court, Badminton Court, Swimming Pool, Guest Suites, BBQ Pavilion.
* Displays facility rules, operating hours, hourly/event rates, and maximum guest capacity.

### 8.2. Anti-Collision Slot Booking Engine
1. Resident selects facility, desired date, and specific time slot.
2. **Collision Validation**:
   * System verifies that no overlapping reservation exists for the same slot.
   * Enforces mandatory cleaning/maintenance buffer periods between consecutive bookings.
   * Checks resident eligibility (e.g., restricts bookings if maintenance dues are overdue beyond 60 days).
3. **Pricing & Deposit Calculation**:
   * Calculates facility rental fee + refundable security deposit.
   * Fees can be paid online immediately or appended to the resident's monthly maintenance ledger.

### 8.3. Booking Confirmation & Gate Coordination
* Once confirmed, the system generates a Digital Amenity Pass.
* Details sync automatically to Gate Security to facilitate guest parking and entry clearance on the event day.
* **Cancellation Workflow**: Residents can cancel bookings up to a predefined cutoff window; refunds are automatically credited back to the flat's ledger.

---

## 9. Module 7: Community Engagement & Marketplace

### 9.1. Society Notices & Circulars
* Official digital notice board categorized by importance:
  * `General Announcement`, `Maintenance & Water Shutdown`, `AGM / EGM Notice`, `Rules & Bylaws`.
* Supports attached PDF documents and circular images.
* Push notification alerts residents to critical or urgent announcements.

### 9.2. Resident Community Marketplace (Classifieds)
* Verified, private internal marketplace exclusive to society residents:
  * **Buy & Sell**: Household furniture, appliances, electronics, children's toys.
  * **Property Rentals**: Owners listing flats for rent or sale directly without middleman brokerage.
  * **Parking Space Swap / Rent**: Temporary leasing of unused parking bays between neighbors.
  * **Local Services**: Resident-recommended tutors, yoga instructors, bakers, car cleaners.

### 9.3. Resident Directory & Committee Contacts
* Searchable directory of neighbors (with privacy controls allowing residents to hide personal contact numbers while displaying flat numbers).
* Direct contact directory for Executive Committee members (President, Secretary, Treasurer, Civil Lead, Security Supervisor).

---

## 10. Module 8: Emergency SOS & Community Safety Network

### 10.1. 1-Tap Emergency SOS Trigger
Residents can trigger an immediate emergency alert from their mobile app for four critical crisis categories:
1. **Medical Emergency**: Sudden illness, heart attack, injury.
2. **Fire Emergency**: Fire outbreak in the unit or corridor.
3. **Lift Breakdown**: Stuck inside a stalled elevator.
4. **Security Threat / Intrusion**: Physical threat, burglary, harassment.

### 10.2. Automated Crisis Response Workflow
```
[Resident Triggers SOS]
          │
          ├───────────────────────────────────────────────────────┐
          ▼                                                       ▼
[Security Gate Alarm Activated]                       [Community Network Broadcast]
  • Sounds physical siren at main gate                  • High-priority emergency alert to
  • Guard app flashes unit location (e.g., Flat B-402)    all residents in same building
  • Guards dispatched immediately to flat               • Dispatches notification to registered
                                                          Society Doctors & First Responders
```

### 10.3. Emergency Directory Speed-Dial
Direct one-tap dialing for critical local emergency services:
* Society Main Security Gate Intercom.
* Society Resident Doctor / First Responder.
* Local Police Control Room.
* Local Fire Station.
* Nearest Hospital & Ambulance Service.
* Elevator AMC Breakdown Helpline.

---

## 11. Module 9: Resident Real-Time Notification Matrix

| Event Trigger | Delivery Channel | Notification Message Preview | Resident Action / Result |
| :--- | :--- | :--- | :--- |
| **Walk-In Visitor at Gate** | Push Notification | *"Visitor Rahul Sharma has arrived at Gate 1 with live photo. Tap to approve."* | Taps `Approve`, `Deny`, or `Leave at Gate` |
| **Parcel Deposited at Gate** | Push Notification | *"Delivery agent left a parcel at the security gate for Flat A-302."* | Collects parcel from gate using secure delivery code |
| **Domestic Helper Checked In** | Push Notification | *"Your maid Sunita checked in at Gate 1 at 08:15 AM."* | Alerts resident of domestic staff arrival |
| **Maintenance Demand Issued** | Push, WhatsApp & SMS | *"Maintenance bill of ₹4,200 generated for Oct. Due date: Oct 15."* | Reviews itemized invoice and initiates payment |
| **Late Fee Dynamic Run** | WhatsApp & Push | *"Reminder: Maintenance bill for Flat A-302 is overdue. Current payable: ₹4,400."* | Views dynamically calculated late fee and pays dues |
| **Payment Confirmed** | Push & WhatsApp | *"Payment of ₹4,200 received! Download your official receipt #REC-8921."* | Downloads digitally signed PDF receipt with QR code |
| **Complaint Status Updated** | Push Notification | *"Plumber Ramesh has been assigned to your ticket #TKT-408. Expected: 03:00 PM."* | Tracks work order and contacts assigned staff |
| **Complaint Resolved** | Push Notification | *"Ticket #TKT-408 marked Resolved. Please verify and submit your star rating."* | Verifies work and rates service (1–5 stars) |
| **Emergency SOS in Building** | High-Priority Siren Push | *"🚨 EMERGENCY: Medical SOS triggered in Flat B-402. First responders alerted."* | Alerts nearby neighbors and community doctors |
| **Official Society Notice** | Push & SMS | *"📢 Notice: Scheduled Water Supply Shutdown tomorrow from 10 AM to 2 PM."* | Views complete notice text and attached PDF |
