---
layout: post
author: yashumittal
title: An Introduction to Source Maps
date: 2018-02-24 23:17:40 +0530
categories: code
tags: coding css javascript source-control source-maps web-app
description: One of the easiest performance wins you can gain for your website is to combine and compress your JavaScript and CSS files
image: https://i.imgur.com/FH0zaUR.png
---

One of the easiest performance wins you can gain for your website is to combine and compress your JavaScript and CSS files. But what happens when you need to debug the code within those compressed files? It can be a nightmare. Fear not however, there is a solution on the horizon and it goes by the name of source maps.

A source map provides a way of mapping code within a compressed file back to it’s original position in a source file. This means that – with the help of a bit of software – you can easily debug your applications even after your assets have been optimized. The Chrome and Firefox developer tools both ship with built-in support for source maps.

In this blog post you’re going to learn how source maps work and take a look at how to generate them. We’re going to be focussing primarily on source maps for JavaScript code but the principles apply to CSS source maps too.

***

**Note**: Support for source maps is enabled by default in Firefox’s developer tools. You may need to enable support manually in Chrome. To do this, launch the Chrome dev tools and open the **Settings** pane (cog in the bottom right corner). In the **General** tab make sure that **Enable JS source maps** and **Enable CSS source maps** are both **ticked**.

***

## How Source Maps Work

As the name suggests, a source map consists of a whole bunch of information that can be used to map the code within a compressed file back to it’s original source. You can specify a different source map for each of your compressed files.

You indicate to the browser that a source map is available by adding a special comment to the bottom of your optimised file.

```
    //# sourceMappingURL=/path/to/script.js.map
```

This comment will usually be added by the program that was used to generate the source map. The developer tools will only load this file if support for source maps is enabled and the developer tools are open.
You can also specify a source map is available by sending the `X-SourceMap` HTTP header in the response for the compressed JavaScript file.

```
    X-SourceMap: /path/to/script.js.map
```

The source map file contains a JSON object with information about the map itself and the original JavaScript files. Here is a simple example:

```ssh
    {
        version: 3,
        file: "script.js.map",
        sources: [
            "app.js",
            "content.js",
            "widget.js"
        ],
        sourceRoot: "/",
        names: ["slideUp", "slideDown", "save"],
        mappings: "AAA0B,kBAAhBA,QAAOC,SACjBD,OAAOC,OAAO..."
    }
```

Lets take a closer look at each of these properties.

```ssh
    version – This property indicates which version of the source map spec the file adheres to.
    file – The name of the source map file.
    sources – An array of URLs for the original source files.
    sourceRoot – (optional) The URL which all of the files in the sources array will be resolved from.
    names – An array containing all of the variable and function names from your source files.
    mappings – A string of Base64 VLQs containing the actual code mappings. (This is where the magic happens.)
```

### Generating Source Maps with UglifyJS

[UglifyJS](https://github.com/mishoo/UglifyJS2) is a popular command line utility that allows you to combine and compress JavaScript files. Version 2 supports a number of command line flags that help with generating source maps.

```ssh
    --source-map – The output file for the source map.
    --source-map-root – (optional) This populates the sourceRoot property in the map file.
    --source-map-url – (optional) The path to the source map on your server. This will be used in the comment that is placed in the optimized file. //# sourceMappingURL=/path/to/script.js.map
    --in-source-map – (optional) An input source map. This can be useful if you are compressing JavaScript files that have already been generated from source files elsewhere. Think JavaScript libraries.
    --prefix or -p – (optional) Removes n number of directories from the file paths that appear in the sources property. For example, -p 3 would drop the first three directories from the file path, so one/two/three/file.js would become file.js. Using -p relative will make UglifyJS figure out the relative paths between the source map and the original files for you.
```

Here’s an example command that uses some of these command line flags.

```ssh
uglifyjs [input files] -o script.min.js --source-map script.js.map --source-map-root http://example.com/js -c -m
```

***

**Note**: If you use the `grunt-contrib-uglify` plugin for Grunt, refer to the [documentation](https://github.com/gruntjs/grunt-contrib-uglify#sourcemap) for information on how to specify these options in your Gruntfile.

***

There are also a number of other utilities available that have support for generating source maps. A selection of these are listed below.

* [Closure](http://www.html5rocks.com/en/tutorials/developertools/sourcemaps/#toc-howgenerate)
* [CoffeeScript Compiler](http://coffeescript.org/#source-maps)
* [GruntJS Task for JSMin](https://github.com/twolfson/grunt-jsmin-sourcemap)

### Source Maps in Chrome Dev Tools

![The Sources Tab in Chrome Dev Tools](https://i.imgur.com/FH0zaUR.png)

If you have your source maps set up correctly, you should see each of the original JavaScript files listed in the file pane of the **Sources** tab.

Examining the HTML for your page will confirm that only the compressed JavaScript file is being referenced. The dev tools is loading the source map file for you and then fetching each of the original source files. [**Try a Demo**](http://demos.mattwest.io/source-maps/)

### Source Maps in the Firefox Developer Tools

![The Debugger Tab in the Firefox Developer Tools](https://i.imgur.com/nuoqNzm.png)

Firefox users can see the individual source files in the **Debugger** tab of the developer tools. Again the dev tools has identified that a source map is available and has then fetched each of the referenced source files.

Should you wish to view the compressed versions instead, click the cog icon in the top right corner of the tab and deselect **Show original sources**. [**Try a Demo**](http://demos.mattwest.io/source-maps/)

## Final Thoughts

Using source maps allows developers to maintain a straight-forward debugging environment while at the same time optimizing their sites for performance.
In this post you have learned how source maps work and seen how you can generate them using UglifyJS. If you ever ship websites with compressed assets (which you should), it’s really worth taking the time to integrate source map creation into your workflow.

## Useful Links

* [Source Maps Revision 3 Proposal](https://docs.google.com/document/d/1U1RGAehQwRypUTovF1KRlpiOFze0b-_2gc6fAH0KY0k/edit)
* [UglifyJS](https://github.com/mishoo/UglifyJS2)
* [Source Maps Demo](http://demos.mattwest.io/source-maps/)