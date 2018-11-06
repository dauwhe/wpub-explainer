# An Informal Guide to Web Publications

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
	"metadata": {
		"@type": "http://schema.org/Book",
		"title": "Moby-Dick",
		"author": "Herman Melville",
		"identifier": "urn:uuid:5740a60e-8da1-11e7-bb31-be2e44b06b34",
		"language": "en",
		"modified": "2015-09-29T17:00:00Z"
	},

	"links": [{
		"rel": "self",
		"href": "https://dauwhe.github.io/MobyDickNav/"
	}],
	"spine": [{
			"href": "index.html",
			"type": "text/html"
		},
		{
			"href": "html/c001.html",
			"type": "text/html"
		},
		{
			"href": "html/c002.html",
			"type": "text/html"
		},
		{
			"href": "html/c003.html",
			"type": "text/html"
		}
	],
	"resources": [{
			"href": "css/mobydick.css",
			"type": "text/css"
		},
		{
			"href": "js/shim.js",
			"type": "application/javascript"
		}
	],
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

### 7. Spine

This is an ordered list of the main resources in a publication, analogous to the `spine` in EPUB. This forms the default reading order. 

> Issue: I find the term `spine` non-intuitive, but I seem to be the only one.




### 8. Resources

This is a list of all the other resources that are a part of the publication. No need to list `main` resources again.

> Note: These resources could be HTML documents, if they are not part of a "default reading order".

### 9. Contents

This lists the URL of the publication’s table of contents (i.e. `nav` file). 


## Associating a resource with a manifest

The HTML files in our web publication need to be linked with the manifest.


### Linking to a manifest

To link to a manifest, use the [HTML](https://www.w3.org/TR/appmanifest/#dfn-link-element) `link` element with the `rel` attribute set to `manifest`:

```html
<link rel="manifest" href="manifest.json">
```

> Note: If you have more than one link to the manifest, the first one will be used.


## Launching a web publication


> Issue: are web publications installed? What level of user consent is needed to invoke the additional functionality of a web publication beyond what a browser would typically offer?

A web publication consists of web resources, but with some special behaviors. In order to provide those behaviors, a user agent or prollyfill must recognize that the resources form a web publication, and obtain the information describing the web publication from its manifest.

The process of launching a web publication starts with a user navigating to a resource of the web publication which contains a link to the manifest. In this section, we will simply refer to this particular document as "document" for simplicity. 

> Issue: how would "deep linking" work? What if some resources of the web publication do not have a link to a manifest?

> Issue: in Web App Manifest, the `document` is explicitly part of a top-level browsing context. Is this necessary?


### Obtaining a manifest

> Issue: If web publications use a different manifest than the web app manifest, how are they distinguished?

This section just talks about how user agents process the manifest. It's of no interest to civilians.

> Issue: what is our analog to start url?


## Navigation

Users need to find their way around all the content in a web publication. This involves everything from “turning the page” to finding a table of contents.

### Moving through the default reading order


A web publication has a default reading order, and it should be easy for the reader to see everything in sequence. A user agent must provide means to go to the previous or next resource in the default reading order. 




### Accessing the table of contents

A table of contents is critical for accessing and understanding the components of a web publication. A web publication **should** have a table of contents, with a `nav` element which **should** include entries for all main resources, at least.

If a table of contents exists, the user agent **must** provide a way for the user to request the table of contents and have it displayed in some form. 


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

### Integrity

## Privacy

## Archiving










