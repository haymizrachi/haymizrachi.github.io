---
layout:     post
title:      How Stored XSS Can Lead to Admin Takeover
date: 2024-11-24 12:00
summary:    A Real-World Case Study, How I Took Over an Entire Application Using a Classic XSS Vulnerability.
categories: security
---

Welcome back, folks!
<br /><br />
In this post, I'll walk you through a recent real-world engagement where I exploited a stored XSS vulnerability to gain admin access, even with cookie enabled 
protection by `HTTPOnly` flag. I'll also demonstrate how I able to bypass this security measure to take over the administrative account.
<br /><br />
Stay Tuned!

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
Oh, no!
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
The main page of the application looks something like this:

<p align="center">
  <img src="{{ site.url }}/images/user_comments_section.png" alt="user_comments_section" />
</p>

<br /><br />
### Conclusion

XXX

<br />
Thanks for reading!

<br />
__Disclaimer:__ The attack scenario was conducted with formal approval from the customer. This material is for informational purposes only, and should not be construed as legal advice or opinion. For actual legal advice, you should consult with professional legal services.

---

[^1]: [PlantUML Online Editor](https://www.plantuml.com/)
[^2]: [Online PHP Unserializer](https://www.unserialize.com/)