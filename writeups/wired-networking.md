# wired networking (intel i219-v and friends)

covers: [intel i219-v ethernet](../diamantinoop/intel-i219-v-ethernet.md)
priority: p2 (soon after a first desktop; wifi builds on this)

## what was asked for

support for the intel i219-v onboard nic, tested on an asus b460 pro wifi board (intel 10th gen). the i219-v is on a huge number of intel boards, so it is a good first target.

## the key point

macOS inside anathema will never see the i219-v. it will see a **virtual** network card that anathema emulates. so this is two pieces of work, both inside anathema:

```
macOS guest  ->  virtio-net (virtual nic, emulated by anathema-rt)
                    |
                    v
             anathema loader backend  ->  your real i219-v
```

### 1. the guest side: virtio-net

apple's own vms use virtio network devices, so arm64 macOS already ships a driver for it. anathema-rt already has a pcie host and one virtio device (the boot disk at bus 0, device 1). networking means adding a second virtio function for the network card with its receive and transmit queues.

this half is the same for every pc. it can be built and tested in the linux harness (`rt-boot`) first, with a tap device as the backend.

### 2. the host side: talking to the real nic

| backend | how | trade-off |
| --- | --- | --- |
| uefi simple network protocol (snp) | many boards' firmware include a driver for the onboard nic (it is how network boot works). anathema runs inside the uefi app today, so it may be able to use it. | one code path for many nics. only works while uefi boot services are alive, and quality varies by board. good first backend. |
| own i219-v driver | write a small send/receive driver in the loader from intel's public datasheets. | most control, one chip family per driver. |
| tap device (linux harness only) | for development and testing. | not for end users. |

hackintosh drivers like intelmausi drive the real nic from intel macOS. on this arm path they don't apply, because the guest is arm64 macOS and never touches the real chip (on the native intel path they would apply directly). they (and linux's e1000e) are still useful as documentation of how the chip behaves, but check each license before copying anything: e1000e is gpl, which doesn't mix with anathema's mit code.

## suggested steps

1. virtio-net device in anathema-rt, tested in the linux harness with a tap backend.
2. confirm the macOS guest finds it and gets a dhcp address.
3. snp backend in the loader. test on the asus b460 pro wifi board.
4. if snp is unreliable on real boards, write an i219-v backend.

## why before wifi

wifi needs the same virtio-net device on the guest side. the host side additionally needs firmware loading, scanning, wpa and roaming inside anathema's loader. doing ethernet first means wifi only has to add the host backend.

## open questions

* does anathema keep uefi boot services alive for the whole session, or does it plan to exit them? (snp only works while they are alive.)
* which other onboard nics are common in the community? a quick poll would pick the second backend.
