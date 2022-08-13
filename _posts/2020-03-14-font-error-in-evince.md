---
layout: post
title: "Solution to not displaying Chinese in CTeX"
subtitle: 'Fix font error in Linux'
author: "Xiping Hu"
header-style: text
tags:
- Linux
- LaTeX
---

# Problem Specification #

- After upgrading to BlackArch Linux, Chinese characters in PDF files made by XeLaTeX cannot display.
- Neither of Evince, Document Viewer in GNOME, PDFView in Emacs can display Chinese Characters, but Chrome can display Chinese correctly.
- CTEX is used in compiling LaTeX, XeLaTeX complied without errors, except a warning like "Package fontspec Warning: Font "FandolSong-Regular" does not contain requested (fontspec) Script "CJK"."

# Not Working Solutions #

- Installing Windows fonts
- Install the fandol fonts in `/home/hxp/.fonts/fonts/opentype/public/fandol`

# Solution #

Install `poppler-data` package

``` shell
sudo yay -S poppler-data
```
