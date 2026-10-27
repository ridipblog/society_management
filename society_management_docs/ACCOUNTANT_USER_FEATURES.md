# Society Accountant & Financial Accounts System — Workflow & Feature Specification

---

## 1. Executive Overview & System Scope

The **Society Accountant & Financial Accounts System** governs the complete financial lifecycle, accounting ledgers, billing operations, and audit compliance for residential societies, apartment complexes, and gated communities. The system is designed for the **Society Accountant**, **Treasurer**, and authorized **Finance Officers**, operating under the oversight of the Executive Committee (Hon. Secretary and President).

### Core Operational Principles
* **Double-Entry Financial Integrity**: Every financial transaction (maintenance collection, vendor payout, late fee adjustment, bank charge) strictly conforms to standard accounting practices with real-time General Ledger and Flat Passbook updates.
* **Dual-Track Payment Reconciliation**:
  * *Automated Gateway Stream*: Webhook-driven instant clearing for digital payments (UPI, NetBanking, Cards).
  * *Manual Verification Queue*: Accountant verification for direct NEFT/RTGS bank transfers (UTR matching) and offline cash/cheque deposits.
* **Dynamic Late Fee Calculation & Lock Engine**: Automated real-time penalty calculations when residents fetch dues, coupled with an explicit administrative recalculation action to lock finalized penalties into the database.
---

## 2. End-to-End Accounting Workflow Pipeline

```
┌────────────────────────────────────────────────────────────────────────────────────────┐
│                   ACCOUNTANT & FINANCIAL OPERATIONS WORKFLOW PIPELINE                  │
└────────────────────────────────────────────────────────────────────────────────────────┘
                                            │
                              [Authentication & Role Validation]
                               • Registered Mobile Number (+91)
                               • Password or 6-Digit SMS OTP
                               • Financial Session & Ledger Permissions
                                            │
                                            ▼
                           [Financial Overview & Cashbook Status]
                               • Realized Collections vs Outstanding Arrears
                               • Society Bank Balance & Collection Health
                               • Pending Verification Queue (UTR / Cheques)
                                            │
       ┌────────────────────┬───────────────┴───────────────┬────────────────────┐
       ▼                    ▼                               ▼                    ▼
[Demand Invoicing]    [Online Reconciliation]     [Offline Logging]     [Late Fees & Recovery]
 • Monthly Billing Run • Instant Gateway Webhook   • Cheque Deposit Log  • Real-Time Dynamic Run
 • Area / Fixed Rates  • Bank UTR Verification     • Cheque Clear/Bounce • Recalculate & Lock
 • Due Date Scheduling • Payment Audit Status      • Cash Receipt Entry  • Aging Defaulters
 • Sinking & Reserve   • Digital Receipt Issuance  • Flat Ledger Credit  • WhatsApp/SMS Reminder
```

---

## 3. Module 1: Authentication, Permissions & Session Security

### 3.1. Authentication Workflow
1. **Registered Mobile Number Entry**: Accountant enters registered 10-digit mobile number (`+91` format).
2. **Verification Modes**:
   * **Password**: Secure alphanumeric password.
   * **SMS OTP**: 6-digit one-time password dispatched to registered mobile.
3. **Session & Permission Allocation**:
   * Validates active assignment of financial permissions (`role:accountant` or `role:treasurer`).
   * Initializes access to society chart of accounts, bank ledgers, and billing engines.
4. **Security & Audit Controls**:
   * Inactivity timeout with automatic session termination.
   * Mandatory audit trail stamping (User ID, IP address, timestamp) on every debit/credit voucher, adjustment entry, and bill revision.

---

## 4. Module 2: Maintenance Billing & Demand Generation

### 4.1. Monthly Billing Demand Generation
* **Automated Scheduler**: Generates itemized monthly demands automatically on the 1st of every calendar month.
* **Configurable Society Calculation Models**:
  * **Fixed Rate Model**: Uniform base charge per flat (e.g., ₹3,500/month).
  * **Area-Based Model**: Calculated per square foot (e.g., 1,250 sq.ft. × ₹3.00/sq.ft. = ₹3,750).
  * **Hybrid Model**: Fixed base maintenance + individual meter readings.

### 4.2. Demand Scheduling & Due Dates
* Demands scheduled with predefined payment due dates (e.g., 15th of each month).


---

## 5. Module 3: Payment Verification & Offline Logging

### 5.1. Automated Gateway Payment Reconciliation
* Transactions processed via integrated online gateways (UPI, Cards, NetBanking) are captured through automated server webhooks.

### 5.2. Direct Bank Transfer (NEFT / RTGS / IMPS) Verification Queue
For residents transferring funds directly to the society bank account:
1. **Verification Queue Entry**: Displays resident flat number, transfer amount, payment date, 12-digit UTR reference number, and uploaded proof screenshot.
2. **Bank Statement Matching**: Accountant matches UTR reference against incoming credits in the society's bank statement.
3. **Action - Approve**:
   * Invoice marked `PAID`.
   * Credit transaction posted to resident's individual flat passbook.
   * Debit transaction posted to Society Main Bank Account ledger.
   * Generates official digital receipt and dispatches confirmation notification to resident.
4. **Action - Reject**:
   * Accountant records mandatory rejection reason (e.g., *"UTR not found on statement"*, *"Amount mismatch"*).
   * Status updates to `Rejected`; resident notified immediately to resubmit valid transaction details without creating duplicate invoices.

### 5.3. Offline Payment Logging (Cash & Cheque Entry)
To support elderly or traditional residents, the Accountant manually records offline payments:
* **Cheque Payment Logging**:
  * Resident Flat Number & Payer Name.
  * Cheque Number, Cheque Date, Issuing Bank Name, Branch.
  * Amount (₹) and Target Invoice Allocation.
  * Cheque Lifecycle Tracking: `Received` ➔ `Deposited in Bank` ➔ `Cleared & Reconciled` (or `Bounced / Returned`).
  * If bounced: System automatically reverses credit, updates invoice to overdue, and applies standard cheque bounce penalty charges.
* **Cash Payment Logging**:
  * Cash amount received, date, receipt book reference number.
  * Generates immediate cash receipt and posts credit to flat ledger while debiting the Society Cash-in-Hand ledger.

---

## 6. Module 4: Dynamic Late Fee Management & Defaulter Recovery

### 6.1. Dynamic Late Fee Calculation on Fetch
* When an overdue resident opens their bill in the app, the system dynamically calculates the penalty in real time based on overdue days past the grace period.
* Calculation formulas:
  * **Fixed Penalty Model**: Fixed charge (e.g., ₹200) applied immediately after due date.
  * **Daily / Monthly Percentage Model**: Simple or compounding interest (e.g., 18% or 21% per annum calculated pro-rata).
* Eliminates the need for disruptive daily database penalty write scripts.

### 6.2. Administrative "Recalculate & Lock" Maintenance Action
* In the Accountant portal, every invoice and flat ledger includes the operational action:
  **`[ Re-Generate / Recalculate Late Fees ]`**.
* **Operational Workflow**:
  * Accountant initiates batch run for all overdue flats or for a specific unit.
  * System calculates exact penalty accrued up to the current timestamp.
  * **Permanently locks** the updated late fee and final payable amount directly into database tables (`invoices.late_fee_amount`, `invoices.total_amount`).
  * Updates flat ledger balance with the audited figure prior to issuing formal recovery notices or generating monthly financial exports.

### 6.3. Defaulter Recovery & Aging Analysis
* **Aging Buckets**: Automated grouping of unpaid dues:
  * Current (0–30 days).
  * Aging 31–60 days overdue.
  * Aging 61–90 days overdue.
  * Critical Defaulters (90+ days overdue).
* **Recovery Actions**:
  * **Automated Batch Reminders**: One-click dispatch of reminder notices via WhatsApp and SMS containing direct payment links.
  * **Formal Demand Notice Generation**: Generates official society letterhead demand notices with late fee breakdown and legal warning text.

---

## 7. Module 5: Real-Time Accounting Notification Matrix

| Event Trigger | Delivery Channel | Message Summary | Accountant Action / System Result |
| :--- | :--- | :--- | :--- |
| **New Online Payment Received** | In-App Alert & System Log | *"Flat A-302 paid ₹4,200 via UPI (Ref: UTR9823412093)."* | Auto-reconciled; ledger credited, receipt generated |
| **Bank Transfer Proof Uploaded** | In-App Task Queue | *"Flat B-104 submitted bank transfer proof for ₹3,800. UTR: 4092182019."* | Accountant verifies UTR against bank statement to approve |
| **Cheque Cleared / Deposited** | System Prompt | *"Cheque #004921 for Flat C-202 marked cleared by bank."* | Updates cheque status to cleared; finalizes ledger entry |
| **Cheque Bounced / Returned** | Push Notification & Alert | *"Cheque #004921 for Flat C-202 returned unpaid by bank."* | Reverses credit, applies bounce fee, notifies resident |
| **Late Fee Recalculate Run Completed** | In-App Confirmation Toast | *"Late fees recalculated and locked for 18 overdue flats."* | Updates active invoices and flat ledger balances |
| **Monthly Billing Run Ready** | Scheduled Prompt | *"Monthly maintenance demands for next month generated in draft."* | Accountant audits draft rates and authorizes release |
