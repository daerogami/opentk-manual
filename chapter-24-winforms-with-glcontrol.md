This tutorial assumes familiarity with`Windows.Forms`application development in Visual Studio 2005/C\#, and at least basic knowledge of OpenGL. It also assumes a top-to-bottom readthrough; it is a guide and not a reference.

To begin with, it is quite a different approach one has to take when designing a game/application using the`GLControl`in a Windows.Form compared to using the`GameWindow`.`GLControl`is more low-level than`GameWindow`so you'll have to pull the strings on for example time measurements by yourself. In`GameWindow`, you get more for free!

Just as in the GameWindow case, GLControl uses the default OpenGL driver of the system, so with the right drivers installed it will be hardware accelerated. However, with large windows it will be slower than the corresponding fullscreen GameWindow, because of how the underlying windowing system works \[someone with more detailed knowledge than me may want to elaborate on this..\].

If you come from a "main-loop-background" \(C/SDL/Allegro etc.\) when it comes to coding games, you'll have to rethink that fundamentally. You'll have to change into a mindset of "what event should I hook into, and what events should I trigger, and when?" instead.

**Why use Windows.Forms+GLControl instead of GameWindow?**  
The first thing you'll have to decide is:

_"Do I really need the added complexity of`Windows.Forms+ embeddedGLControl`compared to a windowed`GameWindow`?"_

Here are some reasons why you would like to add that complexity:

1. You want to build a GUI-rich/RAD kind of application using the
   `Windows.`
   `Forms`
   controls. Eg. level editors or model viewers/editors may be in this category while a windowed game leans more towards a
   `GameWindow`
   kind of application.
2. You want to have an embedded OpenGL rendering panel inside an already existing
   `Windows.`
   `Forms`
   application.
3. You want to be able to do drag-and-drop into the rendering panel, for example dropping a model file into a model viewer.

Assuming you've got at least one of those reasons to build a`Windows.Forms+GLControl`based application, here's the steps, gotchas and whys for you.

**Adding the GLControl to your Windows.Form**  
_\(I am assuming you are using Visual Studio 2005 Express Edition. Your mileage may vary if using VS2008 or Monodevelop -- I don't know the details for them -- but the follow sections should be the same no matter how you add the GLControl\)_  
To begin with, create a Form on which you will place your`GLControl`. Right click in some empty space of the Toolbox, pick "Choose Items..." and browse for OpenTK.GLControl.dll. Make sure you can find the "GLControl" listed in the ".NET Framework Components", as in the image below.

[![](https://web.archive.org/web/20151015042206im_/http://www.opentk.com/files/VSAddGLControl6.png)](https://web.archive.org/web/20151015042206/http://www.opentk.com/files/VSAddGLControl6.png)

Then you can add the`GLControl`to your form as any .NET control. A GLControl named`glControl1`will be added to your`Form`.

**Order of creation**  
The fact that`glControl1`'s`GLContext`is created in runtime is important to remember however, since you cannot access or change`glControl1`'s properties reliably until the`GLContext`has been created. The same is true for any`GL.*`commands \(or`Glu`for that matter!\). The conceptual order is this:

1. First the Windows.Form constructor runs.
   _Don't touch_
   _`glControl/`_
   _`GL`_
2. Then the
   `Load`
   event of the form is triggered.
   _Don't touch_
   _`glControl/`_
   _`GL`_
3. Then the
   `Load`
   event of the GLControl is triggered.
   _OK to touch_
   _`glControl/`_
   _`GL`_
4. After the
   `Load`
   event handler has run, any event handler may touch
   `glControl/`
   `GL`
   .

So one approach to address this problem is having a`boolloaded=false;`member variable in your Form, which is set to`true`in the`Load`event handler of the GLControl:

```
 using OpenTK.Graphics;
   using OpenTK.Graphics.OpenGL;
 
  public partial class Form1 : Form
  {
    bool loaded = false;
 
    public Form1()
    {
      InitializeComponent();
    }
 
    private void glControl1_Load(object sender, EventArgs e)
    {
      loaded = true;
    }
  }
```

**Note: the GLControl.Load event is never fired -- please use the Form.Load event instead until**[**this issue**](https://web.archive.org/web/20151015042206/http://www.opentk.com/node/1071)**of OpenTK is fixed.**

Then in any event handler where you are going to access`glControl1/GL`you can put a guard first of all:

```
 private void glControl1_Resize(object sender, EventArgs e)
    {
      if (!loaded)
        return;
    }
```

A popular way of adding a Load event handler to a Form is via the Properties window of Visual Studio, something like this:

1. Click anywhere on the GLControl in the Designer
2. Make sure glControl1 is listed in the Properties window
3. Click the "Events" button to list all events of glControl1
4. Double-click the empty cell right of the Load event to create and hook an event handler for the Load event

**Hello World!**  
The absolutely minimal code you can add at this stage to see something is adding an event handler to the`Paint`event of`glControl1`and filling it with this:

```
private void glControl1_Paint(object sender, PaintEventArgs e)
{
    if (!loaded) // Play nice
    return;
 
    GL.Clear(ClearBufferMask.ColorBufferBit | ClearBufferMask.DepthBufferBit);
    glControl1.SwapBuffers();
}
```

![](https://web.archive.org/web/20151015042206im_/http://www.opentk.com/files/Form1.png)

Yes! A beige viewport. Notice that the`GLControl`provides a color- and a depth buffer, which we have to clear using`GL.Clear()`._\[TODO: how to control which buffers and formats the GLControl has? Possible at all?\]_

Next thing would be setting the clear color. An appropriate place to do GL initialization is in the form's`Load`event handler:

```
 private void glControl1_Load(object sender, EventArgs e)
    {
      loaded = true;
      GL.ClearColor(Color.SkyBlue); // Yey! .NET Colors can be used directly!
    }
```

![](https://web.archive.org/web/20151015042206im_/http://www.opentk.com/files/Form2.png)

**Viewport initialization**  
Next thing we want to do is draw a single yellow triangle.

First we need to be good OpenGL citizen and setup an orthographic projection matrix using`GL.Ortho()`. We need to call`GL.Viewport()`also.

For now we'll add this in the`Load`event handler by the other initialization code -- ignoring the fact that we may want to allow the user to resize the window/`GLControl`. We'll look into window resizing later.

I put the viewport initialization in a separate method to make it a little more readable.

```
private void glControl1_Load(object sender, EventArgs e)
    {
      loaded = true;
      GL.ClearColor(Color.SkyBlue);
      SetupViewport();
    }
 
private void SetupViewport()
    {
      int w = glControl1.Width;
      int h = glControl1.Height;
      GL.MatrixMode(MatrixMode.Projection);
      GL.LoadIdentity();
      GL.Ortho(0, w, 0, h, -1, 1); // Bottom-left corner pixel has coordinate (0, 0)
      GL.Viewport(0, 0, w, h); // Use all of the glControl painting area
    }
```

And between`Clear()`and`SwapBuffers()`our yellow triangle:

```
private void glControl1_Paint(object sender, PaintEventArgs e)
    {
      if (!loaded)
        return;
 
      GL.Clear(ClearBufferMask.ColorBufferBit | ClearBufferMask.DepthBufferBit);
 
      GL.MatrixMode(MatrixMode.Modelview);
      GL.LoadIdentity();
      GL.Color3(Color.Yellow);
      GL.Begin(BeginMode.Triangles);
      GL.Vertex2(10, 20);
      GL.Vertex2(100, 20);
      GL.Vertex2(100, 50);
      GL.End();
 
      glControl1.SwapBuffers();
    }
```

Voila!

![](https://web.archive.org/web/20151015042206im_/http://www.opentk.com/files/Form3.png)

**Keyboard input**  
Next thing we want to do is animate the triangle via user interaction. Every time`Space`is pressed, we want the triangle to move one pixel right.

The two general approaches to keyboard input in a`GLControl`scenario is using`Windows.Forms`key events and using the OpenTK`KeyboardDevice`. Since the rest of our program resides in the`Windows.Forms`world \(our window might be a very small part of a large GUI\) we'll play nice and use`Windows.Forms`key events in this guide.

We'll have an`intx=0;`variable that we'll increment in a`KeyDown`event handler. Adding it to the`glControl1`and not the Form, means the`glControl`has to be focused, ie. clicked by the user for key events to be sent to our handler.

```
int x = 0;
private void glControl1_KeyDown(object sender, KeyEventArgs e)
{
    if (e.KeyCode == Keys.Space)
    x++;
}
```

We add`GL.Translate()`to our`Paint`event handler:

```
private void glControl1_Paint(object sender, PaintEventArgs e)
    {
      if (!loaded)
        return;
 
      GL.Clear(ClearBufferMask.ColorBufferBit | ClearBufferMask.DepthBufferBit);
 
      GL.MatrixMode(MatrixMode.Modelview);
      GL.LoadIdentity();
 
      GL.Translate(x, 0, 0); // position triangle according to our x variable
 
      ...
    }
```

.. and we run our program. But wait! Nothing happens when we push Space! The reason is,`glControl1`is not painted all the time; the operating systems window manager \(Windows/X/OSX\) makes sure as few`Paint`events as possible happens. Only on resize, obscured window and a couple of more situations do`Paint`events actually get triggered.

What we would like to do is have a way of telling the window manager_"This control needs to be repainted since the data it relies on has changed"_. We want to notify the window manager that our glControl1 should be repainted. Easy,`Invalidate()`to the resque:

```
private void glControl1_KeyDown(object sender, KeyEventArgs e)
    {
      if (!loaded)
        return;
      if (e.KeyCode == Keys.Space)
      {
        x++;
        glControl1.Invalidate();
      }
    }
```

**Focus behaviour**  
If you're anything like me you're wondering a little how this focusing behaves; let's find out!

A simple way is painting the triangle yellow when`glControl1`is focused and blue when it is not. Right:

```
private void glControl1_Paint(object sender, PaintEventArgs e)
    {
      ...
 
      if (glControl1.Focused) // Simple enough :)
        GL.Color3(Color.Yellow);
      else
        GL.Color3(Color.Blue);
      GL.Begin(BeginMode.Triangles);
 
      ...
    }
```

So now anytime the triangle is yellow, Space should work alright, and when it's blue any keyboard input will be ignored.

**Freedom at last: resizing the window**  
We will now turn our attention to a sore teeth: window resizing.

Anytime a Windows.Forms control is resized, it's Resize event is triggered. That is true for glControl1 too. That's one piece in the puzzle.

The other piece to find is_"What do we need to update when a GLControl is resized?"_and the answer is_"The viewport and the projection matrix"_.

Well it seems our`SetupViewport()`will come in handy once more! Add an event handler to the Resize event of glControl1 and fill it in:

```
private void glControl1_Resize(object sender, EventArgs e)
    {
      SetupViewport();
    }
```

![](https://web.archive.org/web/20151015042206im_/http://www.opentk.com/files/Form6.png)

There is still one problem though: if you shrink the window using eg. the bottom-right resize grip of the window, no repaint will trigger automatically. That's because the window manager makes assumptions about where the \(0, 0\) pixel of a control resides, namely in the top-left corner of the control. \(Try resizing using the top-left grip instead - the triangle is repainted continously!\). Our general fix to alleviate this problem is instructing the window manager that we really want the repaint to occur upon any resize event:

```
private void glControl1_Resize(object sender, EventArgs e)
    {
      SetupViewport();
      glControl1.Invalidate();
    }
```

**I want my main loop: driving animation using Application.Idle**  
So, what if we wanted our triangle to rotate continously? This would be childs play in a main loop scenario: just increment a`rotation`variable in the main loop, before we render the triangle.

But we don't have any loop. We only have events!

To mend for this lack of continuity we have to force Windows.Forms to do it our way. We want an event triggered every now and then, fast enough to get that realtime interactive feeling.

Now there are several ways to achieve this. One is adding a`Timer`control to our form, changing`rotation`in the timers`Tick`event handler. Another is adding a wild`Thread`to the soup. The first is a little too high-level and slow while the second is really low-level and a bit harder to get right.

We will take a third path and use a`Windows.Forms`event designed just for the purpose of being executed "when nothing else is going on": meet the`Application.Idle`event.

This event is special in a number of ways as you may have guessed already. It is not associated with any Form or other control, but with the program as a whole. You cannot hook into it from the GUI Designer; you'll have to add it manually -- for example in the`Load`event:

```
private void glControl1_Load(object sender, EventArgs e)
    {
      loaded = true;
      GL.ClearColor(Color.SkyBlue);
      SetupViewport();
      Application.Idle += Application_Idle; // press TAB twice after +=
    }
 
    void Application_Idle(object sender, EventArgs e)
    {
    }
```

One good thing about the`Idle`event is that the corresponding event handlers are executed_on the Windows.Forms thread_. That is good since it means we can access all GUI controls without having to worry about threading issues, a pain we would have to deal with if we cooked our own thread.

So we simply increment our`rotation`variable in the`Idle`event handler and`Invalidate()`glControl1 -- business as usual.

```
float rotation = 0;
    void Application_Idle(object sender, EventArgs e)
    {
      // no guard needed -- we hooked into the event in Load handler
      while (glControl1.IsIdle)
      {
          rotation += 1;
          Render();
      }
    }
```

Let's update our rendering code while we're at it:

```
private void Render()
    {
      ...
      if (glControl1.Focused)
        GL.Color3(Color.Yellow);
      else
        GL.Color3(Color.Blue);
      GL.Rotate(rotation, Vector3.UnitZ); // OpenTK has this nice Vector3 class!
      GL.Begin(BeginMode.Triangles);
      ...
    }
 
    private void glControl1_Paint(object sender, PaintEventArgs e)
    {
        Render();
    }
```

Happy wonders! Look:

![](https://web.archive.org/web/20151015042206im_/http://www.opentk.com/files/Form7.png)

**The triangle rotates slower when the window is big! How come?**  
_\(This might not be true if you have a super-fast-computer with a super-fast-graphics-card; but you want your game to run on your neighbours computer too, don't you?\)_

The reason is that windowed 3d rendering just is a lot slower than full-screen rendering, in general.

But you can reduce the damage by using a technique called_frame-rate independent animation_. The idea is simple: increment the`rotation`variable not with`1`but with an amount that depends on the current rendering speed \(if the speed is slow, increment`rotation`with a larger amount than if the speed is high\).

But you need to be able to measure the current rendering speed or, equivalently, how long time it takes to render a frame.

Since .NET2.0 there is a class available to do high-precision time measurements called`Stopwatch`. Here's how to use it:

```
Stopwatch sw = new Stopwatch();
sw.Start();
MyAdvancedAlgorithm();
sw.Stop();
double milliseconds = sw.Elapsed.TotalMilliseconds;
```

\(_don't try with`DateTime.Now`-- it has a granularity of 10 or more milliseconds, which is in the same size as typical frame rendering -- worthless.._\)

Now, if we could measure the time it takes to perform the glControl painting, we would be close to making some kind of frame-rate-independent animation. But there is an even more elegant way: let's measure all time that is not`Application.Idle`time! Then we'll be sure it is not just the painting that is measured, but everything that has been going on since our last Idle run:

```
Stopwatch sw = new Stopwatch(); // available to all event handlers
    private void glControl1_Load(object sender, EventArgs e)
    {
      ...
      sw.Start(); // start at application boot
    }
 
    float rotation = 0;
    void Application_Idle(object sender, EventArgs e)
    {
      // no guard needed -- we hooked into the event in Load handler
 
      sw.Stop(); // we've measured everything since last Idle run
      double milliseconds = sw.Elapsed.TotalMilliseconds;
      sw.Reset(); // reset stopwatch
      sw.Start(); // restart stopwatch
 
      // increase rotation by an amount proportional to the
      // total time since last Idle run
      float deltaRotation = (float)milliseconds / 20.0f;
      rotation += deltaRotation;
 
      glControl1.Invalidate();
    }
Cool! The triangle spins with the same speed regardless of window size.

I want an FPS counter!
Yeah me too. It's quite simple now that we've got a Stopwatch.

The idea is just counting the Idle runs, and every second or so, update a Label control with the counter! But we'll have to know when a second has passed, so we need an accumulator variable adding all time slices together.

Quite a lot of logic started to add up in the Idle event handler, so I split it up a little:

     void Application_Idle(object sender, EventArgs e)
    {
      double milliseconds = ComputeTimeSlice();
      Accumulate(milliseconds);
      Animate(milliseconds);
    }
 
    private double ComputeTimeSlice()
    {
      sw.Stop();
      double timeslice = sw.Elapsed.TotalMilliseconds;
      sw.Reset();
      sw.Start();
      return timeslice;
    }
 
    float rotation = 0;
    private void Animate(double milliseconds)
    {
      float deltaRotation = (float)milliseconds / 20.0f;
      rotation += deltaRotation;
      glControl1.Invalidate();
    }
 
    double accumulator = 0;
    int idleCounter = 0;
    private void Accumulate(double milliseconds)
    {
      idleCounter++;
      accumulator += milliseconds;
      if (accumulator > 1000)
      {
        label1.Text = idleCounter.ToString();
        accumulator -= 1000;
        idleCounter = 0; // don't forget to reset the counter!
      }
    }
```

Our FPS counter in all its glory:

![](https://web.archive.org/web/20151015042206im_/http://www.opentk.com/files/Form8.png)

**Can't you put the complete source code somewhere?**  
Sure, here it is:

```
using System;
using System.ComponentModel;
using System.Data;
using System.Drawing;
using System.Text;
using System.Windows.Forms;
using OpenTK.Graphics;
using OpenTK.Graphics.OpenGL;
using System.Diagnostics;
 
namespace GLControlApp
{
  public partial class Form1 : Form
  {
    bool loaded = false;
 
    public Form1()
    {
      InitializeComponent();
    }
 
    Stopwatch sw = new Stopwatch(); // available to all event handlers
    private void glControl1_Load(object sender, EventArgs e)
    {
      loaded = true;
      GL.ClearColor(Color.SkyBlue); // Yey! .NET Colors can be used directly!
      SetupViewport();
      Application.Idle += Application_Idle; // press TAB twice after +=
      sw.Start(); // start at application boot
    }
 
    void Application_Idle(object sender, EventArgs e)
    {
      double milliseconds = ComputeTimeSlice();
      Accumulate(milliseconds);
      Animate(milliseconds);
    }
 
    float rotation = 0;
    private void Animate(double milliseconds)
    {
      float deltaRotation = (float)milliseconds / 20.0f;
      rotation += deltaRotation;
      glControl1.Invalidate();
    }
 
    double accumulator = 0;
    int idleCounter = 0;
    private void Accumulate(double milliseconds)
    {
      idleCounter++;
      accumulator += milliseconds;
      if (accumulator > 1000)
      {
        label1.Text = idleCounter.ToString();
        accumulator -= 1000;
        idleCounter = 0; // don't forget to reset the counter!
      }
    }
 
    private double ComputeTimeSlice()
    {
      sw.Stop();
      double timeslice = sw.Elapsed.TotalMilliseconds;
      sw.Reset();
      sw.Start();
      return timeslice;
    }
 
    private void SetupViewport()
    {
      int w = glControl1.Width;
      int h = glControl1.Height;
      GL.MatrixMode(MatrixMode.Projection);
      GL.LoadIdentity();
      GL.Ortho(0, w, 0, h, -1, 1); // Bottom-left corner pixel has coordinate (0, 0)
      GL.Viewport(0, 0, w, h); // Use all of the glControl painting area
    }
 
    private void glControl1_Paint(object sender, PaintEventArgs e)
    {
      if (!loaded)
        return;
 
      GL.Clear(ClearBufferMask.ColorBufferBit | ClearBufferMask.DepthBufferBit);
 
      GL.MatrixMode(MatrixMode.Modelview);
      GL.LoadIdentity();
 
      GL.Translate(x, 0, 0);
 
      if (glControl1.Focused)
        GL.Color3(Color.Yellow);
      else
        GL.Color3(Color.Blue);
      GL.Rotate(rotation, Vector3.UnitZ); // OpenTK has this nice Vector3 class!
      GL.Begin(BeginMode.Triangles);
      GL.Vertex2(10, 20);
      GL.Vertex2(100, 20);
      GL.Vertex2(100, 50);
      GL.End();
 
      glControl1.SwapBuffers();
    }
 
    int x = 0;
    private void glControl1_KeyDown(object sender, KeyEventArgs e)
    {
      if (e.KeyCode == Keys.Space)
      {
        x++;
        glControl1.Invalidate();
      }
    }
 
    private void glControl1_Resize(object sender, EventArgs e)
    {
      SetupViewport();
      glControl1.Invalidate();
    }
  }
}
```

**Next step: What about multiple GLControls at once?**  
Yeah it is possible. It is even simple!

All you have to do is "make the appropriate GLControl current".

Let's say you have one GLControl named glCtrl1 and one named glCtrl2. And you have added handlers to the Paint event of both. This is what you do in the event handler of glCtrl1 \(of course you do something similar in the event handler of glCtrl2!\):

```
private void glCtrl1_Paint(object sender, PaintEventArgs e)
    {
      if (!loaded)
        return;
 
      glCtrl1.MakeCurrent(); // Ohh.. It's that simple?
 
      GL.Clear(ClearBufferMask.ColorBufferBit | ClearBufferMask.DepthBufferBit);
      ...
     }
```

The same is true for any code calling a GL.\* or Glu.\* method!

Although each GLControl has its own GraphicsContext, OpenTK will share OpenGL resources by default. This means, any context can use textures, display lists, etc. created on any other context. You can disable this behavior via the`GraphicsContext.ShareContexts`property.

