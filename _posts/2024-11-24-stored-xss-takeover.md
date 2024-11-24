---
layout:     post
title:      How Stored XSS Can Lead to Admin Takeover
date: 2024-11-24 12:00
summary:    A Real-World Case Study, How I Took Over an Entire Application Using a Classic XSS Vulnerability.
categories: security
---

Welcome back, folks!
<br /><br />
In this post, I'll walk you through a recent real-world scenario where I exploited a stored XSS vulnerability to gain admin access, even with cookie enabled 
protection by HTTPOnly flag. I’ll also demonstrate how I able to bypass this security measure to take over the administrative account.
<br /><br />

<p align="center">
  <img src="{{ site.url }}/images/cookie_meme.jfif" alt="cookie_meme" />
</p>

Let’s start with a brief explanation of what HTTPOnly protection is and how it helps developers prevent attackers from stealing account cookies.
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