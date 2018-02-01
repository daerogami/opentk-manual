The most important dependency by far is the Mono/.Net framework. If you need to support users that might not have that installed, the best solution is to[embed the Mono runtime](https://web.archive.org/web/20140922005257/http://www.mono-project.com/Embedding_Mono)into a small launcher \([example code](https://web.archive.org/web/20140922005257/https://github.com/mono/mono/blob/master/samples/embed/teste.c)\).

Additionally, on Windows, you will typically need to install OpenAL drivers:

* either distribute the official
  [oalinst.exe](https://web.archive.org/web/20140922005257/http://climpxwss01.creativelabs.com/openal/Downloads/Forms/AllItems.aspx)
  from Creative \(requires admin rights to install\)
* or distribute
  [OpenAL Soft](https://web.archive.org/web/20140922005257/http://kcat.strangesoft.net/openal.html)
  \(plain dll, no admin rights required\).

There is no such redistributable for OpenGL drivers - the user will have to install those on his own.

On Linux, users will typically have OpenAL and OpenGL pre-installed. If you generate distro-specific packages, then list AL/GL as dependencies and the package manager will take care of the rest. If you provide distro-agnostic packages, then you can either expect the user to have AL/GL preinstalled \(which is quite reasonable\) or you can bundle your own version of OpenAL \(use the OpenAL Soft link, above\). Most commercial games follow the distro-agnostic package approach. Many include libopenal.so, too.

Apart from those, OpenTK uses either core OS components which are always available \(e.g. user32.dll or libX\) or optional components with transparent fallbacks \(e.g. libXi\).

In any case, do try to fail smoothly if AudioContext or GraphicsContext construction fails. It is also a good idea to check the OpenGL version and bail out early if too low.

