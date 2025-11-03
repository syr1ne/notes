# Same-Origin Policy (SOP)

SOP is a default browser security machanism. basically it isolates origins from accessing each other.

```
attacker.com ----- X -----> mail.google.com
(origin)                    (other origin)
```

to bypass such checks and allow certain domains to share information, we use **CORS** policy mechanism.

### which URLs have same origin

- same scheme: http/https
- same domain/subdomain: domain.com
- same port: 80, 443, 8080


### SOP cross-origin network access


##### SOP blocks:
- SOP blocks cross-origin read, means javascript cannot read the response from HTTP request made to other domain, until and unless that other domain allows it (using CORS).  

##### SOP allows:

- SOP allows sending data from one origin to another. such as through form submission, redirects, HTTP requests. but it restricts reading the response.
- SOP allows cross-origin writes.
- SOP allows embedding resources like images, scripts, videos, objects from other origins and displayed/executed within page.


### certain things to understand

1. changing origin (deprecated)

`document.domain = "company.com"` can actually bypass the same origin check of `sub.company.com` (but it no more works)



**Q. what is the difference between SOP and CORS?**

- SOP prevents one origin to interact with other origin whereas CORS allows one origin to interact with other. (in case of need)

- SOP is by-default browser security mechanism whereas CORS is server-side configured mechanism.
