First of all, what**is**OpenTK?

Simply put, the Open Toolkit is a free project that allows you to use OpenGL, OpenGL\|ES, OpenCL and OpenAL APIs from managed languages.

OpenTK started life as an experimental fork of the Tao framework before during the summer of 2006. It's original intention was to provide a cleaner wrapper than Tao.OpenGL, but it quickly grew in focus: right now, it provides access to various Khronos and Creative APIs and handles the necessary initialization logic for each API. As such, the Open Toolkit is most similar to projects like Tao, SlimDX, SDL or GLFW.

Unlike similar libraries, OpenTK attempts provide a consistent interface that utilizes the superior managed runtime. Instead of untyped pointers, OpenTK provides generics. Instead of plain constants, OpenTK uses strongly-typed enumerations. Instead of plain function lists, OpenTK separates functions per extension category. A common math library is integrated and directly usable by each API.

Features:

* Written in cross-platform C\# and usable by all managed languages \(F\#, Boo, VB.Net, C++/CLI\).
* Consistent, strongly-typed bindings, suitable for RAD development.
* Usable stand-alone or integrated with Windows.Forms, GTK\#, WPF.
* Cross-platform binaries that are portable on .Net and Mono without recompilation.
* Wide platform support: Windows, Linux, Mac OS X, with iPhone port in progress.

The Open Toolkit is suitable for games, scientific visualizations and all kinds of software that requires advanced graphics, audio or compute capabilities. It's license makes it suitable for both free and commercial applications.

