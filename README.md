# Extaas Connect

Public Workspace integration for linking an installation to an Extaas account.

## Install

Add https://github.com/Osauhing-X/extaas-connect.git (branch main) under Workspace → Library → Source → Source → GitHub. Open Discovery, select Extaas Connect and install it. Open Configuration to connect your account. Sign in to Iseteenindus and confirm the displayed code.

This package uses the native Workspace UI contract `extaas-connect-account-v1`. It requires Core support for that contract. The Core host compiles the account panel; it does not execute arbitrary Svelte files from packages. The UI source is included in ui/account_link.svelte. Older Core builds can list the manifest but do not provide this package's account panel.

## Scope

- Displays connection status and the linked account.
- Starts account linking and checks the confirmation.
- Links to Iseteenindus for service management.
- Ships no issuer keys, administrator passwords or installation secrets.
- Declares no module API actions, automatic payment actions or public webhooks.

The Core host retains encrypted connection state and authenticates all account requests. Removing or disabling this package does not revoke a connection at the account service. Manage linked installations in Iseteenindus. Paid access is checked separately; installing this integration does not purchase or grant a service.

The legacy catalogue transport is not a Source entry. Existing installed packages and Core-owned connection state are retained during migration.

See [API](api/README.md), [documentation](docs/README.md) and [changelog](CHANGELOG.md).
