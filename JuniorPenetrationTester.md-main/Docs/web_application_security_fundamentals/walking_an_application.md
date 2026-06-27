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


