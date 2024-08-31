---
title: "Blind SSRF in Skype (Microsoft)"
author: "Jayateertha Guruprasad"
date: 2022-10-28T09:25:46.440Z
lastmod: 2024-08-31T13:11:53+05:30

description: ""

subtitle: ""

image: "/post/img/2022-10-28_blind-ssrf-in-skype-microsoft_0.gif" 
images:
 - "/post/img/2022-10-28_blind-ssrf-in-skype-microsoft_0.gif"


aliases:
- "/blind-ssrf-in-skype-microsoft-6639f4961052"

---

Server Side Request Forgery is a vulnerability that allows attacker to make server request to attacker controlled network location/path.

While analyzing requests in Burp for Skype for Web, found a endpoint at **\*.\*.skype.com/path?url=https://example.com** , As the url param appeared interesting tried to change the url with my ngrok instance & got a hit !

Confirmed that it’s Skype which hit the url by looking at the ngrok inspect web console by verifying received User-Agent header(Skype)and IP address in who.is.

Although I was able to make the server hit arbitrary webpage, I couldn’t get full response. I could only get status code, content-type, content-length(size) of response and text content from few selected HTML tags. That’s, it’s not full SSRF as expected, but is a blind/partial SSRF.

Tried to access below paths —

1. localhost/internal ip address -> Failed
2. Tried to bypass localhost/internal ip address using url redirect/url shortner methods -> Failed
3. External ip address/webpage -> Success
4. Common Azure/AWS/DigitalOcean Meta data IP addresses -> Failed
5. Not so commonly used, Azure related IP address (168.63.129.16) -> Success -> This IP can be used to determine VM’s health by using [http://168.63.129.16/metadata/v1/maintenance](http://168.63.129.16/metadata/v1/maintenance) endpoint, which should return OK (200 Status Code) if VM is functioning. (Refer [this](https://learn.microsoft.com/en-us/azure/virtual-network/what-is-ip-address-168-63-129-16?WT.mc_id=docs-azuredevtips-azureappsdev) for more information)

Tried changing url param value to [http://168.63.129.16/metadata/v1/maintenance](http://168.63.129.16/metadata/v1/maintenance) , got 200 Ok response, with size of response as 2 bytes which confirms that response text probably contains OK in response.

Made a nice report mentioning all the details and sat back waiting for Microsoft to reproduce and fix the report.

Fortunately this was in scope for bounty under the M365 Bounty Program and got a nice $$$$ bounty !

![](/post/img/2022-10-28_blind-ssrf-in-skype-microsoft_0.gif#layoutTextWidth)

Report Timeline:

1. Reported — Sep 23, 2022
2. Additional Details Updated — Oct 3, 2022
3. Bounty Rewarded — Oct 8, 2022
4. Fixed — Oct 12, 2022

#### **Liked my article ? Follow me on** [**LinkedIn**](https://www.linkedin.com/in/jayateerthag/)**, Twitter (**[**@jayateerthaG**](https://twitter.com/jayateerthag)**), and **[**Medium**](https://jayateerthag.medium.com/)** for more content about bugbounty, Infosec, cybersecurity and hacking.**


* * *
Written on October 28, 2022 by Jayateertha Guruprasad.

Originally published on [Medium](https://medium.com/@jayateerthag/blind-ssrf-in-skype-microsoft-6639f4961052)
