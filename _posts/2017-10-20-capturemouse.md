---
layout: default
title: Capture Windows Desktop with the Mouse Cursor
category: Dev
---

# Capture Windows Desktop with the Mouse Cursor #

Something annoying about Windows - everytime you use the Print Screen key it captures the desktop image but hides the mouse cursor.  For me, this is fine maybe 50% of the time - often I want the mouse cursor included in the image capture.  Sure, there are plenty of tools available to do this ... but I was curious how hard it would be to just do it.

Here's a old-school Win32 function that should capture the desktop and draw the mouse cursor back in:

```cpp
void capture_screen()
{
   HDC screen = GetDC(NULL);

   int w = GetSystemMetrics(SM_CXSCREEN);
   int h = GetSystemMetrics(SM_CYSCREEN);

   // create a bitmap and DC that we can draw to
   HDC dc = CreateCompatibleDC(screen);
   HBITMAP bmp = CreateCompatibleBitmap(screen, w, h);
   HGDIOBJ bmp_old = SelectObject(dc, bmp);

   // copy the screen
   BitBlt(dc, 0, 0, w, h, screen, 0, 0, SRCCOPY);
   ReleaseDC(NULL, screen);

   // copy the cursor to the bitmap
   CURSORINFO cinfo;
   cinfo.cbSize = sizeof(cinfo);
   GetCursorInfo(&cinfo);
   if (cinfo.hCursor && (cinfo.flags & CURSOR_SHOWING)) 
   { 
      DrawIcon(dc, cinfo.ptScreenPos.x, cinfo.ptScreenPos.y, (HICON)cinfo.hCursor);
   }

   // copy the bitmap to the clipboard
   if (OpenClipboard(nullptr))
   {
      EmptyClipboard();
      SetClipboardData(CF_BITMAP, (HANDLE)bmp);
      CloseClipboard();
   }

   // cleanup our drawing objects
   SelectObject(dc, bmp_old);
   DeleteDC(dc);
   DeleteObject(bmp);
}
```

... and to add to the plethora of utilities available to do this - it is now integrated in [ColorLens](/projects/2017/10/18/colorlens.html) as well by pressing `Ctrl+F10`.
