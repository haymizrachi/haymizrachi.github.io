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
<br /><br />

### What is Postman Platform?

Postman is an API tool development platform that simplify the process of building, testing, and managing API's under a group of HTTP requests that called `Collections`. 

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

Postman Workflows is based on a <ins>sequence of HTTP requests</ins> that need to occur in a certain flow to achieve its goal. For example, combining a series of dependent requests (passing some parameters between them) to achieve a BOLA (Broken Object-Level Authorization) vulnerability, iterating over a list of API requests that implement a pagination feature to pull out all of it data, you got the idea :smiley:
<br /><br />

### BOLA Demonstration using Workflow

For our first workflow creation, let's demonstrate it using the first crAPI challenge: accessing details of another user's vehicle location as a <a href="https://github.com/OWASP/crAPI/blob/develop/docs/challenges.md">BOLA vulnerability</a>.

After signing up on the crAPI platform and adding my own vehicle under my profile, there is a blue button called 'Refresh Location.' Clicking this button will trigger a background API GET request including my vehicle's unique UUID as a parameter, providing us with the vehicle's real-time location:

<p align="center">
  <img src="{{ site.url }}/images/my_vehicle_request.png" alt="my_vehicle_request" />
</p>
<p align="center">Getting my vehicle location by passing vehicle's UUID paramter</p>

The response of the request will provide us with the vehicle location using the carId as a UUID parameter. If we can change the carId to a different UUID belonging to another user and receive their vehicle location, we have found a BOLA vulnerability. All we need to do is find a way to obtain the UUIDs of other victims and exploit them in this request:

<p align="center">
  <img src="{{ site.url }}/images/my_vehicle_response.png" alt="my_vehicle_response" />
</p>
<p align="center">Return latitude and longitude vehicle data</p>

By using the community page of the crAPI platform, we can obtain all victims data, including their vehicleId, which represents their car's UUID parameter to be provided in the above request. With this information, we can iterate over each vehicleId and send a request to the `/identity/api/v2/vehicle/UUID_PARAMETER/location` endpoint to receive their vehicle's location and achieve our malicious goal.

<br />
So, let's summarize this into one flow:
1. Retrieve all victims personal information from the `/community/api/v2/community/posts/recent` endpoint.
2. Iterate through each result in the list and extract the vehicleId associated with each victim.
3. For each vehicleId, use it as a UUID parameter and send an HTTP GET request to the `/identity/api/v2/vehicle/UUID_PARAMETER/location` endpoint.
4. Print out the results.

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