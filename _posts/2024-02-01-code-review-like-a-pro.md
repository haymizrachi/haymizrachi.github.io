---
layout:     post
title:      Code Review Like a Pro
date: 2024-02-01 19:00
summary:    Hacking methodology to discover source code vulnerabilities.
categories: jekyll pixyl
---

Hello everyone!
<br />
Welcome to my first blog post! I'm excited to be here :smiley:

Today, I'm going to share with you my research methodology for analyzing and reviewing source code applications to identifying vulnerabilities on Whitebox engagements. 
<br /><br />
The skill of finding bugs and weaknesses inside a code that you didn't write and getting into the developer mind's required to be familiar with code reading - __and a lot from it__, but I can promise to you, it's will <ins>get much easier over time</ins> as long as you practice and deal with code on your daily basis. 

<p align="center">
  <img src="{{ site.url }}/images/code_review_meme.jpg" alt="CR meme"/>
</p>

So, let's begin...

### Approaches to perform Code Review:

There are numerous different ways to investigate code, which are:
* Covering code line by line
* Focusing on low-hanging fruit functions such as Login, Registration, and Password Reset mechanisms
* Greping regex keywords for quick-wins
* Following user input using bottom up and top down approaches

Let's break those things up:

<ins>Covering line by line</ins>
<br />
In cases where the project we are checking is considered large, this approach will consume a significant amount of time to cover end to end. Therefore, it is more suitable for projects with a small number of code files to cover.

<ins>Focus on Low-hanging fruit functions</ins>
<br />
Focusing on low-hanging fruit functions involves prioritizing web application mechanisms where the severity of identifying weaknesses and vulnerabilities is higher due to the complexity of requirements, such as validations, checks, and dependencies on other internal functions, thus we will examine first functions such as Login, Register, Forgot Password, Upload Files, and more.



### The Power of Tools

In this section, we will discuss some awesome tools that can save us a lot of time during our research and automate our efforts.

One of the main thoughts that need to be in your head is - 'How can I save time to make the process more efficient and more focused and achieving better results than the manual process?'

As showed earlier, the process of covering line by line, code by code is  very time-consuming process that we want to avoid. Therefore, we seek to apply the best fit approach discussed above to the specific code project.

* cloc
* graudit
* TruffleHog
* Driftwood

A Security Researcher without a tools is like a baker without his 

A cool tool that I've discovered recently called `cloc`[^1] ('Count Lines of Code') that quickly assess the files content - such as comments, blank lines, and the actual code count.
This tool can give us some first imperssions on the material before we get our hands dirty with the code.

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

### Conclusion

Checkout the [GitHub repository](https://github.com/johno/pixyll) to request,
or add, features.

Thank you for reading!

<br />
__Disclaimer:__ This material is for informational purposes only, and should not be construed as legal advice or opinion. For actual legal advice, you should consult with professional legal services.

---

[^1]: cloc - [https://github.com/AlDanial/cloc](https://github.com/AlDanial/cloc)
