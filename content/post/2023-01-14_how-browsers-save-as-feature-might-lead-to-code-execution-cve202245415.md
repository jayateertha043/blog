---
title: "How Browser’s Save As Feature might lead to Code Execution (CVE-2022–45415)"
author: "Jayateertha Guruprasad"
date: 2023-01-14T13:49:39.430Z
lastmod: 2024-08-31T13:12:01+05:30

description: ""

subtitle: ""

image: "/post/img/2023-01-14_how-browsers-save-as-feature-might-lead-to-code-execution-cve202245415_0.gif" 
images:
 - "/post/img/2023-01-14_how-browsers-save-as-feature-might-lead-to-code-execution-cve202245415_0.gif"


aliases:
- "/how-browsers-save-as-feature-might-lead-to-code-execution-cve-2022-45415-ebaa8711692"

---

Few days ago, while I was exploring browser based bugs, I read a article over internet explaining about a path traversal vulnerability in Safari browser using **Save Page As** Feature.
The file name was generated from content of **<title></title>** tag.
If title tag contained ../characters, The file could be saved in a directory other than desired or default Downloads directory. (If anyone finds the article, feel free to mention the article link in comments)

I wanted to recheck for the same vulnerability in other major browsers, all major browsers were safe from this vulnerability, They were clearly sanitizing user input or checking the canonical path to prevent path traversal vulnerability.

I also noticed that some major browsers such as **Mozilla Firefox** were also clearly generating file name from content of title tag, but sanitizing the input to prevent path traversal vulnerability.

> So, I begin think… **Okay, As input sanitization is in place, Is it possible to exploit in this scenario without path traversal ?**

Remember the file name is still generated using title tag, only difference being canonical check/input sanitization might be present to prevent path traversal vulnerability -> So, we still have control over extension of the file.

Scenario — Host a index.html file with below html content and try opening the hosted content in browser and use **Save Page As** feature of browser. ([Live Preview](https://bafybeibrsggi64rxlwptr3o4oihx6ggwld2yupglqh75axcjd6rldtb75y.ipfs.w3s.link/))

```
<html><head><title>malicious.exe</title></head><body>This is a html file</body></html>
```

**Although the original file is index.html (Which is a html file), it will be saved as malicious.exe as filename is generated with content from title tag.** (You might not reproduce this now, as this will already be fixed in Mozilla Firefox, when you are reading this article)

But, unfortunately you cannot execute a .exe, .bat, .ps1 etc files which are having html content.

I even tried to add binary/executable content inside html file with malicious filename in title tag, still you cannot execute it, as it’s not a valid executable.

So, now we control filename, but content inside file can only be html & we need a code execution…

> Is there a file extension which can have html content, but execute code ?

Here comes, **Microsoft Hyper Text Application** to our rescue, Found that **.hta** extension can contain classic HTML as well as **VBScript/JSScript**.

When .hta file is opened, the content inside it is parsed and executed by mshta.exe. FYI, this is not a new technique and is widely known and used by many APT groups. ([MITRE Reference](https://attack.mitre.org/techniques/T1218/005/))

Hence, made a good POC with html content and filename(index.html) which when saved using **Mozilla Firefox** browser’s **Save Page As** Feature, would result in saving the file with .hta extension (file name — mal.hta) which when opened, results in **code execution/victim getting pwned** !!!

**You can view the POC video for Mozilla Firefox browser below** —

{{<youtube 5PBJ1MlikFk>}}

PASTEBIN link for source code of malicious html file & live hosted preview can be viewed by clicking on [link 1](https://pastebin.com/8NcCBmmL) and [link 2](https://bafybeidgvz56ys2kt6jfgzyyrthotjewcugdh4jtf56igseupy4bh57psi.ipfs.w3s.link/index2.html) respectively.

Reported the same to Mozilla Firefox, found that they were already aware of a similar issue partially but without a security impact.

Seems,**Jefferson Scher** has earlier found & reported that —**Save Page As feature creates erroneous file extension if page title ends with a TLD**.

Once, I reported the issue providing a valid POC & security impact, it was quickly fixed and released.

![](/post/img/2023-01-14_how-browsers-save-as-feature-might-lead-to-code-execution-cve202245415_0.gif#layoutTextWidth)

They also credited [CVE-2022–45415](https://www.mozilla.org/en-US/security/advisories/mfsa2022-47/#CVE-2022-45415) under my name and also provided a good bounty ($xxxx) for finding this vulnerability.

**Report Timeline —**

1. Reported — Oct 04, 2022
2. Fixed — Oct 20, 2022
3. Bounty Awarded — Oct 26, 2022

#### **Liked my article ? Follow me on** [**LinkedIn**](https://www.linkedin.com/in/jayateerthag/)**, Twitter (**[**@jayateerthaG**](https://twitter.com/jayateerthag)**), and **[**Medium**](https://jayateerthag.medium.com/)** for more content about bugbounty, Infosec, cybersecurity and hacking.**

* * *
Written on January 14, 2023 by Jayateertha Guruprasad.

Originally published on [Medium](https://medium.com/@jayateerthag/how-browsers-save-as-feature-might-lead-to-code-execution-cve-2022-45415-ebaa8711692)
