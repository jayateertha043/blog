---
title: "GOOGLE REFERER LEAK BUG"
author: "Jayateertha Guruprasad"
date: 2019-09-15T14:43:13.184Z
lastmod: 2024-08-31T13:11:23+05:30

description: ""

subtitle: ""

image: "/post/img/2019-09-15_google-referer-leak-bug_0.png" 
images:
 - "/post/img/2019-09-15_google-referer-leak-bug_0.png"


aliases:
- "/google-referer-leak-bug-434f6293ce66"

---

This is a low hanging bug ,I discovered in Google ,This blog is going to be to short and to the point.

I followed the usual Recon process after enumerating subdomains ,

I selected [https://datastudio.google.com](https://datastudio.google.com).I tried to check for popular vulnerabilities XSS,CSRF,SSRF and What not!!!

But couldn’t find anything .Then I tried to see the features in the website.There was an option to EMBED any site in a report .

I embeded a site and watched the request through BURP suite,I couldn’t believe my eyes ,Private link of the document was passed as referer header to the EMBEDED link.

![](/post/img/2019-09-15_google-referer-leak-bug_0.png#layoutTextWidth)

The impact was that ,A user could EMBED a website which he doesn’t own ,But the website owner can get to know the user’s private link of the report by seeing his logs.

Reported the incident to Google VRP ,and recieved reward of $$$.

Moral:So look for low hanging bugs too ,They may sometimes be unnoticed.

Link to GOOGLE HOF:[https://bughunter.withgoogle.com/profile/46aa4887-b189-4d69-bda2-8f2f5fc569be](https://bughunter.withgoogle.com/profile/46aa4887-b189-4d69-bda2-8f2f5fc569be)

#### **Liked my article ? Follow me on** [**LinkedIn**](https://www.linkedin.com/in/jayateerthag/)**, Twitter (**[**@jayateerthaG**](https://twitter.com/jayateerthag)**), and **[**Medium**](https://jayateerthag.medium.com/)** for more content about bugbounty, Infosec, cybersecurity and hacking.**

* * *
Written on September 15, 2019 by Jayateertha Guruprasad.

Originally published on [Medium](https://medium.com/@jayateerthag/google-referer-leak-bug-434f6293ce66)
