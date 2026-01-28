## Resurrecting a CoffeeScript Program

*January 28, 2026*

I was digging through my archives.  Back in 2011 I was really
into CoffeeScript. I wrote a little program with some math
widgets:

https://showell.github.io/MathWidgets/client.htm

Unfortunately, it was using a really old version of jQuery.
I could have simply upgraded jQuery, but there was never
any reason to have that dependency.  The DOM API is perfectly
fine.

I wasn't using anything particularly fancy, so I just replaced
a couple jQuery-ism with my own wrappers:

~~~ diff
+get_div = (selector) ->
+    document.querySelector selector
+
+append = (div, html) ->
+    div.innerHTML += html
+
 Canvas = (div, id, width=600, height=300) ->
   canvas_html = """
     <canvas id='#{id}' width='#{width}' height='#{height}' style='border: 1px blue solid'>
     </canvas>
   """
-  div.append canvas_html
+  append div, canvas_html

   canvas = document.getElementById(id)
   ctx = canvas.getContext("2d")
@@ -60,7 +66,7 @@ Linkage = ->
     y *= y_distort
     [x * 20 + 100, height - y * 20 - 10]

-  canvas = Canvas $("#linkage"), "linkage_canvas", width, height
+  canvas = Canvas get_div("#linkage"), "linkage_canvas", width, height
~~~

And problem solved!

I have no intention of cleaning up the program further. It was a pretty experimental
program to begin with, and I honestly don't remember all the math concepts that went
into it.  But now I still have it running!
