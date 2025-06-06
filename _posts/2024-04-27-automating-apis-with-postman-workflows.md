---
layout:     post
title:      Automating API Vulnerability Testing Using Postman Workflows
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

For our workflow creation, let's demonstrate it using the first crAPI challenge: accessing details of another user's vehicle location as a <a href="https://github.com/OWASP/crAPI/blob/develop/docs/challenges.md">BOLA vulnerability</a>.

After signing up on the crAPI platform and adding my own vehicle under my profile, there is a blue button called `Refresh Location`. Clicking this button will trigger a background API GET request including my vehicle's unique UUID as a parameter, providing us with the vehicle's real-time location:

<p align="center">
  <img src="{{ site.url }}/images/my_vehicle_request.png" alt="my_vehicle_request" />
</p>
<p align="center">Getting my vehicle location by passing vehicle's UUID paramter</p>

The response of the request will provide us with the vehicle location using the carId as a UUID parameter. If we can change the carId to a different UUID belonging to another user and receive their vehicle location, we have found a BOLA vulnerability. All we need to do is find a way to obtain the UUIDs of other victims and exploit them in this request:

<p align="center">
  <img src="{{ site.url }}/images/my_vehicle_response.png" alt="my_vehicle_response" />
</p>
<p align="center">Returned latitude and longitude vehicle data</p>

By using the community page of the crAPI platform, we can obtain all victims data, including their vehicleId, which represents their car's UUID parameter to be provided in the above request. With this information, we can iterate over each vehicleId and send a request to the `/identity/api/v2/vehicle/UUID_PARAMETER/location` endpoint to receive their vehicle's location and achieve our malicious goal:

<p align="center">
  <img src="{{ site.url }}/images/community_vehicle_id_exposure.png" alt="community_vehicle_id_exposure" />
</p>
<p align="center">Community page exposed all victims personal information</p>

<br />
So, let's summarize this into one flow:
1. Retrieve all victims personal information from the `/community/api/v2/community/posts/recent` endpoint.
2. Iterate through each result in the list and extract the vehicleId associated with each victim.
3. For each vehicleId, use it as a UUID parameter and send an HTTP GET request to the `/identity/api/v2/vehicle/UUID_PARAMETER/location` endpoint.
4. Print out the results.

<br />
Workflows come into play to help us automate this entire process. The final workflow will look like this:

<p align="center">
  <img src="{{ site.url }}/images/crapi_vehicle_postman_workflow.png" alt="crapi_vehicle_postman_workflow" />
</p>
<p align="center">BOLA Flow (<a href="https://haymiz.dev//images/crapi_vehicle_postman_workflow.png">Click here for higher picture resolution</a>)</p>

Now, let's break down each part of the graph:

<p align="center">
  <img src="{{ site.url }}/images/workflow_explain_1.png" alt="workflow_explain_1" />
</p>
<p align="center">First part of the Workflow</p>

1. The workflow starts running from the `Start` orange label, serving as the entry point of the flow.
2. Immediately after that, the first HTTP request is executed to the `/community/api/v2/community/posts/recent` endpoint. (This endpoint was previously saved as an HTTP request in my Postman `Collection` section)
3. This block is then divided into two separate parts:
  * Handling the request when it's considered a `Success` (i.e., when a 200 HTTP Status Code is returned).
  * Handling the request when it's considered a `Fail` (i.e., when a 400/500 HTTP Status Code is returned).
4. In this case, we only want to handle the `Success` part. Therefore, at this step, we extract the `HTTP Body` of the response, which contains the victims' data.
5. Next, we iterate over each victim's data returned. To accomplish this, we use the `For` block loop, which functions similarly to the `for` loop in `Python`.
6. From each of the returned victim JSON lists, our focus is on extracting the `vehicleId`, which will be sent to the `/identity/api/v2/vehicle/UUID_PARAMETER/location` endpoint.

<p align="center">
  <img src="{{ site.url }}/images/workflow_explain_2.png" alt="workflow_explain_2" />
</p>
<p align="center">Second part of the Workflow</p>

1. The flow continues by passing the vehicleId from the previous response as a Postman pre-defined `VEHICLE_ID` variable and adding additional `Send Request` block to trigger the BOLA API request with this parameter.
2. Once again, we extract the data directly from the `HTTP Body` section of the returned response.
3. To merge all of the returned victims' data from the BOLA request into one comprehensive output, we utilize the `Collect` block.
4. Finally, we display the victims' real-time vehicle locations using the `Output` block.
<br /><br />

### Summarizing Up

In the end, Postman Workflows can enhance your automation and presentation skills by allowing you to easily build drag-and-drop flows with just a click of a button.

For more coverage on this topic, I'm providing you a link to a `Youtube Playlist`[^4] of a short series presenting additional workflow features. You can learn from and use these for your demonstrations, both for fun and profit.

Thanks for reading!

<br />
__Disclaimer:__ This material is for informational purposes only, and should not be construed as legal advice or opinion. For actual legal advice, you should consult with professional legal services.

---

[^1]: [OWASP crAPI](https://github.com/OWASP/crAPI)
[^2]: [Writing API exploits in Python by DANA Epp](https://danaepp.com/writing-api-exploits-in-python)
[^4]: [Postman Flows Playlist by Postman](https://youtube.com/playlist?list=PLM-7VG-sgbtCWIWHJSXdJPbahXb_QWWEC&si=kidlwrwYo0boUEK5)

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