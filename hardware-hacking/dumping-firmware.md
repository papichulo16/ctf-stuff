# Dumping Firmware

So in my previous crappy blog-like thing I talked about how I got to communicate with a device through UART. Now I am going to talk about how I was able to dump the firmware from that device.

So when I first run the device I get a lot of noise from the boot loader like this:
```
Booting...
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
@ chip__no chip__id mfr___id dev___id cap___id size_sft dev_size chipSize
@ 0000000h 05e4016h 000005eh 0000040h 0000016h 0000000h 0000016h 0400000h
@ blk_size blk__cnt sec_size sec__cnt pageSize page_cnt chip_clk chipName
@ 0010000h 0000040h 0001000h 0000400h 0000100h 0000010h 000004eh ZB25VQ32B
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
---RealTek(RTL8196E)at 2022.10.11-21:19+0800 v3.4.11E [16bit](400MHz)
P0phymode=01, embedded phy
wait for multicast packet!
not find active multicast server!
check_image_header  return_addr:05010000 bank_offset:00000000
no rootfs signature at 00150000!
no rootfs signature at 00160000!
no rootfs signature at 001A0000!
Jump to image start=0x80c00000...
decompressing kernel:
Uncompressing Linux... done, booting the kernel.
done decompressing kernel.
start address: 0x80003780
Linux version 3.10.90 (root@wwyl) (gcc version 4.6.4 (Realtek RSDK-4.6.4 Build 2080) ) #en14 Tue Nov 1 11:17:03 CST 2022
CPU revision is: 0000cd01
Determined physical RAM map:
 memory: 02000000 @ 00000000 (usable)
```

A lot of this info can be useful but I am lazy and just waited a little bit and I got a regular shell with a busybox instance. 
```
# ls
bin   etc   init  mnt   root  sys   usr   web
dev   home  lib   proc  sbin  tmp   var
```

I then looked through to see what kind of commands I was able to use by looking at `/bin`. Turns out I don't have much but I did find `cat`. What I did next was find the partitions and then I would print out the partitions, while logging it all to a file. The way I did that was when I first ran `tio` I added the `-L --log-file <file>` flags. Then I got a dump.

```
# cat /proc/cpuinfo 
system type		: RTL8196E
machine			: Unknown
processor		: 0
cpu model		: 52481
BogoMIPS		: 398.13
tlb_entries		: 32
mips16 implemented	: yes

# cat /proc/partitions 
major minor  #blocks  name
  31        0       2112 mtdblock0
  31        1       1728 mtdblock1
  31        2        128 mtdblock2
  31        3        128 mtdblock3

# cat /dev/mtdblock0
...
```

This one was fairly easy but this taught me the fact that dumping the firmware is not much of a one way to do it kind of thing. Instead it is more like you have to figure out what you have available and then think of creative ways to go about it.
