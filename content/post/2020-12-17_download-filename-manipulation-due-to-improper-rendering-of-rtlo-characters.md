---
title: "Download Filename Manipulation due to improper rendering of RTLO characters"
author: "Jayateertha Guruprasad"
date: 2020-12-17T09:43:09.067Z
lastmod: 2024-08-31T13:11:34+05:30

description: ""

subtitle: ""




aliases:
- "/download-filename-manipulation-due-to-improper-rendering-of-rtlo-characters-69e2751a8f28"

---

This is one of the easiest bug that I have found in a private bugbounty program.

The program had two of it’s browsers in it’s scope. I was testing for RTLO related bugs,I found that the downloads section of the browser was rendering the rtlo characters in the improper way.

RTLO characters are "Right-To-Left-Override" characters which is rendered from right to left ,unlike English which is rendered from left to right.

I made a quick POC,

```
<html>
<head><title></title></head>
<body>
<a href="Link_TO_File_With_RTLO" download>apk rendered as txt file in browser downloads click here</a>
</body>
</html>
```

I named a file as textfile%E2%80%AEtxt.apk, which is a apk.But when downloaded in those two browser was rendered as textfilekpa.txt due to the improper handling of RTLO characters.

**IMPACT**: Victim downloads a file thinking it’s text file but ends up installing malicious apk.

The bug was fixed quickly in a month and a new release was rolled out ,The private program also acknowledged me in their HOF and rewarded a small amount.

**References to similar bugs**:

[Illegal Rendered at Download Feature in Opera Mini that Lead to Extension Manipulation (with RTLO)](https://medium.com/bugbountywriteup/illegal-rendered-at-download-feature-in-opera-mini-that-lead-to-extension-manipulation-with-rtlo-685bf2d77d51)

[HackerOne disclosed on HackerOne: Domain spoofing in redirect page](https://hackerone.com/reports/299403)

[Snapchat disclosed on HackerOne: RTLO char allowed in chat](https://hackerone.com/reports/196222)

#### **Liked my article ? Follow me on** [**LinkedIn**](https://www.linkedin.com/in/jayateerthag/)**, Twitter (**[**@jayateerthaG**](https://twitter.com/jayateerthag)**), and **[**Medium**](https://jayateerthag.medium.com/)** for more content about bugbounty, Infosec, cybersecurity and hacking.**

* * *
Written on December 17, 2020 by Jayateertha Guruprasad.

Originally published on [Medium](https://medium.com/@jayateerthag/download-filename-manipulation-due-to-improper-rendering-of-rtlo-characters-69e2751a8f28)
