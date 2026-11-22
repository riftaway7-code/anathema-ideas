# apple account support (smbios / identity)

from: itsnotungofa (discord)

> Maybe try to make apple accounts work? something like smbios o some thing

## notes

apple services (icloud, imessage, facetime, app store) check the machine's identity: serial number, board serial, uuid and rom. on a hackintosh that is the smbios setup. in anathema the machine is an emulated virtual mac, so the identity has to be supplied by the emulated platform (device tree / boot data) and made to look like a believable, unique mac.

this only matters once the system reaches the desktop. current blocker is getting launchd to run, so this is a later-stage addon. also worth knowing: apple can still refuse a sign-in or block messaging on identities it dislikes, so it is a best-effort feature, not a guarantee.

status: open, blocked on booting to userspace
