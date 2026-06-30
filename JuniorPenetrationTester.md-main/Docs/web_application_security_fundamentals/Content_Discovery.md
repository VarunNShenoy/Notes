**Manual Discovery - Common Files**

**Robots.txt**

The robots.txt file tells search engine crawlers which pages they may index. Site owners often list sensitive directories here to prevent them from appearing in search results, making it a ready-made list of interesting locations for a penetration tester.

This robots.txt file tells web crawlers - how to interact with the site. It allows all bots to access most of the site (Allow: /) but asks them not to visit /staff-portal. Keep in mind, this is only a guideline for bots, not a security control, so restricted paths may still be accessible if visited directly.

**sitemap.xml:**

Unlike robots.txt (which restricts crawlers), sitemap.xml tells search engines which pages the owner wants listed. These files sometimes include staging pages, old content, or URLs that are hard to reach via the normal site.

**Questions**

1. What is the directory in robots.txt that isn't allowed to be viewed by web crawlers? --> /staff-portal
2. What is the path of the secret area found in sitemap.xml? --> /s3cr3t-area

**Manual Discovery -Headers & Framework Stack**

**HTTP Headers**

When a web server responds to a request, it includes HTTP Headers that can reveal technical details. Headers like Server and X-Powered-By often expose the webserver software and the language or framework the application runs on.

Run the following command against the Acme IT Support web server. The -v flag enables verbose output, which includes the response headers:

curl http://10.66.180.84 -v

![HTTP HEADERS](HTTP_Headers.png)

**Framework Stack**

Once you've identified the framework (from the favicon, headers, or by inspecting the page source for comments and copyright notices), visit the framework's own website to learn more. Documentation pages often describe default directory structures, admin panel paths, and default credentials.

**Questions:**

1. What is the flag value from the X-FLAG header? --> Check the response haeaders while ruuning curl http://10.66.180.84 -v --> you will get the flag as THM{HEADER_FLAG}.
2. What is the flag from the framework's administration portal? --> THM{CHANGE_DEFAULT_CREDENTIALS} -->? View the Acme IT Support page source at http://10.66.180.84, there's a comment at the bottom of every page with a link to the framework's website. Follow that link and check the documentation to find the administration portal path. Access that path on the Acme IT Support site and log in with the default  admin / admin credentials to retrieve the flag.

**OSINT - Search Engines & Web Tools**

**Google Dorking/Hacking**

Google's advanced search operators let you filter results in ways that can surface sensitive content indexed from your target. By combining operators, you can find exposed admin panels, leaked documents, and login pages that the site owner never intended to be public.

| Filter   | Example              | Description                                         |
|----------|----------------------|-----------------------------------------------------|
| site     | `site:tryhackme.com` | Returns results only from the specified domain      |
| inurl    | `inurl:admin`        | Returns results with the specified word in the URL  |
| filetype | `filetype:pdf`       | Returns results of a specific file type             |
| intitle  | `intitle:admin`      | Returns results with the specified word in the page title |
| intext   | `intext:password`    | Returns results containing the specified word in the body |
| cache    | `cache:tryhackme.com`| Shows Google’s cached version of the page           |

**Wappalyzer**

Wappalyzer(opens in new tab) is a browser extension and online tool that identifies the technologies a website uses, frameworks, CMS platforms, CDNs, analytics tools, payment processors, and more. It can often detect version numbers, which helps when searching for known vulnerabilities. Install it from your browser's extension store and visit any site to see the tech stack immediately.

**Questions**

1. What Google dork operator limits results to a specific site? --> site:
2. What online tool and browser extension identifies what technologies a website is running? --> Wappalyzer

**OSINT - Repositories and Archives**

**Wayback Machine**: 

The Wayback Machine(opens in new tab) is an archive of the Internet dating back to the late 1990s. Search for a domain, and you'll see every snapshot captured over time. This is useful for finding pages that have been removed from the live site but may still be accessible: old login forms, forgotten API endpoints, or content that was published briefly before being taken down.

**GitHub**

Git(opens in new tab) is a version control system that tracks changes to files over time. GitHub is the most widely used cloud-hosted platform for Git repositories. Developers sometimes accidentally commit sensitive data: API keys, credentials, configuration files, and .env files, before realising the repository is public.

Search GitHub for the company name or domain you're targeting. Once you find a relevant repository, look through the commit history, not just the current files. Sensitive data is often removed in a later commit, but remains in the history.

**S3 Buckets**

Amazon S3(opens in new tab) (Simple Storage Service) is a cloud storage platform that many organisations use to host files and static website content. The URL format for an S3 bucket is https://{name}.s3.amazonaws.com. Bucket owners set permissions, but misconfigurations are common: a publicly accessible bucket can expose files that were never meant to be seen.

Common naming patterns include {company}-assets, {company}-backup, {company}-www, and {company}-dev. Try these patterns against your target's company name. You can also find bucket URLs in the website's page source or in GitHub repositories.

**Questions**

1. What is the website address for the Wayback Machine? --> https://web.archive.org/
2. What URL format do Amazon S3 buckets end in? (Answer starts with a .)  --> .s3.awazonaws.com


















