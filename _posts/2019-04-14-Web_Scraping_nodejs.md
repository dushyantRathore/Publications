---
layout:     post
title:      Getting started with Web Scraping in Node.js
date:       2019-04-14 18:40:18
summary:    Steps to build a basic web scraper using Node.js
categories: 
---

# What is Web Scraping ?

Web scraping is data scraping used for extracting data from websites. Web scraping software may access the World Wide Web directly using the Hypertext 
Transfer Protocol, or through a web browser. While web scraping can be done manually by a software user, the term typically refers to automated 
processes implemented using a bot or web crawler. Nowadays, scraping plays an integral role in the design of Web APIs etc.

# Fundamental steps involved in Web Scraping

* Make a request to the Web Page to scrape data from.
* Extracting the body of the web page.
* Understanding the structure of the tags/elements that you want to extract from the webpage and making changes in the code to traverse the DOM 
accordingly.

# Why use Node.js ?

The open source project Node.js is one of the most popular runtime environments and has a bunch of features that make it easy to develop awesome 
stuff with it.

Manipulating the DOM inside a web browser is something that Javascript and libraries like jQuery do really well and so it makes a lot of sense 
to write web scraping scripts in Node.js, since we can use many techniques that we know from DOM manipulation in the client-side code for the web 
browser.

# Setup

To get started with web scraping using Node.js, one would need the following things setup :-

* [node.js](https://nodejs.org/)
* [npm](https://docs.npmjs.com/downloading-and-installing-node-js-and-npm)
* [request](https://www.npmjs.com/package/request)- `npm install request`
* [cheerio](https://www.npmjs.com/package/cheerio) - `npm install cheerio`

Cheerio is a fast, flexible, and lean implementation of core jQuery designed specifically for the server. In Cheerio, we use selectors to select 
tags of an HTML document. The selector syntax was borrowed from jQuery.The following is a partial list of available selectors:

* $("*") — selects all elements
* $("#first") — selects the element with id="first"
* $(".intro") — selects all elements with class="intro"
* $("div") — selects all `<div>` elements
* $("h2, div, p") — selects all `<h2>, <div>, <p>` elements
* $("li:first") — selects the first `<li>` element

# Example

For a basic example, we would be scraping the number of commits on [Cheerio's Github Page](https://github.com/cheeriojs/cheerio). The webpage 
with the DOM structure is shown below:

![Cheerio.png](https://cdn.filestackcontent.com/9LooU1AT9O6TS6F0ORYz)

As you can observe from the image, the number of commits exists in a `span` element having the class `num text-emphasized`. This span element is 
a child of `a (anchor)` tag which is in turn a child `li` tag having the class `commits'. The following code loads the necessary packages and 
extracts the number of commits from the webpage.

```javascript

// Load the necessary packages
var request = require("request");
const $ = require('cheerio');

//URL to scrape
var url = "https://github.com/cheeriojs/cheerio";

//Make the request
request(url, function(error, response, body){
    if(error){
        console.log(error);
    }
    else{
        console.log("Response : " + response);
        console.log("Response Status : " + response.status);
        //console.log("Body : " + body);
        
        //Extract the children of the list tag having class commits
        var li = $('li.commits', body)[0]["children"];
        
        //Loop over to extract the anchor tag
        for(var i = 0; i < li.length; i++){
        	//Check for anchor tag/type
            if(li[i]["type"] == "tag" && li[i]["name"] == "a"){
                for(var j = 0; j < li[i]["children"].length; j++){
                    //Check for the span having the class num text-emphasized
                    if(li[i]["children"][j]["name"] == "span"){
                        if(li[i]["children"][j]["attribs"]["class"] == "num text-emphasized"){
                            //console.log(li[i]["children"][j]);
                            console.log("Commits : " + li[i]["children"][j]["children"][0]["data"].trim());
                        }
                    }
            }
        }

    }
}})
```

Now, we will go through the code snippet section wise. 

* The initial part of the code imports the packages `request` and `cheerio` which are vital for the scraping process.
* Next, we store the URL to scrape in a var `url`. After this, we make a request to the URL using the request module and check for the error/response 
and handle it accordingly in two separate blocks (1st block for the error and the 2nd block for the scraping code).
* In the scraping block, we first extract the children of the `li` tag having the class `commits`. One can observe the list of children for the `li` 
tag in the figure below.

![Cheerio_2.png](https://cdn.filestackcontent.com/cdBuJNWjQu6Jl3zG0m6w)

* Once, we have the list of children for `li` tag ready, we loop over each to select the one having `["type"] == "tag"` and `["name"] == "span"`.
* Once we have extracted the `anchor` tag, we perform the same loop over its list of children to find the `span` child having the class 
`num text-emphasized`. This is done using `["name"] == "span"` and `["attribs"]["class"] == "num text-emphasized"`.
* Finally, after we have the `span` element with us, we extract its data and trim it to remove the whitespaces. This leaves us with the number 
of commits. The output of the entire code is shown in the following snippet.

![Cheerio_3.png](https://cdn.filestackcontent.com/naSd7ABVRMCaMhpeRLNd)

Congrats! You just wrote your first web scraper using node.js! :D

