**Exploring the website:**

As a penetration tester - your role is to review the website or web application and identify features that could be vulnerable and attempt to exploit them to assess whether they are. These features are part of the website that require user interaction.

Excellent part to start is just with your browser.

Below mentioned is the site review for the Acne IT Support website:

| Feature                 | Endpoint                | Summary                                                                                                                                                       |
| ----------------------- | ----------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Home Page               | `/`                     | This page contains a summary of what Acme IT Support does, along with a company photo of its staff.                                                           |
| Latest News             | `/news`                 | This page contains a list of recently published news articles by the company, and each news article has a link with an ID number (i.e. `/news/article?id=1`). |
| News Article            | `/news/article?id=1`    | Displays the individual news article. Some articles seem to be blocked and reserved for premium customers only.                                               |
| Contact Page            | `/contact`              | This page contains a form for customers to contact the company. It contains name, email, and message input fields, along with a send button.                  |
| Customers               | `/customers`            | This link redirects to `/customers/login`.                                                                                                                    |
| Customer Login          | `/customers/login`      | This page contains a login form with username and password fields.                                                                                            |
| Customer Signup         | `/customers/signup`     | This page contains a user signup form with input fields for username, email, password, and password confirmation.                                             |
| Customer Reset Password | `/customers/reset`      | Password reset form with an email address input field.                                                                                                        |
| Customer Dashboard      | `/customers`            | This page lists the user's submitted tickets to the IT support company and includes a **Create Ticket** button.                                               |
| Create Ticket           | `/customers/ticket/new` | This page contains a form with a textbox for entering the IT issue and a file upload option to create an IT support ticket.                                   |
| Customer Account        | `/customers/account`    | This page allows the user to edit their username, email and password.                                                                                         |
| Customer Logout         | `/customers/logout`     | This link logs the user out of the customer area.                                                                                                             |


**Questions:**

1. What is the endpoint for creating new tickets? --> /customers/ticket/new


**Viewing the page source:**

Most browsers also support putting view-source in front of the URL, for example, view-source:https://www.google.com/.

To view the source of the page, right click and select View Page Source from the menu:

Some of the important parts of page source are as follows:

1. At the top of the page --> you will notices some code starting with <!-- and ending with -->, these are comments. Comments are messages left by the website developer, usually to explain something in the code to other programmers or even notes/reminders for themselves.
2. Links to different pages in HTML are written in anchor tags (these are HTML elements that start with <a), and the link that you'll be directed to is stored in the href attribute.
3. In some of the website --> the page source has a tag called secr that is used to hide hidden link.
4. External files such as CSS, Java Script and images can be concluded using the HTML code. In this example, you will notice that these fiels area stored in the same directory. If you view this directory in your web browser, you should see a configuration error: either a blank page or a 403 Forbidden page with an error stating you don't have access to the directory. Instead, the directory listing feature has been enabled, which actually lists every file in the directory. Sometimes this isn't an issue, and all the files in the directory are safe for public viewing, but in some cases, backup files, source code, or other confidential information could be stored here.
5. Many websites these days aren't built from scratch; they use a framework. A framework is a collection of pre-made code that makes it easy for developers to include common features a website would require, such as blogs, user management, form processing, and more, saving developers hours or days of development time.

Viewing the page source can often give us clues into whether a framework is in use and, if so, which framework and even what version. Knowing the framework and version can be a powerful find, as there may be public vulnerabilities in the framework, and the website might not be using the most up-to-date version. At the bottom of the page, you'll find a comment about the framework and its version in use, along with a link to the framework's website. Viewing the framework's website, you'll see that our website is, in fact, out of date.

**Questions**

1. What is the flag from the HTML comment? --> THM{HTML_COMMENTS_ARE_DANGEROUS} --> Check the comments in the source code, visit the directory to get the flag. That is http://Machine-IP/new-home-beta.
2. What is the flag from the secret link? --> THM{NOT_A_SECRET_ANYMORE} --> Search for secr in the source code and visit the secret site to get the flag
3. What is the directory listing flag? --> THM{INVALID_DIRECTORY_PERMISSIONS}
   ![Directory Listing Flag](../../Images/directory_listing_flag.png)
4. What is the framework flag? --> go to the source code --> End of the source code you will find framework url. In the framework page go to change log , which says V1.2 had an issue where our backup process was creating a file in the web directory called /tmp.zip which potentially could of been read by website visitors. This file is now stored in an area that is unreadable by the public. No use this vulnerabiltiy to be exploited. visit http://Machine_ip/tmp.zip. Zip file gets downloaded and extract the file to get the flag. --> THM{KEEP_YOUR_SOFTWARE_UPDATED}
   ![Framework FLag](../../Images/framework_flag.png)


**Developer Tools - Inspector**

Every modern browser includes developer tools; this is a toolkit used to aid web developers in debugging web applications and gives you a peek under the hood of a website to see what is going on. As pentesters, we can leverage these tools to gain a much better understanding of the web application. We're specifically focusing on three features of the developer toolkit: Inspector, Debugger and Network.

**Inspector:**

The page source doesn't always reflect what's shown on a webpage; CSS, JavaScript, and user interaction can change the page's content and style, so we need a way to view what's been displayed in the browser window at this exact time. The Inspector tab provides a live view of what is currently on the website. In addition to viewing this live view, we can also edit and interact with page elements, which is helpful for web developers to debug issues.

On the Acme IT Support website, click into the News section, where you'll see three news articles. The first two articles are readable, but the third is blocked by a floating notice above the content stating that you need a premium subscription to view it. These floating boxes that block page content are often called paywalls, as they put up a metaphorical wall in front of the content you want to see until you pay.

Right-click the premium notice (paywall), then select Inspect from the menu to open the developer tools at the bottom or right-hand side, depending on your browser or preferences.

In the Inspector tab, you'll now see the elements/HTML that make up the website.

Locate the DIV element with the class premium-customer-blocker, and then click on it. You'll see all the CSS styles in the styles box that apply to this element, such as margin-top: 60px and text-align: center. The style we're interested in is the display: block. If you click on the word block, you can type a value of your own choice. Try typing none, and this will make the box disappear, revealing the content underneath it and a flag. If the element didn't have a display field, you could click the bottom of the last style and add your own. 

**Questions:**

1. What is the flag behind the paywall? --> Change the value shown below into none to remove the paywall and get the flag. --> THM{NOT_SO_HIDDEN}
   ![Paywall Answers](../../Images/paywall_answers.png)


**Developer Tools - Debugger**

This panel in the developer tools is intended for debugging JavaScript, and again is an excellent feature for web developers wanting to work out why something might not be working. But as penetration testers, we can dig deep into the JavaScript code. In Firefox and Safari, this feature is called Debugger, but in Google Chrome, it's called Sources.

Now in the tryhackme lab --> lets use the debuuger feature.

On the Acme IT, click on the contact page. Each time the page loads, you might notice a rapid flash on the screen, we can use the debuugger tab or the sources to discover flashing page and find the flag.

In the Debugger tab, on the left-hand side, you see a list of all the resources the current webpage is using. If you click into the assets folder, you'll see a file named flash.min.js. Clicking this file displays its contents.

Often when viewing JavaScript files, you'll notice that everything is on one line because they've been minimised, meaning all formatting (tabs, spacing, and newlines) has been removed to make the file smaller. This file is no exception; it has also been obfuscated, making it purposely difficult to read and harder for other developers to copy.

We can return some of the formatting by using the Pretty Print option, which looks like two braces { } to make it a little more readable, though due to the obfuscation, it's still difficult to comprehend what is going on in the file. If you scroll to the bottom of the flash.min.js file, you'll see the line: flash['remove']();.

This little bit of JavaScript is what is removing the red pop-up from the page. We can utilise another feature of the Debugger called breakpoints. These are code points we can use to force the browser to stop processing JavaScript and pause the current execution.

If you click line 110 that contains the code above, you'll notice it turns blue.

Questions:

What is the flag in the red box? --> THM{Catch_me_if_you_can}
![Debugger_Answer](../../Images/debugger_results.png)

**Developer Tools - Network**

This tab can be used to track every external request a webpage makes. If  If you click on the Network tab and refresh the page, you'll see all the files the page requests. 

you can use the trash icon if you want clear all the requests and start fresh.

With the Network tab open, try filling in the contact form and pressing the Send Message button. You'll notice an event in the Network tab; this is the form being submitted in the background via AJAX. AJAX is a method for sending and receiving network data in a web application in the background without interfering with the current web page.

Once you examine the request, you will be able to see the request headers, cookie details and HTML response that would help you further help in enumeration and exploitation. Examine the new entry on the Network tab created by the contact form, and view the page the data was sent to reveal a flag.

**Questions**

What is the flag under the Response tab on the contact-msg network request? --> THM{GOT_AJAX_FLAG}
![networkflag](../../Images/networktab-answers.png)

**Storage Tab**

The Storage tab in developer tools lets us view and manage data that a website stores in our browser. This data is stored on the client side and may contain sensitive or interesting information useful during a manual pentest. As pentesters, checking browser storage helps us understand how the application handles authentication, session data, user preferences, and other stored values. 

The Storage tab has the following important options:

1. Local Storage: Stores data persistently in the browser, even after the browser is closed.
2. Session Storage: Stores data temporarily for a single browser tab/session.
3. Cookies: Small pieces of data sent by the server and stored in the browser, often used for sessions and authentication.
4. Cache Storage: Stores cached resources like images, scripts, and API responses for faster loading

Among storage options, cookies are among the most important for a pentester. If you navigate to the Cookies section, you’ll see the data stored on the client side by the website.

This often includes session identifiers, user preferences, and sometimes authentication-related tokens. Cookies also have important security flags. The HttpOnly flag prevents JavaScript from accessing the cookie, helping protect against XSS attacks. The Secure flag ensures the cookie is only sent over HTTPS, and the SameSite attribute helps mitigate CSRF attacks. Carefully reviewing cookies can reveal how the application manages sessions and whether any security best practices are missing.

**Questions:**

What is the value of the HttpOnly flag after logging in? --> false

Create an account in the customers session, after creating open the debugger and anvigate to storage,. go check cookies and in the data check for the httpOnly flag whose value is false as shown below.

![Storage Tab Answers](../../Images/storagetab_answers.png)














   


















