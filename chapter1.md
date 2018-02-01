# Learn OpenTK in 15 minutes

So, you have downloaded the latest version of OpenTK - what now?

This is a short tutorial that will help you get started with OpenTK in 3 simple steps.



#### \[Step 1: Installation\]

Run the installer you downloaded. It will ask where you want OpenTK to be installed. Any folder will do.



#### \[Step 2: Use OpenTK\]

Create a new project in your .NET IDE \(don't have a .NET IDE? Check out MonoDevelop or Visual Studio Express\). Make it of type "Console Application".

Now, add a reference to OpenTK.dll: In your new project, right click "References" and select "Add Reference". Locate OpenTK.dll and add it.

Repeat this process for System.Drawing.dll. OpenTK depends on this.

Now you are ready to add some graphics! Open Program.cs and copy-paste the following code:

```
using System;
using System.Drawing;
using OpenTK;
using OpenTK.Graphics;
using OpenTK.Graphics.OpenGL;
using OpenTK.Input;
 
namespace Example
{
    class MyApplication
    {
        [STAThread]
        public static void Main()
        {
            using (var game = new GameWindow())
            {
                game.Load += (sender, e) =>
                {
                    // setup settings, load textures, sounds
                    game.VSync = VSyncMode.On;
                };
 
                game.Resize += (sender, e) =>
                {
                    GL.Viewport(0, 0, game.Width, game.Height);
                };
 
                game.UpdateFrame += (sender, e) =>
                {
                    // add game logic, input handling
                    if (game.Keyboard[Key.Escape])
                    {
                        game.Exit();
                    }
                };
 
                game.RenderFrame += (sender, e) =>
                {
                    // render graphics
                    GL.Clear(ClearBufferMask.ColorBufferBit | ClearBufferMask.DepthBufferBit);
 
                    GL.MatrixMode(MatrixMode.Projection);
                    GL.LoadIdentity();
                    GL.Ortho(-1.0, 1.0, -1.0, 1.0, 0.0, 4.0);
 
                    GL.Begin(PrimitiveType.Triangles);
 
                    GL.Color3(Color.MidnightBlue);
                    GL.Vertex2(-1.0f, 1.0f);
                    GL.Color3(Color.SpringGreen);
                    GL.Vertex2(0.0f, -1.0f);
                    GL.Color3(Color.Ivory);
                    GL.Vertex2(1.0f, 1.0f);
 
                    GL.End();
 
                    game.SwapBuffers();
                };
 
                // Run the game at 60 updates per second
                game.Run(60.0);
            }
        }
    }
}
```



Build and run this application. You should see a window with a triangle inside. Press escape to exit.



#### \[Step 3: Play\]

Now it's time to start playing with the code. This is a great way to learn OpenGL and OpenTK at the same time.

Every OpenTK game will contain 4 basic methods:

1. Load: this is the place to load resources from disk, like images or music.

2. UpdateFrame: this is a suitable place to handle input, update object positions, run physics or AI calculations.

3. RenderFrame: this contains the code that renders your graphics. It typically begins with a call to GL.Clear\(\) and ends with a call to SwapBuffers.

4. Resize: this method is called automatically whenever your game window changes size. Fullscreen applications will typically call it only once. Windowed applications may call it more often. In most circumstances, you can simply copy & paste the code from Game.cs.

Why don't you try modifying a few things? Here are a few suggestions:

* Change the colors of the triangle or the window background \(OnLoad and OnRenderFrame methods\). Hint: use GL.Color4\(\) to control the triangle color and GL.ClearColor\(\) to control the background color.

* Make the triangle change colors when you press a key \(OnUpdateFrame and OnRenderFrame methods\).

* Make the triangle move across the screen. Use the arrow keys or the mouse to control its position \(OnUpdateFrame\). Hint: use Matrix4.CreateTranslation\(\) to create a translation matrix and call GL.LoadMatrix\(\) to load it \(OnRenderFrame\).

* Use a for-loop to render many triangles arranged on a plane \(OnRenderFrame method\).

* Rotate the camera so that the plane above acts as ground \(OnRenderFrame method\). Hint: use Matrix4.LookAt\(\) to create a modelview matrix and use GL.LoadMatrix\(\) to load it.

* Use the keyboard and mouse to walk on the ground. Make sure you can't fall through it! \(OnUpdateFrame and OnRenderFrame methods\).

Some things you might find useful: Vector2, Vector3, Vector4 and Matrix4 classes for camera manipulations. Mouse and Keyboard properties for interaction with the mouse and keyboard, respectively. Joysticks property for interaction with joystick devices.



Don't be afraid to try things and see the results. OpenTK lends itself to explorative programming - even if something breaks, the library will help you pinpoint the cause of the error.

Check out the [examples on github](https://github.com/opentk/opentk-examples)!

There's a lot of functionality that is not visible at first glance: audio, advanced OpenGL, display devices, support for GUIs through GLControl... Then there's the subject of proper engine and game design, which could cover a whole book by itself.



Hopefully, you'll have gained a feel of the library by now and you'll be able to accomplish more complex tasks. You might wish to consult the complete documentation for the more advanced aspects of OpenTK and, of course, don't hesitate to post on [gitter](https://gitter.im/opentk/opentk) if you hit any roadblocks!

