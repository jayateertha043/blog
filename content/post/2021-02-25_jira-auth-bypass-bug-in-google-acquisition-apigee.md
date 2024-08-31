---
title: "Jira Auth Bypass bug in Google Acquisition (Apigee)"
author: "Jayateertha Guruprasad"
date: 2021-02-25T14:46:40.403Z
lastmod: 2024-08-31T13:11:39+05:30

description: ""

subtitle: ""

image: "/post/img/2021-02-25_jira-auth-bypass-bug-in-google-acquisition-apigee_0.png" 
images:
 - "/post/img/2021-02-25_jira-auth-bypass-bug-in-google-acquisition-apigee_0.png"


aliases:
- "/jira-authenticated-dashboard-access-in-google-acquisition-apigee-ff20cfe11d99"

---

I was looking for blogs on GoogleVRP reports as well as noting down it’s popular aquisitions.

Then I found a blog ([https://tutorgeeks.blogspot.com/2018/08/misconfigured-jira-setting-apigee.html](https://tutorgeeks.blogspot.com/2018/08/misconfigured-jira-setting-apigee.html) ) which talks about unauthenticated Jira instance leaking dashboard name ,project title and user profile picture by applying filters.

It also mentions ,the website supports only logging in with @apigee.com email address,So I thought why not try logging in using Google OAUTH. I signed in using my Gmail account and got successfully logged in !!!

Now I visited [https://apigeesc.atlassian.net/jira/dashboards?view%3Dpopular](https://apigeesc.atlassian.net/jira/dashboards?view%3Dpopular) ,it was leaking dashboard names and project titles along with user display pictures.

![](/post/img/2021-02-25_jira-auth-bypass-bug-in-google-acquisition-apigee_0.png#layoutTextWidth)

If you visit the link unauthenticated you can’t find these information ,so it’s a Auth Bypass bug I could login to internal Jira instance using OAUTH sign-in using Gmail account.

Now , I didn’t stop here , I ran a nuclei scan against the website with Jira templates.

I found a endpoint through which I can access the same information without authentication. ([https://apigeesc.atlassian.net/rest/api/2/dashboard?maxResults=100](https://apigeesc.atlassian.net/rest/api/2/dashboard?maxResults=100))

This provided JSON output even for unauthenticated users.

So ,I found a way to access dashboard using authenticated way(OAUTH login) and also unauthenticated way using rest API.

**Impact**:

1. Attacker is able to find employee details of apigee working in a specific
project/team.
2. Apigee’s new feature or severe bug in project title can be leaked.
3. Moreover when I was authenticated ,I was also able to create private dashboards.

I made a nice report explaining everything and reported to GoogleVRP.

The bug was accepted, rewarded ($xxx) and fixed.

**References**:

[https://hackerone.com/reports/332586](https://hackerone.com/reports/332586)

[https://tutorgeeks.blogspot.com/2018/08/misconfigured-jira-setting-apigee.html](https://tutorgeeks.blogspot.com/2018/08/misconfigured-jira-setting-apigee.html)

#### **Liked my article ? Follow me on** [**LinkedIn**](https://www.linkedin.com/in/jayateerthag/)**, Twitter (**[**@jayateerthaG**](https://twitter.com/jayateerthag)**), and **[**Medium**](https://jayateerthag.medium.com/)** for more content about bugbounty, Infosec, cybersecurity and hacking.**

* * *
Written on February 25, 2021 by Jayateertha Guruprasad.

Originally published on [Medium](https://medium.com/@jayateerthag/jira-authenticated-dashboard-access-in-google-acquisition-apigee-ff20cfe11d99)
