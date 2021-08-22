# FreeBSD Hypervisor Extensions

There are features or bug fixes that are not yet present in BHyVe
shipped with certain FreeBSD versions, and this repository holds a
port to make them accessible.  Changes here are upstreamed to the
FreeBSD ports tree time to time.

Currently, the following extensions are offered over the vanilla
`bhyve` binary:

- For FreeBSD 11 and 12, [support for `virtio-9p` (VirtFS/9p file
  system passthrough)](https://reviews.freebsd.org/D10335) is ported
  back.  This patch will not be installed for later versions.  Note
  that only the latest releases, that is 11.4-RELEASE and 12.2-RELEASE
  are supported.

- Bjoern Zeeb's experimental patch for [supporting non-standard
  mappings in the MSI-X table
  page](https://reviews.freebsd.org/D31241) is added.

- The feature of querying the list of supported PCI devices for
  FreeBSD 11 is ported back from later versions.

## Installation

After cloning the repository, the port can be installed as follows.

```console
# cd sysutils/bhyve+
# make install clean
```
