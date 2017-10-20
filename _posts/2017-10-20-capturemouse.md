



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
	ReleaseDC(0, screen);

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
