---
author: "Rupa Sharma"
date: 2017-03-06
linktitle: Handling symbols from Salesforce with Angular 2
menu:
  main:
    parent: tutorials
next: /tutorials/github-pages-blog
prev: /tutorials/automated-deployments
title: Handling symbols from Salesforce with Angular 2
weight: 10
---


### **Introduction**

I've been working on a new Angular 2 project that connects to a Salesforce backend. What I have noticed is that Salesforce encodes a few protected symbols for all it's returns from their backend. We needed a way to digest the json back from Salesforce and dncode all the html codes that are returned. 

#### First attempt - FAILED

Our first attempt was to test out decodeURIComponent(str: string). We would expect this to decode all the html symbols, but if you check the documentation for it, it is mainly used with URIs and doesn't decode all symbols. 

#### Second attempt - SUCEEDED

Our second attempt was to use the html evaluator what was built into innerHTML. We previously built an email template preview component, it took an html string and evaluated it into the innerHTML of a div. It was great for taking all symbols and converting them to the appropriate symbols... so we built a service called decodeHTML(value: any). It uses Angular Renderer and ElementRef. 

Here's out code:

```
import { Injectable, Renderer, ElementRef } from '@angular/core';

@Injectable()
export class DecoderService { 

    constructor( private renderer : Renderer, private elementRef : ElementRef ) {
    }

    decodeHTML(value: any) : any {
        if (value === undefined) {
            return "";
        }
        
        try {
            let div = this.renderer.createElement(undefined, "div");
            this.renderer.setElementProperty(div, "innerHTML", value);
            let result = div.innerText;
            return result;
        } catch (ex){
            console.log(ex);
            return value;
        }
    }
}
```
 
> This open source code is not gauranteed to work for everyone, please test and see what works for you.

### Take Away

We created a service that creates a dom element that is never attached to the dom. We used this element to process an encoded string from Salesforce to convert html symbols to actual symbols. This was safer than using innerHTML within our pages and forms, as it could've allowed for malicious string to trigger the execution of some scripts. 

This is definitely a case that the Angular 2 team should look to solve in the future.