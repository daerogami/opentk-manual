_This text is intended for someone with a C/OpenGL background._

Even though OpenTK automatically translates GL/AL calls from C\# to C, some things work slightly differently in the managed world, when compared to plain C. This page describes a few rules you need to keep in mind:

**Rules of thumb**

1. **Use server storage rather than client storage.**
   A few legacy OpenGL functions use pointers to memory managed by the user. The most popular example is Vertex Arrays, with the GL. \*\*\*Pointer family of functions.


   This approach cannot be used in a Garbage Collected environment \(as .NET\), as the garbage collector \(GC\) may move the contents of the buffer in memory. It is strongly recommended that you replace[legacy Vertex Arrays](https://web.archive.org/web/20140824101459/http://www.opentk.com/node/296)with[Vertex Buffer Objects](https://web.archive.org/web/20140824101459/http://www.opentk.com/doc/chapter/2/opengl/geometry/vbo), which do not suffer from this problem.

   Unlike OpenGL 2.1, OpenGL 3.0 willnotcontain any functions with client storage.

2. **Try to minimize the number of OpenGL calls per frame.**
   This is true for any programming environment utilizing OpenGL, but a little more important in the OpenTK case; while the OpenGL/OpenAL bindings are quite optimized, the transition from managed into unmanaged code incurs a small, but measurable, overhead.


   To minimize the impact of this overhead, try to minimize the number of OpenGL/OpenAL calls. A good rule of thumb is to make no more than a few thousand OpenGL calls per frame, which can be achieved by avoiding Immediate Mode, in favour of Display Lists and VBO's.

3. **For optimal math routine performance, use the`ref`and`out`overloads.**
   This is because Vector3, Matrix4 etc. are structures, not classes. Classes are passed by reference by default in C\#.

```
Vector3 v1 = Vector3.UnitX;
Vector3 v2 = Vector3.UnitZ;
Vector3 v3 = Vector3.Zero;
v3 = v1 + v2;                        // requires three copies; slow.
Vector3.Add(ref v1, ref v2, out v3); // nothing is copied; fast!
```

The same holds true when calling OpenGL functions:

```
GL.Vertex3(ref v1.X);  // pass a pointer to v1; fast!
GL.Vertex3(v1);        // copy the whole v1 structure; slower!
```

**Measuring performance**

1. _GameWindow provides built-in frames-per-second counters _\[Someone with confidence in the details please fill in here\] However,`GLControl`does not.
2. _A simple and convenient way _to measure the performance of your code, is via the .NET 2.0 / Mono 1.2.4 `Stopwatch` class. Use it like this:
   ```
   Stopwatch sw = new Stopwatch();
   sw.Start();
   // Your code goes here.
   sw.Stop();
   double ms = sw.Elapsed.TotalMilliseconds;
   ```

   _Note:_Avoid using`DateTime.Now`or other`DateTime`-based method on any periods shorter than a couple of seconds, since its granularity is 10 ms or worse. \(rumour has it, it may even go backwards on occasion!\) Using`DateTime`to measure very long operations \(several seconds\) is OK.

3. _If you are on Windows_, you can download [Fraps](https://web.archive.org/web/20140824101459/http://www.fraps.com/) to measure how many frames per second are rendered in your application. For linux \(and Windows\), you can use the commercial tool [gDEBugger](https://web.archive.org/web/20140824101459/http://www.gremedy.com/) \[anyone: any similar tools for Mac? Any free tool for Linux?\]



