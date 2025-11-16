# Security Policy

This documentation template is intended to be used as a reusable submodule in Laravel projects. While it does not contain executable application code by itself, it defines strict security requirements and patterns that production code should follow.

If you believe you have found a security issue related to this repository (for example, in example configurations, suggested patterns, or automation workflows), please follow the process below.

## Supported Versions

This template targets projects running:

- PHP **8.4+**
- Laravel **12.x**

Security-related guidance in this repository assumes these versions or newer.

## Reporting a Vulnerability

1. **Do not open a public issue for security reports.**
2. Please email your report to:

   - **Security contact:** Viet Vu `<jooservices@gmail.com>`

3. Include as much detail as possible:

   - A clear description of the issue.
   - Steps to reproduce (if applicable).
   - The files/sections of documentation involved.
   - Any potential impact you foresee if the guidance were followed as‑is.

4. If you can, include:

   - Suggested remediation or safer patterns.
   - References to relevant OWASP or Laravel security best practices.

## What to Expect

- We will review your report and respond as soon as reasonably possible.
- If the issue is confirmed, we will:
  - Update the documentation to fix or clarify the insecure pattern.
  - Optionally credit you in the changelog or commit message, if you wish.
- We may ask you for additional information to fully understand or validate the report.

## Scope

This policy covers:

- Security issues in the **patterns and examples** documented here (e.g., guidance that could lead to insecure configurations if followed literally).
- Security issues in any supporting scripts or configuration examples (if added in the future).

For security issues in actual application code, infrastructure, or deployments that use this template, please follow the security process of the respective parent project or organization. Each consuming project is responsible for its own operational security and incident response.


