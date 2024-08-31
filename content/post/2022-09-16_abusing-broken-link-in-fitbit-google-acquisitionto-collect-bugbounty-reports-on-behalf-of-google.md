---
title: "Abusing Broken Link In Fitbit (Google Acquisition)To Collect BugBounty Reports On Behalf Of Google !"
author: "Jayateertha Guruprasad"
date: 2022-09-16T06:24:59.683Z
lastmod: 2024-08-31T13:11:47+05:30

description: ""

subtitle: ""

image: "/post/img/2022-09-16_abusing-broken-link-in-fitbit-google-acquisitionto-collect-bugbounty-reports-on-behalf-of-google_0.png" 
images:
 - "/post/img/2022-09-16_abusing-broken-link-in-fitbit-google-acquisitionto-collect-bugbounty-reports-on-behalf-of-google_0.png"
 - "/post/img/2022-09-16_abusing-broken-link-in-fitbit-google-acquisitionto-collect-bugbounty-reports-on-behalf-of-google_1.jpeg"


aliases:
- "/abusing-broken-link-in-fitbit-google-acquisition-to-collect-bugbounty-reports-on-behalf-of-google-5885a556eb7c"

---

![](/post/img/2022-09-16_abusing-broken-link-in-fitbit-google-acquisitionto-collect-bugbounty-reports-on-behalf-of-google_0.png#layoutTextWidth)

I usually track acquisitions of websites for which I am hunting bugs regularly.

I knew that Fitbit acquisition has been completed by Google and is eligible for bounty in GoogleVRP platform.

But, I previously remember that, Fitbit was also part of some other bugbounty platform before Google’s acquisition, So wanted to make sure that I am reporting to correct platform.

Hence, I made a simple Google [**search**](https://www.google.com/search?q=fitbit+bugbounty) and found this broken [**link**](https://bugcrowd.com/fitbit) in official website of Fitbit in the 1st [**page**](https://help.fitbit.com/articles/en_US/Help_article/1758.htm) of Google result.

Now, as the reported vulnerability is fixed, you can visit the [**archive**](https://web.archive.org/web/20220320194903/https://help.fitbit.com/articles/en_US/Help_article/1758.htm) to see how it was, when I reported.

**It means that, although the acquisition is fully complete by Google, The website mentions that vulnerabilities found in Fitbit should be reported through — Bugcrowd.**

Although Bugcrowd may not host a malicious page at this broken [**link**](https://bugcrowd.com/fitbit) and start collecting Bugbounty reports from security researchers, By following zero trust for better security —It’s suggested not to trust any entity blindly whether internal or external !

**Impact**:

> Attacker might create a new company in bugcrowd with that url and may take vulnerability reports from actual reported and exploit.

> Bugcrowd platform itself may exploit this. (Although they might not, there is still a possibility)

Hence, created a nice report and submitted via GoogleVRP platform, This was triaged to **Trust & Safety team**, as the reported issue was identified as an **Abuse Risk**.

I was hoping for a good bounty, but unfortunately I was awarded with only honorable mentions, But as I am already in Google’s [**Hall Of Fame**](https://bughunters.google.com/profile/46aa4887-b189-4d69-bda2-8f2f5fc569be) (Leaderboard), This wouldn’t be much beneficial for me.

As this is my first abuse risk report not a usual vulnerability report, I asked the team how the severity was assessed and evaluated to learn more.

![](/post/img/2022-09-16_abusing-broken-link-in-fitbit-google-acquisitionto-collect-bugbounty-reports-on-behalf-of-google_1.jpeg#layoutTextWidth)

**Timeline**:

Reported — Aug 10 2022

Triaged — Aug 11 2022

Accepted — Aug 16 2022

Fixed — Sep 9 2022

#### **Liked my article ? Follow me on** [**LinkedIn**](https://www.linkedin.com/in/jayateerthag/)**, Twitter (**[**@jayateerthaG**](https://twitter.com/jayateerthag)**), and **[**Medium**](https://jayateerthag.medium.com/)** for more content about bugbounty, Infosec, cybersecurity and hacking.**


* * *
Written on September 16, 2022 by Jayateertha Guruprasad.

Originally published on [Medium](https://medium.com/@jayateerthag/abusing-broken-link-in-fitbit-google-acquisition-to-collect-bugbounty-reports-on-behalf-of-google-5885a556eb7c)
