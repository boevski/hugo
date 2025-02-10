Collection of Hugo shortcodes, templates, and render hooks.

Contents:

* [Installation](#installation)
* [elink.html](#elinkhtml)
  * [Usage](#usage)
  * [Examples](#examples)
  * [Multilingual Considerations](#multilingual-considerations)
  * [Foolproof-ing](#foolproof-ing)
* [Working Links on Docsy `_print` Pages](#working-links-on-docsy-_print-pages)
  * [Usage](#usage-1)

## Installation

Copy the file(s) that you want into your Hugo project, preserving the directory structure of this repo.

## elink.html

This shortcode allows you to link pages using a unique ID. This way, you get a working link wherever and whenever you move the linking file or the target file.

In addition, the link automatically pulls the name of the target resource:

* If the target is a page, it uses the page Title as the link text.
* If the target is a heading (on the same or another page), it uses the heading text as the link text.

### Usage

First, set a unique ID in your page metadata, under `params`. Create `params` if you don't have it:

```
---
title: Overview
weight: 10
params:
  eid: overview
---
```

If you have multiple pages with the same name under different sections, you'll need to make the IDs unique. I recommend prefixing them with the section name: `car-washing-an-elephant-overview`, `feeding-your-elephant-overview`, and so on. Spaces are allowed.

Then use the following synatax to create links:

{{< elink "eid#headingId" "link text" >}}

The "link text" part is optional, in which case the link text is pulled automatically.

> Note that this is the full syntax. Do not place it in a markdown link in RelRef fashion.

### Examples

To link the "Foo Bar" heading on the same page:

```
{{< elink "#foo-bar" >}} -> [Foo Bar](#foo-bar)
```

To link the "Foo Bar" heading on the "Overview" page:

```
{{< elink "overview#foo-bar" >}} -> [Foo Bar](overview.md#foo-bar)
```

To link the "Overview" page:

```
{{< elink "overview" >}} -> [Overview](overview.md)
```

To link the "Foo Bar" heading on the "Overview" page with a custom link text:

```
{{< elink "overview#foo-bar" "click here" >}} -> [click here](overview.md#foo-bar)
```

### Multilingual Considerations

EID linking works great in multilingual sites. You don't need to change your links as long as you keep the EID the same between translated pages.

One exception are links to heading IDs. Relying on auto-generated IDs will break the links. In this case, I recommend enforcing the policy to tag all linked headings with a manual ID as shown below and using it consistently across translations.

```
## Foo Bar { id=resilient-id }
```

### Foolproof-ing

The reason the code relies on a new parameter (`eid`) instead of on an existing one like `.Page.File.UniqueId` is to make linking easy and intuitive. As long as you create logical EIDs, anyone would be able to quickly identify where the link ponts at.

The code is set to emit a console warning if it finds a duplicated EID, but it will not fail the build. Instead, it will create adjacent links to all files with the specified EID.

You can make the build fail if you substitute `errorf` for `warnf` in the final line.

## Working Links on Docsy `_print` Pages

The original [Docsy](https://github.com/google/docsy) `_print` page has a drawback—it keeps links unchanged, meaning they target articles outside of that page. As a result, PDFs generated from it have broken/unfunctional links.

This collection of redefines fixes that. It depends on the Docsy theme for Hugo.

Pick the following files from this repo and install them in your Docsy project as described in [Installation](#installation).

* `layouts/_default/_markup/render-heading.print.html`
  
  Print-specific render hook for headings.
  
  It modifies the original hook to append the unique File ID of each file (`href="#foobar875935964fc6d1e64cb4d3451cdf0779"`) to prevent repeating heading IDs within the `_print` page in case multiple files contain the same ID.
  
* `layouts/_default/_markup/render-link.print.html`
  
  Print-specific render hook for links.

  It uses the `IsAbs` method to recognize external links and leave them unchanged. It replaces link targets that only contain a file path (like `docs/overview.html`) with the file's unique File ID (`href="#28cedb2567cb30e9484048cb10eaac4a"`), which matches the H1 IDs normally created by Docsy (with a twist; see `content.html` below).
  
  It appends link targets that only contain an anchor (`href="#foobar"`) with the unique File ID of the file they are contained in (`href="foobar#28cedb2567cb30e9484048cb10eaac4a"`).

* `layouts/partials/print/content`
  
  Slight modification of the original code. It drops the `pg-` prefix from H1 IDs on line 9 for shorter IDs (instead of id="#pg-875935964fc6d1e64cb4d3451cdf0779" you'll get id="#875935964fc6d1e64cb4d3451cdf0779"). The render hooks rely on this change.

* `layouts/partials/print/toc-li.html`

  Slight modification of the original code. It drops the `pg-` prefix from H1 IDs on line 9 for shorter IDs (instead of id="#pg-875935964fc6d1e64cb4d3451cdf0779" you'll get id="#875935964fc6d1e64cb4d3451cdf0779"). The render hooks rely on this change.

### Usage

Copy the files and then restart your Dev server or rebuild. The Dev server can't pick up the changes live.
