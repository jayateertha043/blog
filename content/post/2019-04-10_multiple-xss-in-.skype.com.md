---
title: "Multiple xss in *.skype.com"
author: "Jayateertha Guruprasad"
date: 2019-04-10T06:47:27.819Z
lastmod: 2024-08-31T13:11:17+05:30

description: ""

subtitle: ""

image: "/post/img/2019-04-10_multiple-xss-in-.skype.com_0.png" 
images:
 - "/post/img/2019-04-10_multiple-xss-in-.skype.com_0.png"
 - "/post/img/2019-04-10_multiple-xss-in-.skype.com_1.png"
 - "/post/img/2019-04-10_multiple-xss-in-.skype.com_2.png"


aliases:
- "/multiple-xss-in-skype-com-81d65919ed24"

---

> **PART 1:**

To keep it simple ,I want to make this blog to the point ,instead of writing a script for MahaBharath !!!

How It all started?

I was thinking of services provided by microsoft, Skype came to my mind.

I tested out skype but couldn’t find anything ,Then after some usual recon ,I found a subdomain manager.skype.com.If your visiting the website as first time the following pop up will appear asking for name of group.

![](/post/img/2019-04-10_multiple-xss-in-.skype.com_0.png#layoutTextWidth)

I entered the payload **```"><svg/onload=confirm(document.domain)>```** and clicked continue.

Guess what ??? Nothing happened then ,I visited My profile Section and was surprised too see the payload got executed!!!

![](/post/img/2019-04-10_multiple-xss-in-.skype.com_1.png#layoutTextWidth)

Then I started researching why this Xss has popped ,I opened the source code and found that Group name was already sanitized but there was a reflection of it in a option called ("make this member admin of group_name"),This is were the input was not sanitized and was the cause of xss.

![](/post/img/2019-04-10_multiple-xss-in-.skype.com_2.png#layoutTextWidth)

I sent this report with the following impacts to let them know severity of what xss can do other than getting cookies of user :

1. Stored xss is reflected in many places ,wherever same input is passed without sanitizing it,Thus if a admin of website visits the profile for inspection etc ,admin can be exploited.

2. XSS attacks can be used to steal cookies and login as user.

3. It can be used to control browser by using a js shell. This is very greatly explained by brutelogic [here](https://brutelogic.com.br/blog/using-xss-to-control-a-browser/).

4. Can be used to redirect user to malicious sites.

5. Possibility of account takeover and user data leak.

This was my first report which I sent to Microsoft for this issue .

But I mentioned Multiple Xss right??? But this was a self-stored xss,How can I make it to affect other users ?

using CSRF??? no — The website was secure from csrf too which uses skype vrf tokens .

But I found a way to escalate XSS to other users that too with 0 user interactions .

How??? Lets’s see that in my next blog to keep this short.

#### **Liked my article ? Follow me on** [**LinkedIn**](https://www.linkedin.com/in/jayateerthag/)**, Twitter (**[**@jayateerthaG**](https://twitter.com/jayateerthag)**), and **[**Medium**](https://jayateerthag.medium.com/)** for more content about bugbounty, Infosec, cybersecurity and hacking.**

* * *
Written on April 10, 2019 by Jayateertha Guruprasad.

Originally published on [Medium](https://medium.com/@jayateerthag/multiple-xss-in-skype-com-81d65919ed24)
