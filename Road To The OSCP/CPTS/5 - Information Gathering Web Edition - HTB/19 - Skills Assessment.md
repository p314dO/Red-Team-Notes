What is the IANA ID of the registrar of the inlanefreight.com domain?

What http server software is powering the inlanefreight.htb site on the target system? Respond with the name of the software, not the version, e.g., Apache.

What is the API key in the hidden admin directory that you have discovered on the target system?

After crawling the inlanefreight.htb domain on the target system, what is the email address you have found? Respond with the full email, e.g., mail@inlanefreight.htb.

*What is the API key the inlanefreight.htb developers will be changing too?*

```js
ffuf -w ~/SecLists/Discovery/DNS/subdomains-top1million-110000.txt:FUZZ -u http://94.237.63.3:55492 -H "HOST :FUZZ.inlanefreight.htb" -fs 120
```

web1337

```js
ffuf -w ~/SecLists/Discovery/DNS/subdomains-top1million-110000.txt:FUZZ -u http://94.237.63.3:55492 -H "HOST :FUZZ.web1337.inlanefreight.htb" -fs 120
```

dev

```js
python3 ReconSpider.py http://dev.web1337.inlanefreight.htb:55492/
```

