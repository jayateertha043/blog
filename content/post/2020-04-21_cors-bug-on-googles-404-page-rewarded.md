---
title: "CORS bug on GOOGLE’s 404 page  REWARDED!!!"
author: "Jayateertha Guruprasad"
date: 2020-04-21T13:47:44.641Z
lastmod: 2024-08-31T13:11:29+05:30

description: ""

subtitle: ""

image: "/post/img/2020-04-21_cors-bug-on-googles-404-page-rewarded_0.png" 
images:
 - "/post/img/2020-04-21_cors-bug-on-googles-404-page-rewarded_0.png"
 - "/post/img/2020-04-21_cors-bug-on-googles-404-page-rewarded_1.png"
 - "/post/img/2020-04-21_cors-bug-on-googles-404-page-rewarded_2.jpeg"
 - "/post/img/2020-04-21_cors-bug-on-googles-404-page-rewarded_3.png"


aliases:
- "/cors-bug-on-googles-404-page-rewarded-2163d58d3c8b"

---

This is a story of CORS bug that I found in one of Google’s aquisition -Kaggle,Where I got rewarded for CORS bug in 404 page.

One fine day I was looking at one of the aquisitions of Google-(Kaggle),Kaggle is used worldwide by Machine Learning community and is pretty famous.

I tried looking for CSRF bugs all over website but everything went in vain.I also searched for CORS misconfigurations but couldn’t find anything useful. I visited a 404 page accidentally and looked at it’s source code — It had a script tag whose body contained AntiForgery token which is the same token sent as X-XSRF-TOKEN in all the requests ,This is the CSRF token which is sent as headers in all the sensitive endpoints.

![](/post/img/2020-04-21_cors-bug-on-googles-404-page-rewarded_0.png#layoutTextWidth)

> I changed the origin header of 404 page to mydomain.tld then replayed the request and got the response — The header Access-Control-Allow-Origin and Access-Control-Allow-Credentials was set to mydomain.tld and True respectively.This means any other 3rd party domain is able to get and view response from Kaggle.com.This only happens in the 404 page and is a CORS misconfig in 404 page only!!!

I quickly generated a POC ,which contained 2 parts :

1. Get User specific Kaggle CSRF TOKEN from the 404 page.
2. Using the CSRF Token from recieved from above to generate a API key on behalf of user.

![](/post/img/2020-04-21_cors-bug-on-googles-404-page-rewarded_1.png#layoutTextWidth)

I then reported the GoogleVRP team about this incident with this POC and got rewareded with $$$ ,This time it was lower amount.A bug on Google’s aquisition is rewarded relatively lower compared to a bug on Google.

The GoogleVRP platform is always very friendly and is a great one for security researchers like me.I recommend everyone to try researching in this great Platform.

> I would also like to mention and Thank about the GoogleVRP team’s great decision which benefits most of the Google security researchers community at this crucial COVID-19 time — [https://security.googleblog.com/2020/04/research-grants-to-support-google-vrp_20.html](https://security.googleblog.com/2020/04/research-grants-to-support-google-vrp_20.html)

#### **Liked my article ? Follow me on** [**LinkedIn**](https://www.linkedin.com/in/jayateerthag/)**, Twitter (**[**@jayateerthaG**](https://twitter.com/jayateerthag)**), and **[**Medium**](https://jayateerthag.medium.com/)** for more content about bugbounty, Infosec, cybersecurity and hacking.**

* * *
Written on April 21, 2020 by Jayateertha Guruprasad.

Originally published on [Medium](https://medium.com/@jayateerthag/cors-bug-on-googles-404-page-rewarded-2163d58d3c8b)
