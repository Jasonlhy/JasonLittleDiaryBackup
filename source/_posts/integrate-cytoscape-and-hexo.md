title: 整合cytoscape 和hexo
date: 2015-12-08 04:57:24
description: 
categories:
tags:
cytoscape: true

---

Waiting for finish

結果：
Result:
<div id="cy" style="width: 700px; height: 700px; display: block;"></div>


<script>
function draw(){
	var cy = cytoscape({

	  container: document.getElementById('cy'), // container to render in

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

$waitUntil(function(){ return typeof(cytoscape) != "undefined" }, 
	       draw);
</script>