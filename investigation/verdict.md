# Investigation Verdict

## Severity

HIGH

## Verdict

Suspected brute-force attack followed by successful authentication.

## Evidence

- 6 failed SSH authentication attempts
- Same source IP: 10.10.30.50
- Same targeted account: admin
- Successful authentication after repeated failures
- Successful login originated from the same source IP

## Confidence

Medium-High

The pattern is suspicious, but additional investigation is required
to determine whether the successful login was authorized.
