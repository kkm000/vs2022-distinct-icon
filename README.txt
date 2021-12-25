A DISTINCT ICON FOR VS2022
==========================

(c) CC-BY-4.0 2021 kkm
This work is licensed under a Creative Commons Attribution 4.0
International License: https://creativecommons.org/licenses/by/4.0/

Since I have VS2017, VS2019 and VS2022 installed and pinned to the taskbar...
You get the point. There should better be a way to tell them apart somehow.
So I dusted my iconification superpowers, and gave it some volume and gradient.

How to Use
----------

If you replace the icon, put in into a local directory; it won't be embedded
into the shortcut. Then: Start, VS2022, right-click, More, Open File Location.
This opens the directory "C:\ProgramData\Microsoft\Windows\Start Menu\Programs"
by default. On the shortcut: context menu, properties, Change Icon... button.
Select the copy of vs2022-distinct.ico that you just found the permanent home
for. You'll get the admin required prompt when saving, this is expected.

What Is All This Stuff?
-----------------------

The mother image is assets/256.png. The other assets/*.png are obtained by
a careful scaling of the largest one.

Then, I do not have any software to build an ICO file from PNG images. I tried
a couple of online services. One mangled the images, storing them as BMP and
somehow adding a white outline to the glyph. Another did not produce an .ico
file recognized as such. And yeah, VS2022 itself has an ICO editor but has no
way of importing images, and cannot save PNG (only shows them).

So I just computed future offsets in Excel, typed the 134 byte header in a hex
editor, and concatenated the individual asset PNGs to it. The quickest way
to get the job done, believe it or not!

icon-offsets.xlsx is the computation worksheet, and icon-offsets.tsv is the
table exported from it, with offset and length columns easy to copy from and
paste into the hex editor.

BUILD NOTES TO MYSELF
=====================

Ground truth source file sizes, in ICO directory order, for xslx computation
(simple copy/paste into Excel):

$ cd assets
$ ls -lr *.png
-rw-r--r-- 1 kkm kkm 67399 2021-12-21 14:49:18 256.png
-rw-r--r-- 1 kkm kkm 21399 2021-12-21 14:50:03 128.png
-rw-r--r-- 1 kkm kkm 13816 2021-12-21 14:50:29 096.png
-rw-r--r-- 1 kkm kkm  7564 2021-12-21 14:51:16 064.png
-rw-r--r-- 1 kkm kkm  4966 2021-12-21 14:51:40 048.png
-rw-r--r-- 1 kkm kkm  2673 2021-12-21 14:52:44 032.png
-rw-r--r-- 1 kkm kkm  1729 2021-12-21 14:53:07 024.png
-rw-r--r-- 1 kkm kkm   943 2021-12-21 14:53:33 016.png

ICO format description:
https://en.wikipedia.org/w/index.php?title=ICO_(file_format)&oldid=1048679157#Icon_resource_structure

ICO fixed header is 6 bytes. 0008 is the number of images:

$ od -Ax -N6 -tx2 vs2022-distinct-head.ico
0000000  0000 0001 0008

Followed by 16-byte image directory entries as computed. PNG files follow
the header. Offset of the first file is 6 + 16 × 8 = 134 = 0x0086.

$ od -Ax -j6 -N128 -tx4 vs2022-distinct-head.ico
0000006  00000000 00180000 00010747 00000086
0000016  00008080 00180000 00005397 000107cd
0000026  00006060 00180000 000035f8 00015b64
0000036  00004040 00180000 00001d8c 0001915c
0000046  00003030 00180000 00001366 0001aee8
0000056  00002020 00180000 00000a71 0001c24e
0000066  00001818 00180000 000006c1 0001ccbf
0000076  00001010 00180000 000003af 0001d380
             ---- ----     -------- --------
             xxyy bits     datasize fileofst

Then simply concatenate the header with the PNG files:

$ cat >../vs2022-distinct.ico vs2022-distinct-head.ico \
   256.png 128.png 096.png 064.png 048.png 032.png 024.png 016.png

This is all.
