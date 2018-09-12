<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->
**Table of Contents**

- [Constructable Stylesheet Objects](#constructable-stylesheet-objects)
    - [Problem Description](#problem-description)
    - [Past Solution: Shadow Piercing](#past-solution-shadow-piercing)
    - [Proposed Solution](#proposed-solution)
    - [Example Usage](#example-usage)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

# Constructable Stylesheet Objects


### Problem Description

A web page may contain tens of thousands of web components. The styles for these components will be specified in a small number of style sheets, perhaps one for each component library.

Each web component uses Shadow DOM. For a style sheet to take effect within the Shadow DOM, it currently must be specified using a style element within each shadow root.

This can easily have a large time and memory cost if user agents force the style sheet rules to be parsed and stored once for every style element. Some user agents might attempt to optimize by sharing internal style sheet representations across different instances of the style element. However, component libraries may use JavaScript to modify the style sheet rules, this will thwart style sheet sharing and have large costs in performance and memory.

Often, the main functionality inside the shadow root is provided by a single HTML element that may not itself have children. For a style sheet to also appear inside the shadow root, the shadow root can't be the single HTML element, or a style element, instead it must be a third element that simply contains the style element and the main HTML element that provides the functionality. Thus the shadow root contains three elements when one would otherwise suffice.

### Past Solution: Shadow Piercing

Early versions of the Web Component specifications allowed shadow piercing (/deep/, >>> and ::shadow). This allowed document-level style sheets to specify the styles that apply within shadow roots. This avoiding the problem of needing a style element within each Shadow DOM, but reduced encapsulation and has been [removed](https://www.w3.org/wiki/Webapps/WebComponentsApril2015Meeting) from the specifications.

### Proposed Solution

We can provide an API for creating stylesheet objects from script, without needing style elements. Script can optionally add or remove rules from a stylesheet object. Each stylesheet object can be added directly to any number of shadow roots (and/or the top level documemt).

### Example Usage

```
// Create style sheet when registering components.
let styleSheetList = new StyleSheetList([
    await document.createCSSStyleSheet("hr { color: green; }")
]);

// ...

// Apply style sheet in custom element constructor.
shadowRoot.adoptedStyleSheets = styleSheetList;
```


