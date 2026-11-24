# display output (and amd / nvidia gpus)

covers: [amd gpu support](../majestic-sparrow-37877/amd-gpu-support.md), [amd gpu support +1](../npcplayz-14599/amd-gpu-support.md)
priority: p1 (next big job after booting)

## what people asked for

"can amd cards work, not just nvidia?" two separate people asked, so this is the most-wanted idea in the repo.

## how it works today

* the guest kernel draws into a plain framebuffer that anathema emulates.
* in the usb loader, anathema copies that framebuffer to the screen through uefi's graphics output protocol (gop), with the kernel log underneath.
* gop is provided by your graphics card's firmware. **any card that can show the pc's boot screen works for this, amd, nvidia or intel.** no gpu driver is involved.
* apple's paravirtual gpu device is removed from the device tree, so the kernel runs headless.

## why a desktop needs more than that

as far as we know, the macOS window server expects a metal-capable gpu. inside apple's own vms that gpu is a **paravirtual** device: macOS sends it commands through a ring buffer and a host program answers. that host program is apple's `ParavirtualizedGraphics` framework, which only exists on macOS. qemu's vmapple support gets graphics by calling that framework on a mac host, so it doesn't help on a pc.

so the real question is not "amd or nvidia". it is "what plays the role of apple's host renderer on a pc?"

## options

| option | what it means | size |
| --- | --- | --- |
| a. plain framebuffer desktop | find out whether the window server can come up without the paravirtual gpu (unaccelerated). if it can, gop output already works on every card. | unknown, possibly small. the first thing to test. |
| b. reimplement the host side of the paravirtual gpu | learn the guest/host protocol by observing it (clean-room, no apple code copied) and answer the guest's commands ourselves. | very large |
| c. b, plus a real gpu backend | turn the guest's metal-level commands into vulkan. vulkan runs on amd, nvidia and intel, so this is where "amd support" actually lives. | very large, after b |

## suggested steps

1. (p0, already in progress) get launchd and user space running.
2. boot with the paravirtual gpu still removed and record what the window server does: does it start, what does it log, what does it wait for?
3. if option a looks possible, ship it: a slow desktop on every gpu beats a fast desktop on none.
4. if not, start a protocol notes document for the paravirtual gpu: what the guest writes to the ring, in what order, what replies it expects. notes only, no apple code or binaries in the repo.
5. pick the host backend later. vulkan is the obvious candidate because it covers amd and nvidia with one code path.

## how someone can help right now

* people with any gpu: once the loader can boot further, test that the boot screen and kernel log show up on your card and report the card + board.
* people who know metal / vulkan: read about how other projects translate between graphics apis (ideas only, check licenses first).

## open questions

* does any macOS 27 component fall back to software rendering when no gpu is present?
* is the paravirtual gpu protocol stable between macOS versions, or does it change every release?
