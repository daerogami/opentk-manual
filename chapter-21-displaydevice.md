There are three main types ofdisplay devices: monitors, projectors and TV screens. OpenTK exposes all of them through the same interface:`OpenTK.DisplayDevice`.

You can use`OpenTK.DisplayDevice`to query available display devices, discover and modify their properties.

Example 1: discover available devices

```
using OpenTK;
 
foreach (DisplayDevice device in DisplayDevice.AvailableDisplays)
{
    Console.WriteLine(device.IsPrimary);
    Console.WriteLine(device.Bounds);
    Console.WriteLine(device.RefreshRate);
    Console.WriteLine(device.BitsPerPixel);
    foreach(DisplayResolution res in device.AvailableResolutions)
   {
      Console.WriteLine(res);
   }
}
```

Example 2: set the resolution of the first device to 800x600x32@60Hz:

```
using OpenTK;
 
devices[0].ChangeResolution(800, 600,  32,60);
```

Example 3: set the resolution of the second device to 800x600x32 using the preferred refresh rate:

```
using OpenTK;
 
devices[1].ChangeResolution(800, 600, 32, -1);
```

Example 4: restore all devices to their default settings

```
using OpenTK;
 
foreach (DisplayDevice device in DisplayDevice.AvailableDevices)
{
    device.RestoreResolution();
}
```

OpenTK will try to match your custom resolution to the closest supported resolution. If a specific bit depth or refresh rate is not supported, the current bit depth or refresh rate will be used. If no custom resolution matches the specified parameters, the current`DisplayResolution`will be used. You can specify a negative number or zero to indicate that a specific parameter is of no interest: for example, specifying a refresh rate of 0 will result in the default refresh rate being used.

Note that it is**your**responsibility to call`RestoreResolution()`prior to exiting your application. Failing to call this method will result in undefined behavior.

\[Todo: add information about hotplugging support\]

