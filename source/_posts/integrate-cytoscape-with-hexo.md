title: Integrate cytoscaope with hexo (整合cytoscape和hexo)
date: 2015-12-08 04:57:24
description: 
categories: Note
tags:
- Hexo
- Graph Theory
cytoscape: true

---

# Introduction
[Cytoscape.js](http://js.cytoscape.org) is an open source graph theory visaulization library written in Javascript. This article guide you through the procedures of integrating the library with hexo. There are many ways to do so, this is only one of the methods. People tend to put the javascript files inside `after_footer.ejs`, however, the article content is loaded before javascript files. If you want to write the script of drawing graphs JUST INSIDE the markdown file, you have to wait until the javascript files of is loaded.

# Installation:
The following guideline is written based on the themes called pacman.
1. Download the `js` file and the `js.map` file from [the offical website](http://js.cytoscape.org)
2. Put the `js` file and the `js.map` file inside the `themes/pacman/source/js` folder
3. Download the waitUntil function from [here](http://www.devign.me/asynchronous-waiting-for-javascript-procedures/), create a file called `waitUntil.js` put it inside `themes/pacman/source/js` folder
4. Put the code inside `themes/pacman/layout/_partial/head.ejs`, which load `waitUntil` function before the article content
{% codeblock %}
<% if (page.cytoscape) { %>
	<%- js('js/waitUntil.js') %>
<% } %>
{% endcodeblock %}
5. Put the code inside `themes/pacman/layout/_partial/after_footer.ejs`
{% codeblock %}
<% if (page.cytoscape) { %>
	<%- js('js/cytoscape.js') %>
<% } %>
{% endcodeblock %}
6. Add an attribute to the post: `cytoscape: true`
{% asset_img 'attribute.png' %}
7. Put the following codes inside the markdown file. It draws the graph inside the container called `cy` after the `cytoscape` and `jquery` are loaded. Remember that you have to set the style of the container, otherwise the graph will be invisible.
{% codeblock %}
<div id="cy" style="width: 700px; height: 700px; display: block;"></div>

<script>
function draw(){
	var cy = cytoscape({

	  container: $("#cy"), // container to render in

	  elements: [ // list of graph elements to start with
	    { // node a
	      data: { id: 'a' }
	    },
	    { // node b
	      data: { id: 'b' }
	    },
	    { // edge ab
	      data: { id: 'ab', source: 'a', target: 'b' }
	    }
	  ],

	  style: [ // the stylesheet for the graph
	    {
	      selector: 'node',
	      style: {
	        'background-color': '#666',
	        'label': 'data(id)'
	      }
	    },

	    {
	      selector: 'edge',
	      style: {
	        'width': 3,
	        'line-color': '#ccc',
	        'target-arrow-color': '#ccc',
	        'target-arrow-shape': 'triangle'
	      }
	    }
	  ],

	  layout: {
	    name: 'grid',
	    rows: 1
	  }

	});
}

$waitUntil(function(){ return typeof(cytoscape) != "undefined" && typeof($) != "undefined"}, 
	       draw);
</script>
{% endcodeblock %}

# Result:
<div id="cy" style="width: 700px; height: 700px; display: block;"></div>

<script>
function draw(){
	var cy = cytoscape({

	  container: $("#cy"), // container to render in

	  elements: [ // list of graph elements to start with
	    { // node a
	      data: { id: 'a' }
	    },
	    { // node b
	      data: { id: 'b' }
	    },
	    { // edge ab
	      data: { id: 'ab', source: 'a', target: 'b' }
	    }
	  ],

	  style: [ // the stylesheet for the graph
	    {
	      selector: 'node',
	      style: {
	        'background-color': '#666',
	        'label': 'data(id)'
	      }
	    },

	    {
	      selector: 'edge',
	      style: {
	        'width': 3,
	        'line-color': '#ccc',
	        'target-arrow-color': '#ccc',
	        'target-arrow-shape': 'triangle'
	      }
	    }
	  ],

	  layout: {
	    name: 'grid',
	    rows: 1
	  }

	});
}

// wait util the javascript file of cytoscape and jquery are loaded
$waitUntil(function(){ return typeof(cytoscape) != "undefined" && typeof($) != "undefined"}, 
	       draw);
</script>