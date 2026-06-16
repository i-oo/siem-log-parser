# siem-log-parser

> Parses raw Windows Event Logs, Linux auth.log, and Apache access logs into structured JSON for SIEM ingestion.

![Status](https://img.shields.io/badge/Status-In%20Progress-yellow?style=flat)
![Language](https://img.shields.io/badge/Python-3776AB?style=flat&logo=python&logoColor=white)
![License](https://img.shields.io/badge/License-MIT-green?style=flat)

---

## What It Does

Raw log files from different systems use completely different formats — a Windows Event Log looks nothing like a Linux auth.log, which looks nothing like an Apache access log. Before any SIEM (e.g. Microsoft Sentinel, Splunk) can correlate events across these sources, the logs need to be normalized into a consistent structure.

This tool reads raw log files, extracts key fields using format-specific parsers, applies a severity score to each event, and writes the output as structured JSON — ready for SIEM ingestion or further analysis.

---

## Supported Formats

| Format | Source | Status |
|---|---|---|
| Apache / Nginx access log | Web servers | ✅ Done |
| Linux auth.log | SSH, sudo, PAM events | 🔄 In progress |
| Windows Event Log (XML) | Windows Security log | 🔲 Planned |

---

## Output

Each parsed event produces a normalized JSON object:

```json
{
  "timestamp": "2024-11-15T03:52:11",
  "source_ip": "203.0.113.42",
  "event_type": "failed_login",
  "user": "root",
  "severity": "high",
  "raw": "Nov 15 03:52:11 server sshd[1234]: Failed password for root from 203.0.113.42 port 54321 ssh2"
}
```

---

## Severity Scoring

| Condition | Severity |
|---|---|
| Successful login | low |
| Single failed login | medium |
| 5+ failed logins from same IP within 60s | high |
| Failed login attempt for `root` or `admin` | high |
| HTTP 5xx response | medium |
| HTTP 4xx response | low |

---

## Usage

```bash
# Clone the repo
git clone https://github.com/i-oo/siem-log-parser
cd siem-log-parser

# Install dependencies
pip install -r requirements.txt

# Parse a log file
python parser.py --input samples/auth.log --format syslog --output events.json

# Parse with terminal summary
python parser.py --input samples/access.log --format apache --summary
```

---

## Project Structure

```
siem-log-parser/
├── parser.py            # Main entry point
├── parsers/
│   ├── apache.py        # Apache/Nginx access log parser
│   ├── syslog.py        # Linux auth.log parser
│   └── windows.py       # Windows Event Log (XML) parser
├── samples/             # Sample log files for testing
│   ├── access.log
│   ├── auth.log
│   └── security.evtx.xml
├── requirements.txt
└── README.md
```

---

## Planned Features

- [ ] Severity scoring engine
- [ ] Terminal summary table (counts by severity and source)
- [ ] Windows Event Log XML parser
- [ ] `--output` flag for JSON / CSV export
- [ ] Unit tests for each parser

---

## Transferable Skills

This project covers skills directly relevant to security operations work:

- Log normalization and field extraction (the foundation of any SIEM pipeline)
- Pattern recognition with regex across multiple log formats
- Severity classification — translating raw events into actionable signals
- Structured data pipelines in Python
