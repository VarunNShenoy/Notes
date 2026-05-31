Example Pages in an Website:

<img width="985" height="723" alt="image" src="https://github.com/user-attachments/assets/8f7543af-1241-4d61-985d-976ba0287d4a" />

The page source is the human readable code returned to our browser/client from the web server each time we make a request.

The returned code is made up of HTML (Hyper Text Markup Language), CSS (Cascading Style Sheets) and Java Scripts.

**Page Source Details:**

Code starting with <!-- and ending with --> these are comments. Comments are messages left by the website developer, usually to explain something in the code to other programmers or even notes/reminders for themselves. These comments don't get displayed on the actual webpage. This comment describes how the homepage is temporary while a new one is in development. 

Achor Tags: Link to different Pages - (These are elements that start with <a) and the link that you will be redirected to is stored in the href attribute.

External files such as CSS, JavaScript and Images can be included using the HTML code. In this example, you'll notice that these files are all stored in the same directory. If you view this directory in your web browser, there is a configuration error. What should be displayed is either a blank page or a 403 Forbidden page with an error stating you don't have access to the directory. Instead, the directory listing feature has been enabled, which in fact, lists every file in the directory. Sometimes this isn't an issue, and all the files in the directory are safe to be viewed by the public, but in some instances, backup files, source code or other confidential information could be stored here.

Many websites these days aren't made from scratch and use what's called a framework. A framework is a collection of premade code that easily allows a developer to include common features that a website would require, such as blogs, user management, form processing, and much more, saving the developers hours or days of development.

Viewing the page source can often give us clues into whether a framework is in use and, if so, which framework and even what version. Knowing the framework and version can be a powerful find as there may be public vulnerabilities in the framework, and the website might not be using the most up to date version. At the bottom of the page, you'll find a comment about the framework and version in use and a link to the framework's website. Viewing the framework's website, you'll see that our website is, in fact, out of date. 

**Develepor Tools:**

Every Modern browser includes developer tools ; this is a tool kit used to aid web developers in debugging web applications and gives you a peek under the hood of a website to see whats going on. 

Inspector: The page source does not always represent whats shown on a webpage; this is because CSS, Javascript and User Interaction can change the content and style of the page which means we need to a way to view whats been displayed in the browser window at this exact time. Element Inspector assists us with this by providing us with a live presnetation of what currently on the website.

As well as viewing this live view, we can also edit and interact with the page elements, which is helpful for web developers to debug issues.

Sometime you seen a webpage which has boxes that says you need to pay to see the content in the following web page - These are often refered as paywalls.

Right-clicking on the premium notice ( paywall ), you should be able to select the Inspect option from the menu, which opens the developer tools either on the bottom or right-hand side depending on your browser or preferences. You'll now see the elements/HTML that make up the website ( similar to the screenshots below ).

<img width="474" height="286" alt="image" src="https://github.com/user-attachments/assets/bc414def-25d6-4fd4-bbbb-3377ae051122" />

Some of these might be vulnerable which can be exploited - where you can read the content without buying the premium.

Locate the DIV element with the class premium-customer-blocker and click on it. You'll see all the CSS styles in the styles box that apply to this element, such as margin-top: 60px and text-align: center. The style we're interested in is the display: block. If you click on the word block, you can type a value of your own choice. Try typing none, and this will make the box disappear, revealing the content underneath it.


**Developer Tools - Debugger**

This panel in the developer tools is intended for debugging JavaScript, and again is an excellent feature for web developers wanting to work out why something might not be working. But as penetration testers, it gives us the option of digging deep into the JavaScript code. In Firefox and Safari, this feature is called Debugger, but in Google Chrome, it's called Sources.

**Developer Tools - Network**

The network tab on the developer tools can be used to keep track of every external request a webpage makes. If you click on the Network tab and then refresh the page, you'll see all the files the page is requesting. 

AJAX is a method for sending and receiving network data in a web application background without interfering by changing the current web page.













