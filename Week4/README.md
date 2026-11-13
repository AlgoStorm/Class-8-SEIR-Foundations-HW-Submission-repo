# SEIR Foundations – Module 1, Lesson 2  
## Seven Alerts, Seven Wins

**Course:** SEIR Foundations (Systems Engineering and Infrastructure Integration)  
**Lab / Homework:** Lesson 2 – Slack Alerting (Week 4)  
**Assigned:** Tuesday, November 3, 2026  
**Due:** Monday, November 9, 2026  
**Source:** [BalericaAI/SEIR-Foundations – module1/lesson2_slack/homework](https://github.com/BalericaAI/SEIR-Foundations/tree/main/module1/lesson2_slack/homework)

---

### Overview

This lab introduces operational alerting using Slack as the notification channel for the fictional **Balerica Bank**. You will run seven progressive Python scripts that call a shared `send_alert()` function. Each script demonstrates a different monitoring or reporting pattern commonly seen in production systems: severity filtering, time reporting, resource thresholds, load averages, synthetic incident generation, and a multi-line morning report.

The goal is to understand how alert severity, environment variables, and a kill switch control what operators actually see—and why quiet or suppressed alerts are often correct behavior.

---

### Prerequisites

- Complete the preceding lab so that  
  `python slack.py --test` prints  
  `SUCCESS: Chewbacca approves!`  
  and the test alert appears in the `#balerica-alerts` channel.
- Know the basic nano workflow: `Ctrl+O`, `Enter`, `Ctrl+X`.

---

### One-Time Setup

```bash
# Pull latest course materials
cd ~/SEIR-Foundations
git pull

# Copy the seven homework scripts next to slack.py
cp module1/lesson2_slack/homework/hw*.py /opt/balerica-bank/

# Verify all seven scripts are present
ls /opt/balerica-bank/hw*.py

# Set the class severity filter (anything below ERROR is normally suppressed)
# Edit .env and ensure:
# SLACK_ALERT_SEVERITY=INFO

# Activate the virtual environment (required for every new SSH session)
cd /opt/balerica-bank
source venv/bin/activate
```

---

### The Seven Scripts

Read each script’s source before running it. If a line is unclear, ask for an explanation—do not rewrite the call to `send_alert()`.

| Script | Purpose | Notes |
|--------|---------|-------|
| `hw1_hello_slack.py` | Personalized hello alert | **Must** edit `MY_NAME` in nano before it will run |
| `hw2_severity_tour.py` | Sends all four severities (INFO, WARNING, ERROR, CRITICAL) | Observe which ones appear based on the current filter |
| `hw3_server_time.py` | Reports the server’s UTC time | Compare with your local wall clock |
| `hw4_disk_watch.py` | Measures disk usage and chooses severity | ≥75% = WARNING, ≥90% = ERROR; otherwise INFO |
| `hw5_load_check.py` | Compares load average against CPU count | Useful for spotting contention |
| `hw6_incident_drill.py` | Sends a random synthetic bank incident | Run 2–3 times to see different scenarios |
| `hw7_morning_report.py` | Capstone multi-line report (time, host, Python version, disk, load) | Demonstrates richer, structured alerting |

**Run order (from `/opt/balerica-bank` with the venv active):**

```bash
python hw1_hello_slack.py
python hw2_severity_tour.py
python hw3_server_time.py
python hw4_disk_watch.py
python hw5_load_check.py
python hw6_incident_drill.py   # run 2–3 times
python hw7_morning_report.py
```

---

### Experiments (after all seven succeed)

1. **Severity filter test**  
   Set `SLACK_ALERT_SEVERITY=CRITICAL` in `.env` and rerun `hw2_severity_tour.py`.  
   **Predict** how many of the four messages will arrive, then observe the result.

2. **Kill switch**  
   Set `SLACK_ALERTS_ENABLED=false` and run `hw1_hello_slack.py`.  
   Explain what happens and why a kill switch is valuable during maintenance windows.

3. **Threshold experiment**  
   In `hw4_disk_watch.py`, lower the WARNING threshold below your current disk usage.  
   Rerun and note whether the severity changes.

**Restore original settings** when finished:

```bash
# In .env
SLACK_ALERT_SEVERITY=INFO
SLACK_ALERTS_ENABLED=true
```

---

### Rules

- Send **synthetic messages only**. Never include passwords, keys, customer data, or anything from `.env`.
- Do not spam. Running the set a few times is acceptable; do not create loops that fire hundreds of alerts.
- Editing the scripts is encouraged for learning, but **always** call  
  `send_alert(agent, severity, message, incident_id)` exactly as written.

---

### Evidence of Completion

Submit in the same format as previous weeks:

- One screenshot of `#balerica-alerts` showing **at least 5 different** homework alerts (sensitive information redacted).
- One screenshot of the terminal running `hw7_morning_report.py`, including the Lesson 1 identity line:  
  `whoami && TZ='Your/Zone' date && ec2-metadata -i`
- Notes containing:
  - Your prediction vs. actual result from Experiment 1.
  - Answers to each script’s “In your notes” question:
    - **hw2:** Which severities arrived, and why those?
    - **hw3:** How many hours apart are server time and your wall clock?
    - **hw4:** Why is a quiet INFO here good design, not a failure?
    - **hw5:** What would make this number spike on the Balerica bank?
    - **hw6:** Which fake incident deserves to wake a human at 3 AM?
  - (Scripts 1 and 7 do not ask notes questions, so five answers total.)

---

### Learning Objectives

- Understand severity-based filtering and why most alerts should stay quiet.
- Practice environment-variable driven configuration (`SLACK_ALERT_SEVERITY`, `SLACK_ALERTS_ENABLED`).
- Observe real resource metrics (disk, load) and map them to actionable severities.
- Recognize the value of a kill switch during planned maintenance.
- Build a simple structured operational report suitable for Slack.

---

**Happy alerting.**  
May Chewbacca approve of your signals.
