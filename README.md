# FreeBSD Hypervisor Extensions

There are features or bug fixes that are not yet present in BHyVe
shipped with certain FreeBSD versions, and this repository holds a
port to make them accessible.  Changes here are upstreamed to the
FreeBSD ports tree time to time.

Currently, the following extensions are offered over the vanilla
`bhyve` distribution available from the base system:

- For FreeBSD 11 and 12, [support for `virtio-9p` (VirtFS/9p file
  system passthrough)](https://reviews.freebsd.org/D10335) is ported
  back.  Note that only the latest releases, that is 11.4-RELEASE and
  12.2-RELEASE are supported.

- The latest version of the
  [lib9p](https://github.com/conclusiveeng/lib9p) library is imported,
  which is newer than the one present in 13.0-RELEASE.

- Bjoern Zeeb's experimental patch for [supporting non-standard
  mappings in the MSI-X table
  page](https://reviews.freebsd.org/D31241) is added.

- The feature of querying the list of supported PCI devices for
  FreeBSD 11 is ported back from later versions.

- [The fix for PCI passthrough via VT-d on modern chipsets with
  multiple translation
  units](https://svnweb.freebsd.org/base?view=revision&revision=349184)
  is ported back for FreeBSD 11.

## Installation

After cloning the repository, the port can be installed as follows.

```console
# cd sysutils/bhyve+
# make install clean
```

Note that the port is going install a version of `vmm.ko` under
`/boot/modules/` which must be explicitly referenced when using
`kldload(8)` for loading the module.
