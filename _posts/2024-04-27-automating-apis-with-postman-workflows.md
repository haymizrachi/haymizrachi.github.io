---
layout:     post
title:      Automating API Vulnerabilities Using Postman Workflows
date: 2024-04-27 12:00
summary:    Explore the art of automating and visually demonstrating API vulnerabilities you've identified using Postman Workflows.
categories: security
---

Hello there, Hay is here.
<br />
It's been quite a while since I wrote my last post, so let's begin!
<br /><br />
Today, I'm excited to introduce you a new cool feature by Postman platform that I've recently discovered which has the potential to save you time and enhance your efforts on demonstrating API vulnerabilities.

<p align="center">
  <img src="{{ site.url }}/images/omg_yes_please.jpg" alt="omg_yes_please" />
</p>

When we discuss on providing API PoC vulnerabilities for Bug Hunting programs or customer reports, the classic method involves providing a short Python script to demonstrate the full impact (Dana Epp's done a good job on `this`[^2] - Thank you man!).

In the following article, I'll introduce a new alternative approach that can also be effective in certain cases which focusing on the GUI approach, and doesn't even require any programming skills !

__Note:__ For demostartion purposes, all the presented examples will be showed on the `OWASP crAPI`[^1] project that serves as a vulnerable API's platform for new beginners who want to start their hacking learning process.

### What is Postman Platform?

Postman is an API tool development platform that simplify the process of building, testing, and managing API's under a group of HTTP requests that called 'Collections'. 

This tool offers a wide range of built-in capabilities for interacting with API requests, for example: 
* Load `OpenAPI` specifications as API documentation
* Execute a sequence of HTTP requests using the `Collection Runner` feature
* Interact with `GraphQL` API instances
* Visualizing outcomes using the `Postman Visualizer` tool

And finally the crown jewels: the `Postman Workflows` feature.

So,

### What Postman Workflows is all about?

<p align="center">
  <img src="{{ site.url }}/images/postman_flows_demonstration.gif" alt="postman_flows_demonstration" />
</p>
<p align="center">
    <a href="https://learning.postman.com/docs/postman-flows/gs/flows-overview/">Demonstration by Animation</a>
</p>

Postman Workflows is based on a sequential of HTTP requests that needs to occur in a certian flow in order to achive his goal, for example combining a series of depends requests to achive a BOLA vulnerability, or iterate over a list of API requests that implemented a pagination feature to be able to pull out all of his data, you got the idea.

Postman Workflows is based on a <ins>sequence of HTTP requests</ins> that need to occur in a certain flow to achieve its goal. For example, combining a series of dependent requests to achieve a BOLA (Broken Object-Level Authorization) vulnerability, iterating over a list of API requests that implement a pagination feature to pull out all of it data, you got the idea :smiley:

### Summarizing Up

XXXXXX

I'm putting you a link of `Youtube Playlist`[^4] with additional workflows examples that you can learn and using for your demonstrations for fun and profit.

<br />
Thanks for reading!

<br />
__Disclaimer:__ This material is for informational purposes only, and should not be construed as legal advice or opinion. For actual legal advice, you should consult with professional legal services.

---

[^1]: [OWASP crAPI](https://github.com/OWASP/crAPI)
[^2]: [Writing API exploits in Python by DANA Epp](https://danaepp.com/writing-api-exploits-in-python)
[^3]: [Postman Workflow Documentation](https://learning.postman.com/docs/postman-flows/gs/flows-overview/)
[^4]: [Postman Flows Playlist by Postman](https://youtube.com/playlist?list=PLM-7VG-sgbtCWIWHJSXdJPbahXb_QWWEC&si=kidlwrwYo0boUEK5)