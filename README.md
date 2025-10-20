# Youssef_Ahmed-Assignment_2

A safer setup: move secrets out of code

We started by removing sensitive values from the source files and putting them in an environment file (.env). That way the code stays readable and safe — no hard-coded keys sitting in plain sight. The .env file is just a local configuration: you copy the provided template, put strong random secrets inside, and keep that file out of version control. It’s a small change that makes the project a lot more realistic and secure.

Two servers for learning: vulnerable vs. hardened

To learn the difference between bad and good practices, we run two versions of the app:

Vulnerable server — intentionally insecure so you can see attacks in action. It demonstrates common mistakes: weak default secrets, accepting unsigned tokens, and storing tokens where browser scripts can read them.

Hardened server — a more realistic, secure setup. It uses separate, strong secrets for access and refresh tokens, validates token claims (issuer and audience), enforces the signing algorithm, and shortens access token lifetime.

Running both side-by-side helps you clearly see how an insecure choice leads to a real weakness, and how proper safeguards stop the attack.

Frontend: keep it the same, make it work

We did not rebuild the user interface — the supplied frontend is left intact. The only tiny tweak is telling the browser to include cookies in requests. That lets the secure server use HttpOnly refresh cookies (which JavaScript can’t read) while leaving the UI exactly the same. This keeps the demo realistic: same app, different security models.

Demonstrations: attacks and protections

We prepared simple, repeatable steps to show real-world attacks against the vulnerable server and how they fail against the secure server:

Forged tokens: create a token signed with a known weak secret and show the vulnerable server accepts it.

alg:none trick: craft an unsigned token and show the vulnerable server trusts it (intentional insecure behavior for teaching).

Token theft: copy a token from client storage and reuse it to show why storing tokens in accessible places is risky.

Then we replay the same requests against the hardened server — there the forged or unsigned tokens are rejected because the server checks signatures and token claims, and the refresh strategy limits the impact of theft.

Refresh strategy: rotate and protect tokens

On the hardened server refresh tokens are handled carefully: they are signed with a different secret, tracked server-side, and rotated whenever a refresh happens. Access tokens are short-lived. Together, these choices reduce the damage if a token leaks and prevent simple replay attacks.

Network visibility: what an eavesdropper sees

We captured local traffic during the demos to show something important: over plain HTTP, tokens and credentials are visible in requests (Authorization headers, cookies, or request bodies). Over HTTPS, that same traffic is encrypted and the token payloads are not visible without the TLS keys. This visually proves why TLS is required in production.

Evidence we collected

For each demo we saved the things you’ll want in a report or slide deck:

generated token strings (from the helper tool),

API responses showing whether the server accepted or rejected the token,

browser screenshots showing token storage (for the theft demo),

Wireshark screenshots proving tokens are visible on HTTP and hidden on TLS (when decrypted with session keys for controlled analysis).

Short notes & limitations

The refresh token store is in-memory for the lab (convenient for demos). In production you’d use a persistent store like a database or Redis.

For convenience the demo runs over HTTP locally; in production you must use HTTPS and set secure cookies.

The vulnerable server’s insecure behaviors are there intentionally for teaching — never copy them into real apps.
