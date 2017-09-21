# An Informal Guide to Web Publications

> Note: There is some rather formal text here, as I attempt to draft some language for the actual spec. There will be warnings :)

## Introduction

What is a publication? 

A web publication is not a single thing, but rather a loose collection of behaviors which, taken together, make it easier for users to read long, possibly complex documents. 

Two things make web publications different from the “ordinary” web we know and love. One is that a web publication may consist of multiple resources that form a logical whole. Moby-Dick could consist of 136 HTML files in a specified order, but it’s still a single work. So searching should search all 136 chapters.

More importantly, users have a set of expectations about how such content should be presented in order to make it easy to read and understand. Users need to personalize the presentation, using the font and font size that make it easiest for them to read. They want it to be easy to go to the next chapter without interrupting the reading experience by hunting for a link to click. They might need a high- or low-contrast version of the content. They want to read while offline.

Thus the goal of web publications is to provide the information necessary to provide these features ("affordances") to readers. We hope that someday browsers will provide these features, but in the meantime we have to use scripting. That's OK, because we'll learn a lot from trying, and that will help the spec get better.


## Manifest & Metadata

We must answer two fundamental questions about any given web publication:

1. What are the pieces?

2. How do the pieces fit together? Do some of the pieces have to be in a certain order?

A "manifest" is a list of the passengers or cargo on a ship. We need a list of the components of a web publication—all the HTML files, stylesheets, images, scripts, etc. needed to create the whole. We also need a special list of the files that go in a particular order. In EPUB we called this the "spine". We can call this the "default reading order." 

In order to placate the web developers who seemingly rule the world, the manifest is expressed as a JSON file. The manifest is also a natural location for metadata that applies to the whole publication, rather than just one of the components. 

Here's a simple example, for a tiny version of *Moby-Dick* with only three HTML files:

```json
{
	"kind": "📖",
	"name": "Moby-Dick",
	"lang": "en-US",
	"locator": "https://dauwhe.github.io/MobyDickNav/",
	"address": "https://dauwhe.github.io/MobyDickNav/",
	"identifier": "urn:uuid:5740a60e-8da1-11e7-bb31-be2e44b06b34",
	"main": ["index.html", "html/c001.html", "html/c002.html", "html/c003.html"],
	"resources": ["css/mobydick.css", "js/shim.js"],
	"contents": "https://dauwhe.github.io/MobyDickNav/"
}

```

Let's take a look at the various "members" of the manifest.

> Issue: why does web app manifest use the word "members"?

### 1. Kind

Our first requirement is to identify our publication as a web publication. We can use the book emoji, or the simpler-to-type `book`. 

### 2. Name

The name or title of the web publication. We use "name" to be compatible with [Web Application Manifest](https://www.w3.org/TR/appmanifest/)

### 3. Lang

This is the language of the manifest. 

> Issue: Should this be the language that any user interface for the book is presented in?

> Issue: What about multi-language publications?

### 4. Locator

This is the URL of the Web Publication itself, wherever it may be currently hosted. It’s like if you went off to college, and this is your local address.

### 5. Address

This is the original, permanent address of the web publication. It may be the same as `locator`. 

### 6. Identifier

A unique identifier for the web publication. This might be an ISBN or other non-resolvable identifer. 

### 7. Main

This is an ordered list of the main resources in a publication, analogous to the `spine` in EPUB. This forms the default reading order. 




### 8. Resources

This is a list of all the other resources that are a part of the publication. No need to list `main` resources again.

> Note: These resources could be HTML documents, if they are not part of a "default reading order".

### 9. Contents

This lists the URL of the publication’s table of contents (i.e. `nav` file). 


## Associating a resource with a manifest

> Warning: Hard-core formal-ish spec text

> Note: This is adapted from the [Web Application Manifest](https://www.w3.org/TR/appmanifest/#associating-a-resource-with-a-manifest) spec. 

A resource is said to be associated with a manifest if the resource representation, an HTML document, has a manifest link relationship.

### Linking to a manifest

To link to a manifest, use the [HTML](https://www.w3.org/TR/appmanifest/#dfn-link-element) `link` element using the keyword `manifest` as the value of the `rel` attribute. This creates an external resource link:

```html
<link rel="manifest" href="manifest.json">
```

> Note: In cases where more than one `link` element with a manifest link type appears in a Document, the user agent uses the first `link` element in tree order and ignores all subsequent `link` element with a manifest link type (even if the first element was erroneous). See the steps for obtaining a manifest.

To obtain a manifest, the user agent must run the steps for obtaining a manifest. The appropriate time to obtain the manifest is left up to implementations. A user agent may opt to delay fetching a manifest until after the document and its other resources have been fully loaded (i.e., to not delay the availability of content and scripts required by the document).

A manifest is obtained and applied regardless of the media attribute of the link element matches the environment or not.


## Launching a web publication

> Warning: Hard-core formal-ish spec text

> Issue: are web publications installed? What level of user consent is needed to invoke the additional functionality of a web publication beyond what a browser would typically offer?

A web publication consists of web resources, but with some special behaviors. In order to provide those behaviors, a user agent or prollyfill must recognize that the resources form a web publication, and obtain the information describing the web publication from its manifest.

The process of launching a web publication starts with a user navigating to a resource of the web publication which contains a link to the manifest. In this section, we will simply refer to this particular document as "document" for simplicity. 

> Issue: how would "deep linking" work? What if some resources of the web publication do not have a link to a manifest?

> Issue: in Web App Manifest, the `document` is explicitly part of a top-level browsing context. Is this necessary?


### Obtaining a manifest

> Note: this is copied from the [Web App Manifest](https://www.w3.org/TR/appmanifest/#manifest-life-cycle) spec. 

> Issue: If web publications use a different manifest, how are they distinguished?

The steps for obtaining a manifest are given by the following algorithm. The algorithm, if successful, returns a processed manifest and the manifest URL; otherwise, it terminates prematurely and returns nothing. In the case of nothing being returned, the user agent must ignore the manifest declaration. In running these steps, a user agent must not delay the load event.

1. From the Document of the top-level browsing context, let manifest link be the first link element in tree order whose rel attribute contains the token manifest.
2. If manifest link is null, terminate this algorithm.
3. If manifest link's href attribute's value is the empty string, then abort these steps.
4. Let manifest URL be the result of parsing the value of the href attribute, relative to the element's base URL. If parsing fails, then abort these steps.
5. Let request be a new [FETCH] request, whose URL is manifest URL, and whose context is " manifest".
6. If the manifest link's crossOrigin attribute's value is 'use-credentials', then set request's credentials to 'include'.
7. Await the result of performing a fetch with request, letting response be the result.
8. If response is a network error, terminate this algorithm.
9. Let text be the result of UTF-8 decoding response's body.
10. Let manifest be the result of running the steps for processing a manifest with text, manifest URL, and the URL that represents the address of the top-level browsing context.
11. Return manifest and manifest URL.

> Note: A [browsing context](https://html.spec.whatwg.org/multipage/browsers.html#windows) is an environment in which Document objects are presented to the user.

A manifest is applied to a top-level browsing context, meaning that the members of the manifest are affecting the presentation or behavior of a browsing context.

A top-level browsing context that has a manifest applied to it is referred to as an publication context.

If an publication context is created as a result of the user agent being asked to navigate to a deep link, the user agent must immediately navigate to the deep link with replacement enabled. Otherwise, when the publication context is created, the user agent must immediately navigate to the <<start URL>> with replacement enabled.

> Issue: what is our analog to start url?


## Navigation

Users need to find their way around all the content in a web publication. This involves everything from “turning the page” to finding a table of contents.

### Moving through the default reading order


A web publication has a default reading order, and it should be easy for the reader to see everything in sequence. A user agent must provide means to go to the previous or next resource in the default reading order. 

> Certain actions cause the browsing context to navigate to a new resource. A user agent may provide various ways for the user to explicitly cause a browsing context to navigate, in addition to those defined in this specification.

A user agent **must** provide a way for the user to explicitly cause the publication context to navigate to the next or previous document in the default reading order. 

> Issue: see https://github.com/whatwg/html/issues/1130







### Accessing the table of contents

A table of contents is critical for accessing and understanding the components of a web publication. A web publication **should** have a table of contents, with a `nav` element which **should** include entries for all main resources, at least.

If a table of contents exists, the user agent **must** provide a way to reach the table of contents any time the user requests it.


## Content Model

Whatever works on the web. In particular, unlike EPUB, the HTML serialization of HTML5 is encouraged.

> Note: there is no list of core media types. 

## Synchronized media 


## Implementation issues
### Local storage
### Service workers
### Packaging

The web packaging spec is designed around HTTP requests and responses, which among other things means that you must know the media type of a resource to package it.
	
## Scripting

	Defer; unknown if this will be needed (maybe in PWP or EPUB4)

## Page transition
	Defer; unknown if this will be needed
	
## Personalization
	User to reading environment
	Author to reading environment
	
## Security


### Scripting

## Privacy

## Archiving










