# Chrome iframe dnd bug

In this demo, we're trying to intercept files being dropped into a cross origin iframe. To do this, our inner iframe listens for drag and drop events and when dragging starts, tells the outer webpage to create an overlay on top of the iframe that should intercept all future drag and drop events.

This approach works fine in Safai and Firefox in my testing, but fails in Chrome. The drag and drop events still fire on the inner iframe even after we add the overlay element to the outer webpage.

### Steps to reproduce

1. Start a local server to serve `index.html` (such as `localhost:8080`)
1. In `index.html`, make update `src` so that it loads `sub.html` on a different origin (such as `127.0.0.1:8080/sub`)
1. Open `localhost:8080` in chrome
1. Open the dev tools to view logs
1. Try dragging a file from the operating system into the iframe (the green area).
1. A red overlay should show.
1. Now drop the file onto the iframe area (which now has the red overlay)

## Bug

In Chrome when `iframe` is not on the same origin as the outer page, the drag and drop events end up being fired on the iframe (even after the overlay is added). The event sequence looks like:

```
Inner —  Drag enter
Inner — Drag over
Inner —  Drag enter
Inner — Drag over
...
Inner — Drag over
Inner — Drop
```

In firefox and Safari, the drag/drop events are instead fired on the overlay once it is added. The events look like:

```
Inner —  Drag enter
Inner — Drag over 
Outer — Drag enter
Outer — Drag over
...
Outer — Drag over
Outer container — Drop
Outer — Drop
```

This is also what you see in Chrome if the iframe is on the same origin as the webpage