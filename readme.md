# Elementor CSRF → Admin PoC (CVE-2026-62062)

Reproduces the Elementor 4.3.0/4.3.1 CSRF (privilege escalation) that lets a
crafted URL make a logged-in admin perform an arbitrary REST action. This PoC
creates an administrator named `test` with password `Mike@17894` on the target.

## Prerequisites
- A WordPress instance running Elementor **4.3.0 or 4.3.1** (patched in 4.3.2).
- An authenticated admin session in the browser you use to open this page.

## Usage
1. Edit the `target` value in `index.html` to your base URL (e.g. http://localhost:8080).
2. Log in as admin to that WordPress site.
3. Open `index.html` (or `nojs.html`) in the same browser and click the link.

## Expected result
- With `x=elementor/v1/events/`  -> HTTP 201, user `test` created with role `administrator`.
- Without that marker            -> HTTP 401 `rest_cannot_create_user`.
- Marker `elementor/v1/event/`   -> also fails (proves the substring is the cause).

## Verify
wp user list --role=administrator

## Notes
- Only test systems you own or that are explicitly in scope.
- GitHub Pages is HTTPS. Top-level navigation to an http:// target works, but
  iframe/fetch-based delivery to an http:// target is blocked as mixed content.
  For iframe/fetch targeting, serve over HTTPS or test from a local file.