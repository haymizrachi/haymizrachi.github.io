---
layout:     post
title:      Code Review Like a Pro
date:       2021-02-01 21:00:00
summary:    Some hacking methodology to review source code vulnerabilities
categories: jekyll pixyll
---

Hello everyone!
<br />
Welcome to my first blog post! I'm excited to be here :smile:

Today, I'm going to share with you my research methodology for analyzing and reviewing source code applications to identifying vulnerabilities on Whitebox engagements. 
<br />
The skill of finding bugs and weaknesses inside a code that you didn't write and getting into the developer mind's required to be familiar with reading code - __and a lot from it__, but I can promise to you, it's will <ins>get much easier over time</ins> as long as you practice and deal with code on your daily basis. 

So, let's begin...

### Approaches to perform Code Review:

There are numerous different ways to investigate code, which are:
* Covering code line by line
* Focusing on low-hanging fruit functions such as Login, Registration, and Password Reset mechanisms
* Greping regex keywords for quick-wins
* Following user input using bottom up and top down approaches

Lets break those things up:

cloc[^1] - is reffering to 'Count Lines of Code'
cloc --show-lang

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
..</br>
..</br>
..</br>

### Conclusion

Checkout the [GitHub repository](https://github.com/johno/pixyll) to request,
or add, features.

Thank you for reading!

---

[^1]: cloc - https://github.com/AlDanial/cloc
