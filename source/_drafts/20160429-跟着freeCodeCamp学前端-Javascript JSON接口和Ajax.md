---
title: 跟着freeCodeCamp学前端-Javascript JSON接口和Ajax
date: 2016-04-17 17:50:30
categories: Javascript
tags: [Javascript]
---

# Javascript Json API and Ajax



```javascript

<script>
  $(document).ready(function() {
    $("#getMessage").on("click", function(){
      // Only change code below this line.
      $(".message").html("Here is the message");
      // Only change code above this line.
    });
  });
</script>


<div class="container-fluid">
  <div class = "row text-center">
    <h2>Cat Photo Finder</h2>
  </div>
  <div class = "row text-center">
    <div class = "col-xs-12 well message">
      The message will go here
    </div>
  </div>
  <div class = "row text-center">
    <div class = "col-xs-12">
      <button id = "getMessage" class = "btn btn-primary">
        Get Message
      </button>
    </div>
  </div>
</div>

```



```javascript
$.getJSON("/json/cats.json", function(json) {
        $(".message").html(JSON.stringify(json));
        });
```



```javascript

<script>
  $(document).ready(function() {

    $("#getMessage").on("click", function(){
      // Only change code below this line.
      $.getJSON("/json/cats.json", function(json) {
        $(".message").html(JSON.stringify(json));
        });
           
      // Only change code above this line.
    });

  });
</script>

<div class="container-fluid">
  <div class = "row text-center">
    <h2>Cat Photo Finder</h2>
  </div>
  <div class = "row text-center">
    <div class = "col-xs-12 well message">
      The message will go here
    </div>
  </div>
  <div class = "row text-center">
    <div class = "col-xs-12">
      <button id = "getMessage" class = "btn btn-primary">
        Get Message
      </button>
    </div>
  </div>
</div>
```



```json
[{
		"id" : 0,
		"imageLink" : "https://s3.amazonaws.com/freecodecamp/funny-cat.jpg",
		"codeNames" : ["Juggernaut", "Mrs. Wallace", "Buttercup"]
	}, {
		"id" : 1,
		"imageLink" : "https://s3.amazonaws.com/freecodecamp/grumpy-cat.jpg",
		"codeNames" : ["Oscar", "Scrooge", "Tyrion"]
	}, {
		"id" : 2,
		"imageLink" : "https://s3.amazonaws.com/freecodecamp/mischievous-cat.jpg",
		"codeNames" : ["The Doctor", "Loki", "Joker"]
	}
]
```



```javascript

<script>
  $(document).ready(function() {

    $("#getMessage").on("click", function() {
      $.getJSON("/json/cats.json", function(json) {

        var html = "";
        // Only change code below this line.
        
        json.forEach(function(val) {
          var keys = Object.keys(val);
          html += "<div class = 'cat'>";
          keys.forEach(function(key) {html += "<b>" + key + "</b>: " + val[key] + "<br>";});
          html += "</div><br>";
          });
        
        // Only change code above this line.

        $(".message").html(html);

      });
    });
  });
</script>

<div class="container-fluid">
  <div class = "row text-center">
    <h2>Cat Photo Finder</h2>
  </div>
  <div class = "row text-center">
    <div class = "col-xs-12 well message">
      The message will go here
   </div>
  </div>
  <div class = "row text-center">
    <div class = "col-xs-12">
      <button id = "getMessage" class = "btn btn-primary">
        Get Message
      </button>
    </div>
  </div>
</div>
```





```javascript

<script>
  $(document).ready(function() {

    $("#getMessage").on("click", function() {
      $.getJSON("/json/cats.json", function(json) {

        var html = "";

        json.forEach(function(val) {

          html += "<div class = 'cat'>";

          // Only change code below this line.
          
          html += "<img src = '" + val.imageLink + "'>";
          
          // Only change code above this line.

          html += "</div>";

        });

        $(".message").html(html);

      });
    });
  });
</script>

<div class="container-fluid">
  <div class = "row text-center">
    <h2>Cat Photo Finder</h2>
  </div>
  <div class = "row text-center">
    <div class = "col-xs-12 well message">
      The message will go here
    </div>
  </div>
  <div class = "row text-center">
    <div class = "col-xs-12">
      <button id = "getMessage" class = "btn btn-primary">
        Get Message
      </button>
    </div>
  </div>
</div>

```



```javascript
<script>
  // Only change code below this line.
  if (navigator.geolocation) {
  navigator.geolocation.getCurrentPosition(function(position) {
  $("#data").html("latitude: " + position.coords.latitude + "<br>longitude: " + position.coords.longitude);
  });
  }
  
  
  // Only change code above this line.
</script>
<div id = "data">
  <h4>You are here:</h4>
  
</div>
```

