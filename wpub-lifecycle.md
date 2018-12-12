# Life without Scope

I've been trying to think through how user agents interact with web publications. This brings up lots of questions, and lots of consequences that I'm not sure we've fully understood yet.

## 1. Identifying a web publication.

I navigate my browser to a URL. Question: is this URL the primary entry page of a web publication? 


1.  Is there an embedded manifest? 

  NO: Go to step 2.

  YES: the current page is the primary entry page. End algorithm.

2.  Is there a link to a manifest?

  NO: the current page is not the primary entry page. End algorithm.

  YES: Go to step 3.

3. Fetch the manifest

4. Find the value of the `address` member of the manifest.

5. Compare the `address` value to the URL of the current page. Do they match?

  YES: the current page is the primary entry page. End algorithm.

  NO: the current page is not the primary entry page. End algorithm.

## 2. Navigating within a web publication

OK. We have found the primary entry page of a web publication. We have access to the information in the manifest. What happens next?

Presumably a user agent would load the HTML, CSS, scripts, images, etc. found at the URL. In fact, it must load some of the HTML at least before even finding any indication of whether this is a web publication. So what happens when it discovers that yes, it is a web publication? We talk about creating a [publication mode](https://w3c.github.io/wpub/#feature-publication-mode): 

> When a user agent obtains a manifest it SHOULD provide the option to switch the display to publication mode.


