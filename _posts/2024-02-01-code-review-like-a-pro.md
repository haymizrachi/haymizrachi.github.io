---
layout:     post
title:      Code Review Like a Pro
date: 2024-02-19 12:00
summary:    Discover how to uncover vulnerabilities in source code applications and learn key techniques to identify and mitigate security risks effectively.
categories: security
---

Hello everyone!
<br />
Welcome to my first blog post! I'm excited to be here :smile:

<br />
Today, I'm going to share with you my own research methodology for analyzing and reviewing source code applications to identify vulnerabilities on Whitebox engagements. 

Whitebox assessments refer to a specific security testing scenario where the attacker/tester __has prior access__ to the internal workings of the application, including its architecture, schemas or source code files. This access enables them to identify vulnerabilities more quickly and efficiently compared to the blackbox approach, which is what this article will be talking about.

The following article will focus on source code files that we are able to obtain or find using some Recon techniques (which will be explore in a future blog posts).

The skill of finding bugs and weaknesses inside a code that you don't familiar with and getting into the developer mind's required to be experienced with code reading - __and a lot of it__, but I can promise to you, it will <ins>get much easier over time</ins> as long as you practice and deal with code on your daily basis. 

<p align="center">
  <img src="{{ site.url }}/images/code_review_meme.jpg" alt="shut_up_please" />
</p>

So let's get started...

### Approaches to perform Code Review:

There are numerous different ways to investigate code, which are:
* Covering code line by line
* Focusing on low-hanging fruit functions such as Login, Registration, and Password Reset mechanisms
* Greping regex keywords for quick-wins
* Following user input using bottom-up and top-down approaches

Let's break those things up:

#### Covering line by line:
In cases where the project we are checking is considered large, this approach will consume a significant amount of time to cover end to end. Therefore, it is more suitable for projects with a small number of code files to cover.

#### Focus on Low-hanging fruit functions:
This approach involves prioritizing web application mechanisms where the severity of identifying weaknesses and vulnerabilities is higher due to the complexity of requirements, such as validations, checks, and dependencies on other internal functions, thus we will focus on functions such as Login, Register, Forgot Password, Upload Files, and more.

#### Greping regex keywords for quick-wins:
One of the fastest and quickest ways to achieve success in finding vulnerabilities is by looking after a list of keywords and dangerous functions that could potentially lead to weaknesses. Examples include `username`, `password`, `token`, `system`, `shell_exec` `move_uploaded_file`, `file_get_contents`, and other equivalent functions.

__Note:__ Don't forget to adapt your keyword vocabulary to the programming language you're researching.

#### Following user input using bottom-up and top-down approaches:
This strategy is based on the functionality of web applications, which rely on user input and are controlled by it in order to work appropriately. For example, variables like `$_GET`, `$_POST`, `$_COOKIE`, and `$_REQUEST` are commonly used.

The bottom-up and top-down approaches is based on the concepts of `sink` and `source` terms. The former refer to any part of the program that may be influenced by external data, while the latter refer any input or external data that enters a system.
<br /><br />

### The Power of Tools

A Security Researcher without his tools is like a baker without his mixer gentlemens, so we need to discuss on some tools that can save us a lot of time during our research and automate our efforts.

One of the main thoughts that need to be in your head is - 'How can I save time to make the process more efficient and more focused to achieving better results than the manual process?'

As discussed earlier, the process of covering line by line, code by code is  very time-consuming process that we want to avoid. Therefore, we seek to apply the best fit approach presented above to the specific code project we are handle with.

Let's explore some awesome tools:

* cloc
* graudit
* TruffleHog
* Driftwood

### cloc:
A cool tool that I've discovered recently called `cloc`[^1] ('Count Lines of Code') that quickly assess the files content - such as comments, blank lines, and the actual code count.
This tool can give us some first imperssions on the material before we get our hands dirty with the code:

<p align="center">
  <img src="{{ site.url }}/images/cloc.png" alt="cloc_output" />
</p>
<p align="center">cloc categorized the code content by its counts</p>

__Note:__ The 'cloc --show-lang' command will shows us the code languages that supported by this tool:

> ABAP                       (abap)<br />
ActionScript               (as)<br />
Ada                        (ada, adb, ads, pad)<br />
ADSO/IDSM                  (adso)<br />
Agda                       (agda, lagda)<br />
AMPLE                      (ample, dofile, startup)<br />
Ant                        (build.xml, build.xml)<br />
ANTLR Grammar              (g, g4)<br />
Apex Class                 (cls)<br />
Apex Trigger               (trigger)<br />
APL                        (apl, apla, aplc, aplf, apli, apln, aplo, dyalog, dyapp, mipage)<br />
Arduino Sketch             (ino, pde)<br />
AsciiDoc                   (adoc, asciidoc)<br />
ASP                        (asa, ashx, asp, axd)
<br />
..

### graudit:
Another tool that can save us time during our research is the `graudit`[^2] ('grep rough audit') tool. This tool is signature-based that utilizes a large dataset of popular programming codes. Behind the scenes, it runs predefined keywords using the `grep` utility with severity to identify vulnerabilities in a code section:

<p align="center">
  <img src="{{ site.url }}/images/graudit.png" alt="graudit_output" />
</p>
<p align="center">graudit detected some potential vulnerable code requiring further examination</p>

An output results of this tool could be a  line focus on a function that depends on user input or an SQL statement executed without passing through a sanitation check before, or __for god's sake__ - existence of command execution functions that controlled by the end user:

<p align="center">
  <img src="{{ site.url }}/images/rce_born.png" alt="how_rce_are_born" />
</p>

### TruffleHog

`TruffleHog`[^3] is a powerful open-source tool designed to detect secrets and leaked credentials that might be accidentally or intentionally left behind within Git repositories or raw filesystems. It achieves this by scanning all commit histories to identify high-entropy strings, which may indicate the presence of API keys, passwords, and other types of credentials.

As discussed previously, our goal is to identify the quickest wins and expose the crown jewel assets, and this tool comes to help us achieving this.

<p align="center">
  <img src="{{ site.url }}/images/crown_jewel.jpg" alt="crown_jewel_pic" />
</p>

In the picture below, while running the tool against a GitHub repo, we can see the tool is able to find an exposed SSH private key, an API key of the PagerDuty asset, and even a MongoDB database connection string:

<p align="center">
  <img src="{{ site.url }}/images/trufflehog_secrets.png" alt="trufflehog_secrets_output" />
</p>

By the way, there is also a cool TruffleHog extension specifically built for the `Google Chrome`[^4] that monitors API keys and credentials on websites we visit. Once the extension found one, it immediately alerts with a popup on the screen:

<p align="center">
  <img src="{{ site.url }}/images/trufflehog_chrome_ext.png" alt="trufflehog_chrome_plugin" />
</p>
<p align="center">Secrets revealed without the need to dig into the source code</p>

### Driftwood

Additional tool from the same open-source creators is `Driftwood`[^5], which continues the process of TruffleHog's tool output once a private key is found.

Many times, we come a across hard-coded private SSH/TLS keys, and we're not always certain about which asset they belong to. Private keys have a small number of use cases that typically used for TLS and SSH protocols.

So, the purpose of this tool is to take a given Private Key, derive its Public Key component, and then perform lookups on a large database set of known exposed public keys in order to determine its belonging asset:

<p align="center">
  <img src="{{ site.url }}/images/driftwood_output.png" alt="driftwood_output_poc" />
</p>
<p align="center">Found 2 matches of TLS certificates to a given Private Key</p>

You can read more about [here](https://trufflesecurity.com/blog/driftwood-know-if-private-keys-are-sensitive/) to figure out how it really works behind the scenes.
<br /><br />

### Wrapping Up

The ideas and techniques presented in this article are just the tip of the iceberg in becoming a Code Reviewer master. I hope you learned one or two things you didn't knew before.

For those who want to take this a step further, I highly recommend the OSWE[^6] course by Offensive Security to gain additional hands-on practice covering all the necessary knowledge and techniques to achive this goal.

<br />
Thank you for reading!

<br />
__Disclaimer:__ This material is for informational purposes only, and should not be construed as legal advice or opinion. For actual legal advice, you should consult with professional legal services.

---

[^1]: cloc - [https://github.com/AlDanial/cloc](https://github.com/AlDanial/cloc).
[^2]: graudit - [https://github.com/wireghoul/graudit](https://github.com/wireghoul/graudit).
[^3]: TruffleHog - [https://github.com/trufflesecurity/trufflehog](https://github.com/trufflesecurity/trufflehog).
[^4]: TruffleHog for Chrome - [https://chromewebstore.google.com/detail/trufflehog/](https://chromewebstore.google.com/detail/trufflehog/bafhdnhjnlcdbjcdcnafhdcphhnfnhjc).
[^5]: Driftwood - [https://github.com/trufflesecurity/driftwood](https://github.com/trufflesecurity/driftwood).
[^6]: WEB-300 - [https://www.offsec.com/courses/web-300/](https://www.offsec.com/courses/web-300/).

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
        data-theme="preferred_color_scheme"
        data-lang="en"
        crossorigin="anonymous"
        async>
</script>
💡 Prefer not to sign in here? You can comment directly on the related GitHub discussion.