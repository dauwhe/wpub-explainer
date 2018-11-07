# An Informal Guide to Web Publications

The unanswered question:

> is the goal of this group to make a new packaging format for specialist book reading software and devices, or is it to obtain first class support for missing book-related features in the web platform as a whole?

## Introduction

What is a web publication? A web publication is not a single thing, but rather a loose collection of behaviors which, taken together, make it easier for users to read long, possibly complex documents. 

Two things make web publications different from the “ordinary” web we know and love. First, a web publication may consist of multiple resources that form a logical whole. Moby-Dick could consist of 136 HTML files in a specified order, but it’s still a single work. So searching should search all 136 chapters.

More importantly, users have a set of expectations about how such content should be presented in order to make it easy to read and understand. Users need to personalize the presentation, using the font and font size that make it easiest for them to read. They want it to be easy to go to the next chapter without interrupting the reading experience by hunting for a link to click. They might need a high- or low-contrast version of the content. They want to read while offline.

Thus the goal of web publications is to provide the information necessary to provide these features ("affordances") to readers. We hope that someday browsers will provide these features, but in the meantime we have to use scripting. That's OK, because we'll learn a lot from trying, and that will help the spec get better.

## Goals

 
- Provide a mechanism for defining a collection of web resources as a publication 
 
- Provide a mechanism for ascribing descriptive metadata to a collection of web resources.

- Provide a mechanism for defining an ordering of the resources in a publication

- Describe the affordances needed for reading publications on the web

 
 
## Non-goals

 - Issues of layout, such as pagination or achieving effects similar to EPUB's fixed layout. 
 
 - Extending the DOM to include collections of document elements 
 
 - DRM
 
## Basic design

A web publication must have an <i>entry page</i>, which the HTML document returned by the URL of the publication. This page must have either a link to the manifest (`<link rel="publication" href="manifest.json">`, or an embedded manifest. 

A “manifest” is a list of the passengers or cargo on a ship. For web publications, a manifest lists the constituents of the publication—all the HTML files, stylesheets, images, scripts, etc.—needed to create the whole. It further describes the sequence of primary resources, so that we know that chapter-02.html comes after chapter-01.html. In EPUB we called this list the "spine"; for web publications it's now the `readingOrder`. 

The manifest is also the natural location for metadata that applies to the whole publication, rather than just one of the constituents. The metadata vocabulary is based on schema.org; the entire manifest is serialized as JSON-LD. 


## Example Manifest

Here's a simple example of a web publication manifest, for a tiny version of *Moby-Dick* with only a few HTML files:

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

Note that `readingOrder` defines the sequence of primary resources that form the publication. `resources` enumerates all the other resources that are required to render the publication. 

## Design choices


The key questions are [1] identifying the "bounds" of the publication, [2] defining the ordering of the primary resources, and [3] figuring out how to express metadata for the publication as a whole. 

### 1. What's part of the publication?

- The [Web Application Manifest spec](https://w3c.github.io/manifest/) uses `scope` to define the extent of a web application. 

- Every component of an EPUB publication must be listed in an XML package file.

- Web sites do not define their boundaries.

- XML sometimes uses the idea of *transclusion*: having a single resource represent the whole but containing pointers to the constituent sub-resources. This concept can also be realized in HTML using iframes, HTML imports, etc. 

The web publications spec has essentially adopted EPUB's approach, with an explicit list of resources. Anything that is not part of the `resources` or `readingOrder` manifest members is considered to be outside the web publication. 

### 2. Sequence of primary resources

- HTML can express ordering of resources via `rel=prev` and `rel=next`, but these do not have UI support from the browsers (with notably rare exceptions).

- HTML can also express an ordered list of links with the `nav` element. 

- Transclusion can also define an ordering of the transcluded components. 

- EPUB uses the `spine` element in the XML package file to determine a primary reading order.

Once again, web publications use an explicit list outside of the content itself, via the `readingOrder` member of the manifest. 

### 3. Metadata

- EPUB includes metadata in the XML package file

- There are many ways of embedding metadata in individual HTML files

- Metadata about a web application can be expressed in the web application manifest file. 

- With transclusion, one could define metadata in the containing file to apply to the whole.

Web publication metadata is expressed in the manifest.

### 4. Obtaining a manifest

The spec has two ways of getting to a manifest from the entry page of the publication.

1. Link to the manifest from html

```html
<link rel="publication" href="manifest.json">
```

> Note: If you have more than one link to the manifest, the first one will be used.


2. It's possible to embed the manifest in a `script` element in the HTML entry page. Among other things, this makes it possible to have a single-resource web publication. Some people think the manifest [should be required to be embedded](https://github.com/w3c/wpub/issues/327) in the entry page.


### Relationship to Web Application Manifest

The Web Publication Manifest appears to be very similar to the Web Application Manifest. Both are JSON files that are linked to from HTML, and provide metadata about a composite resource. Several [arguments](https://github.com/w3c/wpub/wiki/Options-for-Processing-a-Manifest) [have](https://github.com/w3c/wpub/issues/32) been made against using WAM.

1. WP use cases are orthogonal to those of WAM. Nothing is stopping a creator of a web publication from also using a web application manifest, if the publication author desires for the publication to be installable, etc. 

2. The web application manifest spec is not designed to be extensible. 

3. Web publications are fundamentally different from web apps, as the goal is for the user agent to provide the user interface. 


But note that the [TAG has spoken](https://github.com/w3c/wpub/issues/32#issuecomment-362273649) in favor of using WAM. 



## Launching a web publication


> Issue: are web publications installed? What level of user consent is needed to invoke the additional functionality of a web publication beyond what a browser would typically offer?

A web publication consists of web resources, but with some special behaviors. In order to provide those behaviors, a user agent or prollyfill must recognize that the resources form a web publication, and obtain the information describing the web publication from its manifest.

The process of launching a web publication starts with a user navigating to a resource of the web publication which contains a link to the manifest. In this section, we will simply refer to this particular document as "document" for simplicity. 

> Issue: how would "deep linking" work? What if some resources of the web publication do not have a link to a manifest?

> Issue: in Web App Manifest, the `document` is explicitly part of a top-level browsing context. Is this necessary?


## The User Experience

Reading something that takes a day or a week rather than a few minutes influences what sort of user experience is best for publications. 


### Moving through the default reading order


A web publication has a default reading order, and it should be easy for the reader to see everything in sequence. A user agent must provide means to go to the previous or next resource in the default reading order. 


### Accessing the table of contents

A table of contents is critical for accessing and understanding the components of a web publication. A web publication **should** have a table of contents, with a `nav` element which **should** include entries for all main resources, at least.

If a table of contents exists, the user agent **must** provide a way for the user to request the table of contents and have it displayed in some form. 

















