# hugo
Collection of Hugo shortcodes, templates, and render hooks.

## Installation

Copy the file that you want into your Hugo project, preserving the directory structure of this repo. 

## elink.html

This shortcode allows you to link pages using a uniqe ID. This way, you get a working link wherever and whenever you move the file containing the link.

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
{{< elink "overview#foo-bar" >}} -> [Foo Bar](#foo-bar)
```

To link the "Overview" page:

```
{{< elink "overview" >}} -> [Overview](#foo-bar)
```

To link the "Foo Bar" heading on the "Overview" page with a custom link text:

```
{{< elink "overview#foo-bar" "click here" >}} -> [click here](overview#foo-bar)
```

### Foolproof-ing

The code is set to emit a console warning if it finds a duplicated EID, but it will not fail the build. Instead, it will create adjacent links for all EIDs it finds.

You can make the build fail if you substitute `errorf` for `warnf` in the final line.
