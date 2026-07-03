MERN Stack: The stack (MangoDB, Express.js,React, Node.js) powers a large share of modern SaaS products, internal tools and API Backends. Express is the most deployed Node.js web framework.

Stack Identity : 

MERN apps are the default choice for java script only shops that want one language across the full stack. On ubantu, the typical deployment is node.js from the NodeSource PPA. Express listening portis 5000 or 3000 and Mango DB port is 27017.

A reverse proxy (usually Nginx) sits in front in production, but in misconfigured environments and internal tools, the Express process is often directly exposed.

**Fingerprinting the MERN Stack:**

Lets start with header check:

curl -I 10.64.186.232:3000/

Results obtained are as below:

![Header_check](../../Images/header_check_mern.png)

Look for these responses

| Signal | Value |
|--------|-------|
| X-Powered-By header| Express |
| Set-Cookie header | connect.sid=s%3A... |
| Unhandled route response | Cannot GET /nonexistent (plain text) |
| Frontend root element | In the HTML Body |

X-Powered-By: Express is the primary signal. Express sends this header on every response by default. It is only absent if the developer explicitly called app.disable('x-powered-by') or added the Helmet middleware.

If X-Powered-By is absent, fall back to cookie name and unhandled-route format as secondary signals.

The connect.sid cookie comes from the express-session middleware. It is present when the app uses express-session with saveUninitialized: true (the default for many apps). With saveUninitialized: false, the recommended setting for login sessions, the cookie only appears after a session is created. Absence of connect.sid does not rule out Express.

To confirm the Expres unhandled-route fingerprint, request a nonexistent path from the Attackbox

Run curl http://10.64.186.232:3000/nonexistent

![Express_unhandled-route_fingerprint_check](../../Images/express_unhandled_route_fingerprint_check.png)

An Express app with default settings returns plain text: Cannot GET /nonexistent. This is distinct from Django (which shows an HTML error page), Apache (which shows a styled 403 or 404), and Next.js (which returns an HTML page with a styled error). That plain-text response is unambiguous.

Exploiting MERN:

We have confirmed that the Stack: Express on port 3000 with connect.sid session cookie. In a pentest against a MERN application, the next step after fingerprinting is API surface enumeration. MERN apps commonly expose JSON APIs for profile updates, preferences, and user settings, and developers often write their own utility functions to apply partial updates to user objects. Those utility functions are where prototype pollution typically lives.







