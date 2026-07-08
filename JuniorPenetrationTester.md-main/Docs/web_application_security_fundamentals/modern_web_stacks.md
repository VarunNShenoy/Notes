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

React Server components and the flight protocol:

The App Router runs React components directly on the server. Instead of shipping JavaScript to the browser, the server executes the component and streams the result to the client using a binary-like format called the RSC Flight protocol. That streaming channel, the endpoint that serves this payload, is the attack surface for CVE-2025-55182.

**Fingerprinting Next.js**

Since port 3000 --> we already know this is running port 3000, automatically when we start npm run dev --> it automatically assignes to port 3001. So lets run the command.

curl -I http://Machine_IP:3001 --> which gives the following result as shown below:

![Next.js_Fingerprinting](../../Images/Fingerprinting_Next.js.png)

| Signal                      | Value                                       | Confidence                  |                                                                                                                                                                                   
| --------------------------- | ------------------------------------------- | --------------------------- | 
| X-Powered-By header         | `Next.js`                                   | High                        |
| HTML source                 | `window.__next_f` present in HTML           | High (App Router indicator) |                                                                 
| Static asset paths          | `/_next/static/chunks/`                     | High                        | 
| Middleware headers          | `x-middleware-rewrite`, `x-middleware-next` | Medium                      |
| Redirect to protected route | HTTP `307` → `/login`                       | Medium                      | 

window.__next_f in the page source is the definitive App Router indicator. It is the hydration array for React Server Component data, injected by Next.js into every App Router page's HTML output. It does not appear in Pages Router or any other framework.

**CVE-2025-29927: Middleware Bypass**

In Next.js, middleware is a function that runs before every request reaches a page. Developers use it as the central gatekeeper; authentication checks, session validation, and redirect logic all live here. Because middleware sits in front of every route, it is the single most common place developers implement access control in Next.js applications.

The /dashboard route in this app is a typical example. The middleware checks for a valid session cookie. Without one, it redirects to /login. Let us confirm that it is working:

Run the command curl -v http://10.64.149.216:3001 - this command has to check, if the middleware is working fine that it needs to redirected to the /login page.

![Middleware_checking](../../Images/Middleware_checking.png)

Now for the vulnerability, Next.js uses an internal headder called x-middleware-subrequest to prevent infinite loops. When middleware calls itself recursively (for example, to forward a modified request to another route), Next.js attaches this header so it knows not to run middleware again on that forwarded request. It is a performance and safety mechanism built into the framework itself. 

The critical flaw: Next.js never checked whether x-middleware-subrequest was coming from an internal process or from an external client. If you include the header in your own request, Next.js treats it the same as an internal subrequest and skips middleware entirely. The authentication check never runs.

The header value encodes the middleware module path, repeated five times. For an app with a root-level middleware.ts file:

Questions: 

1.What HTML artifact in the page source confirms a Next.js App Router application? --> window.__next_f
2.Send the CVE-2025-29927 bypass header to the protected /dashboard route. What flag is displayed on the page? (Answer Format: THM{...}) --> THM{m1ddl3w4r3_byp4ss3d}

![Flag](../../Images/middleware_flag.png)

**Django:**

The Mern and Next.js stack run with node.js whereas Django is the python-native alternative framework that government agencies, newsrooms, and SaaS companies with Python engineering teams reach for first.

CVE-2021-35042 is a SQL injection vulnerability in Django's order_by() query method, rated CVSS 9.8 Critical and requiring no authentication to exploit.

Stack identity:

Django powers a large share of Python-backed web applications. On Ubuntu, it runs under Gunicorn or Django's built-in development server, typically on port 8000. The Django admin panel at /admin/ and CSRF middleware are enabled by default in virtually every Django project. The admin panel alone is a reliable stack signal before you send a single exploit payload.

Fingerprint Django:

curl -I http://Machine_IP:8000/products


-------Need TO fill More Details---DO Later.

Questions

What hidden form field in Django POST forms is a near-certain stack fingerprint? --> csrfmiddlewaretoken

Using manual curl payloads, what is the name of the vulnerable database? --> vuln_db

**LMAP:**

The Four ComponentsThe acronym LAMP represents the four layers of the development 
environment:

1. Linux: The open-source operating system that serves as the base layer for the entire stack.
2. Apache: The web server software that processes incoming HTTP client requests and delivers web assets.
3. MySQL: The relational database management system (RDBMS) used to store and manage application data.
4. PHP: The backend programming language used to write business logic and generate dynamic web content (sometimes substituted with Perl or Python

LAMP (Linux, Apache, MySQL, PHP) is one of the earliest and most widely adopted web application stacks. It became popular because all its components are open-source, stable, and easy to deploy. Linux provides the operating system, Apache handles web requests, MySQL manages the database, and PHP processes dynamic content.

Stack Identity:

On Ubuntu, Apache usually runs under www-data, serves files from /var/www/html, and passes dynamic requests to PHP through mod_php or PHP-FPM. MySQL stores the application data, while PHP handles server-side logic. This classic Linux, Apache, MySQL, and PHP combination creates common attack surfaces such as exposed PHP files, database errors, weak file permissions, and misconfigured Apache/PHP settings.

Fingerprinting the LAMP Stack 

Start with header check using curl, Apache Advertisies in every response

Run the command : curl -I http://Machine_IP:8000

root@tryhackme:~# curl -I http://10.66.133.74:8080/
HTTP/1.1 200 OK
Server: Apache/2.4.49 (Unix)
Last-Modified: Mon, 11 Jun 2007 18:53:14 GMT
ETag: "2d-432a5e4a73a80"
Accept-Ranges: bytes
Content-Length: 45
Content-Type: text/html

The Output says this is a Apache/2.4.49 (UNIX Version), To confirm, we can request an non-existent path to confirm.

Run curl -v http://MachineIP:8000/nonexistent --> Even this confirms that the server is running apache/2.4.9. This exact version maps to CVE-2021-41773 and nothing else. Apache also repeats the version in 404 error page footers.

The final signal is /cgi-bin/. A 403 Forbidden means the directory exists, and listing is disabled. mod_cgi is configured. A 404 would mean it is not present at all. For confirming this run curl -v http://machineip:8000/cgi-bin/

Once Done, look for the header : 

## Enumeration Findings

| Signal | Value | Confidence |
|----------|----------|------------|
| Server Header | `Apache/2.4.49 (Unix)` | High — Exact CVE match |
| 404 Error Page Footer | `Apache/2.4.49` version string | High |
| `/cgi-bin/` Response | `403 Forbidden` (instead of `404 Not Found`) | High — Indicates `mod_cgi` is enabled |

**CVE-2021-41773: The Vulnerability**

Apache 2.4.49 introduced a change to the ap_normalize_path() function. The change inadvertently broke the path traversal filter. Normally, Apache blocks any URL containing ../ before it reaches the filesystem. The bug is in the decode order: the traversal filter runs before full URL decoding.

















































