---
title: "Zoho QEngine: Arbitrary Local File Read Using File Protocol"
author: "Jayateertha Guruprasad"
date: 2024-12-10T12:00:00.058Z
lastmod: 2024-12-10T12:00:00+05:30

description: ""

subtitle: ""

image: "" 
images:
 - "/post/img/2024-12-10_QEngineTestCaseCode.png"
 - "/post/img/2024-12-10_QEngineLFI.png"

aliases:
- "/2024_12_10_QEngine_Arbitrary_Local_File_Read"

---

[**Zoho QEngine**](https://www.zoho.com/qengine/) is a test automation software to test your code on various devices & browsers before they get released.

🚀 One of its handy functions is openURL(), which lets you load a test URL in a supported browser like Chrome. Sounds neat, right? But as a security researcher, I couldn’t resist digging a bit deeper. 😏

## 🛠️ First Stop: Common SSRF Attack Vectors

Initially like all security researchers, My intinct was telling me to test for good ol' SSRF test cases & check if I am able to access their internal services or cloud metadata urls [169.254.169.254](https://book.hacktricks.xyz/pentesting-web/ssrf-server-side-request-forgery/cloud-ssrf).

💡 <i>**Fun fact**</i>: **Zoho runs most of its services on its own cloud, no surprise—these URLs didn't fetch anything interesting.**

## 🔄 Switching Gears: Testing Non-HTTP Protocols

What if we try something besides http://? 🤔 Like… the file:// protocol?

💻 Enter: **openURL("file:///etc/passwd", "new tab")**
This should open & display content of **/etc/passwd** file in a new tab, if all goes well !

![](/post/img/2024-12-10_QEngineTestCaseCode.png)

🎆 BOOM! we now get to see the content of /etc/passwd from Zoho QEngine's test environment. 🕵️‍♂️

![](/post/img/2024-12-10_QEngineLFI.png)


## 🔒 Why It’s Low Risk (But Still Cool)

Zoho runs each test case in an isolated Docker environment. So, there’s no sensitive data here to steal—phew! 😌 

Imagine if this was run on a real system without Docker. The impact could be HUGE! 🚨

I made a detailed report of my findings and submitted it to **Zoho's** [BugBounty](https://bugbounty.zohocorp.com/bb/#/submitbug) platform. They confirmed the issue and rewarded me with 💲💲.

## 💡 Key Takeaways

1. Always validate user inputs by enforcing strict whitelisting of protocols, domains, and paths for URLs. This includes checking for internal service URLs and metadata endpoints to prevent unintended access.
2. While Dockerized environments enhance security, don’t rely solely on them for isolation. They might still expose environment secrets, configuration files, or other sensitive data. In some cases, vulnerabilities could even lead to Docker escapes.