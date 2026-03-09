# RFID Time Tracking System

> RFID badge scanner → Python → Google Sheets. Automated team attendance tracking with a live web terminal, season summary, and Windows auto-start.

A fully automated workforce time tracking system built for the Robotics Team. Physical RFID badge scans clock team members in and out, syncing to Google Sheets in real time while displaying a live web terminal dashboard with audio feedback.

---

## Features

- **RFID toggle logic** — first scan clocks in, second scan clocks out
- **Web terminal dashboard** — dark mode, color-coded events, audio beeps on each scan
- **Google Sheets integration** — three tabs updated automatically:
  - `Log` — every clock-in and clock-out event
  - `Live Status` — who is currently clocked in right now
  - `Season Summary` — cumulative attendance per person for the full season
- **Local CSV backup** — every event saved locally regardless of internet status
- **Master override card** — clocks out all active users at once
- **Midnight auto-clockout** — no timers accidentally run overnight
- **Windows auto-start** — system starts itself on boot, no manual steps needed
- **Multi-device support** — RFID reader can be plugged into any device on the same network

---

## Tech Stack

- Python 3
- Flask
- gspread + Google OAuth2
- Web Audio API (browser-side beeps)
- Windows Task Scheduler via `.bat` startup script

---

## Project Structure

```
kimai-docker/
├── rfid_kimai_all_in_one.py   # Main script — entire system runs from here
├── start_rfid_system.bat      # Windows auto-start script
├── credentials.json           # Google service account key (not committed)
├── backup.csv                 # Local clock-in/out backup (auto-generated)
└── venv/                      # Python virtual environment
```

---

## Setup

### 1. Clone the repo
```bash
git clone https://github.com/YOUR_USERNAME/YOUR_REPO_NAME.git
cd YOUR_REPO_NAME
```

### 2. Create and activate a virtual environment
```bash
python -m venv venv
source venv/Scripts/activate   # Git Bash on Windows
```

### 3. Install dependencies
```bash
pip install flask gspread google-auth requests
```

### 4. Add Google Sheets credentials
- Create a service account in [Google Cloud Console](https://console.cloud.google.com)
- Download the JSON key and save it as `credentials.json` in the project folder
- Share your Google Sheet with the service account email
- Name the sheet: `Kimai RFID Log`

### 5. Configure RFID tags
Open `rfid_kimai_all_in_one.py` and update the `USER_TAGS` dictionary with your card numbers and names:
```python
USER_TAGS = {
    "0012345678": "FirstName",
    ...
}
```

Also set your master override card:
```python
MASTER_TAG = "0000000000"
```

### 6. Run the system
```bash
python rfid_kimai_all_in_one.py
```

Open a browser and go to `http://localhost:5000`

---

## Auto-Start on Windows Boot

1. Save `start_rfid_system.bat` to the project folder
2. Press `Win+R`, type `shell:startup`, hit Enter
3. Copy `start_rfid_system.bat` into that folder

The system will now start automatically every time the computer boots.

---

## Accessing From Another Device

Find the main computer's IP address:
```bash
ipconfig
```
Look for `IPv4 Address` under `Wireless LAN adapter Wi-Fi`.

Then on any device on the same network open:
```
http://192.168.1.XXX:5000
```

If the page won't load, open the firewall port:
```bash
netsh advfirewall firewall add rule name="RFID Terminal" dir=in action=allow protocol=TCP localport=5000
```

---

## Adding a New Person

1. Have them scan their card — the number will be saved to `backup.csv`
2. Add their tag and name to `USER_TAGS` in the script
3. Save and restart the script

---

## Google Sheets Layout

### Log Tab
| User | Date | Day | Clock In | Clock Out | Duration |
|------|------|-----|----------|-----------|----------|

### Live Status Tab
| User | Clocked In At | Duration So Far | Date | Last Updated |
|------|--------------|-----------------|------|--------------|

### Season Summary Tab (resets Jan 10 each year)
| User | Mon | Tue | Thu | Sat | Other Days | Total Days | Total Hours |
|------|-----|-----|-----|-----|------------|------------|-------------|

---

## Terminal Color Guide

| Color | Meaning |
|-------|---------|
| 🟢 Green | Clocked IN |
| 🟠 Orange | Clocked OUT |
| 🟡 Yellow | Master card / Midnight auto-clockout |
| 🔴 Red | Error |

---

## License

Internal use — Robotics Team, Network Cyberblue.
