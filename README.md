![Image](https://m.media-amazon.com/images/I/71hDrsN5qvL._AC_SL1500_.jpg  "LV-PYN3-W-SP")

![Image](https://github.com/user-attachments/assets/9f139e1c-ae6a-4ff1-842f-e053b95b7511)
![Image](https://github.com/user-attachments/assets/82fd8fc8-c697-4887-a5f3-f5e45f3b92b9)
**T23ZN**
![Image](https://github.com/user-attachments/assets/6be2b45f-cdd4-49e0-8eb6-39dc2f125348)
**Showing IP5209 charging controller & ATBM6441**
![Image](https://github.com/user-attachments/assets/ba66514f-aec0-4e23-a4f7-098b3d0d1334)
**Rear of board - UART in top right.  Top interface for ATBM6441, bottom for T23ZN**

Shop link: https://www.amazon.com/dp/B0DFLJHQ2Q
Model: LV-PYN3-W-SP
Firmware: 2022-0923-0945
App: LaView
PTZ: no
Onvif: Yes
CPU: T23-ZN
Sensors: sc3336
Wireless: atbm6441

**Root can be achieved by:**

- modifying boot string at 0x00042000 to init=/bin/sh

`> CMDLconsole=ttyS1,115200n8 mem=39424K@0x0 rmem=26112K@0x2680000 root=/dev/ram0 rw rdinit=/linuxrc mtdparts=jz_sfc:256k(uboot),352k(tag),1984k(kernel),2592K(rootfs),2624k(app),384k(config),8M@0(all) lpj=4493312 quiet`

**to**

`> CMDLconsole=ttyS1,115200n8 mem=39424K@0x0 rmem=26112K@0x2680000 root=/dev/ram0 rw rdinit=/bin/sh mtdparts=jz_sfc:256k(uboot),352k(tag),1984k(kernel),2592K(rootfs),2624k(app),384k(config),8M@0(all) lpj=4493312`

- modifying shadow file at /etc/shadow to remove root password hash
- busybox login to root from /bin _(blank password now)_
- busybox linuxrc
- (while hitting enter) - gv login prompt will show - login root with no password - root access gained to filesystem & rootfs

**binwalk output:**

```
> DECIMAL       HEXADECIMAL     DESCRIPTION
> --------------------------------------------------------------------------------
> 36328         0x8DE8          CRC32 polynomial table, little endian
> 200700        0x30FFC         CRC32 polynomial table, little endian
> 205804        0x323EC         AES Inverse S-Box
> 206060        0x324EC         AES S-Box
> 207912        0x32C28         Android bootimg, kernel size: 0 bytes, kernel addr: 0x70657250, ramdisk size: 543519329 bytes, ramdisk addr: 0x6E72656B, product name: "mem boot start"
> 622592        0x98000         uImage header, header size: 64 bytes, header CRC: 0x86C9B513, created: 1970-01-01 00:00:00, image size: 2022795 bytes, Data Address: 0x80010000, Entry Point: 0x8033E480, data CRC: 0x2A17A3B2, OS: Linux, CPU: MIPS, image type: OS Kernel Image, image name: "Linux-3.10.14"
> 1333197       0x1457CD        MPEG transport stream data
> 3840618       0x3A9A6A        Cisco IOS microcode, for ""
> 4768327       0x48C247        HPACK archive data
> 5308416       0x510000        Squashfs filesystem, little endian, version 4.0, compression:gzip (non-standard type definition), size: 2578136 bytes, 42 inodes, blocksize: 65536 bytes, created: 2022-01-01 00:00:00
> 7995392       0x7A0000        JFFS2 filesystem, little endian
```

binwalk export tree _(note the absence of rootfs)_:

```
> .
> ├── 510000.squashfs
> ├── 7A0000.jffs2
> ├── jffs2-root
> │   ├── factory.json
> │   ├── tuya
> │   │   ├── tuya_enckey.db
> │   │   └── tuya_user.db
> │   └── tuya.json
> └── squashfs-root
>     ├── bin
>     │   ├── daemon
>     │   ├── flashcp
>     │   ├── gv_apns.bin
>     │   ├── gv_apns_iccid.bin
>     │   ├── gv_led_flash
>     │   ├── impdbg
>     │   ├── log
>     │   ├── logcat
>     │   ├── update
>     │   └── wifi
>     ├── drivers
>     │   ├── gv-gpio.ko
>     │   └── gv_step_motor.ko
>     ├── init
>     │   └── app_init.sh
>     ├── lib
>     │   ├── libgv_device_id_decryption.so
>     │   ├── libgv_device_id_decryption.so.sign
>     │   ├── libgv_ipc.so
>     │   ├── libgv_vad.so
>     │   ├── libgvalarm.so
>     │   └── libipc.so
>     ├── osd
>     │   ├── logo.rgba
>     │   ├── osd.json
>     │   └── time.rgba
>     ├── sound
>     │   ├── ble_conn_success.wav
>     │   ├── dialler.wav
>     │   ├── door_bell.wav
>     │   ├── factory_button.wav
>     │   ├── factory_test.wav
>     │   ├── qrcode_success.wav
>     │   ├── reset.wav
>     │   ├── restart.wav
>     │   ├── siren_warn.wav
>     │   ├── wifi_config.wav
>     │   ├── wifi_connect_success.wav
>     │   └── wifi_pwd_error.wav
>     └── verdeps.json
> 
```

 

**U-boot magic files:**

> jzt23ZNall.bin
> jzt23ZNsd.bin
> jzt23ZNota.bin

**cat /proc/mtd:**
```

> dev:    size   erasesize  name
> mtd0: 00040000 00001000 "uboot"
> mtd1: 00058000 00001000 "tag"
> mtd2: 001f0000 00008000 "kernel"
> mtd3: 00288000 00008000 "rootfs"
> mtd4: 00290000 00008000 "app"
> mtd5: 00060000 00008000 "config"
> mtd6: 00800000 00008000 "all"
```

**cat /proc/version:**

`> Linux version 3.10.14 (cheng@cheng-PC) (gcc version 5.4.0 (Ingenic Linux-Release3.3.0-Default(xburst1(fp32)+uclibc0.9.33.2) Smaller libc 2022.11-07) ) #16 PREEMPT Tue Dec 31 17:11:46 CST 2024`


**lsmod:**

```
> Module                  Size  Used by    Tainted: G
> gv_gpio                 1808  1
> atbm6041_wifi_sdio    157008  2
> cfg80211              142560  1 atbm6041_wifi_sdio
> gvfs                   57840  0
> mmc_block              22368  0
> jzmmc                  14128  1 atbm6041_wifi_sdio
> mmc_core               72864  3 atbm6041_wifi_sdio,mmc_block,jzmmc
> squashfs               23776  1
> jffs2                 104224  1
> jz_sfc                 12976  2
```


**dmesg:**

```
> <6>[    0.000000] Initializing cgroup subsys cpu
> <6>[    0.000000] Initializing cgroup subsys cpuacct
> <5>[    0.000000] Linux version 3.10.14 (cheng@cheng-PC) (gcc version 5.4.0 (Ingenic Linux-Release3.3.0-Default(xburst1(fp32)+uclibc0.9.33.2) Smaller libc 2022.11-07) ) #16 PREEMPT Tue Dec 31 17:11:46 CST 2024
> <4>[    0.000000] CPU0 RESET ERROR PC:E2D39F03
> <6>[    0.000000] CPU0 revision is: 00d00100 (Ingenic Xburst)
> <6>[    0.000000] FPU revision is: 00b70000
> <7>[    0.000000] cgu_get_rate, parent = 900000000, rate = 0, m = 129, n = 255, reg val = 0x081000ff
> <7>[    0.000000] cgu_get_rate, parent = 900000000, rate = 0, m = 129, n = 255, reg val = 0x081000ff
> <4>[    0.000000] cgu_macphy clk should be opened!
> <4>[    0.000000] CCLK:900MHz L2CLK:450Mhz H0CLK:200MHz H2CLK:200Mhz PCLK:100Mhz
> <6>[    0.000000] Determined physical RAM map:
> <6>[    0.000000]  memory: 003fe000 @ 00010000 (usable)
> <6>[    0.000000]  memory: 00032000 @ 0040e000 (usable after init)
> <6>[    0.000000] User-defined physical RAM map:
> <6>[    0.000000]  memory: 02680000 @ 00000000 (usable)
> <6>[    0.000000] Initial ramdisk at: 0x80600000 (6059520 bytes)
> <4>[    0.000000] Zone ranges:
> <4>[    0.000000]   Normal   [mem 0x00000000-0x0267ffff]
> <4>[    0.000000] Movable zone start for each node
> <4>[    0.000000] Early memory node ranges
> <4>[    0.000000]   node   0: [mem 0x00000000-0x0267ffff]
> <7>[    0.000000] On node 0 totalpages: 9856
> <7>[    0.000000] free_area_init_node: node 0, pgdat 8040d570, node_mem_map 81000000
> <7>[    0.000000]   Normal zone: 77 pages used for memmap
> <7>[    0.000000]   Normal zone: 0 pages reserved
> <7>[    0.000000]   Normal zone: 9856 pages, LIFO batch:1
> <4>[    0.000000] Primary instruction cache 16kB, 8-way, VIPT, linesize 32 bytes.
> <4>[    0.000000] Primary data cache 16kB, 8-way, VIPT, no aliases, linesize 32 bytes
> <4>[    0.000000] pls check processor_id[0x00d00100],sc_jz not support!
> <4>[    0.000000] MIPS secondary cache 64kB, 8-way, linesize 32 bytes.
> <7>[    0.000000] pcpu-alloc: s0 r0 d32768 u32768 alloc=1*32768
> <7>[    0.000000] pcpu-alloc: [0] 0
> <4>[    0.000000] Built 1 zonelists in Zone order, mobility grouping off.  Total pages: 9779
> <5>[    0.000000] Kernel command line: console=ttyS1,115200n8 mem=39424K@0x0 rmem=26112K@0x2680000 root=/dev/ram0 rw rdinit=/bin/sh mtdparts=jz_sfc:256k(uboot),352k(tag),1984k(kernel),2592K(rootfs),2624k(app),384k(config),8M@0(all) lpj=4493312▒▒▒▒▒▒▒ senv;[HW];init_vw=2304;init_vh=1296;nrvbs=2;mode=0;pwm_duty=80000;pwm_freq=80000;hv_flip=3;ir_mode=led;wl_mode=on;eenv; lzo_size=0 rd_start=0x80600000 rd_size=0x5c7600  sensor_i2c_error=0
> <4>[    0.000000] Invalid ir_mode info
> <4>[    0.000000] ir_switch_parse mode: 2 threshold min:2000 max:2500
> <4>[    0.000000] ir_switch_parse fs0(2304x1296x2),fs1(0x0x0),fs2(0x0x0)
> <4>[    0.000000] ir_switch_parse dayEv:0 nightEv:0 coeff:0 wbr:0 wbb:0
> <4>[    0.000000] Sensor Calibration Mode:0
> <6>[    0.000000] PID hash table entries: 256 (order: -2, 1024 bytes)
> <6>[    0.000000] Dentry cache hash table entries: 8192 (order: 3, 32768 bytes)
> <6>[    0.000000] Inode-cache hash table entries: 4096 (order: 2, 16384 bytes)
> <6>[    0.000000] Memory: 26752k/39424k available (3283k kernel code, 12672k reserved, 804k data, 200k init, 0k highmem)
> <6>[    0.000000] SLUB: HWalign=32, Order=0-3, MinObjects=0, CPUs=1, Nodes=1
> <6>[    0.000000] Preemptible hierarchical RCU implementation.
> <6>[    0.000000] NR_IRQS:347
> <4>[    0.000000] clockevents_config_and_register success.
> <6>[    0.000000] Console: colour dummy device 80x25
> <6>[    0.000188] Calibrating delay loop (skipped) preset value.. 898.66 BogoMIPS (lpj=4493312)
> <6>[    0.000206] pid_max: default: 32768 minimum: 301
> <6>[    0.000492] Mount-cache hash table entries: 512
> <6>[    0.001389] Initializing cgroup subsys debug
> <6>[    0.001420] Initializing cgroup subsys freezer
> <6>[    0.004890] NET: Registered protocol family 16
> <4>[    0.005667] register process exit failed.
> <7>[    0.006621] set gpio pull: 17-90
> <6>[    0.009601] bio: create slab <bio-0> at 0
> <6>[    0.012476] jz-dma jz-dma: JZ SoC DMA initialized
> <6>[    0.012887] i2c-gpio i2c-gpio.1: using pins 57 (SDA) and 58 (SCL)
> <6>[    0.013092] i2c i2c-0: i2c0 skip clk init.
> <6>[    0.013150] media: Linux media interface: v0.10
> <6>[    0.013242] Linux video capture interface: v2.00
> <4>[    0.014510] TTFF T23 ISP CLK: 150000000
> <4>[    0.014530] TTFF tx_isp_riscv_hf_prepare:620 tx_isp_stop_riscv start:180
> <4>[    0.014544] AE Para ADDR = a3f10000 HEAD:55aaaa55 SENSOR:23361114
> <4>[    0.075588] TTFF tx_isp_riscv_hf_prepare:641 wait riscv frame done:241
> <4>[    0.143572] TTFF tx_isp_riscv_hf_prepare:653 tx_isp_stop_riscv done:309
> <4>[    0.143588] Current sensor again:1024 isp gain:1025 inttime:856 luma:80 ev:856
> <4>[    0.143597] Riscv frame count:3
> <4>[    0.143607] led value=[0]
> <4>[    0.143614] Tuning mode is:2
> <4>[    0.143976] cgu_isp can not set to 153000000, will change!!!
> <4>[    0.144039] cgu_vpu can not set to 416000000, will change!!!
> <4>[    0.145227] @@@@ tx-isp-probe ok(version H20240221a - Z20240319r1), compiler date=Apr  8 2024 14:36:47@@@@@
> <4>[    0.145335] cgu_vpu can not set to 416000000, will change!!!
> <4>[    0.145974] [sc2336p_probe,1520] -> 1920*1080*15
> <7>[    0.145992] probe ok ------->sc2336p
> <4>[    0.146012] sc2336p chip found @ 0x30 (i2c0)
> <4>[    0.146021] sensor driver version H20231223a
> <4>[    0.160540] Calibration ADDR = a382a000
> <4>[    0.160558] Data = 54455353
> <4>[    0.167380] Calibration len = 176288
> <4>[    0.167461] Calibration len = 176288
> <4>[    0.167478] Load Sensor Setting DATE:calibration mode 0 MD5:calibration crc 2314831433
> <4>[    0.167488] Calibration len = 176288
> <4>[    0.171054] NCU: size = 3114816  paddr = 0x2680000
> <4>[    0.171078] motor_00:62
> <4>[    0.171189] motor_01:61
> <4>[    0.171244] motor_02:60
> <4>[    0.171298] motor_03:59
> <4>[    0.171364] motor_10:64
> <4>[    0.171424] motor_11:53
> <4>[    0.171493] motor_12:52
> <4>[    0.171550] motor_13:63
> <4>[    0.171876] sc2336p stream on
> <4>[    0.171920] [CHN0]TTFF frame_channel0_fast_start 493 W:2304 H:1296 N:2
> <4>[    0.171958] [frame_channel0_fast_start 579][CHN0] buf_id = 0 buf_ptr = 02979000 buf_len = 4478976
> <4>[    0.171976] [frame_channel0_fast_start 579][CHN0] buf_id = 1 buf_ptr = 02dbe800 buf_len = 4478976
> <4>[    0.172762] isp fast start
> <6>[    0.173577] Switching to clocksource jz_clocksource
> <6>[    0.178036] NET: Registered protocol family 2
> <6>[    0.178801] TCP established hash table entries: 512 (order: 0, 4096 bytes)
> <6>[    0.178837] TCP bind hash table entries: 512 (order: -1, 2048 bytes)
> <6>[    0.178859] TCP: Hash tables configured (established 512 bind 512)
> <6>[    0.178932] TCP: reno registered
> <6>[    0.178952] UDP hash table entries: 256 (order: 0, 4096 bytes)
> <6>[    0.178980] UDP-Lite hash table entries: 256 (order: 0, 4096 bytes)
> <6>[    0.179283] NET: Registered protocol family 1
> <6>[    0.179739] Trying to unpack rootfs image as initramfs...
> <4>[    0.233726] TTFF isp_vic_interrupt_service_routine:907 Chn:0  Frame  Start:399
> <6>[    0.243524] Freeing initrd memory: 5916K (80600000 - 80bc7000)
> <4>[    0.244236] freq_udelay_jiffys[0].max_num = 10
> <4>[    0.244252] cpufreq       udelay  loops_per_jiffy
> <4>[    0.244261] 12000  59910   59910
> <4>[    0.244269] 24000  119821  119821
> <4>[    0.244277] 60000  299554  299554
> <4>[    0.244285] 120000         599108  599108
> <4>[    0.244293] 200000         998513  998513
> <4>[    0.244302] 300000         1497770         1497770
> <4>[    0.244310] 600000         2995541         2995541
> <4>[    0.244318] 792000         3954114         3954114
> <4>[    0.244326] 1008000        5032509         5032509
> <4>[    0.244334] 1200000        5991082         5991082
> <6>[    0.250130] msgmni has been set to 63
> <6>[    0.252868] io scheduler noop registered
> <6>[    0.253059] io scheduler cfq registered (default)
> <4>[    0.253348] cgu_vpu can not set to 450000000, will change!!!
> <6>[    0.254093] jz-uart.0: ttyS0 at MMIO 0x10030000 (irq = 59) is a uart0
> <6>[    0.254292] jz-uart.1: ttyS1 at MMIO 0x10031000 (irq = 58) is a uart1
> <4>[    0.264879] TTFF frame_channel_buffer_done:293 Chn:0 Buf:0 Write Done:430
> <4>[    0.300913] TTFF isp_vic_interrupt_service_routine:907 Chn:0  Frame  Start:466
> <4>[    0.372538] TTFF isp_vic_interrupt_service_routine:907 Chn:0  Frame  Start:538
> <4>[    0.443436] TTFF isp_vic_interrupt_service_routine:907 Chn:0  Frame  Start:608
> <4>[    0.503430] TTFF isp_vic_interrupt_service_routine:907 Chn:0  Frame  Start:669
> <4>[    0.571034] TTFF isp_vic_interrupt_service_routine:907 Chn:0  Frame  Start:736
> <4>[    0.600040] TTFF frame_channel_buffer_done:293 Chn:0 Buf:1 Write Done:765
> <4>[    0.637526] TTFF isp_vic_interrupt_service_routine:907 Chn:0  Frame  Start:803
> <4>[    0.700799] TTFF isp_vic_interrupt_service_routine:907 Chn:0  Frame  Start:866
> <4>[    0.773049] TTFF isp_vic_interrupt_service_routine:907 Chn:0  Frame  Start:938
> <4>[    0.834639] TTFF isp_vic_interrupt_service_routine:907 Chn:0  Frame  Start:1000
> <4>[    0.900963] TTFF isp_vic_interrupt_service_routine:907 Chn:0  Frame  Start:1066
> <6>[    1.024433] console [ttyS1] enabled
> <6>[    1.029666] brd: module loaded
> <6>[    1.033581] loop: module loaded
> <6>[    1.037813] zram: Created 2 device(s) ...
> <6>[    1.042099] logger: created 256K log 'log_main'
> <4>[    1.063771] jz SADC driver registeres over!
> <4>[    1.071728] jz TCU driver register completed
> <6>[    1.076653] i2c /dev entries driver
> <4>[    1.081495] pipe_request_dma: paddr = 0xb80000
> <6>[    1.086246] dma dma0chan24: Channel 24 have been requested.(phy id 7,type 0x06 desc a2072000)
> <4>[    1.095529] pipe_request_dma: paddr = 0x2080000
> <6>[    1.100291] dma dma0chan25: Channel 25 have been requested.(phy id 6,type 0x06 desc a2074000)
> <4>[    1.109720] pipe_request_dma: paddr = 0x20c0000
> <6>[    1.114575] dma dma0chan26: Channel 26 have been requested.(phy id 5,type 0x04 desc a2075000)
> <7>[    1.123818] cgu_set_rate, parent = 900000000, rate = 2048000, n = 28125, reg val = 0x04006ddd
> <7>[    1.123853] cgu_enable,cgu_i2s_spk reg val = 0x24006ddd
> <7>[    1.123935] cgu_set_rate, parent = 900000000, rate = 2048000, n = 28125, reg val = 0x04006ddd
> <7>[    1.123959] cgu_enable,cgu_i2s_mic reg val = 0x24006ddd
> <4>[    1.143672] @@@@@ inner codec power up@@@@@@
> <4>[    1.148187] @@@@ audio driver ok(version H20230614a) @@@@@
> <6>[    1.154227] TCP: cubic registered
> <6>[    1.158259] NET: Registered protocol family 10
> <6>[    1.163941] NET: Registered protocol family 17
> <4>[    1.169319] soc_vpu probe success,version:1.0.0-03203fd46d
> <6>[    1.177683] Freeing unused kernel memory: 200K (8040e000 - 80440000)
> <4>[    1.283676] codec_fast_start is finish
> <4>[   50.997696] TTFF frame_channel_buffer_done:293 Chn:1 Buf:0 Write Done:6423
> <4>[   50.997742] TTFF frame_channel_buffer_done:293 Chn:0 Buf:0 Write Done:6424
> <4>[   51.083742] TTFF frame_channel_buffer_done:293 Chn:0 Buf:1 Write Done:6509
> <4>[   51.083774] TTFF frame_channel_buffer_done:293 Chn:1 Buf:1 Write Done:6510
> <4>[   51.131024] TTFF frame_channel_buffer_done:293 Chn:1 Buf:0 Write Done:6557
> <4>[   51.131067] TTFF frame_channel_buffer_done:293 Chn:0 Buf:0 Write Done:6557
> <4>[   51.157475] jz_sfc Build : Dec 30 2024 15:14:27
> <4>[   51.164564] the id code = ef4017, the flash name is W25Q64
> <6>[   51.164592] jz-sfc jz-sfc: sfc use DMA mode
> <6>[   51.173870] jz-sfc jz-sfc: nor flash quad mode is set, cmd = 6b,now use quad mode!
> <6>[   51.173892] JZ SFC Controller for SFC channel 0 driver register
> <5>[   51.173928] 7 cmdlinepart partitions found on MTD device jz_sfc
> <5>[   51.173939] Creating 7 MTD partitions on "jz_sfc":
> <5>[   51.173970] 0x000000000000-0x000000040000 : "uboot"
> <5>[   51.185368] 0x000000040000-0x000000098000 : "tag"
> <5>[   51.201963] 0x000000098000-0x000000288000 : "kernel"
> <4>[   51.213745] TTFF frame_channel_buffer_done:293 Chn:0 Buf:1 Write Done:6639
> <4>[   51.213776] TTFF frame_channel_buffer_done:293 Chn:1 Buf:1 Write Done:6639
> <5>[   51.218052] 0x000000288000-0x000000510000 : "rootfs"
> <5>[   51.240251] 0x000000510000-0x0000007a0000 : "app"
> <5>[   51.257565] 0x0000007a0000-0x000000800000 : "config"
> <4>[   51.264362] TTFF frame_channel_buffer_done:293 Chn:1 Buf:0 Write Done:6690
> <5>[   51.282571] 0x000000000000-0x000000800000 : "all"
> <4>[   51.298351] SPI NOR MTD LOAD OK
> <6>[   51.316467] jffs2: version 2.2. (NAND) (SUMMARY)  © 2001-2006 Red Hat, Inc.
> <6>[   51.333973] squashfs: version 4.0 (2009/01/31) Phillip Lougher
> <4>[   51.374208] cgu_msc0 can not set to 24000000, will change!!!
> <6>[   51.377522] jzmmc_v1.2 jzmmc_v1.2.0: register success!
> <4>[   51.377612] cgu_msc1 can not set to 24000000, will change!!!
> <6>[   51.380956] jzmmc_v1.2 jzmmc_v1.2.1: register success!
> <4>[   51.421099] gvffs:Oct 11 2023:10:32:49 initialized
> <6>[   51.447935] cfg80211: Calling CRDA to update world regulatory domain
> <4>[   51.498489]
> <4>[   51.498489] DRIVER VER: 2022-0923-0945.
> <3>[   51.516315] [atbm_log]:atbm_init_firmware
> <6>[   51.540405] zram0: detected capacity change from 0 to 16777216
> <4>[   51.543781] cgu_msc1 can not set to 24000000, will change!!!
> <4>[   51.564749] cgu_msc1 can not set to 24000000, will change!!!
> <4>[   51.567258] cgu_msc1 can not set to 24000000, will change!!!
> <6>[   51.572883] Adding 16380k swap on /dev/zram0.  Priority:-1 extents:1 across:16380k SS
> <4>[   51.575759] cgu_msc1 can not set to 24000000, will change!!!
> <4>[   51.586030] wait stable.[288][cgu_msc1]
> <6>[   51.587662] mmc1: new SDIO card at address 0001
> <3>[   51.608551] [atbm_log]:Probe called
> <3>[   51.608657] [atbm_log]:atbm_sdio_probe:v12
> <3>[   51.631074] [atbm_log]:atbm_after_load_firmware++
> <3>[   51.642286] [atbm_log]:atbm_sdio_rx_thread
> <3>[   51.644647] [atbm_log]:set_block_size=256
> <3>[   51.647373] [atbm_log]:mdelay wait wsm_startup_done  !!
> <3>[   51.654301] [atbm_log]:firmwareCap 5
> <3>[   51.654322] [atbm_log]:firmwareCap2 0
> <3>[   51.654331] [atbm_log]:wsm_caps.firmwareCap 5
> <3>[   51.654355] [atbm_log]:apollo wifi WSM init done.
> <3>[   51.654355]    Input buffers: 24 x 1632 bytes
> <3>[   51.654355]    Hardware: 8.25665
> <3>[   51.654355]    WSM firmware [=IOT+SDIO=RF=ATHENA_BX  2GHZ Dec 15 2023 14:06:59], ver: 15043, build: 0, api: 15043, cap: 0x0005 Config[30004]  expection 90094c4, tx prog 24
> <4>[   51.683738] [atbm_log]:rx timeout
> <3>[   51.693791] [atbm_log]:atbm_sdio_tx_thread
> <3>[   51.693845] [atbm_log]:atbm_sdio_tx_bh:cmd free(23),used(1)
> <3>[   51.694731] [atbm_log]:atbm_get_mac_address.f4:94:e:d3:66
> <4>[   51.716971] gv-gpio initialized
> <3>[   51.741992] [atbm_log]:atbm_sdio_tx_bh:cmd free(22),used(1)
> <3>[   51.742319] [atbm_log]:wifi mode:STA
> <3>[   51.742337] [atbm_log]:[atbm_wtd]:set wtd_probe = 1
> <3>[   51.755517] [atbm_log]:mmc1 has been attached
> <4>[   51.761642] atbm_ioctl_open cost time: 0 ms
> <3>[   51.761759] [atbm_log]:atbm_sdio_tx_bh:cmd free(21),used(1)
> <3>[   51.762279] [atbm_log]:atbm_sdio_tx_bh:cmd free(20),used(1)
> <4>[   51.882719] ===sfc_ioctl [1186]===>sr1 = 0 || sr2 = 2 || sr3 = 60
> <4>[   52.097249] gv_gpio init :gpio7:1
> <4>[   52.100048] gv_gpio init :gpio50:1
> <4>[   52.103182] gv_gpio init :gpio49:1
> <4>[   52.103313] gv_gpio init :gpio16:1
> <4>[   52.103330] gv_gpio init :gpio14:1
> <3>[   52.124786] [atbm_log]:atbm_sdio_tx_bh:cmd free(19),used(1)

```

**ps:**

```
> PID   USER     TIME  COMMAND
>     1 root      0:01 /bin/sh
>     2 root      0:00 [kthreadd]
>     3 root      0:00 [ksoftirqd/0]
>     4 root      0:00 [kworker/0:0]
>     5 root      0:00 [kworker/0:0H]
>     6 root      0:00 [kworker/u2:0]
>     7 root      0:00 [rcu_preempt]
>     8 root      0:00 [rcu_bh]
>     9 root      0:00 [rcu_sched]
>    10 root      0:00 [watchdog/0]
>    11 root      0:00 [khelper]
>    12 root      0:00 [writeback]
>    13 root      0:00 [bioset]
>    14 root      0:00 [kblockd]
>    15 root      0:00 [isp_limit]
>    16 root      0:00 [irq/37-isp-m0]
>    17 root      0:00 [irq/38-isp-w02]
>    18 root      0:00 [irq/46-isp-ivdc]
>    19 root      0:00 [kworker/u2:1]
>    22 root      0:03 [isp_fw_process]
>    23 root      0:00 [kworker/0:1]
>    24 root      0:00 [kswapd0]
>    25 root      0:00 [fsnotify_mark]
>    26 root      0:00 [crypto]
>    40 root      0:00 [kworker/0:2]
>    41 root      0:00 [deferwq]
>    43 root      0:00 -sh
>    49 root      0:00 busybox init
>    54 root      0:00 daemon @ipc
>    57 root      0:50 ipc
>   146 root      0:00 [cfg80211]
>   149 root      0:00 atbm_iot_supplicant
>   152 root      0:00 [jffs2_gcd_mtd5]
>   160 root      0:00 [kworker/0:1H]
>   165 root      0:00 [kworker/u2:2]
>   170 root      0:00 -sh
>   171 root      0:00 [atbm_wq]
>   172 root      0:00 [sdio_tx]
>   173 root      0:00 [sdio_rx]
>   174 root      0:00 [ksdioirqd/mmc1]
>   176 root      0:00 [kworker/u2:3]
>   250 root      0:00 ps

```

**mount -t debugfs none /sys/kernel/debug; cat /sys/kernel/debug/gpio:**

```
> GPIOs 0-31, GPIO A:
>  gpio-7   (p▒▒tL▒▒v            ) out lo
>  gpio-14  (IR_P                ) out lo
>  gpio-16  (IR_N                ) out lo
>  gpio-17  (mmc_detect          ) in  hi
> 
> GPIOs 32-63, GPIO B:
>  gpio-49  (p▒▒tL▒▒v            ) out lo
>  gpio-50  (p▒▒tL▒▒v            ) out hi
>  gpio-52  (                    ) out lo
>  gpio-53  (                    ) out lo
>  gpio-57  (sda                 ) in  hi
>  gpio-58  (scl                 ) in  hi
>  gpio-59  (                    ) out lo
>  gpio-60  (                    ) out lo
>  gpio-61  (                    ) out lo
>  gpio-62  (                    ) out lo
>  gpio-63  (                    ) out lo
> 
> GPIOs 64-95, GPIO C:
>  gpio-64  (                    ) out lo
```


**/etc/init.d/rcS:**

```
> #!/bin/sh
> 
> #bootup_timer start >> /tmp/bootup_time
> 
> # Set Global Environment
> export PATH=/app/bin:/bin:/sbin:/usr/bin:/usr/sbin:/usr/jzbin
> export LD_LIBRARY_PATH=/app/lib:/usr/lib
> 
> echo 0 > /proc/sys/kernel/printk
> 
> bootup_timer prog_run >> /tmp/bootup_time
> 
> ulimit -s 256
> 
> daemon @ipc
> 
> if [ -e /usr/jzbin/batcam_4G_bridge ]; then
>     batcam_4G_bridge &
> fi
> 
> hostname gv
> 
> # Set mdev
> echo /sbin/mdev > /proc/sys/kernel/hotplug
> /sbin/mdev -s #&& echo "mdev is ok......"
> 
> # networking
> ifconfig lo up &
> 
> # mount jffs2 rw partition
> insmod_sfc
> insmod_mmc
> 
> if [ -e /usr/jzbin/atbm_iot_supplicant ]; then
>     insmod /lib/modules/cfg80211.ko
> 
>     bootup_timer atbm >> /tmp/bootup_time
>     atbm_iot_supplicant&
> fi
> 
> mount -t jffs2 -o sync /dev/mtdblock5 /conf
> touch /tmp/conf_mounted
> 
> bootup_timer atbm >> /tmp/bootup_time
> 
> mount -t squashfs /dev/mtdblock4 /app
> if [ $? -ne 0 ]; then
>     echo "mount app failed"
>     recovery
> fi
> 
> echo 100 > /proc/sys/vm/swappiness
> echo 16777216 > /sys/block/zram0/disksize
> mkswap /dev/zram0
> swapon /dev/zram0
> 
> echo 1 > /proc/sys/vm/overcommit_memory
> echo 5 > /proc/sys/vm/dirty_background_ratio
> echo 10 >  /proc/sys/vm/dirty_ratio
> echo 50 > /proc/sys/vm/dirty_writeback_centisecs
> echo 100 > /proc/sys/vm/dirty_expire_centisecs
> echo 10000 > /proc/sys/vm/vfs_cache_pressure
> echo 1 > /proc/sys/kernel/panic_on_oops
> 
> 
> if [ -f /app/init/app_init.sh ]; then
>     /app/init/app_init.sh &
> fi
> 
> bootup_timer all_done >> /tmp/bootup_time
```

**/etc/inittab:**

```
> # /etc/inittab
> #
> # Copyright (C) 2001 Erik Andersen <andersen@codepoet.org>
> #
> # Note: BusyBox init doesn't support runlevels.  The runlevels field is
> # completely ignored by BusyBox init. If you want runlevels, use
> # sysvinit.
> #
> # Format for each entry: <id>:<runlevels>:<action>:<process>
> #
> # id        == tty to run on, or empty for /dev/console
> # runlevels == ignored
> # action    == one of sysinit, respawn, askfirst, wait, and once
> # process   == program to run
> 
> # Startup the system
> #::sysinit:/sbin/swapoff -a
> #::sysinit:/bin/mount -t tmpfs tmpfs /dev
> #::sysinit:/bin/mkdir -p /dev/pts
> #::sysinit:/bin/mkdir -p /dev/shm
> ::sysinit:/bin/mount -a
> #::sysinit:/bin/hostname -F /etc/hostname
> 
> # now run any rc scripts
> ::sysinit:/etc/init.d/rcS
> 
> # Put a getty on the serial port
> console::respawn:/sbin/getty -L console 115200 vt100 # GENERIC_SERIAL
> 
> # Stuff to do for the 3-finger salute
> #::ctrlaltdel:/sbin/reboot
> 
> # Stuff to do before rebooting
> ::shutdown:/bin/umount -a -r
```

**/etc/sync.ini:**

```
> [SENSOR]
> NAME=sc3336
> I2C=48
> WIDTH=2304
> HEIGHT=1296
> WIDTH_SUB=640
> HEIGHT_SUB=360
> [PARENT]
> FRAME=4
> JPEG=2
```
