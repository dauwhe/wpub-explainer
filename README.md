# An Informal Guide to Web Publications

## Introduction

What is a web publication? A web publication is not a single thing, but rather a loose collection of behaviors which, taken together, make it easier for users to read long, possibly complex documents. 

Two things make web publications different from the “ordinary” web we know and love. One is that a web publication may consist of multiple resources that form a logical whole. Moby-Dick could consist of 136 HTML files in a specified order, but it’s still a single work. So searching should search all 136 chapters.

More importantly, users have a set of expectations about how such content should be presented in order to make it easy to read and understand. Users need to personalize the presentation, using the font and font size that make it easiest for them to read. They want it to be easy to go to the next chapter without interrupting the reading experience by hunting for a link to click. They might need a high- or low-contrast version of the content. They want to read while offline.

Thus the goal of web publications is to provide the information necessary to provide these features ("affordances") to readers. We hope that someday browsers will provide these features, but in the meantime we have to use scripting. That's OK, because we'll learn a lot from trying, and that will help the spec get better.

## Goals

 
- Provide a mechanism for defining a collection of web resources as a publication 
 
- Provide a mechanism for ascribing descriptive metadata to the collection of web resources.

- Provide a mechanism for defining an ordering of the resources in a publication

 
 
## Non-goals

 - Issues of layout, such as pagination
 
 - The equivalent of fixed-layout publications in EPUB
 
 - The equivalent of multiple-rendition publications in EPUB
 
 - DRM
 
## The manifest

A “manifest” is a list of the passengers or cargo on a ship. We need a list of the components of a web publication—all the HTML files, stylesheets, images, scripts, etc. needed to create the whole. We also need a special list of the files that go in a particular order. In EPUB we called this the "spine". We can call this the "default reading order." 

In order to placate the web developers who seemingly rule the world, the manifest is expressed as a JSON file. The manifest is also a natural location for metadata that applies to the whole publication, rather than just one of the components. 


## Example

Here's a simple example, for a tiny version of *Moby-Dick* with only a few HTML files:

```json
{
    "@context": ["https://schema.org", "https://www.w3.org/ns/wp-context"],
    "type": "Book",
    "url": "https://publisher.example.org/mobydick",
    "author": "Herman Melville",
    "dateModified": "2018-02-10T17:00:00Z",

    "readingOrder": [
        "html/title.html",
        "html/copyright.html",
        "html/introduction.html",
        "html/epigraph.html",
        "html/c001.html",
        "html/c002.html",
        "html/c003.html",
        "html/c004.html",
        "html/c005.html",
        "html/c006.html"
    ],

    "resources": [
        "css/mobydick.css",
        {
            "type": "PublicationLink",
            "rel": "https://www.w3.org/ns/wp#cover-page",
            "url": "images/cover.jpg",
            "encodingFormat": "image/jpeg"
        },{
            "type": "PublicationLink",
            "url": "html/toc.html",
            "rel": "contents"
        },{
            "type": "PublicationLink",
            "url": "fonts/STIXGeneral.otf",
            "encodingFormat": "application/vnd.ms-opentype"
        },{
            "type": "PublicationLink",
            "url": "fonts/STIXGeneralBol.otf",
            "encodingFormat": "application/vnd.ms-opentype"
        },{
            "type": "PublicationLink",
            "url": "fonts/STIXGeneralBolIta.otf",
            "encodingFormat": "application/vnd.ms-opentype"
        },{
            "type": "PublicationLink",
            "url": "fonts/STIXGeneralItalic.otf",
            "encodingFormat": "application/vnd.ms-opentype"
        }
    ]
}

```

`readingOrder` defines the sequence of resources that form the publication. `resources` enumerates all the other resources that are required to render the publication. Schema.org is used for most publication metadata. 

## Design choices

### Structural

The key questions are [1] identifying which resources form the publication and [2] defining an ordering of primary resources. 

One possibility is **transclusion**, having a single resource which contains all the other resources. XML has often used such an approach, and it can be expressed in HTML in several ways, from HTML imports to iframes.

Membership in a publication could also be defined using `scope`, as WAM does. 

The PWG has decided to use a different approach, using lists linked from (or embedded in) HTML to both identify the consituents of a publication and define an ordering. In EPUB such information was defined in a "package" file and serialized in XML. As mentioned above, WPUB uses a "manifest" file which is serialized as JSON-LD. 

### Relationship to Web Application Manifest

The Web Publication Manifest appears to be very similar to the Web Application Manifest. Both are JSON files that are linked to from HTML, and provide metadata about a composite resource. 







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

















