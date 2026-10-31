# Society Executive Committee Member — System Workflow Specification

---

## 1. Executive Overview & Role Scope

The **Executive Committee Member** is an authorized representative appointed by society executive leadership (President / Secretary) to oversee specific functional portfolios within the residential society (such as Civil Infrastructure, Water Supply, Electrical & Elevators, Gardening, or Security Liaison).

### Key Role Responsibilities
* **User Directory Oversight**: Browse society user listings and view detailed profiles of flat owners, tenants, security guards, and maintenance staff.
* **Grievance Resolution & Dispatch**: Review resident helpdesk complaints, take ownership of issues within their functional portfolio, coordinate with maintenance technicians, and resolve tickets.
* **Dual-Persona Operation**: Seamlessly switch between personal Flat Owner profile and Executive Committee administrative view using a single login.

---

## 2. Executive Committee Member Workflow Pipeline

```
┌────────────────────────────────────────────────────────────────────────────────────────┐
│               EXECUTIVE COMMITTEE MEMBER OPERATIONAL WORKFLOW PIPELINE                 │
└────────────────────────────────────────────────────────────────────────────────────────┘
                                            │
                              [Authentication & Role Login]
                               • Registered Mobile Number (+91)
                               • Password or 6-Digit SMS OTP
                               • Role Context Switcher (Owner vs Committee)
                                            │
                                            ▼
                           [Committee Operational Dashboard]
                               • Portfolio Grievance Summary (Active / Pending)
                               • Quick Access to User Directory
                               • Society Operational Notices
                                            │
                     ┌──────────────────────┴──────────────────────┐
                     ▼                                             ▼
          [User Directory Module]                       [Helpdesk Dispatch Module]
           • List Society Users                          • Shared Committee Inbox
           • Filter: Owners, Tenants, Staff              • Portfolio-Specific Tickets
           • Search by Flat / Name / Phone               • 4 Resolution Actions:
           • View Full User Profile Details:               - Self-Assign
             - Contact & Flat Mapping                      - Forward to Committee Lead
             - Occupancy & Role Status                     - Escalate to President
             - Registered Vehicles & Bays                  - Assign Maintenance Staff
             - Emergency Contact Info                    • Mark Resolved & Log Notes
```

---

## 3. Module 1: Authentication & Role Context Switching

### 3.1. Authentication Workflow
1. **Registered Mobile Number Entry**: Member enters registered 10-digit mobile number (`+91` format).
2. **Verification Options**:
   * **Password**: Secure alphanumeric password.
   * **SMS OTP**: 6-digit one-time password sent to registered mobile.
3. **Role Context Selection**:
   * Because committee members are typically flat owners residing in the society, the system provides a profile selection prompt:
     * *Profile A: Flat Owner (Personal flat dues, visitors, personal household)*
     * *Profile B: Executive Committee Member (Society governance and helpdesk)*
   * Members can switch between profiles at any time without logging out.

---

## 4. Module 2: User Directory — List & View User Details

### 4.1. Society User List
The Executive Committee Member has access to the society user directory to inspect resident and staff records:
* **Directory Categorization**:
  * **Flat Owners**: Resident and non-resident property owners.
  * **Tenants**: Active lessees currently residing in flats.
  * **Committee Members**: Fellow executive committee members and designation leads.
  * **Security Guards**: Active gate personnel.
  * **Maintenance Staff**: Society technicians (Plumbers, Electricians, Housekeeping).
* **Directory Search & Filters**:
  * Search by Resident Full Name.
  * Search by Flat Number (e.g., `A-302`, `B-104`).
  * Search by Registered Mobile Number.

### 4.2. View User Details
Selecting any user in the directory opens their comprehensive profile information:
* **Personal & Contact Details**: Full Name, Verified Mobile Number, Email Address, and Role/Designation.
* **Unit Mapping**: Wing / Tower and Flat Number.
* **Occupancy Status**: Owner-occupied, Tenant-occupied, or Staff deployment status.
* **Authorized Vehicle Details**:
  * Vehicle Type: 2-Wheeler, 4-Wheeler, EV.
  * License Plate Number (e.g., `MH-12-AB-1234`).
  * Assigned Parking Bay / Slot Number (e.g., `Bay #14`).
* **Emergency Contact Details**: Primary emergency contact name, relationship, and phone number.

---

## 5. Module 3: Grievance Handling & Helpdesk Dispatch Workflow

### 5.1. Committee Ticket Queue
* Committee members access a shared ticket queue with filtered views:
  * **Assigned to Me**: Tickets directly assigned to the member's functional portfolio.
  * **Unassigned / Urgent**: New resident grievances awaiting committee dispatch.
  * **In Progress**: Work orders currently being serviced by staff.
  * **Resolved & Closed**: Verified and completed tickets.
* **Ticket Inspection Details**:
  * Unique Ticket Reference ID.
  * Reporting Flat Number and Resident Name.
  * Issue Category (Plumbing, Electrical, Civil/Structural, Lift, Security, Common Amenities).
  * Priority Level (`Low`, `Medium`, `High`, `Urgent`).
  * Resident Description and Attached Photo/Video Media.

### 5.2. Resolution & Dispatch Actions
For any assigned or open ticket, the Executive Committee Member can execute one of five actions:
1. **Self-Assign**: Member assumes personal responsibility for supervising the inspection and resolution.
2. **Forward to Committee Lead**: Re-routes ticket ownership to a colleague leading a different portfolio (e.g., reassigning an electrical surge ticket from Civil Lead to Electrical Lead).
3. **Escalate to President**: Routes high-cost structural damages, legal disputes, or vendor contract violations directly to the President.
4. **Assign Maintenance Staff / Issue Work Order**:
   * Selects an authorized society maintenance technician (e.g., Society Plumber or Electrician).
   * Dispatches an automated work order notification containing flat details, resident contact info, and issue description.
5. **Mark Resolved**:
   * Updates status to `Resolved` upon completion of work.
   * Records resolution remarks and completion notes.
   * Triggers an automated verification prompt to the resident for confirmation and 1-to-5 star service rating.

---

## 6. Module 4: Real-Time Notification Triggers

| Event Trigger | Delivery Channel | Notification Summary | Member Action Required |
| :--- | :--- | :--- | :--- |
| **New Grievance Assigned to Member** | Push Notification | *"Ticket #TKT-412 (Water Leakage in A-302) assigned to you."* | Opens ticket to inspect and issue work order |
| **Staff Updates Work Order** | Push Notification | *"Plumber Ramesh updated status to 'Work in Progress' on #TKT-412."* | Monitors ongoing maintenance progress |
| **Emergency SOS in Building** | High-Priority Push Alert | *"🚨 EMERGENCY: Lift Breakdown reported in Tower B."* | Coordinates immediate emergency response |
| **Society Notice Published** | Push Notification & SMS | *"📢 Notice: Management Committee Meeting on Sunday at 10 AM."* | Reviews official circular and agenda |
