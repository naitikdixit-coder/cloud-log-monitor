# Cloud Log Monitor
**By Naitik Dixit**

Beginner-friendly tool to analyze **AWS CloudTrail / CloudWatch** authentication and IAM activity logs for suspicious events.
Works in **two modes**:
- `aws` (default): fetch events from CloudTrail in the last N minutes and flag anomalies.
- `offline`: analyze the provided sample logs (no AWS account needed).

## 🔍 What it detects (starter rules)
- Multiple **failed ConsoleLogin** attempts from the same IP (brute-force signal).
- Root user activity events (should be rare).
- Critical **IAM changes** (e.g., `CreateUser`, `AttachUserPolicy`, `PutUserPolicy`).
- Console login **without MFA** (`additionalEventData.MFAUsed = "No"`).
- AWS access from **unusual geo-IP** (basic check via allowlist; optional).

> Rules are editable in `rules.yaml`.

## 📦 Quickstart

### 1) Clone & setup
```bash
git clone https://github.com/your-username/cloud-log-monitor.git
cd cloud-log-monitor
python -m venv .venv && source .venv/bin/activate  # Windows: .venv\Scripts\activate
pip install -r requirements.txt
```

### 2a) Run in OFFLINE mode (no AWS needed)
```bash
python src/log_monitor.py --mode offline --input sample_logs/events.json --outdir reports
```

### 2b) Run in AWS mode (needs AWS creds & permissions)
Ensure your AWS credentials are configured (e.g., `aws configure`) and the role/user has **read-only** CloudTrail permissions (see below).

```bash
python src/log_monitor.py --mode aws --region ap-south-1 --lookback-min 60 --outdir reports
```

### 3) Outputs
- JSON report with all matched findings: `reports/report_YYYYMMDD_HHMM.json`
- Markdown summary for easy reading: `reports/report_YYYYMMDD_HHMM.md`

## 🔐 Minimum IAM Permissions (policy snippet)
```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "cloudtrail:LookupEvents",
        "sts:GetCallerIdentity"
      ],
      "Resource": "*"
    }
  ]
}
```

## ⚙️ Config & Rules
- **`rules.yaml`** — turns rules on/off and sets thresholds (like failed login burst window).
- **`config.yaml`** — optional allowlists (e.g., corporate IPs / countries).

## 🧪 Sample Data
A sanitized `events.json` sample is included so you can run **offline** and see how findings look.

## 🧰 Extend the project
- Add Slack/Webhook notifications when a rule triggers.
- Add S3/GuardDuty events parsing.
- Add Streamlit dashboard to visualize trends.

## 📄 License
MIT — free to use and modify for learning.
