---
title: "Bypass Duplicate Tweet Protection using negative tweet id"
author: "Jayateertha Guruprasad"
date: 2022-11-11T10:54:05.419Z
lastmod: 2024-08-31T13:11:57+05:30

description: ""

subtitle: ""

image: "/post/img/2022-11-11_bypass-duplicate-tweet-protection-using-negative-tweet-id_2.jpeg" 
images:
 - "/post/img/2022-11-11_bypass-duplicate-tweet-protection-using-negative-tweet-id_0.jpeg"
 - "/post/img/2022-11-11_bypass-duplicate-tweet-protection-using-negative-tweet-id_1.jpeg"
 - "/post/img/2022-11-11_bypass-duplicate-tweet-protection-using-negative-tweet-id_2.jpeg"


aliases:
- "/bypass-duplicate-tweet-protection-using-negative-tweet-id-71e6802955b4"

---

Twitter doesn’t allow making tweets of same content in relatively near time frame.

![](/post/img/2022-11-11_bypass-duplicate-tweet-protection-using-negative-tweet-id_0.jpeg#layoutTextWidth)

But same content can be there in tweet which is made as reply to another tweet.

![](/post/img/2022-11-11_bypass-duplicate-tweet-protection-using-negative-tweet-id_1.jpeg#layoutTextWidth)

Attached below is the request made, when replying to a specific tweet.

{{<highlight bash>}}
POST /i/api/graphql/SECRET/CreateTweet HTTP/2
Host: twitter.com
Cookie: SECRET
Content-Length: 981
X-Twitter-Client-Language: en
X-Csrf-Token: SECRET
X-Twitter-Auth-Type: OAuth2Session
X-Twitter-Active-User: yes
Authorization: Bearer SECRET
User-Agent: SECRET
Accept: */*
Sec-Gpc: 1
Accept-Language: en-US,en;q=0.7
Origin: https://twitter.com
Sec-Fetch-Site: same-origin
Sec-Fetch-Mode: cors
Sec-Fetch-Dest: empty
Referer: https://twitter.com/compose/tweet
Accept-Encoding: gzip, deflate


{"variables":{"tweet_text":"Hi","reply":{"in_reply_to_tweet_id":"1234567","exclude_reply_user_ids":[]},"media":{"media_entities":[],"possibly_sensitive":true},"withDownvotePerspective":false,"withReactionsMetadata":false,"withReactionsPerspective":false,"withSuperFollowsTweetFields":true,"withSuperFollowsUserFields":true,"semantic_annotation_ids":[],"dark_request":false},"features":{"tweetypie_unmention_optimization_enabled":true,"responsive_web_uc_gql_enabled":true,"vibe_api_enabled":true,"responsive_web_edit_tweet_api_enabled":true,"graphql_is_translatable_rweb_tweet_is_translatable_enabled":true,"interactive_text_enabled":true,"responsive_web_text_conversations_enabled":false,"verified_phone_label_enabled":true,"standardized_nudges_misinfo":true,"tweet_with_visibility_results_prefer_gql_limited_actions_policy_enabled":false,"responsive_web_graphql_timeline_navigation_enabled":true,"responsive_web_enhance_cards_enabled":true},"queryId":"SECRET"}
{{</highlight>}}

The tweet to which we want to tweet as reply is decided based on param **in_reply_to_tweet_id**.

> Here, comes my hacker mind voice telling me to try non-existent tweet id, negative tweet id, zero tweet id for the **in_reply_to_tweet_id** param.

**To my surprise when the value of in_reply_to_tweet_id is zero or negative integer, It’s tweeted as a original tweet & not as a reply to tweet also bypassing the twitter duplicate content protection** !

![](/post/img/2022-11-11_bypass-duplicate-tweet-protection-using-negative-tweet-id_2.jpeg#layoutTextWidth)

Made a nice report to twitter through H1 platform explaining the issue, unfortunately it was closed as informative as this doesn’t cause any considerable security impact to it’s users or the infrastructure, but is only a bypass to a security control placed on a feature.

#### **Liked my article ? Follow me on** [**LinkedIn**](https://www.linkedin.com/in/jayateerthag/)**, Twitter (**[**@jayateerthaG**](https://twitter.com/jayateerthag)**), and **[**Medium**](https://jayateerthag.medium.com/)** for more content about bugbounty, Infosec, cybersecurity and hacking.**

* * *
Written on November 11, 2022 by Jayateertha Guruprasad.

Originally published on [Medium](https://medium.com/@jayateerthag/bypass-duplicate-tweet-protection-using-negative-tweet-id-71e6802955b4)
