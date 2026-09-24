# Security policy

## Reporting a vulnerability

Report a vulnerability privately through GitHub:
https://github.com/grigoriev/renovate-config/security/advisories/new
(the **Security** tab, **Report a vulnerability**). Do not open a public issue for it.

We answer within a week. The fix goes to `main`, and the CHANGELOG names it.

## Supported versions

Only `main` gets fixes. Renovate reads the preset from it.

## Scope

The preset in `default.json`, the repository config and the workflows.

Vulnerabilities in Renovate itself or in the Mend Renovate app belong to the upstream
project. Tell us as well if this preset is affected, so we can change it.
