# Brute Force Authentication Investigation

## About This Project

This project is a small SOC-style investigation focused on detecting and analyzing suspected brute-force authentication activity.

The main goal was to investigate authentication logs, identify repeated login failures, find the source IP and targeted account, check whether the login was eventually successful, and then decide the severity of the incident.

I used sample Linux SSH authentication logs for this investigation.

---

## Objective

The main objectives of this investigation were:

- Identify repeated failed login attempts
- Find the targeted username
- Identify the source IP address
- Check the time of the activity
- Look for a successful login after multiple failures
- Create an incident timeline
- Determine the severity
- Recommend appropriate containment actions

---

## Tools Used

- Kali Linux
- Linux SSH authentication logs
- Linux command-line tools such as `grep`, `awk`, `sort` and `uniq`
- Git and GitHub
- Splunk queries planned for SIEM-based analysis

---

## Investigation Scenario

In this scenario, an attacker is suspected of trying to gain access to an SSH account by repeatedly attempting to authenticate with incorrect credentials.

The logs show multiple failed login attempts against the `admin` account.

All of the failed attempts came from the same source IP:

```text
10.10.30.50
```

After several failed attempts, the same source IP successfully authenticated to the same account.

This made the activity suspicious and required further investigation.

---

## Evidence

The following sequence was observed:

```text
09:55:01  Failed login
09:55:04  Failed login
09:55:07  Failed login
09:55:10  Failed login
09:55:13  Failed login
09:55:16  Failed login
09:55:20  Successful login
```

Target account:

```text
admin
```

Source IP:

```text
10.10.30.50
```

Total failed attempts:

```text
6
```

Successful authentication:

```text
Yes
```

---

## Investigation Process

### 1. Identify Failed Login Attempts

I first searched the authentication log for failed SSH password attempts.

Command used:

```bash
grep "Failed password" data/sample_linux.log
```

This showed six failed authentication attempts.

---

### 2. Count Failed Attempts

I counted the number of failed authentication events using:

```bash
grep -c "Failed password" data/sample_linux.log
```

Result:

```text
6
```

---

### 3. Identify the Source IP

I extracted the source IP addresses from the failed authentication events.

```bash
grep "Failed password" data/sample_linux.log | awk '{print $(NF-3)}' | sort | uniq -c
```

Result:

```text
6 10.10.30.50
```

This shows that all six failed attempts came from the same IP address.

---

### 4. Identify the Targeted Account

The targeted username was identified as:

```text
admin
```

All six failed attempts were directed at the same account.

---

### 5. Check for Successful Authentication

I then checked whether the account was successfully authenticated after the failed attempts.

```bash
grep "Accepted password" data/sample_linux.log
```

A successful authentication was found at:

```text
09:55:20
```

The successful login came from the same source IP:

```text
10.10.30.50
```

---

## Timeline

| Time | Event | User | Source IP | Result |
|---|---|---|---|---|
| 09:55:01 | SSH authentication | admin | 10.10.30.50 | Failed |
| 09:55:04 | SSH authentication | admin | 10.10.30.50 | Failed |
| 09:55:07 | SSH authentication | admin | 10.10.30.50 | Failed |
| 09:55:10 | SSH authentication | admin | 10.10.30.50 | Failed |
| 09:55:13 | SSH authentication | admin | 10.10.30.50 | Failed |
| 09:55:16 | SSH authentication | admin | 10.10.30.50 | Failed |
| 09:55:20 | SSH authentication | admin | 10.10.30.50 | **Success** |

The successful authentication happened only four seconds after the final failed attempt.

---

## Findings

The investigation identified the following:

1. Six failed authentication attempts occurred.
2. All attempts targeted the `admin` account.
3. All failed attempts originated from `10.10.30.50`.
4. A successful authentication occurred shortly after the failed attempts.
5. The successful authentication came from the same IP address.

The pattern is suspicious and is consistent with a possible brute-force attack followed by successful credential use.

However, the logs alone do not prove that the successful login was malicious. The account owner and source system should be checked before confirming a compromise.

---

## Severity

### HIGH

The incident was classified as **High severity** because repeated authentication failures were followed by a successful authentication from the same source IP against the same account.

The risk would be higher if the account were confirmed to be privileged or if suspicious activity occurred after the successful login.

---

## Verdict

**Suspected brute-force attack followed by successful authentication.**

The available evidence indicates suspicious authentication activity involving the `admin` account.

Further investigation is required to determine whether the successful login was legitimate or unauthorized.

---

## Recommended Actions

### Immediate Actions

- Verify whether the successful login was authorized.
- Investigate the source IP `10.10.30.50`.
- Review activity performed after the successful login.
- Check whether other accounts were targeted.
- Reset the account password if compromise is suspected.
- Revoke active sessions if unauthorized access is confirmed.

### Security Improvements

- Enable MFA for important and privileged accounts.
- Implement SSH rate limiting.
- Configure appropriate account lockout controls.
- Disable unused accounts.
- Monitor repeated authentication failures.
- Create alerts for failed logins followed by successful authentication.

---

## Screenshots

### Failed Authentication Attempts

![Failed Attempts](screenshots/01_failed_attempts.png)

### Source IP

![Source IP](screenshots/02_source_ip.png)

### Successful Authentication

![Successful Login](screenshots/03_successful_login.png)

### Full Authentication Timeline

![Timeline](screenshots/04_full_timeline.png)

---

## Project Structure

```text
brute-force-investigation/
│
├── README.md
│
├── data/
│   └── sample_linux.log
│
├── queries/
│   └── splunk_queries.txt
│
├── investigation/
│   ├── timeline.md
│   └── verdict.md
│
├── recommendations/
│   └── containment.md
│
└── screenshots/
    ├── 01_failed_attempts.png
    ├── 02_source_ip.png
    ├── 03_successful_login.png
    └── 04_full_timeline.png
```

---

## Conclusion

This project demonstrates a basic SOC investigation workflow:

```text
Authentication Logs
        ↓
Failed Login Detection
        ↓
Identify User and Source IP
        ↓
Check for Successful Login
        ↓
Build Timeline
        ↓
Assess Severity
        ↓
Recommend Containment
```

The investigation shows how authentication logs can be used to identify suspicious login behavior and support an initial incident response decision.
