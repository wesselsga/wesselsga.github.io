---
layout: default
title: Build freetype and harfbuzz as Windows DLLs
category: Dev
---

# Build freetype and harfbuzz as Windows DLLs #

Here is a quick guide for building both FreeType and Harfbuzz as shared libraries (DLLs) on Windows.  For this guide, only the x64 version will be built - although the instructions should also work for x86.

For the following I'm using Visual Studio 2017 Community Edition.

### Build FreeType DLL ###

Download the source code for FreeType2.  At the time of this writing, the version I'm using is 2.7.1.

Open **builds/windows/vc2010/freetype.sln** in Visual Studio 2017.  Hit OK when asked to Retarget Projects to the newer compiler.

Select **Release Multithreaded** for the Configuration, and **x64** for the platform.  If you want to link dynamically to the CRT, select **Release** for the Configuration.

Select **Project | freetype Properties ...** from the menu.  Make sure your configuration (Release Multithreaded) and platform (x64) are selected.

Change the Configuration Type from Static Library to **Dynamic Library (.dll)** and change the Target Name to **freetype** as highlighted below:

![alt text][freetype_1]

Open the `ftoption.h` header file and find the defines for `FT_EXPORT` and `FT_EXPORT_DEF`, they are probably commented out.  

Set the value of both defines to `__declspec(dllexport) x`:

```C
/*************************************************************************/
  /*                                                                       */
  /* DLL export compilation                                                */
  /*                                                                       */
  /*   When compiling FreeType as a DLL, some systems/compilers need a     */
  /*   special keyword in front OR after the return type of function       */
  /*   declarations.                                                       */
  /*                                                                       */
  /*   ...                                                                 */
  /*                                                                       */
  /*   You can provide your own implementation of FT_EXPORT and            */
  /*   FT_EXPORT_DEF here if you want.  If you leave them undefined, they  */
  /*   will be later automatically defined as `extern return_type' to      */
  /*   allow normal compilation.                                           */
  /*                                                                       */
  /*   Do not #undef these macros here since the build system might define */
  /*   them for certain configurations only.                               */
  /*                                                                       */
#define FT_EXPORT(x) __declspec(dllexport) x
#define FT_EXPORT_DEF(x)  __declspec(dllexport) x
```

Build the Solution.  When complete, you should end up with a freetype.dll and freetype.lib in the **objs/vc2010/x64** subfolder.


[freetype_1]: https://s3.amazonaws.com/gregwessels/posts/2017/freetype-vc.jpg "FreeType VC Project Settings"

