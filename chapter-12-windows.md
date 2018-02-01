OpenTK does not come with any installer or setup. Instead, you[download](https://web.archive.org/web/20140922011257/https://sourceforge.net/project/showfiles.php?group_id=177681)the OpenTK binaries and add a reference to "OpenTK.dll" in your Visual Studio/SharpDevelop/MonoDevelop project. \(Unzip the binaries first!\)

**OpenTK demo**  
To run all of the OpenTK builtin examples, the following software is required:

1. [.NET2.0](https://web.archive.org/web/20140922011257/http://www.microsoft.com/downloads/details.aspx?familyid=0856eacb-4362-4b0d-8edd-aab15c5e04f5&displaylang=en)
   or
   [Mono 1.2.6](https://web.archive.org/web/20140922011257/http://www.go-mono.com/mono-downloads/download.html)
2. [OpenAL 2.0.3](https://web.archive.org/web/20140922011257/http://connect.creativelabs.com/developer/Wiki/OpenAL%20Installer%20for%20Windows.aspx)

This is also the software required for an end-user running an OpenTK application. Note that OpenAL is not strictly required if the application does not use any sound.

**OpenTK development**  
If you want to start developing applications using OpenTK, first make sure the items under "OpenTK demo" are installed, then download a compiler/IDE for .NET/mono. Here are some popular choices:

1. [SharpDevelop](https://web.archive.org/web/20140922011257/http://sharpdevelop.net/OpenSource/SD/Default.aspx)
2. [MonoDevelop](https://web.archive.org/web/20140922011257/http://www.go-mono.com/mono-downloads/download.html)
   \(bundled in the mono installer\)
3. [Visual Studio Express](https://web.archive.org/web/20140922011257/http://www.microsoft.com/Express/)

**Setting up an OpenTK application in Visual Studio Express**  
It is a good idea to add "OpenTK.dll.config" to your project, and make sure the "Copy To Output Folder" \(not "compile"!\) is set to "Copy Always". The application will run without this on Windows, but not on Linux or Mac OS X.

Last, but not least, make sure the "Copy Local" property is set to true for the OpenTK reference, to simplify the distribution of your application.

**Setting up an OpenTK application in SharpDevelop**  
Include the "OpenTK.dll.config" in your project, if you want it to run under Linux Mac OS X.  
Visual explanation:

[![](https://web.archive.org/web/20140922011257im_/http://www.opentk.com/files/imagecache/preview/inline_images/OpenTKonSharpDevelop.PNG "OpenTK References on SharpDevelop")](https://web.archive.org/web/20140922011257/http://www.opentk.com/files/inline_images/OpenTKonSharpDevelop.PNG)

