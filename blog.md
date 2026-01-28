## Math Links (YouTube)

*January 28, 2026*

Without much comment, I will just post some links from my
favorite YouTube math people.

* [General Math (3blue1brown)](https://www.youtube.com/watch?v=6dTyOl1fmDo)
* [AMC 12 Math Exam](https://www.youtube.com/watch?v=SPbTyq3Dz_0&list=LL&index=8)
* [Lambda Calculus](https://www.youtube.com/watch?v=pAnLQ9jwN-E&list=LL)
* [Abstract Linear Algebra](https://www.youtube.com/watch?v=k2iFmlNRgBE&list=LL&index=108)
* [Abstract Algebra](https://www.youtube.com/watch?v=9hmr_Fjot_8&list=LL&index=121)
* [Visual Group Theory](https://www.youtube.com/watch?v=jZCG-ac7I_s&list=LL&index=105)

Some non-math stuff too:
* [Italian](https://www.youtube.com/watch?v=bZ1_vxGcwUQ&list=LL&index=34)

## Resurrecting a CoffeeScript Program

*January 28, 2026*

I was digging through my archives.  Back in 2011 I was really
into CoffeeScript. I wrote a little program with some math
widgets: [MathWidgets/client.htm](https://showell.github.io/MathWidgets/client.htm)

The main code is in [client.coffee](https://github.com/showell/MathWidgets/blob/master/client.coffee).

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

I'm still relatively fluent in CoffeeScript, it turns out.  Which isn't that much
use to me any more, since I now prefer more modern JavaScript and TypeScript. But
it was a fun language back in the time.
