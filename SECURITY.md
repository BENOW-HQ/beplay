# Security policy

Report vulnerabilities privately using this repository's Security tab and “Report a vulnerability”. Do not include company credentials or data in public reports.

This repository distributes client tools only. Company application access still requires Google sign-in and app-specific permission. It contains no production credentials or deployment workflows. GitHub Actions is disabled.

Published releases are immutable. Fixes are issued as a new version; existing release assets are not replaced. Installers pin the matching archive SHA-256. Verify release integrity with GitHub release attestations when supported by your GitHub CLI.

Changes to the default branch require a pull request and an approving review. Release creation is limited to repository administrators. Organization owners retain administrative authority; organization MFA is required. No security control eliminates the risk of compromised administrator accounts.

Support: use the latest published version. For a suspected compromised release, stop installing it and report privately before proceeding.
