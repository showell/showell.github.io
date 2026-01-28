## Online Drawing (2011)

*January 28, 2026*

Back in 2011 I created a little logo-like tool to teach folks
how to use the canvas.  It used CoffeeScript as its language.
I think it's a pretty good language for that particular task.

Here is a screenshot from the app or you can
[just run the app here](http://showell.github.io/OnlineDrawing/demo.htm):

![OnlineDrawing](smiley.webp)

You can explore the code at [my OnlineDrawing repo](https://github.com/showell/OnlineDrawing)

The program was able to run 14 years later. I just had to update
jQuery so that modern browsers like Brave would run it.

~~~ diff
-    <script type="text/javascript" src="http://ajax.googleapis.com/ajax/libs/jquery/1.6.1/jquery.min.js"></script>
+    <script src="https://code.jquery.com/jquery-4.0.0.min.js"
+                   integrity="sha256-OaVG6prZf4v69dPg6PhVattBXkcOWQB62pdZ3ORyrao="
+                   crossorigin="anonymous">
+    </script>
~~~

There are some fun technical details in the program.  For example, I actually run
the CoffeeScript compiler in the browser:

~~~ coffeescript
run_code = (code) ->
  try
    js = CoffeeScript.compile CHALLENGE.prelude + code
  catch e
    console.log e
    console.log "(problem with compiling CS)"
  eval js
~~~

Here is a typical challenge called "Launch the Ball":

~~~ coffeescript
  {
    title: "Launch the Ball"

    prelude: '''
      env = window.helpers()
      {circle, launch} = env
      ''' + '\n'

    code: '''
      # Challenge: Change the angle so that you launch the ball clear over the wall.
      # Just use trial and error to find the correct steepness.
      ball = circle()
      angle = 35
      launch ball, angle
      '''
  },
~~~

Here's the launch helper:

~~~ coffeescript
  launch = (ball, angle) ->
    wall_offset = 315
    wall_height = 427
    ball_radius = 15
    line [wall_offset, 0], [wall_offset, wall_height]
    line [wall_offset - ball_radius, wall_height], [wall_offset, wall_height]

    cx = 0
    cy = 0
    ball.goto(0, 0)
    v = 7
    dx = v * cosine(angle)
    dy = v * sine(angle)
    over_wall = false
    flying = true
    repeat ->
      return if !flying

      flying = false if cy < 0 or cx > width

      if flying and !over_wall and cx + ball_radius >= wall_offset
        if cy > wall_height + ball_radius
          if cx >= wall_offset
            over_wall = true
        else
          flying = false

      if flying
        cx += dx
        cy += dy
        ball.goto(cx, cy)
        dy -= 0.05
~~~


I used a home-grown HAML-like system to buid out my HTML. I called
it PipeDent.

~~~ coffeescript
demo_layout = \
  '''
  table
    tr valign="top"
      td id ="sideBar"
        ul id="program_list" |
      td id="leftPanel"
        h2 id="leftPanel" | Input
        input type="submit" value="Run" id="runCode" |
        <br>
        textarea id="input_code" rows=30 cols=80 |
      td id="rightPanel"
        h4 | Output
        div id="main" |
  '''
~~~

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
