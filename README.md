# FreeBSD Hypervisor Extensions

There are features or bug fixes that are not yet present in BHyVe
shipped with certain FreeBSD versions, and this repository holds a
port to make them accessible.  Changes here are upstreamed to the
FreeBSD ports tree time to time.

Currently, the following extensions are offered over the vanilla
`bhyve` distribution available from the base system:

- A more up-to-date version of the
  [lib9p](https://github.com/conclusiveeng/lib9p) library is imported,
  which is newer than the one present in the FreeBSD base system.

## Installation

After cloning the repository, the port can be installed as follows.

```console
# cd sysutils/bhyve+
# make install clean
```

Note that the port is going to install a version of `vmm.ko` under
`/boot/modules/` which must be explicitly referenced when using
`kldload(8)` for loading the module.
