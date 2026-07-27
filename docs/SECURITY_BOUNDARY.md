# Security boundary

This repository is intentionally incapable of real execution.

- Public, unauthenticated read endpoints only
- No wallet integration, signing, credential input, authentication header, trading SDK, or order endpoint
- No token approvals, transfers, deposits, withdrawals, or position management
- No geographic restriction bypass, proxy rotation, or VPN behavior
- Geoblock response IP is discarded
- Paper trades are local database records derived from current public book snapshots
- Unknown fees fail closed and cannot be labelled a successful opportunity

Run `python scripts/security_scan.py` before every release. The scanner checks tracked executable/configuration files and Git ignore coverage.
