---
layout: default
title: Build freetype and harfbuzz as Windows DLLs
category: Dev
---

# Build freetype and harfbuzz as Windows DLLs #

Here is a quick guide for building both [FreeType](https://www.freetype.org/) and [Harfbuzz](https://www.freedesktop.org/wiki/Software/HarfBuzz/) as shared libraries (DLLs) on Windows.  For this guide, only the x64 version will be built - although the instructions should also work for x86.

For the following I'm using Visual Studio 2017 Community Edition.

## Build FreeType DLL ##

Download the source code for FreeType2.  At the time of this writing, the version is 2.7.1.

Open **builds/windows/vc2010/freetype.sln** in Visual Studio 2017.  Hit OK when asked to Retarget Projects to the newer compiler.

Select **Release Multithreaded** for the Configuration, and **x64** for the platform.  If you want to link dynamically to the CRT, select **Release** for the Configuration.

Select **Project -> freetype Properties** from the menu.  Make sure your configuration (Release Multithreaded) and platform (x64) are selected.

Change the Configuration Type from Static Library to **Dynamic Library (.dll)** and change the Target Name to **freetype** as highlighted below:

<br/>
![alt text][freetype_1]

<br/>
Open the `ftoption.h` header file and find the defines for `FT_EXPORT` and `FT_EXPORT_DEF` - they are probably commented out.  

Set the value of both defines to `__declspec(dllexport) x`

```cpp
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
<br/>
#### Build the Solution ####
When complete, you should end up with a freetype.dll and freetype.lib in the **objs/vc2010/x64** subfolder.

Now, we can use the .lib and headers from freetype to build a [harfbuzz](https://www.freedesktop.org/wiki/Software/HarfBuzz/) DLL for text shaping.  

## Build Harfbuzz DLL ##

To build harfbuzz, we'll copy the headers from the **include** folder in the freetype source distro to the directory **C:\usr\local\include**.  

Copy the newly built freetype.lib to **C:\usr\local\lib**

#### Download a release tarball of harfbuzz ####

Unfortunately, the Windows build instructions for harfbuzz do not seem to work with the source tree on github.  Instead, use a [release tarball](https://www.freedesktop.org/software/harfbuzz/release/) (I'm using 1.4.6).

If you targeted **Release Multithreaded** in the above steps, then freetype was built with the `/MT` compiler option to statically link to the CRT.  By default, Harfbuzz will use `/MD` - so we'll change that in the win32/detectenv-msvc.mak file.  You can skipped this step, if you targeted freetype to use '/MD' as well.

```Batchfile
# One may change these items, but be sure to test
# the resulting binaries
!if "$(CFG)" == "release"
CFLAGS_ADD = /MD /O2 /GL /MP
!if $(VSVER) > 9 && $(VSVER) < 14
# Undocumented "enhance optimized debugging" switch. Became documented
# as "/Zo" in VS 2013 Update 3, and is turned on by default in VS 2015.
CFLAGS_ADD = $(CFLAGS_ADD) /d2Zi+
!endif
!else
CFLAGS_ADD = /MDd /Od
!endif
```

Change the `/MD` and /MDd above to `/MT` and `/MTd` respectively.

Use the CMD shortcuts installed with Visual Studio to open a command prompt using the x64 Native Tools.

`cd` to the win32 subdirectory in the harfbuzz source tree.

Run the following command for nmake: (note: the PREFIX option to use our freetype from above)

```Batchfile
nmake /f Makefile.vc CFG=release PREFIX="c:\usr\local" FREETYPE=1 HARFBUZZ_DLL_FILENAME=release\x64\harfbuzz
```
When complete, you should end up with a harfbuzz.dll and harfbuzz.lib in the **win32\release\x64** subfolder.


[freetype_1]: https://s3.amazonaws.com/gregwessels/posts/2017/freetype-vc.jpg "FreeType VC Project Settings"
