---
title: "2FA Bypass in PickMyCareer.in"
author: "Jayateertha Guruprasad"
date: 2022-05-06T14:37:59.235Z
lastmod: 2024-08-31T13:11:45+05:30

description: ""

subtitle: ""

image: "/post/img/2022-05-06_2fa-bypass-in-pickmycareer.in_1.jpeg" 
images:
 - "/post/img/2022-05-06_2fa-bypass-in-pickmycareer.in_0.jpeg"
 - "/post/img/2022-05-06_2fa-bypass-in-pickmycareer.in_1.jpeg"


aliases:
- "/2fa-bypass-in-pickmycareer-in-8abbde4c4903"

---

I found a 2fa bypass recently in a responsible disclosure program — pickmycareer.in .

The vulnerability allows an attacker to register any mobile number with his account bypassing OTP verifications.

The process is very simple during registration process, attacker gives his own mobile number and receives OTP, enters correct OTP and intercepts request to /api/user/account/register [POST] api endpoint, Here the attacker keeps the OTP unchanged, but changes the mobileNumber param in the request into victim’s mobile number and forwards request.

![Request](/post/img/2022-05-06_2fa-bypass-in-pickmycareer.in_0.jpeg#layoutTextWidth)

As the website fails to validate if OTP associated with Mobile number correctly, attacker is able to register any mobile number with his account bypassing OTP verification.

![Response](/post/img/2022-05-06_2fa-bypass-in-pickmycareer.in_1.jpeg#layoutTextWidth)

The vulnerability is most probably still open and can be exploited, I reported it to support@pickmycareer.in as per guidelines mentioned [here](https://web.archive.org/web/20220302031602/pickmycareer.in/legal/vulnerability-disclosure/) and also tried to contact by many methods such as alternate emails/mobile number/social media handle. As there is no reply for nearly 3 months regarding the fix, posting this now as it might be educational for beginners in infosec/bugbounty community.

#### **Liked my article ? Follow me on** [**LinkedIn**](https://www.linkedin.com/in/jayateerthag/)**, Twitter (**[**@jayateerthaG**](https://twitter.com/jayateerthag)**), and **[**Medium**](https://jayateerthag.medium.com/)** for more content about bugbounty, Infosec, cybersecurity and hacking.**

* * *
Written on May 6, 2022 by Jayateertha Guruprasad.

Originally published on [Medium](https://medium.com/@jayateerthag/2fa-bypass-in-pickmycareer-in-8abbde4c4903)
