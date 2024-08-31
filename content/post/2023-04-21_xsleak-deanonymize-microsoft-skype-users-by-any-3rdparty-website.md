---
title: "XS-Leak: Deanonymize Microsoft Skype Users by any 3rd-party website"
author: "Jayateertha Guruprasad"
date: 2023-04-21T07:04:13.106Z
lastmod: 2024-08-31T13:12:07+05:30

description: ""

subtitle: ""

image: "/post/img/2023-04-21_xsleak-deanonymize-microsoft-skype-users-by-any-3rdparty-website_0.jpeg" 
images:
 - "/post/img/2023-04-21_xsleak-deanonymize-microsoft-skype-users-by-any-3rdparty-website_0.jpeg"
 - "/post/img/2023-04-21_xsleak-deanonymize-microsoft-skype-users-by-any-3rdparty-website_1.jpeg"


aliases:
- "/xs-leak-deanonymize-microsoft-skype-users-by-any-3rd-party-website-69849e4501a8"

---

XS-Leaks — These are class of vulnerabilities derived from side channel attack. Although browsers have security features like SOP, which prevents access to data of websites from different origin. Browsers support various interactions between different web applications such as embedding a image, loading a sub-resource, postMessage, navigation to different websites etc

XS-Leaks exploit small pieces of information that are exposed during interactions between websites, despite the security mechanisms in place to constrain these behaviors.

> **XS-Leaks through Error Events — when a website loads a resource from another website using HTML tags like ‘img’ or ‘script’. Depending upon the response status code of the loaded resource ‘onerror’ and ‘onload’ events are triggered, which can inadvertently expose sensitive information.**

**Vulnerability Summary —**

**An XS-Leak vulnerability in Skype allows attackers to deanonymize and track users without their knowledge**. The attacker can exploit this vulnerability by sending an image attachment to the victim and taking note of the image’s URL. With this information, they can create a malicious website that tries to load the same URL. As only the victim and attacker can access the image, When the victim accesses the website, the ‘onload’ event is triggered, whereas the ‘onerror’ event is triggered for other users. By leveraging this behavior, The attacker can **de-anonymize & trace the victim’s online activities** without requiring the victim to accept any cookies from the malicious website.

**Proof Of Concept**

{{< youtube BhR-xKsT8DM >}}

**Proof Of Concept Code**
{{<highlight html>}}
<html>
<head></head>
<body>
<img src="https://api.asm.skype.com/v1/objects/SECRET/views/imgpsh_fullsize_anim" onload="alert('User Jayateertha Detected !')" onerror="alert('Not User Jayateertha')">
</body>
</html>
{{</highlight>}}

**Attack Scenario**

1. An organization/attacker sends image attachments to multiple skype users & note’s down the image link of attachment.
2. Organization/attacker crafts a malicious website which includes the skype image attachment urls along with onerror/onload event attached to img tag to pinpoint the victim & track his activities online.
3. Think of a large organization exploiting this vulnerability by embedding multiple such urls to track users , They could use this to pinpoint victim to Microsoft Skype account & also track their activities online even if victim is not logged in to organization account/not opted in to use cookies for the site.

**Impact**

![](/post/img/2023-04-21_xsleak-deanonymize-microsoft-skype-users-by-any-3rdparty-website_0.jpeg#layoutTextWidth)

This vulnerability can be used for deanonymization, which can be especially dangerous in certain contexts. For example, a victim who is accessing sensitive information may be at risk if an attacker can deanonymize them. This vulnerability can also be used to track individuals and their activities online.

> **For, ex — Victim is visiting a site where cookies are not being used for tracking & he prefers to perform activities anonymously, But the remote website could use this vulnerability to deanonymize victim to his exact Microsoft Skype account as well as track his activities.**

This vulnerability can lead to significant privacy concerns for individuals using Skype. Victims may not be aware that their anonymous activity in a website is being tracked down to their Microsoft Skype account.

**Fix Suggestions**

1. Don’t set SAMESITE attribute to None
2. Incase, SAMESITE needs to be set to None, use a custom authorization header to access the image with proper authorization checks.
3. Add a query param & value which is different for attacker & victim such that , only if query param & value is present in the request & is that of victim the image can be accessed.

**Reply From Microsoft**

![](/post/img/2023-04-21_xsleak-deanonymize-microsoft-skype-users-by-any-3rdparty-website_1.jpeg#layoutTextWidth)

**References:**

1. [XS-Leak Deanonymize OpenSea NFT Owners](https://www.imperva.com/blog/deanonymizing-opensea-nft-owners-via-xs-leaks-vulnerability/)
2. [Account & User Identification Vulnerability involving XS-Leak eligible for FaceBook Bounty](https://www.facebook.com/whitehat/payout_guidelines/xsleak/?paipv&#61;0&eav&#61;AfZ6bkWYrKD07bqS6grCvYu6xl5wGBpHf-pzRIAHpkE4ifXsorcvmpfGuoIDk2a99S0&_rdr)
3. [Multiple XS-Leak in Google found by Tezranq](https://portswigger.net/daily-swig/new-xs-leak-techniques-reveal-fresh-ways-to-expose-user-information)
4. [XS-Leak in Slack](https://jub0bs.com/posts/2021-10-12-xsleak-stack/)
5. [XS-Leaks.dev](https://xsleaks.dev/docs/attacks/error-events/)

**Time Line**:

1. First Report to MSRC (VULN-097675) — Apr 9, 2023
2. Second Report to MSRC (VULN-098011) for similar vulnerability in Teams— Apr 15, 2023
3. Reply from Microsoft ~ Cannot Fix (VULN-097675) — Apr 11, 2023
4. Third Report to MSRC (VULN-098156) again mentioning impact clearly for VULN-097675 — Apr 18, 2023
5. Report closed by Microsoft ~ Cannot Fix (VULN-098156) — Apr 21, 2023
6. Public Disclosure — Apr 21, 2023

#### **Liked my article ? Follow me on** [**LinkedIn**](https://www.linkedin.com/in/jayateerthag/)**, Twitter (**[**@jayateerthaG**](https://twitter.com/jayateerthag)**), and **[**Medium**](https://jayateerthag.medium.com/)** for more content about bugbounty, Infosec, cybersecurity and hacking.**

* * *
Written on April 21, 2023 by Jayateertha Guruprasad.

Originally published on [Medium](https://medium.com/@jayateerthag/xs-leak-deanonymize-microsoft-skype-users-by-any-3rd-party-website-69849e4501a8)
