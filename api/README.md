# API contract

Verified package identity: `github:osauhing-x/extaas-connect`. The source owner comes from Core's verified GitHub source, not a manifest author string.

This integration has no declared cross-package API actions. Other packages must not request or copy account tokens from it.

The browser account panel uses same-origin `/@_connect`, owned by Workspace Core:

- GET: public connection state for the authenticated local administrator. Tokens and signing keys are excluded. Unauthenticated/non-owner requests return 403.
- POST JSON `{"action":"begin"}`: request an installation link.
- POST JSON `{"action":"poll"}`: check the pending confirmation.
- POST JSON `{"action":"refresh"}`: verify an existing link.

Only these three actions are supported. POST requests require the owner's session and trusted Origin. Account linking must be enabled by the Core deployment; otherwise POST returns 409. Invalid actions return 400. Verification/service failures return 503. Responses are not cacheable. This is not a public webhook or an API for module service tokens.

The integration does not provide a password-reset endpoint, a payment endpoint or an unlink endpoint. Follow Iseteenindus links for account/service management.
