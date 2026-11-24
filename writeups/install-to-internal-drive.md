# install to an internal drive

covers: [install to the internal ssd](../runemonkey/install-to-the-internal-ssd.md)
priority: p2 (after booting to the installer)

## what was asked for

a way to install macOS and everything anathema needs onto the pc's internal ssd, so the usb stick isn't needed anymore.

## how it works today (and why that is safe)

* anathema boots from a usb stick prepared by `anathema-setup` on windows.
* the guest sees one virtual disk. **every write the guest makes goes to an in-memory overlay and is thrown away at power-off.** nothing the guest does reaches a real disk.
* the nvme driver in the bring-up test is read-only.

that is a very safe starting point. install is the first feature that would deliberately write to a user's internal drive, so it needs the most care.

## what install needs

1. **a write path.** a disk backend in the loader that can write, either through uefi's block i/o protocol or a write-capable nvme driver.
2. **a place to write.** a dedicated partition created for anathema. never a whole disk by default, never someone else's partition.
3. **a way to boot without the stick.** copy the loader to the efi system partition and add a boot entry. boot entries live in nvram, so keep nvram writes few and small (filling firmware variable storage can brick some boards).
4. **a macOS installer that can run.** this needs the display work first, or a text-mode installer path.

## safety rules (proposed)

* show the disk's model, size and serial number, and the exact partition to be written, before doing anything.
* require a typed confirmation (for example the disk size or the word `install`), not just a click.
* refuse disks that hold bitlocker, an active windows install partition, or an unknown partition table, unless an advanced option is turned on.
* never write outside the partition anathema created. check every write against that range in code.
* dry-run mode that prints every write it would make.
* a clear "back up your data first" screen. dual-boot setups lose data when installers are sloppy.

## open questions

* whole disk, or a partition next to windows? (partition next to windows is what most people will want, and is also the riskiest.)
* does the installed system keep using the same virtual disk format, or a raw apfs partition?
* how are anathema updates installed once it lives on the internal drive?
