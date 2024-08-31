---
title: "Intro to Debugging Java Web Servers Without Source Code For Security Researchers"
author: "Jayateertha Guruprasad"
date: 2024-07-16T16:13:28.058Z
lastmod: 2024-08-31T13:12:56+05:30

description: ""

subtitle: ""

image: "/post/img/2024-07-16_intro-to-debugging-java-web-servers-without-source-code-for-security-researchers_12.png" 
images:
 - "/post/img/2024-07-16_intro-to-debugging-java-web-servers-without-source-code-for-security-researchers_0.png"
 - "/post/img/2024-07-16_intro-to-debugging-java-web-servers-without-source-code-for-security-researchers_1.png"
 - "/post/img/2024-07-16_intro-to-debugging-java-web-servers-without-source-code-for-security-researchers_2.png"
 - "/post/img/2024-07-16_intro-to-debugging-java-web-servers-without-source-code-for-security-researchers_3.png"
 - "/post/img/2024-07-16_intro-to-debugging-java-web-servers-without-source-code-for-security-researchers_4.png"
 - "/post/img/2024-07-16_intro-to-debugging-java-web-servers-without-source-code-for-security-researchers_5.png"
 - "/post/img/2024-07-16_intro-to-debugging-java-web-servers-without-source-code-for-security-researchers_6.png"
 - "/post/img/2024-07-16_intro-to-debugging-java-web-servers-without-source-code-for-security-researchers_7.png"
 - "/post/img/2024-07-16_intro-to-debugging-java-web-servers-without-source-code-for-security-researchers_8.png"
 - "/post/img/2024-07-16_intro-to-debugging-java-web-servers-without-source-code-for-security-researchers_9.png"
 - "/post/img/2024-07-16_intro-to-debugging-java-web-servers-without-source-code-for-security-researchers_10.png"
 - "/post/img/2024-07-16_intro-to-debugging-java-web-servers-without-source-code-for-security-researchers_11.png"
 - "/post/img/2024-07-16_intro-to-debugging-java-web-servers-without-source-code-for-security-researchers_12.png"


aliases:
- "/intro-to-debugging-java-web-servers-without-source-code-for-security-researchers-80ff00de4753"

---

Debugging Java web servers in an on-premise environment is crucial for pentesting and source code reviews. It’s possible to easily decompile java based apps using tools like JD-GUI.

Often during dynamic analysis, we feel that, there’s a need to debug the application at runtime as if we had the source code, by placing breakpoints.
**Assuming that we do not have the source code, How do we debug Java based On-Premise Webservers ?**

> "🚀 JD-Eclipse to the Rescue! 🛠️"

### **Steps to Debug On-Premise Java Web Servers Without Source Code —**

First ,Download and Install "**Eclipse IDE for Enterprise Java and Web Developers**".

Then, Download and Install **JD-Eclipse** Plugin from [here](https://github.com/java-decompiler/jd-eclipse) into your Eclipse IDE.

**Configure Eclipse IDE to associate \*.class files without source code to JD Class File Viewer** as follows. This can be done from "**Window > Preferences > General > Editors > File Associations**".

![](/post/img/2024-07-16_intro-to-debugging-java-web-servers-without-source-code-for-security-researchers_0.png#layoutTextWidth)

**Restart the Eclipse IDE** to finish the JD-Eclipse installation setup successfully.

**Install any Java-based web server for pentesting. In this example, we’ll use**[**ManageEngine Endpoint Central**](https://www.manageengine.com/products/desktop-central/)**.**

**Create any Java Project** in Eclipse IDE, For example, in our case I created a Java project with name EC.

Now open the created java project in Eclipse IDE, **create any valid java file inside project source**. It doesn’t matter what the code does.

![](/post/img/2024-07-16_intro-to-debugging-java-web-servers-without-source-code-for-security-researchers_1.png#layoutTextWidth)

Right click on your project folder in Eclipse IDE, Go to "**Properties" -> "Java Build Path" -> "Libraries" -> "Classpath" -> "Add External JARs**" to **Add jar files/libraries associated with the On-Premise software**.

![](/post/img/2024-07-16_intro-to-debugging-java-web-servers-without-source-code-for-security-researchers_2.png#layoutTextWidth)![](/post/img/2024-07-16_intro-to-debugging-java-web-servers-without-source-code-for-security-researchers_3.png#layoutTextWidth)

#### **Enabling remote debugging on java web server —**

This step is dependent on the installed Web server & might require some research & changes depending on your web server,

**You need to configure remote debugging in the webserver by enabling JPDA (Java Platform Debugger Architecture).**

For this, you need to find how the server is started & configure [JPDA](https://stackoverflow.com/a/36420167) before starting the server.

For, **Endpoint Central Server,** By viewing the service properties of "**ManageEngine UEMS — Server**", We find that, The service is started using command "**D:\BB\Zoho\UEMS_CentralServer\bin\wrapper.exe -s D:\BB\Zoho\UEMS_CentralServer\conf\wrapper.conf**".

![](/post/img/2024-07-16_intro-to-debugging-java-web-servers-without-source-code-for-security-researchers_4.png#layoutTextWidth)

Searching for "**JPDA**" word in **wrapper.conf**, shows we need to uncomment few lines to enable JPDA.

![](/post/img/2024-07-16_intro-to-debugging-java-web-servers-without-source-code-for-security-researchers_5.png#layoutTextWidth)

**After configuring the server for JPDA, make sure all changes are saved & restart the server.**

#### **Configure Eclipse IDE for Remote Debugging —**

Go to "**Run**" -> "**Debug Configurations**" -> "**Remote Java Application**" -> Right Click & select "**New Configuration**" & **Configure connection properties like Name, JPDA Host & Port** to use for debugging.

From **wrapper.conf** we find that *:8787 is used for debugging, Hence we can configure Host as localhost, Port as 8787 in Eclipse as follows.

![](/post/img/2024-07-16_intro-to-debugging-java-web-servers-without-source-code-for-security-researchers_6.png#layoutTextWidth)

Name can be configured any, for ex: **EC Debug**.

**Apply and Close the Debug Configurations window** for now after configuring the above parameters.

#### **Setting Breakpoint for Debugging —**

Now, as we have configured everything, we should be able to debug. But we need to set a initial breakpoint first to track code flow.

As this is Java based server, It should most probably have web.xml file where servlet & filters are configured. This can give a idea of where we can set initial breakpoint.

Searching for web.xml files in Web server directory, We find that web.xml file exists at "**D:\BB\Zoho\UEMS_CentralServer\webapps\DesktopCentral\WEB-INF\web.xml**"

Searching for "**/***" pattern in web.xml file, We find that all urls pass through **SecurityFilter** as follows.

![](/post/img/2024-07-16_intro-to-debugging-java-web-servers-without-source-code-for-security-researchers_7.png#layoutTextWidth)

**SecurityFilter** class path is com.adventnet.iam.security.SecurityFilter.

Thus, Search for **com.adventnet.iam.security.SecurityFilter** class in Eclipse IDE & add a breakpoint inside **doFilter()** method.

![](/post/img/2024-07-16_intro-to-debugging-java-web-servers-without-source-code-for-security-researchers_8.png#layoutTextWidth)![](/post/img/2024-07-16_intro-to-debugging-java-web-servers-without-source-code-for-security-researchers_9.png#layoutTextWidth)

#### Debugging the Java Server —

Go to "**Debug Configurations**" -> Select your previously saved configuration under "**Remote Java Application**", Click on "**Debug**".

![](/post/img/2024-07-16_intro-to-debugging-java-web-servers-without-source-code-for-security-researchers_10.png#layoutTextWidth)

**Open any url associated with Endpoint Central server in a browser, say "**[**http://localhost:8020/client#/login**](http://localhost:8020/client#/login)**".**

**Now we can debug the Java Web Server successfully in runtime, giving us the overview of variable values & expressions useful for dynamic analysis purpose.**

![](/post/img/2024-07-16_intro-to-debugging-java-web-servers-without-source-code-for-security-researchers_11.png#layoutTextWidth)![](/post/img/2024-07-16_intro-to-debugging-java-web-servers-without-source-code-for-security-researchers_12.png#layoutTextWidth)

#### Liked my article ? Follow me on [LinkedIn](https://www.linkedin.com/in/jayateerthag/), Twitter ([@jayateerthaG](https://twitter.com/jayateerthag)), and [Medium](https://jayateerthag.medium.com/) for more content about bugbounty, Infosec, cybersecurity and hacking.

* * *
Written on July 16, 2024 by Jayateertha Guruprasad.

Originally published on [Medium](https://medium.com/@jayateerthag/intro-to-debugging-java-web-servers-without-source-code-for-security-researchers-80ff00de4753)
