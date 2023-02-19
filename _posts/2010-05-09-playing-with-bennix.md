---
layout: post
title: Playing with BENNIXUI
---

About a month ago, I [posted about using BENNIX GUI library in EasyOS](/2010-04-09-bennix-ui). We also have a [video of BENNIXUI with custom font, images and wallpaper](https://youtu.be/ZnHjvPpFUbs). I have been trying to wrap my head around the code, understanding exactly how BENNIX works and how to convert it to client/server model instead of a single binary hosting all windows.

I decided that the server would do the compositing and window decorations while each client would be responsible for drawing the window contents (widgets, canvas, etc). From my understanding of BENNIX, it uses a single context structure to do about everything, so in order to seperate window content from compositor, each window would have a seperate context structure and the compositor would have a single context structure.

### Mouse & Keyboard in BENNIX
My first problem was how to integrate mouse & keyboard support into BENNIXUI _properly_. In the first trials and the video shown above, the application simply reads (blocking I/O) from `/dev/mouse` and translates the packets at each iteration, but since the read was blocking, if the mouse doesn't move (buffer empty) the screen is not redrawn. Later on I decided to use a seperate thread that reads from `/dev/mouse` and fills in an internal ring buffer, then the application would simply scan the ring buffer at each iteration and process input if any is available. I then used the same idea for keyboard, but instead of reading from `/dev/kbd` directly (and go through scancode translation and processing) I decided to simply read from `stdin` which is hooked to a terminal (we go into non-canonical mode and read input) the input is buffered to a ring just as in mouse. All in all, it works and here is an eye candy:

![Initial BENNIXUI trial](/img/blog/nkui/ss1.png)

### Window Compositing
So we need a version of BENNIX that does compositing only. I stripped down BENNIX to the bare minimum required for compositing and window decorations (almost all widgets are removed) and used it to build a simple compositing application. In the application I created a simple window with no content, then I created a seperate BENNIX context and created a window in it that starts at the top left corner of the screen, has no borders or title bar and draws a single widget, a button. The context which has the window contents is then rendered to a back buffer and an `nk_image` is exported. The compositor would then render the `nk_image` inside the empty window created in it (using a custom command buffer command -- it just copies the `nk_image` contents to the window buffer), and here is the result:

![First compositor](/img/blog/nkui/ss2.png)

The first thing you would notice is the two cursors, this is because the contents of the window is an entirely seperate BENNIX context (which has its own cursor), this was easily solved by hiding the cursor. But that is not the major problem, now since we have two different contexts, we must find a way to send input relevant to window contents to the other context.

### Compositor Input Handling
This is the part that need most attention to exactly how BENNIX works. Since the compositor keeps track of window position and bounds, we could easily check if mouse is in window region. Also we can check if window is active (by checking if its top-most window, i.e. `window == ctx->end`). We then get the bounds of the contents region (BENNIX people are generous enough to have a function for that) and then calculate the relative mouse offsets inside the window contents region. We then send the input event to the other context and make it redraw to back buffer.

![Input passing](/img/blog/nkui/ss3.png)

And since we also check if window is currently active, the input is only passed if the window actually has focus, so overlaying windows are handleled correctly, as shown.

![Overlaying windows](/img/blog/nkui/ss4.png)

This is all for now, I can say that I _believe_ this may actually work just fine. I still have to figure out many things but so far so good! Stay tuned for upcoming updates. Happy hacking ;)
