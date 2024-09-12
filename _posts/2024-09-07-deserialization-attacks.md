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
Let's discuss today on what Deserialization is and give a demonstration example, as it can sometimes can lead to Remote Code Execution (RCE), Privilege Escalation and additional weaknesses with severe impacts on the entire application.
<br /><br />
This time, I was digging deep inside the Internet and discovered a cool Deseralization challenge from '<a href="https://ctftime.org/event/119">Plaid CTF 2014</a>' called 'the kPOP challenge' which will help us better understand this vulnerability in this blog post.
<br /><br />
__Note:__ This challenge can be solved using two different approaches to achieve the same outcome. In this post, we chose to present one of them.
<br /><br />
The CTF source code files can be downloaded directly from <a href="https://github.com/pwning/plaidctf2014/tree/master/web/kPOP">plaidctf2014 </a> Github repo.

<p align="center">
  <img src="{{ site.url }}/images/i_dunno_what_to_choose.jpeg" alt="i_dunno_what_to_choose" />
</p>

<br />
Let's get started –
<br /><br />
Applications, in general, often rely on handling serialized data to function correctly. It's crucial to examine how this data is deserialized to ensure it's done safely. As attackers or researchers, we focus on instances where data is deserialized without proper validation or where serialized input is directly trusted. These deserialization opportunities, known as `sinks` can occur in functions like `unserialize()` and `serialize()` that depend on user-provided input.
<br /><br />
Once we understand what we're looking for, let's take a closer look at the application's source code:
<br /><br />
The first step is to identify the PHP classes used within the application and examine their relationships and interactions. This can be easily done by using the `CTRL+SHIFT+F` shortcut in Visual Studio Code:
<br />
<p align="center">
  <img src="{{ site.url }}/images/vscode_classes_list.png" alt="vscode_classes_list" />
</p>

In order to better understand the relationships between kPOP classes in a more visual way, we can create a UML diagram based on the above class properties. This diagram represents the system's structure and design, illustrating the various classes and their relationships, including inheritance, associations, and dependencies:
<br />

<p align="center">
  <img src="{{ site.url }}/images/kpop_uml.png" alt="kpop_uml" />
</p>
<p align="center">kPOP UML Diagram</p>

<br />
Once we have a basic understanding of the class relations, let's focus on the relevant `sinks` that handle serialization based on user-supplied input.
<br />
Using the same method in VSCode, let's search for all occurrences of the `unserialize` function in the code:
<br /><br />

<p align="center">
  <img src="{{ site.url }}/images/unserialize_function.png" alt="unserialize_function" />
</p>
The search results show three different occurrences, appearing in two files:
<br />
1. classes.php
2. import.php
<br />
<br />
We can see that some of the occurences of `serialize` is depends on SQL return results ($row[0]) which not depends on user input, while the other looks more promising.
<br />
We will focus on import.php file:
<br />
<p align="center">
  <img src="{{ site.url }}/images/unserialize_import_php.png" alt="unserialize_import_php" />
</p>


<br /><br />
The final serialized payload will be as follows, in Base64 format:

`Tzo2OiJMeXJpY3MiOjI6e3M6OToiACoAbHlyaWNzIjtzOjEyOiJzaGVsbF9seXJpY3MiO3M6`
<br />
`NzoiACoAc29uZyI7Tzo0OiJTb25nIjo0OntzOjk6IgAqAGxvZ2dlciI7Tzo2OiJMb2dnZXIi`
<br />
`OjE6e3M6MTI6IgAqAGxvZ3dyaXRlciI7TzoxNDoiTG9nV3JpdGVyX0ZpbGUiOjI6e3M6MTE6`
<br />
`IgAqAGZpbGVuYW1lIjtzOjc6ImNtZC5waHAiO3M6OToiACoAZm9ybWF0IjtPOjEzOiJMb2dG`
<br />
`aWxlRm9ybWF0IjoyOntzOjEwOiIAKgBmaWx0ZXJzIjthOjE6e2k6MDtPOjEyOiJPdXRwdXRG`
<br />
`aWx0ZXIiOjI6e3M6MTU6IgAqAG1hdGNoUGF0dGVybiI7czoxOToiL1xbaVxdKC4qKVxbXC9p`
<br />
`XF0vaSI7czoxNDoiACoAcmVwbGFjZW1lbnQiO3M6OToiPGk+XDE8L2k+Ijt9fXM6NzoiACoA`
<br />
`ZW5kbCI7czoxOiIKIjt9fX1zOjc6IgAqAG5hbWUiO3M6MzU6Ijw/cGhwIHN5c3RlbSgnbHMg`
<br />
`LWw7IGNhdCBmbGFnJyk7ID8+IjtzOjg6IgAqAGdyb3VwIjtzOjExOiJzaGVsbF9ncm91cCI7`
<br />
`czo2OiIAKgB1cmwiO3M6MTk6Imh0dHBzOlwvXC9zaGVsbC5jb20iO319`


We can use the PlantUML Editor[^1] to visualize the decoded payload in a Class Object format:
<br />

<p align="center">
  <img src="{{ site.url }}/images/unserialize_poc.png" alt="unserialize_poc" />
</p>
<p align="center">PHP Class Object representation</p>

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