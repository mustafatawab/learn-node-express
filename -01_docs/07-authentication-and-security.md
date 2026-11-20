# 7. Authentication and Security

Authentication answers “who is making this request?” Authorization answers “may this identity perform this action on this resource?” Both checks belong on the server.

## Learning outcomes

You should be able to distinguish identity from permission, identify common browser/API threats, and explain how you would test access control.

**Prerequisites:** Guides 2–6.  
**Key terms:** authentication (verify identity), authorization (check permission), CSRF (trick a browser into sending an authenticated request), least privilege (grant only required access).

## Passwords, sessions, and tokens

Never store plaintext or reversibly encrypted passwords. Follow current OWASP guidance: prefer Argon2id for new systems; use scrypt when Argon2id is unavailable; reserve bcrypt for compatibility/legacy situations and account for its input limit. Use a maintained library, tune its work factors for your environment, and plan upgrades. Validate credentials, rate-limit login attempts, avoid responses that reveal whether an account exists, and require HTTPS.

The repository's sample auth projects currently use bcrypt. Treat that as a compatibility example to review, not the default recommendation for a new system. Before reuse, inspect password input limits, work factor, refresh-token storage and rotation, cookie settings, CSRF coverage, logout/revocation, and authorization tests.

Choose sessions or tokens based on the application. Server-side sessions store state on the server and send an identifier in a cookie. Signed tokens can carry claims, but signing does not encrypt their contents or make them revocable by itself. Expiry, refresh, storage, rotation, and revocation need deliberate design.

For a server-side session design, use a high-entropy opaque session identifier, store session state server-side, rotate the identifier after login or privilege changes, expire idle/old sessions, and invalidate the session on logout. Do not use Express's in-memory development session store for production. For token designs, plan revocation and key rotation before choosing long-lived credentials.

For password reset, use short-lived, single-use random tokens; store a verifier rather than the raw token where practical; return a generic response that does not disclose account existence; and rate-limit requests. Do not email passwords or reuse login tokens as reset tokens.

For browser apps, configure cookies with appropriate `HttpOnly`, `Secure`, and `SameSite` settings. The right policy depends on deployment. Cookie authentication may need CSRF protection. CORS is not authentication, authorization, or CSRF protection.

Do not choose JWT just because an API is “stateless.” Compare server-side sessions and tokens against revocation needs, browser/mobile clients, key rotation, and operational complexity. Never put sensitive secrets in a token payload. Store only the minimum claims needed and validate signature, issuer, audience, and expiry according to the token design.

## Authorization and common risks

Check permissions for every resource and action. Knowing an ID must not let one user access another user's data. Include ownership conditions in database queries where practical, and test allowed and denied cases.

- Validate and bound input; encode output for its destination.
- Use parameterized database operations; do not build commands or paths from raw input.
- Allow only intended CORS origins, especially when credentials are enabled.
- Use security headers such as Helmet and check their effect on the app.
- Set request size and rate limits for sensitive or expensive endpoints.
- Keep dependencies updated. Never return stack traces, secrets, or raw database errors.

For personal data, collect only what the feature needs, define retention and deletion behavior, protect exports, and document where data is stored. Privacy and data-residency obligations depend on users, organization, and jurisdiction; involve the appropriate legal/security owners rather than assuming one global rule.

## Threat-model exercise

For the todo API, list assets, trust boundaries, and likely attackers. For registration, login, refresh, and `GET /todos/:id`, list possible attacks. Add a test or manual check for each authorization boundary. Consider token theft, CSRF, brute force, and ID guessing.

**Check your work:** unauthenticated and unauthorized cases are distinct; ownership is checked on every read/write path; password hashes are stored instead of passwords; cookie and CSRF decisions match the client architecture; threat-model risks map to tests or documented mitigations.

**Common mistake:** hiding a button in the UI and treating that as authorization. Every protected operation must enforce permission on the server.

Use the [OWASP ASVS](https://asvs.owasp.org/) as a verification reference. It is a source of testable controls, not a claim that this tutorial or capstone is compliant.

## Further reading

- [OWASP: Authentication](https://cheatsheetseries.owasp.org/cheatsheets/Authentication_Cheat_Sheet.html)
- [OWASP: Session management](https://cheatsheetseries.owasp.org/cheatsheets/Session_Management_Cheat_Sheet.html)
- [OWASP: CSRF prevention](https://cheatsheetseries.owasp.org/cheatsheets/Cross-Site_Request_Forgery_Prevention_Cheat_Sheet.html)
- [OWASP: REST security](https://cheatsheetseries.owasp.org/cheatsheets/REST_Security_Cheat_Sheet.html)
- [OWASP: Password storage](https://cheatsheetseries.owasp.org/cheatsheets/Password_Storage_Cheat_Sheet.html)
- [OWASP: Forgot password](https://cheatsheetseries.owasp.org/cheatsheets/Forgot_Password_Cheat_Sheet.html)
- [Express: Production security](https://expressjs.com/en/advanced/best-practice-security.html)
