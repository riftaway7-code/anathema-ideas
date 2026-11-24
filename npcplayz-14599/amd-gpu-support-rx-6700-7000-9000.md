# AMD GPU support (RX 6700 / 7000 / 9000 series)

requested by: npcplayz_14599, majestic_sparrow_37877, lamantine.

- RDNA2 and older (RX 400/500, Vega, 5000/6000): apple already ships drivers, should work once the intel kernel boots natively on the pc
- RDNA3 (7000) and RDNA4 (9000): no apple driver. Cobalt64 (github.com/AizawaHikaru233/Cobalt64) does RDNA4 with a kernel driver plus RADV port plus Metal-on-Vulkan; RDNA3 is future work there
- plan: optional separate add-on after boot works, same approach as the NVIDIA add-on idea. check licenses before borrowing anything, ideas only for now
