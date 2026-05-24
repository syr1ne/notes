### X-Frame-Options: SAMEORIGIN; (Content-Security-Policy: frame-ancestors 'self')
these headers prevents the website from loading in an iframe on a different origin. (self origin is allowed)
as such, they also prevents postmessage attacks through the iframe.

#### is there any bypass?
- use `window.open()` instead of iframe
- no other known bypass


### COOP protection blocks postmessage attacks

```
Cross-Origin-Opener-Policy: same-origin
Cross-Origin-Opener-Policy: same-origin-allow-popups
```


**what `same-origin-allow-popups` does is:**
- keeps its own browsing context group isolated.
- limits opener relationships across origins. they postmessages from the parent window as it set the value of `window.opener` to `null` (which will drop all postmessages from attacker's site to the victim site. this is how browser works)
- but still permits popups.

**what `same-origin` does is:**
- even block website from open as a popup window.

#### is there any bypass?
- use iframe instead of `window.open()`
- no other known bypass 


# the HEADERS we have discussed above if configured in the website together, prevents it from all kinds of postMessage Attacks. even if you find a vulnerability for example XSS, these headers will not let you build a working exploit POC.
