# Brute Force Investigation Timeline

| Time | Event | User | Source IP | Result |
|---|---|---|---|---|
| 09:55:01 | SSH authentication | admin | 10.10.30.50 | Failed |
| 09:55:04 | SSH authentication | admin | 10.10.30.50 | Failed |
| 09:55:07 | SSH authentication | admin | 10.10.30.50 | Failed |
| 09:55:10 | SSH authentication | admin | 10.10.30.50 | Failed |
| 09:55:13 | SSH authentication | admin | 10.10.30.50 | Failed |
| 09:55:16 | SSH authentication | admin | 10.10.30.50 | Failed |
| 09:55:20 | SSH authentication | admin | 10.10.30.50 | SUCCESS |

## Analysis

Six failed authentication attempts were observed against the
admin account from the same source IP.

Four seconds after the final failed attempt, the same source IP
successfully authenticated to the same account.
