#### Prerequisites

[Download the latest OpenTK release](https://web.archive.org/web/20160419235444/http://www.opentk.com:80/project/opentk#downloads) or [checkout the latest source code directly](https://web.archive.org/web/20160419235444/http://www.opentk.com:80/project/svn).

OpenTK is a C\# library that targets .Net 2.0. To use it, you will need either the Mono or the .Net runtime, plus device drivers for OpenGL \(graphics\), OpenAL \(audio\) and OpenCL \(compute\), depending on which parts of OpenTK you are interested in:

* [Mono runtime 2.0 or higher](https://web.archive.org/web/20160419235444/http://www.mono-project.com/Downloads)
  \(Linux/Mac OS X/Windows\)
* [.Net runtime 2.0 or higher](https://web.archive.org/web/20160419235444/http://www.microsoft.com/downloads/details.aspx?familyid=0856eacb-4362-4b0d-8edd-aab15c5e04f5&displaylang=en)
  \(Windows\)

Most operating systems come with one of the above preinstalled. Note that OpenTK does not currently support .Net 1.1 or .Net CF.

#### Installation

OpenTK 1.0 is distributed in two flavors: a Windows installer and a plain zip archive.

On Windows, simply execute the installer and follow the on-screen instructions. The installer does not require administrator rights, unless you opt to install OpenAL drivers or the GLSL plugin for Visual Studio Professional. Once installation is complete, you will be able to select OpenTK in your project references \(double click "References" in the solution pane, select the ".Net" tab, locate OpenTK and click add\).

On Linux and Mac OS X, extract the zip archive to a folder of your choice and add OpenTK.dll to your project references. You can find this file under Binaries/OpenTK/Release. Additionally, you should addOpenTK.dll.configto your project and instruct your IDE to copy this file to the output directory. This is necessary for your project to function under Linux and Mac OS X.

#### Build

You can either build OpenTK through the commandline or through an IDE:

* For a commandline build, you will need either msbuild \(.Net\) or xbuild \(Mono\). For msbuild, open a "Visual Studio command prompt", navigate to the OpenTK folder and type:
 
  ```
  msbuild OpenTK.sln /p:
  Configuration=
  Release
  ```

  For xbuild, make sure the tool is located in your path and type:

  ```
  xbuild OpenTK.sln /p:
  Configuration=
  Release
  ```

* For an IDE build, you will need either Visual Studio 2010 \(Express Edition or higher\) or MonoDevelop 2.4. Simply open OpenTK.sln and click "Build".

OpenTK can be built in four different configurations: "Release" \(shown above\), "Debug", "Nsis" and "Documentation". To build a different configuration, use your IDE's configuration manager or modify the commandline invocation like this:

```
msbuild OpenTK.sln /p:
Configuration=
Documentation
```

**Important note: **MonoDevelop versions prior to 2.6 fail to bootstrap and build OpenTK directly from source control. The workaround is simple: open OpenTK.sln, right click the "Build.UpdateVersion" project and select "Run". You can now build OpenTK as normal.

#### Dependencies

OpenTK 1.0 requires a C\# 2.0 compiler and the following .Net 2.0 libraries:

* System
* System.Data
* System.Drawing
* System.Windows.Forms
* System.Xml

Additionally, the binding generator requires a C\# 3.0 compiler and the following .Net 3.5 library: System.Core.

These dependencies will be reduced in future OpenTK versions.

The "Documentation" build configuration requires [doxygen](https://web.archive.org/web/20160419235444/http://www.stack.nl/~dimitri/doxygen/) and [latex](https://web.archive.org/web/20160419235444/http://miktex.org/) to be present in your path. If you are using Linux, you can install these through your package manager \(search for "doxygen", "texlive-core" and "texlive-extras"\).

The "Nsis" build configuration requires doxygen, latex \(refer to "Documentation" configuration, above\) and the [Nsis installer system](https://web.archive.org/web/20160419235444/http://nsis.sourceforge.net/Main_Page). Linux users will also be able to install this through the package manager \(search for "nsis"\).

