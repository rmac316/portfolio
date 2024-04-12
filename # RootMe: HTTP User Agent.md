# RootMe: HTTP User Agent

## Notes

Starting the challenge opens the following window:

[insert photo]

Frame in BurpSuite displays:
```
GET /web-serveur/ch2/ HTTP/1.1
Host: challenge01.root-me.org
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/121.0.6167.85 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7
Accept-Encoding: gzip, deflate, br
Accept-Language: en-US,en;q=0.9
Connection: close
```

**User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/121.0.6167.85 Safari/537.36**

by changing the "User-Agent" entry to "admin", we may be able to access the admin browser.

Success!


Can also use curl command:
```
curl -A admin http://challenge01.root-me.org/web-serveur/ch2/
```