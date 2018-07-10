---
layout: default
title: Viewify Quick Start
permalink: /viewify-quick-start/
tags: ['getting-started']
---

# Getting Started with Views

- [schema.json](#schema)
- [View Entity](#view-entity)
- [config.json](#config)
- [browser.json](#browser-config)

## Schema

```javascript
{
  "Apex": {
    "Entity": "CustomView.js",
    "$Setup": "Setup"
  }
}
```

## View Entity

```javascript
(function CustomView() {
  class CustomView {
    Setup(com, fun) {
      this.super(com, (err, cmd) => {
        this.Vlt.text = $(document.createElement('p'));
        this.Vlt.text.html('Hello Views!');
        fun(null, com);
      });
    }
    Render(com, fun) {
      // detaching the children is just customary.
      // be sure to properly remove children if need be
      // otherwise you'll have memory leak
      this.Vlt.div.children().detach();
      this.Vlt.div.append(this.Vlt.text);
      this.super(com, (err, cmd) => {
        fun(null, com);
      });
    }
  }

  return Viewify(CustomView, "3.0");

})();
```

## Config

```javascript
{
  "Sources":{
    "core": "{core}"
  },
  "Modules": {
    "Server": {
      "Module": "xGraph.WebViewer",
      "Source": "core"
      "Par": {
        "Url": "Index",
        "Port": 8080
      }
    }
  }
}
```

## Browser Config

```javascript
{
  "Sources": {
    "core": "{core}",
    "local": "{local}"
  }
  "Modules": {
    "Root": {
      "Module": "xGraph.RootView",
      "Source": "core"
      "Par": {
        "Layout": "$CustomView"
      }
    },
    "CustomView": {
      "Module": "CustomView",
      "Source": "local"
    }
  }
}
```