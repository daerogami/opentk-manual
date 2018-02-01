#### Installing Mono

If you are using a recent Linux distribution, all prerequisites for OpenTK projects should be readily available: the Mono runtime and the Mono compilers. Execute "`mono --version`" and "`gmcs --version`" and check if the output looks like this:

```
$ mono --version
Mono JIT compiler version 1.2.6 (tarball)
Copyright (C) 2002-2007 Novell, Inc and Contributors. www.mono-project.com
        TLS:           __thread
        GC:            Included Boehm (with typed GC)
        SIGSEGV:       altstack
        Notifications: epoll
        Architecture:  amd64
        Disabled:      none
 
$ gmcs --version
Mono C# compiler version 1.2.6.0
```

If one or both of these commands fail, you'll have to install Mono. Mono packages should be readily available through your package manager:

```
# Ubuntu and other .deb-based distributions
sudo apt-get install mono mono-gmcs
# or
su -c "apt-get install mono mono-gmcs"
 
# Fedora Core and .rpm-based distributions
su -c "yum install mono mono-gmcs"
```

If no Mono packages are available, or they are outdated \(`mono --version`returns something less than 1.2.6\), you should build Mono from source. There is a message in the support forum describing the process of building mono from source[here](https://web.archive.org/web/20140731151841/http://www.opentk.com/node/157#comment-465).

Alternatively, you can find use one of the Mono binary packages on the[Mono download page](https://web.archive.org/web/20140731151841/http://www.go-mono.com/mono-downloads/download.html).

#### Using a binary release

Download the latest[opentk-x.y.z.zip](https://web.archive.org/web/20140731151841/https://sourceforge.net/project/showfiles.php?group_id=177681)release from Sourceforge and unzip it.

A new opentk-x.y.z will be created with four subfolders: "Binaries", "Documentation", "Source" and "Installers". Try running the examples binary to make sure everything works alright:

```
unzip
 opentk
-1.0
-2010
-10
-06
.
zip
cd
 opentk
-1.0
-2010
-10
-06
/Binaries/OpenTK/Release
mono Examples.exe
```

A new window will hopefully show up, listing all available examples. If not, check the troubleshooting section below.

The "Binaries/OpenTK/Release" folder contains the main OpenTK assembly \(OpenTK.dll\) and the OpenTK.dll.config file - these are all you need to run OpenTK projects. If you are using MonoDevelop, check the "QuickStart.sln" solution for a ready-to-use project. Last, don't forget to take a look at the release notes contained in the "Documentation" folder.

#### Troubleshooting

The following error has been reported on Fedora Core 8, when running Examples.exe:

```
Unhandled Exception: System.TypeInitializationException: An exception was thrown by the type initializer for System.Windows.Forms.Form ---> System.Reflection.TargetInvocationException: Exception has been thrown by the target of an invocation. ---> System.TypeInitializationException: An exception was thrown by the type initializer for System.Drawing.GDIPlus ---> System.DllNotFoundException: gdiplus.dll
  at (wrapper managed-to-native) System.Drawing.GDIPlus:GdiplusStartup (ulong&,System.Drawing.GdiplusStartupInput&,System.Drawing.GdiplusStartupOutput&)
  at System.Drawing.GDIPlus..cctor () [0x00000] --- End of inner exception stack trace ---
```

This is caused by a missing entry in`"/etc/mono/config"`. To correct this issue, open the aforementioned file \(you must be root!\), and add this line:`<dllmap dll="gdiplus.dll"target="/usr/lib/libgdiplus.so.0"/>`. Now, Examples.exe should work.

#### Building OpenTK from source

OpenTK's build system relies on xbuild, so you'll need to install that:

```
# Ubuntu
sudo apt-get install xbuild
 
# Debian
su -c "apt-get install xbuild"
 
# Fedora
su -c "yum install xbuild"
```

Note that xbuild versions earlier than 2.6.7 are quite buggy and may fail to build OpenTK.

Once xbuild is installed, unzip the source release and cd to the Build folder:

```
unzip opentk-1.0-2010-10-06.zip
cd opentk-1.0-2010-10-06/
xbuild OpenTK.sln /p:Configuration=Release
```

Wait a few seconds for the compilation to end, and check the "Binaries" folder that just appeared in the base OpenTK directory.

