# Changelog

## 0.4.0 - 2026-10-26

- Added Docker Compose configuration for Notes API.
- Added PostgreSQL 17 with persistent storage.
- Added database healthcheck and service dependencies.
- Added environment configuration with .env.example.
- Added optional Adminer service using the tools profile.
- Documented Docker Compose startup and data persistence.
- Accepted ADR 0002: PostgreSQL database selection.


## 0.2.0 - 2026-10-17

- Initial template: notes API with health and version endpoints.
- Completed the service documentation, local runbook, CODEOWNERS and contributor guidelines.
- Expanded the glossary to 16 alphabetically ordered terms and resolved the merge conflict locally.
- Added ADR 0002 for database selection and ADR 0003 for service hosting, with index and navigation links.
- Completed the on-call schedule for weeks 5-8, with an explicit disclosure of the simulated backup role.
- Selected GitHub Flow after resolving the CONTRIBUTING.md conflict through a local rebase.
- Demonstrated a runbook port change to 9000 and restored port 8000 through a GitHub Revert pull request.
