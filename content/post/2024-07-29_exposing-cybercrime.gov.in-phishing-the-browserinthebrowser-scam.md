---
title: "Exposing cybercrime.gov.in Phishing: The Browser-In-The-Browser Scam"
author: "Jayateertha Guruprasad"
date: 2024-07-29T07:15:55.941Z
lastmod: 2024-08-31T13:13:07+05:30
description: ""

subtitle: ""

image: "/post/img/2024-07-29_exposing-cybercrime.gov.in-phishing-the-browserinthebrowser-scam_4.gif" 
images:
 - "/post/img/2024-07-29_exposing-cybercrime.gov.in-phishing-the-browserinthebrowser-scam_0.jpeg"
 - "/post/img/2024-07-29_exposing-cybercrime.gov.in-phishing-the-browserinthebrowser-scam_1.jpeg"
 - "/post/img/2024-07-29_exposing-cybercrime.gov.in-phishing-the-browserinthebrowser-scam_2.png"
 - "/post/img/2024-07-29_exposing-cybercrime.gov.in-phishing-the-browserinthebrowser-scam_3.png"
 - "/post/img/2024-07-29_exposing-cybercrime.gov.in-phishing-the-browserinthebrowser-scam_4.gif"


aliases:
- "/exposing-cybercrime-gov-in-phishing-the-browser-in-the-browser-scam-0c0bad0a93d5"

---

Long story short, We have a private WhatsApp/Telegram group which includes renowned infosec people & law enforcement officials from Tamil Nadu.

I received a notification that, there is a new ongoing attack where victim when visited to a malicious site are being redirected to cybercrime.gov.in in a new pop-up window & made to pay fine to cybercrime.gov.in as shown in picture below.

![](/post/img/2024-07-29_exposing-cybercrime.gov.in-phishing-the-browserinthebrowser-scam_0.jpeg#layoutTextWidth)

> **Note: At this point of time I had no direct contact with victim, nor have any other additional information.**

Looking at the image, first thing we will notice that the url is— [https://cybercrime.gov.in/en/k54845–6278.htm](https://cybercrime.gov.in/en/k54845–6278.htm) , The url literally looks like a real & non-phishing url. At first look, people in the group were assuming that cybercrime.gov.in website itself has been hacked & malicious webpage got hosted at Cyber Crime Portal to carry out phishing attack.

On visiting [https://cybercrime.gov.in/en/k54845–6278.htm](https://cybercrime.gov.in/en/k54845–6278.htm), 404 webpage is shown.

Searching web archive for the url also shows no evidences of cybercrime portal getting hacked — [https://web.archive.org/web/20240000000000*/https://cybercrime.gov.in/en/k54845%E2%80%936278.htm](https://web.archive.org/web/20240000000000*/https://cybercrime.gov.in/en/k54845%E2%80%936278.htm)

**My Initial Assumptions of Attack Vector were —**

1. [Browser-In-The-Browser](https://mrd0x.com/browser-in-the-browser-phishing-attack/) (BITB) attack
2. DNS Hijacking
3. Victim Using Compromised WiFi Router, Proxy, VPN Services
4. Hackers exploiting a unknown 0day involving url spoofing in Google Chrome browser

**But let’s analyze the image closely & analyze the evidences —**

![](/post/img/2024-07-29_exposing-cybercrime.gov.in-phishing-the-browserinthebrowser-scam_1.jpeg#layoutTextWidth)

**Observations from Evidence Image —**

1. The box around Close, Minimize & Maximize icon in the browser appeared to be of irregular size & appears to be edited.
2. The social media icons in the Webpage appears to slightly overlap with the browser’s omnibox, which shouldn’t normally happen in a browser, as html content of webpage can never overlap with browser’s renderer.
3. We notice that "2023 INDIA" is written on the webpage & But current date on system is 28–07–2024.
4. We notice that title bar shows — "**National Crime Records Bureau**" (NCRB) text & also has logo of **NCRB**, But the actual website is [https://cybercrime.gov.in/](https://cybercrime.gov.in/), whose original title should be "**Cyber Crime Portal"**.
5. The url from evidence is [https://cybercrime.gov.in/en/k54845–6278.htm](https://cybercrime.gov.in/en/k54845–6278.htm) 
6. The text in the webpage obviously looks like a scam 🤣 — "The police will come to your home to arrest you and criminal charges will be filed against you"
7. We also know that, Victim has been redirected to Cyber Crime portal from a malicious website by opening a pop-up window as mentioned earlier.

Based on my observations, I initially assumed that hackers were using a phishing page designed to mimic the appearance of the Cyber Crime Portal. The attack strategy employed in this case appears to be a [Browser-In-The-Browser](https://mrd0x.com/browser-in-the-browser-phishing-attack/) (BITB) attack.

> **How do we confirm our assumptions, we don’t have contact with victim nor we have information about initial malicious website visited by the victim.**

I searched online using **Google Dorks** for texts obtained from the image evidence & found similar cases — [case 1](https://www.ctm360.com/blogs/a-browser-in-the-browser-bitb-attack-cyber-advisory-a-browser-in-the-browser-bitb-attack-100-10/), [case 2](https://shreshtait.com/blog/2024/01/national-crime-records-bureau-your-browser-has-been-locked/), [case 3](https://www.zscaler.com/blogs/security-research/browser-browser-sextortion-scam-makes-victims-pay-imitating-indian-gov).

[Case 2](https://shreshtait.com/blog/2024/01/national-crime-records-bureau-your-browser-has-been-locked/) appears very similar to our case & seems to be using a very similar webpage template as in our case.

![](/post/img/2024-07-29_exposing-cybercrime.gov.in-phishing-the-browserinthebrowser-scam_2.png#layoutTextWidth)

Hence, we can assume from previous attack histories that this should be a phishing case of Cyber Crime Portal carried through [Browser-In-The-Browser](https://mrd0x.com/browser-in-the-browser-phishing-attack/) (BITB) attack.

> **But we still do not have source link of phishing site, How do we find such phishing sites to confirm our assumptions ?**

From our observations — We already noted that — title bar shows "**National Crime Records Bureau**" (NCRB) text & also has logo of **NCRB**, But the actual website is [https://cybercrime.gov.in/](https://cybercrime.gov.in/), whose original title should be "**Cyber Crime Portal"**.

Assuming the hackers has copied the design, template & logo of NCRB to create their phishing page due to laziness (🤣), **We can search for favicon hash of NCRB logo in shodan/censys** to obtain websites/hosts using the same logo of **NCRB**, filtering out the real one’s whose SSL certificate is from *.gov.in/*nic.in.

There are several ways to calculate favicon hash, easiest is to use [https://favicon-hash.kmsec.uk/](https://favicon-hash.kmsec.uk/).

![](/post/img/2024-07-29_exposing-cybercrime.gov.in-phishing-the-browserinthebrowser-scam_3.png#layoutTextWidth)

> Searching the **[**Censys**](https://search.censys.io/search?resource=hosts&sort=RELEVANCE&per_page=25&virtual_hosts=EXCLUDE&q=services.http.response.favicons.md5_hash%3A83b07680f99f120470dbda661c87f62b)** & [**Shodan**](https://www.shodan.io/search?query=http.favicon.hash%3A1588839859) using obtained favicon hash for hosts having Non-Indian IP’s & filtering out SSL having *.gov.in, *.nic.in will give list of all malicious IP’s hosting the phishing pages at scale.**

![](/post/img/2024-07-29_exposing-cybercrime.gov.in-phishing-the-browserinthebrowser-scam_4.gif#layoutTextWidth)

**Sample Malicious IP’s used for phishing cybercrime.gov.in using BITB attack —**

1. [https://2.57.149.44/](https://2.57.149.44/)
2. [https://2.57.149.251/](https://2.57.149.251/)
3. [https://2.57.149.113/](https://2.57.149.113/)
4. [https://2.57.149.98/](https://2.57.149.98/)
5. [http://193.187.175.127/](http://193.187.175.127/)
6. [https://45.152.84.47/](https://45.152.84.47/)

**Finally, shared the list of malicious IP’s with required proofs for blocking those IP’s to **[**Subash Jaganathan**](https://www.linkedin.com/in/subash-j/?originalSubdomain=in)**(**[**@subash_0fficial**](https://x.com/subash_0fficial?lang=en)**), who is a Cyber Crime Investigator & has been assisting Cyber Crime Wing, Tamil Nadu in various cases.**

### Liked my article ? Follow me on [LinkedIn](https://www.linkedin.com/in/jayateerthag/), Twitter ([@jayateerthaG](https://twitter.com/jayateerthag)), and [Medium](https://jayateerthag.medium.com/) for more content about bugbounty, Infosec, cybersecurity and hacking.

* * *
Written on July 29, 2024 by Jayateertha Guruprasad.

Originally published on [Medium](https://medium.com/@jayateerthag/exposing-cybercrime-gov-in-phishing-the-browser-in-the-browser-scam-0c0bad0a93d5)
