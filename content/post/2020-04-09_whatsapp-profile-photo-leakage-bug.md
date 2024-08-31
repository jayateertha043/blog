---
title: "WhatsApp Profile Photo Leakage Bug"
author: "Jayateertha Guruprasad"
date: 2020-04-09T17:06:16.448Z
lastmod: 2024-08-31T13:11:26+05:30
featured:
    url: ./img/2020-04-09_whatsapp-profile-photo-leakage-bug_0.jpeg
    alt: A scale model of the Eiffel tower standing on a map
    caption: Eiffel tower model
    credit: Unknown author
    previewOnly: false

description: ""

subtitle: ""

image: "/post/img/2020-04-09_whatsapp-profile-photo-leakage-bug_0.jpeg" 
images:
 - "/post/img/2020-04-09_whatsapp-profile-photo-leakage-bug_0.jpeg"
 - "/post/img/2020-04-09_whatsapp-profile-photo-leakage-bug_1.jpeg"
 - "/post/img/2020-04-09_whatsapp-profile-photo-leakage-bug_2.jpeg"
 - "/post/img/2020-04-09_whatsapp-profile-photo-leakage-bug_3.jpeg"
 - "/post/img/2020-04-09_whatsapp-profile-photo-leakage-bug_4.png"
 - "/post/img/2020-04-09_whatsapp-profile-photo-leakage-bug_5.png"


aliases:
- "/whatsapp-profile-photo-leakage-bug-a32f68062b32"

---

If You think WhatsApp is totally safe and your Profile Picture is visible to people only in your contacts or depending on your privacy settings then you are totally wrong.

I found a bug in WhatsApp through which any 3rd Party App with only read Storage Permission can access your WhatsApp profile Picture no matter What or How Secure your profile visibility settings are.

Let’s Go into the details of the bug:

The orginal profile picture is stored at location "/data/data/com.whatsapp/files/me.jpg" ,internal storage location which is accessible only to WhatsApp,not even visible to you if you browse through a normal File Explorer.You may need Root access to view this directory.Your WhatsApp Db Encryption Key is also stored inside the very same Folder "/data/data/com.whatsapp/". So this Location is totally safe to keep Private data.

![](/post/img/2020-04-09_whatsapp-profile-photo-leakage-bug_0.jpeg#layoutTextWidth)

But when a user clicks on share button on his profile picture screen ,WhatsApp copies the me.jpg file(your profile photo),from location "/data/data/com.whatsapp/files/me.jpg" to location "/storage/emulated/0/WhatsApp/.Shared/me.jpg".

The Storage location "/storage/emulated/0/WhatsApp/.Shared/me.jpg" is publicly accessible to all the apps installed on your device which has READ STORAGE PERMISSIONS.

![](/post/img/2020-04-09_whatsapp-profile-photo-leakage-bug_1.jpeg#layoutTextWidth)

In the Picture You can see I made a quick POC(Proof of Concept) of this bug.I created a App (using Flutter) called WhatsApp Profile Stealer which shows your WhatsApp Profile Picture inside my App(which means any app which is already present in your phone with storage access can also access your profile picture!!!) and also sends the image to telegram channel t.me/whatsappprofile

> Download Link of my WhatsApp Profile Stealer App POC : [https://drive.google.com/open?id=1cdFcGx7-EQSk1MtUEiCXQqfGy3uNzVVw](https://drive.google.com/open?id=1cdFcGx7-EQSk1MtUEiCXQqfGy3uNzVVw)

> Telegram Invite Link for WhatsApp Profile Stealer POC :[https://telegram.me/whatsappprofile](https://telegram.me/whatsappprofile)

I sent the POC APK along with all these details to Facebook Whitehat Bug Bounty Program. But Sadly my Bug Was not Accepted!!!

> **It’s a critical vulnerability ,Privacy Details(Profile Picture) of user is disclosed to a 3rd party App without user’s knowledge.Think of an App in PlayStore with more than 100M+ downloads which needs Storage Access(I don’t want to name any obviously),This App can possibly create a database of WhatsApp User’s Profile Pictures ,No matter What their Profile Visibility Settings are-.**

> **If the App also takes Mobile No of User during regisration ,It can easily Map a phone number of User to Exact WhatsApp Profile Picture.(A lot of E-Commerce,online delivery,Cab Booking Apps need your mobile no during registration).**

I gave these suggestions to WhatsApp as some of the Fixes:

1. Do you really think there should be a share button in profile section as that image might already be with user on the device?.(You have removed share and download button from user’s contact profiles in the recent updates, similarly removing that option would be a great fix)
2. Another fix I can think of — instead of naming it me.jpg ,you can use a random name so that malicious app cant find the exact profile photo from many image files in the same folder.
3. There is also a another fix — storing the file only for a temporary time and then deleting it once the share is complete(This is risky ,may be app can perform timing attack).
 Fix (2) and (3) can be combined as a effective solution so malicious app cant access profile picture.

Here Are Screenshots of My Chats with Facebook Security Team.

![](/post/img/2020-04-09_whatsapp-profile-photo-leakage-bug_2.jpeg#layoutTextWidth)![](/post/img/2020-04-09_whatsapp-profile-photo-leakage-bug_3.jpeg#layoutTextWidth)


So ,This is the end of Article Finally Remember this — "Any App installed on your device with Read Storage Access is capable of viewing your WhatsApp Profile Picture No matter How safe Your Profile Visiblity inPrivacy Settings are-"

> **To Be More Secure and If you care about Privacy and your Profile Picture and Don’t want your Profile Pictures to be leaked randomly online ,Then Just Don’t use the Share Button on The Profile Section of Your WhatsApp Profile Picture.**

#### **Liked my article ? Follow me on** [**LinkedIn**](https://www.linkedin.com/in/jayateerthag/)**, Twitter (**[**@jayateerthaG**](https://twitter.com/jayateerthag)**), and **[**Medium**](https://jayateerthag.medium.com/)** for more content about bugbounty, Infosec, cybersecurity and hacking.**

* * *
Written on April 9, 2020 by Jayateertha Guruprasad.

Originally published on [Medium](https://medium.com/@jayateerthag/whatsapp-profile-photo-leakage-bug-a32f68062b32)
