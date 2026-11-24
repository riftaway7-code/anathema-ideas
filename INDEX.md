# idea index

every idea in this repo, grouped by area. duplicates are merged into one row and counted as votes. the original files are untouched apart from a `group:` and `priority:` line.

last sorted: 2026-11-22 (7 ideas from 5 people, 6 groups)

## priority tags

| tag | meaning |
| --- | --- |
| `p0` | the current blocker. right now that is getting the system to boot far enough to run userspace (see the main repo's status section). no idea here is p0. |
| `p1` | next big job once the system boots. needed before anyone can use a desktop. |
| `p2` | wanted soon after a first desktop. |
| `p3` | later. real, but depends on several p1/p2 pieces first. |
| `parked` | needs a decision from the maintainer before any work, usually for legal or safety reasons. |

priority is about order, not about how good the idea is. almost everything is blocked on booting, so nothing below can start yet.

## the one thing to know before reading

anathema has two boot paths, and which one an idea belongs to changes what "support my card" means.

**arm path (the original design).** macOS runs as an arm64 guest under translation and never sees your real pc hardware. it sees a small set of **virtual** devices that anathema emulates (today: one virtio disk, a uart, a timer, an interrupt controller and a framebuffer). so support is two jobs on the anathema side:

1. a virtual device that macOS already has a driver for (for example virtio-net for networking), and
2. a backend in anathema's loader that drives your real card.

hackintosh drivers (kexts) don't plug into this path, because the guest never touches the real device. they can still document how a chip works, if their license allows reading them (check before copying anything).

**native intel path (where the project is heading).** the intel macOS kernel boots directly on the pc through anathema's own uefi loader, with ahead-of-time translation for arm-only programs. here real drivers and hackintosh-style kexts do apply, and gpu, network and audio support are driver and kext work on the real hardware instead of emulated devices. the write-ups below were written for the arm path; where they say a driver "doesn't apply", read it as "only on the arm path".

## groups

| group | priority | ideas (votes) | asked by | blocked on | write-up |
| --- | --- | --- | --- | --- | --- |
| display / gpu output | `p1` | [amd gpu support](majestic-sparrow-37877/amd-gpu-support.md), [amd gpu support +1](npcplayz-14599/amd-gpu-support.md) (2) | majestic_sparrow_37877, npcplayz_14599 | booting; apple's paravirtual gpu protocol | [display output](writeups/display-output.md) |
| wired networking | `p2` | [intel i219-v ethernet](diamantinoop/intel-i219-v-ethernet.md) (1) | diamantinoop | booting | [wired networking](writeups/wired-networking.md) |
| install to internal drive | `p2` | [install to the internal ssd](runemonkey/install-to-the-internal-ssd.md) (1) | runemonkey | booting to the installer; display | [install to an internal drive](writeups/install-to-internal-drive.md) |
| audio | `p3` | [sound in early versions](itsnotungofa/sound-in-early-versions.md) (1) | itsnotungofa | booting | |
| wifi | `p3` | [intel wifi support](runemonkey/intel-wifi-support.md) (1) | runemonkey | wired networking first | |
| apple account / identity | `parked` | [apple account support (smbios)](itsnotungofa/apple-account-support-smbios.md) (1) | itsnotungofa | maintainer decision | |

## why this order

* **display first.** an installer and a desktop both need a screen. this is also the hardest open problem (see the write-up), so it is worth thinking about early even while boot work continues.
* **wired networking before wifi.** a wired nic needs a packet send/receive path and nothing else. wifi needs that plus firmware loading, scanning, wpa and roaming in anathema's loader. same virtual device on the macOS side either way, so wifi reuses everything ethernet builds.
* **install to internal drive at p2, not p1.** it writes to a user's real disk, so it needs the most safety work. today every write the guest makes goes to an in-memory overlay and is thrown away at power-off, which is a good safety property to keep until install is designed properly.
* **audio at p3.** it needs a virtual sound device plus a host audio backend (hda codecs vary a lot between boards). nice to have, not needed to use the system.
* **apple account parked.** see the note below.

## note on the apple account idea

apple services check the machine's identity. for anathema that identity would have to be made up so it looks like a real apple machine, or borrowed from a real one. both go against apple's terms, can get the user's apple account locked, and borrowing a real serial can cause trouble for the real owner. this repo shouldn't ship serial generators or borrowed identities. it stays parked until the maintainer decides whether the project wants to touch this at all.

## adding an idea

add `<your-discord-name>/<short-idea-name>.md`:

```
# <idea title>

from: <your name> (discord)

> <the request in your own words>

## notes

<anything useful: which hardware, why you want it>

status: open
group:
priority:
```

leave `group` and `priority` empty, the maintainer fills them in. if the idea is already in the table above, add a line to that file instead (`+1 from <name>: <anything new>`) so votes stay in one place.
