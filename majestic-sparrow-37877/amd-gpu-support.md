# amd gpu support

from: majestic_sparrow_37877 (discord, forwarded by setholidays)

> I wonder where a starting point would be for adding amd cards. Nvidia cards on macos is cool but I do like my AMD

## notes

anathema boots arm macOS under translation, so it does not use a real gpu driver. macOS talks to an apple paravirtual gpu, and that device has to be emulated on the host. a host amd card would only matter as the thing that draws the final picture (and maybe accelerates it later), so amd vs nvidia is mostly a host-side output question, not a macOS driver question.

starting point: the display path. get the paravirtual gpu / framebuffer working first, then pick the host backend (vulkan would cover amd and nvidia).

status: open
group: display / gpu output
priority: p1
