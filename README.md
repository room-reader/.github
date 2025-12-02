# Room-Reader
Room-level care verification for assisted living, memory care, and nursing homes.  
Offline-first. Secure. Staff-friendly. Legally defensible. No subscriptions.

[![Status](https://img.shields.io/badge/status-active-brightgreen.svg)]()
[![Platform](https://img.shields.io/badge/platform-ESP32%20%7C%20Raspberry%20Pi-lightgrey.svg)]()
[![Database](https://img.shields.io/badge/database-SQLite-success.svg)]()
[![Language](https://img.shields.io/badge/language-Python%20%7C%20C%2B%2B-blue.svg)]()

Room-Reader uses NFC scan points placed throughout a resident’s room.  
Staff tap their employee badge at each checkpoint.  
The system records:

- entry
- bedside presence
- ADL support
- exit
- duration

All data is stored on a Raspberry Pi and synced to per-resident Google Sheets when internet returns.

The purpose: provide objective proof of care without cameras, surveillance, or monthly fees.

---------------------------------------------------------------------

# Table of Contents

1. Overview by Audience  
2. Why Facilities Use Room-Reader  
3. Cost and Comparison  
4. Technology Summary (NFC / HF RFID)  
5. What Staff Carry  
6. Why Manual Taps Beat BLE  
7. Tap Workflow Diagrams  
8. LED Feedback System  
9. Legal and Regulatory Benefits  
10. Hardware Architecture  
11. Installation Instructions  
12. Directory Structure  
13. Future Expansion

---------------------------------------------------------------------

# 1. Overview by Audience

## Administrators and Directors

Room-Reader provides objective, timestamped, room-level proof of care.  
It helps answer:

- Did night checks occur?
- How long did staff stay at bedside?
- Are assignments realistic?
- Is memory care understaffed?
- Are agency staff completing tasks?
- Can I justify requesting additional staff?

It turns invisible care work into visible, defensible data.

## Nursing and Care Staff

Room-Reader protects staff by proving the work they actually do.  
Badge tapping already matches existing workflows (med carts, supply rooms, time clocks).  
The system proves:

- entry occurred
- bedside presence occurred
- ADLs were performed
- time spent was reasonable

It reduces false accusations and protects workers during audits.

## Families

Families receive a private, per-resident Google Sheet (synced when online).  
They see:

- entry time
- bedside time
- ADLs
- exit time
- duration

No cameras. No audio. No surveillance.  
Only clean, clear care logs.

## Technical Staff

Room-Reader uses:

- ESP32 microcontrollers
- PN532 NFC readers
- NTAG213 NFC badges
- Raspberry Pi server with FastAPI + SQLite
- Offline-first synchronization model

No vendor lock-in.  
No proprietary cloud.

---------------------------------------------------------------------

# 2. Why Facilities Use Room-Reader

Room-Reader provides:

- verified entry
- verified bedside presence
- verified ADL scans
- verified exit
- care duration
- offline-first storage
- objective audit trails
- family transparency
- staffing workload analytics

The system replaces guesswork with actual timelines.

---------------------------------------------------------------------

# 3. True Cost Per Room

Baseline installation (doorway + bedside + optional bathroom):
- $65–$85 per room (hardware)
- $0 per month (no subscription)
- $0.25–$3 per staff badge
- $60 Raspberry Pi powers entire facility

Even with upgrades (BLE presence, battery backup), rooms remain under $110.

## Cost Comparison
- Room-Reader Install:     $65–$85 per room (one-time)
- Agency CNA shift:        $280–$520 per day
- Unwitnessed fall:        $80,000–$300,000
- Pressure ulcer lawsuit:  $200,000–$500,000
- Wrongful death claim:    $500,000–$1.6M
- Documentation failure:   $25,000–$75,000 fine


Room-Reader is not expensive.  
Not having it is expensive.

---------------------------------------------------------------------

# 4. Technology: High-Frequency NFC (13.56 MHz)

Room-Reader uses HF NFC (NTAG213) because:

- very short range (intentional tap)
- secure and difficult to clone
- used in hospitals, corporate badges, banks
- staff are familiar with the motion
- perfect for documenting human-performed care tasks

Intentional physical action = legally defensible evidence.

---------------------------------------------------------------------

# 5. What Staff Carry

A standard employee ID badge containing:

- photo
- name
- role
- optional QR code
- NTAG213 NFC chip embedded inside

No charging.  
No batteries.  
No BLE.  
No maintenance.

---------------------------------------------------------------------

# 6. Why Manual Taps Are Better Than BLE Presence

BLE suffers from:

- ghost detection
- wall penetration
- hallway detection
- signal bounce
- delayed disconnect
- high battery replacement burden

Manual taps ensure:

- real presence
- bedside proximity
- intentional action
- no guesswork
- clear documentation sequences

A badge tap cannot be faked or triggered from outside the room.

---------------------------------------------------------------------

# 7. Tap Workflow Diagrams

## Basic Three-Point Workflow
```
[Doorway Reader] -> [Bedside Reader] -> [Exit Reader]

10:04:12 Entry scan
10:04:44 Bedside scan
10:08:03 Exit scan

Dwell time: ~4 minutes
```

## ADL Workflow
Entry -> Bedside -> ADL -> Exit

## Minimal Workflow (Doorway + Bedside)
Entry --> Bedside

Time between first and last tap = presence duration.

---------------------------------------------------------------------

# 8. LED Feedback System

Each scan point has a clear LED indicator for staff:

- Green: valid scan logged
- Red: invalid scan or wrong badge
- Yellow: syncing to Pi
- Blue: offline local mode

This reduces staff uncertainty and prevents missed scans during night checks.

---------------------------------------------------------------------

# 9. Legal and Regulatory Benefits

Room-Reader strengthens:

- CMS compliance
- night rounds documentation
- ADL logs
- q2h repositioning documentation
- toileting logs
- wound care logs
- fall investigations
- family trust
- surveyor satisfaction

Quote suitable for court or audit:

"Each scan point provides immediate LED confirmation that the staff member's visit was successfully logged."

---------------------------------------------------------------------

# 10. Hardware Architecture

## Overall Architecture
```
[ESP32 Reader] ––
[ESP32 Reader] —– >  Wi-Fi  ––> [Raspberry Pi Server]
[ESP32 Reader] ––/                      |
|
[SQLite DB]
|
Optional Sync
|
[Google Sheets]

```

## In-Room Device Structure
```
| Green LED Red LED  White LED |
|                              |
|  PN532 NFC Reader            |
|                              |
|        ESP32 DevKit          |
|                              |
|  USB Power Out Bottom        |
```

---------------------------------------------------------------------

# 11. Installation Instructions

## Hardware Steps

1. Mount enclosures at:
   - 5 ft inside doorway  
   - bedside  
   - optional bathroom

2. Install ESP32 inside enclosure.

3. Mount PN532 antenna behind front panel.

4. Drill three 5 mm LED holes.

5. Connect:
   - PN532 SDA → GPIO 21  
   - PN532 SCL → GPIO 22  
   - Green LED → GPIO 4  
   - Red LED → GPIO 16  
   - 220 ohm resistors to ground  
   - USB cable to ESP32, routing out the bottom

6. Plug ESP32 into wall USB adapter.

## Software Steps

1. Flash ESP32 firmware from `esp32/firmware`.
2. Install Pi backend using:
   pip install -r requirements.txt
3. Initialize the database:
    python3 init_db.py
4. Start FastAPI server:
   uvicorn server:app –host 0.0.0.0 –port 8000
5. Configure Google Sheets sync:
 python3 sync_sheets.py

---------------------------------------------------------------------

# 12. Directory Structure
```
room-reader/
│
├── esp32/
│   ├── firmware/
│   ├── wiring_diagrams/
│   └── config/
│
├── server/
│   ├── fastapi/
│   ├── database/
│   ├── models/
│   ├── routes/
│   └── sync_sheets.py
│
├── docs/
│   ├── installation/
│   ├── hardware/
│   └── legal/
│
├── examples/
│   └── sample_care_logs/
│
└── README.md
```

---------------------------------------------------------------------

# 13. Future Expansion

- BLE presence (optional)
- motion sensors
- door-open sensors
- LoRaWAN for campuses
- thermal printer shift summaries
- corporate dashboard integrations

Room-Reader remains intentionally simple while supporting advanced use cases.

---------------------------------------------------------------------

# Final Note

Facilities get sued for being unable to prove that care was provided.  
Room-Reader solves that problem for under $100 per room, with zero recurring costs, and protects staff, residents, and administrative leadership simultaneously.


