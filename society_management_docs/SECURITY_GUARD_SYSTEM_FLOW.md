# Security Guard & Gate Management — System Workflow & Feature Specification

---

## 1. Executive Overview & Gate Security Architecture

The **Gate Security & Guard Management Module** is designed specifically for the on-duty security guard stationed at the society entry and exit gates. It runs on a touch-optimized, high-contrast tablet or desktop interface to ensure gate clearance times remain under **20 seconds per entry**, avoiding vehicle congestion at the gate barrier.

### Core Objectives:
* **Mandatory Entry Logging**: Recording entry details (Name, Mobile, Category, and Flat #) is strictly required for every visitor, vehicle, courier, and worker.
* **Search-First Rapid Clearance**: Guard searches by Mobile Number or Name first. If the person already exists, entry is marked with date/time in 1-click without re-entering details or re-taking photos.
* **New Visitor Registration**: If the person is not found, the guard captures their live photo, enters personal details, selects the target flat, and submits.
* **Automated Resident Push Notification**: The moment any entry is submitted, a real-time push notification is instantly delivered to the resident user app of that target flat number.
* **Live In-Premises Accountability**: Live, searchable tracking of every external person and vehicle currently inside the compound with one-tap exit logging.

---

## 2. Gate Operations Workflow Pipeline

```
┌──────────────────────────────────────────────────────────────────────────────────────────────────┐
│                             GATE SECURITY END-TO-END WORKFLOW PIPELINE                           │
└──────────────────────────────────────────────────────────────────────────────────────────────────┘
                               Visitor / Vehicle Arrives at Gate
                                              │
                                              ▼
                             [Step 1: Search by Mobile Number or Name]
                               • Guard types visitor 10-digit mobile number or name
                               • System queries society visitor database
                                              │
                    ┌─────────────────────────┴─────────────────────────┐
                    ▼                                                   ▼
         [Branch 1: Person FOUND]                            [Branch 2: Person NOT FOUND]
       (Returning Visitor / Exists)                            (New / First-Time Visitor)
                    │                                                   │
     • Existing profile & past photo retrieved           • Guard opens Gate Entry Form
     • Guard selects / confirms Target Flat #            • Guard takes Live Photo of person
     • 1-Click "Mark Entry"                              • Enters Person Details (Name, Phone, Vehicle #)
     • System records Date & Exact Timestamp             • Selects Target Wing & Flat #
                    │                                                   │
                    └─────────────────────────┬─────────────────────────┘
                                              │
                                              ▼
                                 [Step 2: Submit & Open Barrier]
                               • Gate barrier opens
                               • Entry record logged into "Inside Premises" live list
                               • Overstay turnaround timer initiated
                                              │
                                              ▼
                     [Step 3: Automated Push Notification to Flat User App]
                     • System triggers real-time push notification to resident app of that Flat No:
                       "🔔 Visitor Alert: [Visitor Name] has entered the gate for Flat [Flat #] at [Time]"
                                              │
                                              ▼
                                 [Step 4: Departure & Exit Logging]
                               • Guard searches by Vehicle # / Mobile / Name
                               • 1-Tap "Log Exit"
                               • Resident notified: "Visitor [Name] exited gate"
                               • Live inside premises counter decremented
```

---

## 3. Specialized Entry Types & Workflows

Gate security handles different categories of entrants with dedicated operational parameters:

### 3.1. Guest / Flat Visitor Entry Flow (Search-First Unified Flow)
* **Definition**: Friends, family members, or personal visitors entering the society.
* **Step 1: Guard Searches by Mobile Number or Name**:
  * Guard types the entrant's mobile number or name in the gate search bar.
* **Scenario A: If Record is FOUND (Returning Visitor)**:
  1. System displays previous visitor record with existing photo, name, and vehicle details.
  2. Guard selects / confirms the target **Flat #**.
  3. Guard clicks **`[ Mark Entry ]`**.
  4. System immediately stamps the entry with the **current Date & Time** without re-entering details or re-taking photos.
* **Scenario B: If Record is NOT FOUND (New Visitor)**:
  1. Guard opens the **New Entry Form**.
  2. Guard takes a live **Visitor Photo Snapshot** via the camera.
  3. Guard enters **Person Details** (**Full Name**, **Mobile Number**, and **Vehicle Registration #** if driving).
  4. Guard selects the target **Wing & Flat #**.
  5. Guard clicks **`[ Submit Entry ]`**.
* **Step 2: Automated Push Notification to Target Flat User App**:
  * Upon entry submission (for both Scenario A and Scenario B), an automated push alert is immediately dispatched to the resident user app of that Flat Number:
    * 📱 **Resident App Alert**: *"🔔 Visitor Entry: [Visitor Name] (+91 [Phone]) has entered the gate for your flat [Flat #] at [Date & Time]."*
  * Gate barrier opens immediately.

---

### 3.2. Cab & Taxi Entry Flow (Ola, Uber, Quick Rides)
* **Definition**: Commercial passenger vehicles dropping off or picking up residents.
* **Operational Goal**: Rapid gate turnaround time (< 10 seconds) to prevent road blockages.
* **Workflow**:
  1. Guard searches driver mobile number or vehicle plate (if returning cab, profile appears).
  2. If new cab: Guard selects **"Cab / Taxi"** category, enters Driver Name, Phone, and Vehicle License Plate (e.g. `MH-12-DE-4411`).
  3. Guard selects **Target Flat #**.
  4. Guard clicks **`[ Submit Cab Entry ]`**.
  5. **Automated Notification**: Push alert sent to target flat app: *"🚖 Your cab (MH-12-DE-4411) has entered the main gate."*

---

### 3.3. Delivery & Courier Entry Flow (Food & E-Commerce)
* **Definition**: Delivery personnel from Swiggy, Zomato, Blinkit, Zepto, Amazon, Flipkart, BlueDart, etc.
* **Workflow**:
  1. Guard searches delivery agent mobile number. If returning, driver profile loads instantly.
  2. If new delivery rider: Guard inputs Delivery Company (e.g. `Zomato`, `Swiggy`, `Amazon`) + Delivery Agent Name & Phone + Target Flat #.
  3. Guard clicks **`[ Submit Delivery Entry ]`**.
  4. **Automated Notification**: Push alert sent to flat app: *"📦 Swiggy delivery executive has entered the gate for your flat."*

---

### 3.4. Maintenance & Service Worker Entry Flow
* **Definition**: Electricians, plumbers, carpenters, appliance technicians, and Urban Company service partners.
* **Workflow**:
  1. Guard searches worker mobile number (if returning, previous profile and photo load automatically).
  2. If new worker: Guard takes live photo, logs Full Name, Mobile Number, Service Company (e.g. `Urban Company`, `Independent`), and target **Flat #**.
  3. **Remarks / Notes**: Guard adds optional remarks text (e.g. *"AC repair work"*, *"Carrying drill machine & tool kit"*).
  4. Guard clicks **`[ Submit Worker Entry ]`**.
  5. **Automated Notification**: Push alert sent to target flat app: *"🛠️ Service Worker [Worker Name] has entered the gate for your flat (Remarks: [Remarks text])."*

---

### 3.5. Daily Domestic Staff Entry Flow
* **Definition**: Daily maids, cooks, chauffeurs, and nannies working across one or more society flats.
* **Workflow**:
  1. Guard searches staff member by **Mobile Number** or **Name**.
* **Scenario A: If Existing Details Exist (Staff Found)**:
  1. System retrieves existing profile photo, name, and mapped working flat(s).
  2. Guard clicks **`[ Mark Entry ]`**.
  3. System stamps the entry with the **current Date & Time** without needing to re-take photos or re-enter details.
* **Scenario B: If New Staff Member (Not Found / First Time)**:
  1. Guard takes a live **Photo Snapshot** of the person.
  2. Guard enters **Person Entry Details** (**Full Name**, **Mobile Number**, and **Staff Role** e.g. *Maid, Cook, Driver*).
  3. Guard selects / maps the target **Flat Number(s)** where they work.
  4. Guard clicks **`[ Submit Staff Entry ]`**.
* **Automated Notification to Flat Apps**:
  * For both scenarios, the system immediately sends a real-time push notification to the resident app of all mapped flat numbers:
    > 📱 *"🧹 Domestic Staff: [Staff Name] has entered the gate for your flat at [Date & Time]."*

---

## 4. Gate Entry Form Specification

The **Gate Entry Form** supports fast, two-tiered data capture depending on whether the visitor is returning or new:

* **Returning Visitor (Search Found)**: Guard simply confirms/selects the target Flat Number and clicks **`[ Mark Entry ]`**. Current date and exact timestamp are recorded automatically.
* **New Visitor (Search Not Found)**: Guard captures a live photo snapshot, enters personal details (Name, Mobile, Category, Vehicle #), selects the target Flat Number, and clicks **`[ Submit Entry ]`**.
* **Automatic Resident Alert**: When entry is submitted, an instant push notification is sent to the resident user app of that flat number.

### Form Fields & Validation Rules:

| Field Label | Field Type | Requirement | Description / Behavior |
| :--- | :--- | :--- | :--- |
| **Mobile Number** | Numeric (10-digit) | **Mandatory** | Primary lookup key. If found in system, auto-loads previous details for 1-click entry mark. |
| **Visitor Full Name** | Text | **Mandatory** | First and last name of the entrant. Auto-filled if mobile found. |
| **Visitor Photo Snapshot** | Camera Capture | **Required for New Entrants** | Live webcam snapshot from tablet/guard camera. Re-used from profile if returning visitor. |
| **Target Wing / Tower** | Dropdown | **Mandatory** | Select Wing (e.g. `Tower A - Gulmohar`, `Tower B - Amaltas`, `Wing C`). |
| **Target Flat Number** | Dropdown / Search | **Mandatory** | Unit number (e.g. `101`, `402`, `504`) or `Common Facility / Society Admin`. |
| **Entry Category** | Radio / Pill Select | **Mandatory** | Options: `Guest`, `Cab / Taxi`, `Delivery`, `Service / Maintenance`, `Domestic Staff`. |
| **Vehicle Registration #** | Text (Alpha-numeric) | Optional / Required | Mandatory for Cabs & Cars (`MH-12-DE-4411`). Optional for walking guests. |
| **Company / Provider** | Dropdown / Quick Tag | Required for Delivery/Cab | Fast pills: `Uber`, `Ola`, `Swiggy`, `Zomato`, `Amazon`, `Urban Company`, `Other`. |
| **Remarks / Work Notes** | Text | **Optional** | Free-text notes for maintenance workers (e.g. *"AC servicing"*, *"Carrying drill machine"*). |
| **Automated Notification** | System Push | **Automatic** | Dispatches real-time push alert to target Flat Number user app upon entry submission. |

---

## 5. Live In-Premises Activity Directory (Live Entries List)

The **Live In-Premises List** is the central dashboard view for the security guard, displaying all external persons currently inside the society grounds.

### Key Data Columns Displayed:

1. **Category Badge**:
   * `[ 👤 Guest ]` &bull; `[ 🚕 Cab ]` &bull; `[ 📦 Delivery ]` &bull; `[ 🛠️ Maintenance ]` &bull; `[ 🧹 Staff ]`
2. **Entrant Name & Photo Thumbnail**:
   * Clean circular thumbnail photo snapshot + full name.
3. **Vehicle License Plate**:
   * Highlighted in mono font (e.g. `MH-12-DE-4411`).
4. **Target Destination**:
   * Flat number and resident name (e.g. `Flat A-302 (Kunal Roy)`).
5. **Entry Timestamp & Duration Inside**:
   * Entry time (e.g. `11:42 AM`) and live elapsed counter (`Inside: 35 mins`).
6. **Real-Time Status Indicator**:
   * 🟢 **Inside Premises (Normal)**: Within permitted time.
   * 🟡 **Overstay Warning**: Cabs or delivery riders exceeding 20 minutes.
   * ⚪ **Checked Out**: Visitor has departed the premises.
7. **Action Control**:
   * Prominent **`[ 🚪 Log Exit ]`** button for instant departure recording.

---

## 6. Real-Time Search & Lookup Engine

To handle thousands of gate events smoothly, the guard console provides instantaneous searching and filtering across active and past entries.

### 6.1. Search by Mobile Number
* **Functionality**: Guard types any 3 to 10 digits of a phone number (e.g., `98765` or `4411`).
* **Result**:
  * If person is currently inside premises: Filters the live list instantly for quick departure logging.
  * If person is at the entry gate: Retrieves their previous visitor profile and photo for 1-click entry mark with date & time.

### 6.2. Search by Person Name
* **Functionality**: Case-insensitive substring matching across visitor, driver, or delivery agent names (e.g., typing *"Karan"* or *"Sunil"*).
* **Result**: Displays all matching entries with flat mappings.

### 6.3. Search by Vehicle License Plate
* **Functionality**: Guard types full or partial registration string (e.g., `4411` or `MH-12`).
* **Result**: Quickly identifies cab or visitor car parked inside or standing at the exit boom barrier.

### 6.4. Search by Target Flat
* **Functionality**: Guard selects or types a flat number (e.g., `A-302`).
* **Result**: Shows all guests, couriers, or maintenance workers currently authorized inside that specific apartment.

### 6.5. Quick Filter Status Tabs
* **`Inside Now (18)`**: Only persons currently inside the premises.
* **`Checked Out (84)`**: Visitors who entered and exited earlier today.
* **`Overstay Alert (2)`**: Vehicles or workers flagged for exceeding normal duration.

---

## 7. Exit Processing & Departure Audit Trail

1. **Departure Trigger**:
   * Vehicle or visitor reaches the exit gate.
   * Guard searches by vehicle plate number, mobile number, or visitor name.
2. **One-Tap Checkout**:
   * Guard clicks **`[ 🚪 Log Exit ]`**.
   * System records the exact departure timestamp (e.g. `02:15 PM`) and calculates total duration inside (`Duration: 1 hr 22 mins`).
   * The entry is moved from the active "Inside Premises" tab to the historical "Checked Out" ledger.
3. **Resident Exit Alert**:
   * An automatic push notification is dispatched to the host resident:
     * *"Your visitor Karan Johar has exited through Main Gate 1 at 02:15 PM."*
4. **Live Counter Synchronization**:
   * The global "Inside Premises" counter on both the Guard Tablet and the Admin/Secretary Dashboard decrements automatically in real time.

---

## 8. Emergency, Security Alerts & Guard Safety Protocols

### 8.1. Gate SOS Siren & Emergency Broadcast
* Guard tablet features a prominent red **`🚨 Trigger Gate SOS`** button.
* When pressed:
  * Triggers the physical gate siren.
  * Dispatches an emergency alarm notification simultaneously to the Society President, Secretary, and Estate Manager.
  * Broadcasts an emergency banner to all resident applications.

### 8.2. Blacklisted / Banned Visitor Detection
* If a visitor mobile number or vehicle plate is tagged as **"Blacklisted"** by the Society Executive Committee (e.g. due to past theft, unruly behavior, or harassment):
  * Guard screen displays a flashing red warning banner:
    * ⛔ **"ALERT: BLACKLISTED INDIVIDUAL — DO NOT PERMIT ENTRY"**
  * Auto-dispatches an alert to the Security Supervisor.
