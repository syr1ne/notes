# article: https://aszx87410.github.io/beyond-xss/en/ch1/browser-security-model/

this doc contain everything i have learned from the above article.

## Browser Security Model

browsers run web applications in the sandbox environment. it restricts many things for the application that you can't access.
- web application in a browser cannot actively access local files. you can upload the files to the web app first and make changes to it but can't proactively access and manage local files. why? because browser simply don't allow it.
- web application in a browser cannot also make direct system level API calls. its not that javascript can't do it, it's just browser don't allow it. web apps are only allowed to make certain system API calls like web bluetooth APIs, only if browser allows.
- one web page is not allowed to access data of other web pages. every web pages or tabs in the browsers runs in a completely separate sandbox environment. this browser security model is called SOP (same origin policy) which means by default, browsers only allows web pages to access only their own websites data.

## XSS

the `alert(1)` is just the demonstration of XSS. this doesn't show the actual impact of the the code execution on the victim's browser. the XSS allows various action an attacker can perform, for example: stealing the `localStorage` which may include authentication token. thats why it's important to store authentication token in `Cookie` rather than `localStorage` is better. `localStorage` can be stolen, but if the `Cookie` has `HttpOnly` flag set, it therefore cannot be accessed by client-side javascripts.
if the cookie is not using `HttpOnly` flag, one can access it using `document.cookie` or the updated `cookieStore` API.

**`<script>` tag doesn't work in the context of `innerHTML`**

modern frontend frameworks usually handles escaping automatically (even by default, everything rendered will be treated as plain text by these frameworks) unless you are using `dangerouslySetInnerHTML` in React or `v-html` in Vue. these are dangerous functions that can execute HTML. if the input is controlled by user, this could lead to XSS.

### framework info:

- https://legacy.reactjs.org/blog/2019/08/08/react-v16.9.0.html#deprecating-javascript-urls


the robust way of validating URLs is to use `new URL()` for parsing instead of using simple string comparison or usingRegExp and make judgement based on the return value. this will prevent bugs like open redirection.

some people may know that `setTimeout` can also execute code as a string:
```
setTimeout('alert(1)')
```

if `script-src 'noncce-xxx' is set, that means:
```
<!-- Allowed -->
<script nonce=xxx>
  alert(1)
</script>

<!-- Not allowed -->
<script>
  alert(1)
</script>
```
There is also a similar 'sha256-abc...' rule, which allows specific inline scripts based on their hash. For example, if we take alert(1) and calculate its sha256 hash, we get a binary value that, when base64 encoded, becomes bhHHL3z2vDgxUt0W3dWQOrprscmda2Y5pLsLg4GF+pI=.

### conclusion:
- the first line of defense against XSS: the encoding and sanitization of user input.
- the second line of defense against XSS: setting up CSP header
- the third layer is considering extra security measures like setting up MFA, using web workers to isolate API calls and prevent token theft, api limit etc.

