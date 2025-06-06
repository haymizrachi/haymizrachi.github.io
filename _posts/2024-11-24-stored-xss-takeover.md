---
layout:     post
title:      From XSS Vulnerability to Full Admin Access
date: 2024-11-25 12:00
summary:    A Real-World Case Study, How I Took Over an Entire Application Using a Classic XSS Vulnerability.
categories: security
---

Welcome back, folks!
<br /><br />
In this post, I'll walk you through a recent real-world engagement where I exploited a stored XSS vulnerability to gain admin access, even with cookie enabled 
protection by `HTTPOnly` flag. I'll also demonstrate how I able to bypass this security measure to take over the administrative account.
<br /><br />
Stay Tuned!
<br /><br />
__A disclaimer before we begin:__ The attack scenario was conducted with the formal approval of the customer.

<p align="center">
  <img src="{{ site.url }}/images/cookie_meme.jfif" alt="cookie_meme" />
</p>
<br />
Let’s start with a brief explanation of what `HTTPOnly` protection is and how it helps developers prevent attackers from stealing account cookies.
<br /><br />
While we're talking about Cookies, this mechanism is what authenticates a user and distinguishes each one based on their privileges in a web application. Cookies come with various security measures to enhance their safe usage and protect the end user.

Some of the available Cookies attributes are:

<p align="center">
  <img src="{{ site.url }}/images/setcookie_attrs.png" alt="setcookie_attrs" />
</p>
<p align="center"><a href="https://www.php.net/manual/en/function.setcookie.php">setcookie entry on PHP.net documentation</a></p>

`HTTPOnly` explanation by PHP.net:
<br /><br />
`When true the cookie will be made accessible only through the HTTP protocol. This means that the cookie won't be accessible by scripting languages, such as JavaScript.`
<br /><br />
__Oh, no!__
<br /><br />
What does it mean when, despite our injected XSS payload being part of the HTML DOM, we still can't access the cookie value to impersonate the victim for an account takeover.
<br /><br />
We need to explore alternative pathways to exploit our XSS injection point:

<p align="center">
  <img src="{{ site.url }}/images/more_than_xss.jpg" alt="more_than_xss" />
</p>
<br />
After a basic explanation of what `HTTPOnly` is, let's dive deeper into our attack flow:

Our engagement begins with a client who owns a social media platform featuring user-generated posts and a comments section for interactions from other users.
<br /><br />
The comments page of the application looks something like this:

<p align="center">
  <img src="{{ site.url }}/images/user_comments_section.png" alt="user_comments_section" />
</p>

The first thing that immediately comes to my mind is injecting a test XSS payload to see if it's reflected back (either temporarily or persistently) to the frontend of the application.
<br /><br />
Unfortunately, this was not the case here due to CloudFlare protection implemented by the admin during the application deployment:

<p align="center">
  <img src="{{ site.url }}/images/cf_headers.PNG" alt="cf_headers" />
</p>
<p align="center">CloudFlare was recognized by its known HTTP headers</p>

This will require us to work much harder to bypass this protection and inject our malicious XSS payload.
<br /><br />
We can either try to peel off the CloudFlare Proxy protection and expose the underlying IP address of the web server using some Recon techniques, or find an HTML / JavaScript payload that will 'fool' CloudFlare into marking our payload as valid to be running on the frontend.

This time, I chose to go with the latter option of the two.
<br /><br />
__Spoiler Alert__: It's going to hurt...

After several attempts and trying to inject classic XSS payloads from the <a href="https://github.com/Proviesec/xss-payload-list">xss-payload-list</a>, all of the XSS payloads I inserted were filtered out by the CloudFlare proxy and marked as non-executable.

At this point, I realized I needed to try harder and find a dedicated payload __specifically__ designed to bypass CloudFlare protection.

Finally, I came up with a working payload that was reflected back into the DOM of the HTML, which was:

<p align="center">
  <img src="{{ site.url }}/images/xss_cf_bypass.PNG" alt="xss_cf_bypass" />
</p>
<p align="center">An hidden UI payload using display none css attribute</p>
<br />
And we received a callback request to our temporary webhook from the XSS!

<p align="center">
  <img src="{{ site.url }}/images/webhook.png" alt="webhook" />
</p>

__Note:__ it's important to mention that we're dealing with a <ins>Stored XSS</ins>, as the comments are saved in the application database and are extracted every time a user visits the relevant post's comment section.

Let's continue on..

At this point, we have a verified injection point, but we don't want to stop here. We want to find a way to fully exploit the potential impact of this vulnerability.
<br /><br />
Stealing the administrator's cookie value is __out__ (remember the `HTTPOnly` flag in place?)
<br />
So, I came to the realization that we could manipulate the HTML DOM directly instead.

Getting back to the Recon stage..

During the initial stage of discovering the web application, my Wappalyzer Chrome plugin identified that the web app was written in Python:
<br /><br />
<p align="center">
  <img src="{{ site.url }}/images/wappalyzer.PNG" alt="wappalyzer" />
</p>

In the market today, there are a few popular Python frameworks for web development, such as Django and Flask.

Digging into the HTML source code reveals the use of the Django framework in this case:

<p align="center">
  <img src="{{ site.url }}/images/django_framework.png" alt="django_framework" />
</p>
<p align="center">Javascript source code exposed Django technology</p>

This framework is typically managed through the `/admin` path, with a page titled 'Django Administration':

<p align="center">
  <img src="{{ site.url }}/images/django_admin.png" alt="django_admin" />
</p>

Of course at this point I didn't have any administrative privileges and got rejected by a message of unauthorized access when attempting to access the page using my already logged-on cookie:

<p align="center">
  <img src="{{ site.url }}/images/django_admin_rejected.png" alt="django_admin_rejected" />
</p>

__Hold your breath... the best part is yet to come..__
<br /><br />
So, a brilliant idea came into my mind! We can send behind-the-scenes XHR/AJAX requests to the `/admin` path on behalf of the authenticated user (one with admin privileges of course).
<br /><br />
This would allow us to steal the HTML content of the Django Admin page, extract the CSRF nonce token, and then send GET/POST requests.
<br /><br />
Since we're operating within the same domain origin, no any Same-origin policy violations occur in this scenario for the returned response content, enabling us to modify, delete, and alter client data, including resetting their personal passwords 😈

And the jackpot is that <ins>no phishing campaign or any account referrals</ins> to my XSS page were needed since the comment page is publicly accessible to everyone!

The final payload attack used a double JavaScript `fetch` functions call to first retrieve the `/admin` page contents, and then send the response back to my webhook instance:

<p align="center">
  <img src="{{ site.url }}/images/fetch_xss_payload.jfif" alt="fetch_xss_payload" />
</p>
__Bingo!__
<br /><br />
The administrative Django content was captured under the `Raw Content` section as below:

<p align="center">
  <img src="{{ site.url }}/images/webhook_django_content.png" alt="webhook_django_content" />
</p>

And finally, the rendered HTML page of the crown jewels 🥳

<p align="center">
  <img src="{{ site.url }}/images/final_django_admin_page.png" alt="final_django_admin_page" />
</p>

### Conclusion

This article demonstrated some unconventional uses of a simple XSS attack, even with strong protections like `HTTPOnly` in place.

Never underestimate a simple vulnerability like XSS, even in today’s fast-paced, advanced technology-driven webapps. Your imagination can take you to a new heights - use it wisely!

<br />
Thanks for reading!
<br /><br />
__Disclaimer:__ This material is for informational purposes only, and should not be construed as legal advice or opinion. For actual legal advice, you should consult with professional legal services.

---

[^1]: [PlantUML Online Editor](https://www.plantuml.com/)
[^2]: [Online PHP Unserializer](https://www.unserialize.com/)

<script src="https://giscus.app/client.js"
        data-repo="haymizrachi/haymizrachi.github.io"
        data-repo-id="R_kgDOLLvfpA"
        data-category="Announcements"
        data-category-id="DIC_kwDOLLvfpM4CpMZa"
        data-mapping="pathname"
        data-strict="0"
        data-reactions-enabled="1"
        data-emit-metadata="0"
        data-input-position="top"
        data-theme="light"
        data-lang="en"
        crossorigin="anonymous"
        async>
</script>
💡 Prefer not to sign in here? You can comment directly on the related GitHub discussion.