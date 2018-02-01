If you come from a GUI programming background you are probably used to the fact that GUI applications do not take CPU time when idle. You create a window, paint it and then it sits there, consuming negligible CPU time until you interact with it.

In that case, you may be surprised when you use OpenTK for the first time and see it consume 100% of your CPU even when you just draw a blank window! While surprising, there is a good reason why this is so - and it is simple to fix once you understand why that is.

Most applications tend to be designed around one of two broad models:

1. Event loops, where the application sleeps until it receives some specific event \(e.g. a keystroke, mouse movement or paint request\).

```
while (!exit)
{
    var msg = WaitForMessage(); // Blocks until message arrives
    switch (msg.Type)
    {
        case Message.MouseMove: OnMouseMove(msg); break;
        case Message.Paint: OnPaint(msg); break;
        ...
    }
}
```

1. Game loops, where the application runs a simulation, polls user input and draws to screen continuously:

```
while (!exit)
{
    var time = GetTime();
    Update(time);
    Render(time);
}
```

There is a fundamental difference between event and game loops: the former are stopped until something wakes them; the latter run until something stops them. GUI applications tend to be better suited to the first model: wait for the user and react to his input. Games tend to use the second model: enemies usually continue to move even when the user is not moving his mouse!

One side-effect of continuous game loops is that the CPU is always utilized 100% unless you, the developer, take care to release it. \(OpenTK cannot do that for you, since you can code a game loop in any number of exotic ways - the library simply cannot know which one you happen to be using\).

To release CPU time, do one of those:

* Enable vsync via`GraphicsContext.CurrentContext.VSync=true`in your glControl Load. This is very accurate but may fail to work on some older drivers or when the user disables vsync in his driver configuration.
* Call`System.Threading.Thread.Sleep(n)`where n is the amount of milliseconds you have to spare, considering your target framerate. \(For instance, if you target 60fps or 16.6ms per frame, and your frame time is 10ms, you have 6.6ms to spare\). This approach will always work but is less accurate may overshoot the amount of milliseconds you specify.

Don't enable vsync and sleep at the same time, as this may reduce your framerate below your intended target.

In OpenTK, you will typically use GLControl to build event-based applications and GameWindow for game loop applications. This is not a hard rule: it is certainly possible to install a game loop in GLControl or use GameWindow in a event-based manner \(please refer to the relevant documentation for more information\).

