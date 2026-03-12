# Hyperion Kernel Patches

This directory contains source-level patches applied to the Linux kernel tree
during the Hyperion build. Patches are applied in sorted filename order via
`patch -p1 --fuzz=5` before `make olddefconfig` runs.

---

## 0001-docs-add-hyperion-perf-tuning-guide.patch

Adds `Documentation/admin-guide/hyperion-tuning.rst` to the kernel source tree
and registers it in the admin-guide index so it is included in `make htmldocs`.

### What it documents

| Section | Content |
|---|---|
| BPF & JIT | Why JIT-always-on is the correct 2026 default; unprivileged BPF CVE history |
| sched-ext | How to load/unload BPF schedulers at runtime; scx_bpfland, scx_lavd, scx_rusty |
| x86 tuning | X2APIC, ERMSB user copies, AMD P-State Active EPP mode |
| FS integrity | fscrypt, fs-verity, case-insensitive Unicode filesystems |
| IPE | Integrity Policy Enforcement policy syntax and boot-verified property |
| ZRAM multi-comp | Dual-stream ZSTD/LZ4 zram setup walkthrough |
| Debug discipline | Which debug knobs are off and why |

### Application

```bash
cd linux-6.19.6
patch -p1 --fuzz=5 < ../patches/0001-docs-add-hyperion-perf-tuning-guide.patch
```

### Why a documentation patch

The performance and security improvements in Hyperion v2.2.2 are driven
entirely by **kernel configuration** (hyperion.config), not by new driver
code. The configuration changes are self-contained and require no source
modifications.

The patch adds documentation to the kernel tree so that the rationale for
each config decision is co-located with the kernel source, included in the
official HTML documentation build, and readable offline.

This is a no-conflict patch: it only creates a new file and appends one
line to `Documentation/admin-guide/index.rst`.

---

## Adding New Patches

1. Name files `NNNN-short-description.patch` (zero-padded, sorted order)
2. Generate with `git format-patch` from a clean kernel tree
3. Test with `patch -p1 --dry-run` before committing
4. Document the patch in this README

The build CI applies patches with:
```bash
shopt -s nullglob
for patch in ../patches/*.patch; do
  patch -p1 --fuzz=5 < "$patch"
done
```
