---
title: "Grafana Admin Panel bypass in Google Acquisition(VirusTotal)"
author: "Jayateertha Guruprasad"
date: 2021-02-20T09:42:45.614Z
lastmod: 2024-08-31T13:11:36+05:30

description: ""

subtitle: ""

image: "/post/img/2021-02-20_grafana-admin-panel-bypass-in-google-acquisitionvirustotal_0.png" 
images:
 - "/post/img/2021-02-20_grafana-admin-panel-bypass-in-google-acquisitionvirustotal_0.png"


aliases:
- "/grafana-admin-panel-bypass-in-google-acquisition-virustotal-c5ecc9d7b8ae"

---

I started with usual subdomain recon of a google acquisition(VirusTotal).This time I used a online subdomain finder service [https://subdomainfinder.c99.nl/](https://subdomainfinder.c99.nl/) for finding subdomains quickly.

Then I found a subdomain [grafana.internal.virustotal.com](https://grafana.internal.virustotal.com/) ,The word internal in the subdomain made me visit that page due to my curiousity.

But unfortunately ,It’s only for authorized users. I searched for grafana endpoints visited /signup and tried to signup for new account,It showed sign up disabled.

Then I thought if oauth sign in was allowed ,I could try logging in using Google, Github or any other service. But everything was disabled for new user sign up.

I understood only way for logging in, is by using existing username and password. As it was a Google acquisition ,I didn’t wanted to try bruteforce attack on username and password(Admin might have very strong username and password).

Ok ,So everything in vain I wanted to explore more endpoints so visited the docs for grafana. I came across [https://grafana.com/docs/grafana/latest/http_api/user/](https://grafana.com/docs/grafana/latest/http_api/user/) which mentioned about endpoints to add,edit users and also change their passwords. But few of the mentioned functionality was available only to the admin.

I also noticed that in the docs page ,`Authorization: Basic YWRtaW46YWRtaW4=` was sent as Basic Auth header in most of the sensitive request. The above header was base64 encoded,So tried to decode it and was surprised by the result:

```
admin:admin
```

So,The basic auth username and password was just admin:admin.

So I made a quick poc, I installed Modify Header Value extension in firefox([https://mybrowseraddon.com/modify-header-value.html](https://mybrowseraddon.com/modify-header-value.html)) and configured it to send the basic authorization header in all requests for [grafana.internal.virustotal.com](https://grafana.internal.virustotal.com/).

Surprisingly, I was logged in as admin with full rights. I also created a new user for myself and granted admin rights to it, created a admin api key and also sent a poc with just simple curl request using the api key generated to demonstrate I have full access to the application.

```
curl -H "Authorization: Bearer API_KEY" https://grafana.internal.virustotal.com/api/dashboards/home
```

![](/post/img/2021-02-20_grafana-admin-panel-bypass-in-google-acquisitionvirustotal_0.png#layoutTextWidth)

Moreover I also came across a page, which was leaking telegram api key through admin panel.The telegram api key was used as a hook to send notifications to a internal telegram group of virustotal.

Again I generated a quick poc using a simple curl request ,through which I can use the bot to send message to anyone in telegram.

```
https://api.telegram.org/bot_API_KEY/sendMessage?chat_id=userid&text=SOME_URL_ENCODED_TEXT
```

So,I have admin access and control over the grafana panel as well as control over the telegram bot.

I made a detailed poc mentioning all the steps to reproduce the bug as well as the impact and submitted to GoogleVRP. The bug was accepted and rewarded $xxx as it was an acquisition of google.

#### **Liked my article ? Follow me on** [**LinkedIn**](https://www.linkedin.com/in/jayateerthag/)**, Twitter (**[**@jayateerthaG**](https://twitter.com/jayateerthag)**), and **[**Medium**](https://jayateerthag.medium.com/)** for more content about bugbounty, Infosec, cybersecurity and hacking.**

* * *
Written on February 20, 2021 by Jayateertha Guruprasad.

Originally published on [Medium](https://medium.com/@jayateerthag/grafana-admin-panel-bypass-in-google-acquisition-virustotal-c5ecc9d7b8ae)
