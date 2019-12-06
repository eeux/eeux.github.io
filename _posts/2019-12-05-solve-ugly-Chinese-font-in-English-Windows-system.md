---
layout: post
title: "How to solve the ugly Chinese font in the English version of Windows?"
date: 2019-12-05 23:27
categories: tip
keywords: Windows font
---





In the **English version** of Windows, the default fonts are generally `Segoe UI`, `Tahoma` and `Microsoft Sans Serif`.
These fonts do not have a `Chinese font library`, so when Chinese fonts are need to be displayed,
various unsightly text effects appear...





#### Solution: FontLink
When using English fonts, if you encounter characters that
are not in this font, you will go to the corresponding location
in the `registry` to find the font it is linked to.

1. open register editor (`Win + R`, Enter `regedit`), go to
   **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\FontLink\SystemLink**
   ![registry-font](https://github.com/eeux/blog-pictures/blob/master/2019/12/registry-font.png?raw=true)
2. you will find links of some common English fonts, like `Segoe UI`, `Tahoma` and so on.
   What you need to do is **put the Chinese fonts you want to use (such as `Microsoft Yahei: MSYH.TTF`)
   on the top priority of these English fonts**

   ![updated-font-registry](https://github.com/eeux/blog-pictures/blob/master/2019/12/updated-font-registry.png?raw=true)

**attention:** the setting of font link is related to **system locale setting**.
Settings of font link will **reset** every time system local setting is updated.

---
(done)
