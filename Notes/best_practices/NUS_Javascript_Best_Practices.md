# NUS Technology

# Javascript Best Practices

- Avoid using global variables if not neccessary
- Store main JS code inside namespace, do not put your function in window object
- Use JS template instead of string concatenation to create a HTML template
- Coffee Script is preferrable
- Avoid memory leak Refer [Avoid memory leak while using Jquery](http://dev.nustechnology.com/avoid-memory-leak-while-using-jquery/)
- Avoid using eval()
- Alway stop a delay action, before run a same delay action again:

  ```
  $(window).on "resize", =>
    clearTimeout @resizeCanvas
    @resizeCanvas = setTimeout(=>
      @calculateEsignCanvas()
    , 60)
  ```

You can build a helper to do it automatic:

```
@g_delay =
  defaultDelayTime: 100
  delays: {}
  delay: (key, handler, whoCall, time = @defaultDelayTime)->
    clearTimeout @delays[key] if @delays[key]
    @delays[key] = setTimeout(_.bind(handler, whoCall), time)
```

then call with:

```
g_delay.delay("calculateEsignCanvas", @calculateEsignCanvas, @, 60)

```
