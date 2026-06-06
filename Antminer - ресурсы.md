
**Antminer S9**
```sh
ZYNQ XC7Z010
https://mysku.club/blog/aliexpress/84832.html
https://github.com/KarolNi/S9miner_sample
https://github.com/polprog/antminer_zynq?tab=readme-ov-file
```

Ремонт платы
https://www.youtube.com/playlist?list=PL-ko_B9tXrAVHm8mZv9w0FTXPxuhR_y5k

Видео на русском
https://www.youtube.com/playlist?list=PL2tpftA-kds7aUjCE4GzCe0QNaV-ISePy

Другой майнер
https://www.youtube.com/watch?v=OpaxO7RriJ8

**Bare-metal / boot chain (trebisky)**

Серия заметок с полным разбором SD boot, FSBL, U-Boot, JTAG:

| Статья | URL | Содержание |
|--------|-----|------------|
| Индекс Antminer | http://cholla.mmto.org/zynq/antminer/ | Оглавление всей серии |
| SD card boot | http://cholla.mmto.org/zynq/antminer/sdcard.html | Джамперы JP1–JP4, что искать на SD, брак eBay-плат |
| SD FSBL bare-metal | http://cholla.mmto.org/zynq/antminer/sd_fsbl.html | Устройство BOOT.BIN, bootrom header |
| U-Boot rebuild | http://cholla.mmto.org/zynq/antminer/uboot7/rebuild.html | SPL, multi-dtb-fit, mkimage |
| U-Boot plan B | http://cholla.mmto.org/zynq/antminer/uboot7/planb2.html | `bitmain_antminer_s9_defconfig`, `u-boot.img` на FAT |

**PYNQ**

| Ресурс | Что даёт |
|--------|----------|
| [kangyuzhe666/zynq7010-pynq-2.5](https://github.com/kangyuzhe666/zynq7010-pynq-2.5) | Полный PYNQ 2.5: Vivado minimal BD → sdbuild → SD; нюанс **1 GB vs 512 MB DDR** |
| [Блог CN: S9 + PYNQ](https://www.cnblogs.com/tianpanyu/p/18773954) | Питание, джамперы, Rufus, IP 192.168.2.99, `uEnv.txt` + auto-load bitstream |
| [guannan-he/Antminer_s9_pynq](https://github.com/guannan-he/Antminer_s9_pynq) | PYNQ overlays для `s9_pynq` |
| [opensatellite/antminer_t9](https://github.com/opensatellite/antminer_t9) | PYNQ на T9 (тот же Zynq-7010, 1 GbE) |

**Linux / PetaLinux / SDR**

| Ресурс | Что даёт |
|--------|----------|
| [Habr: SDR на Zynq (S9)](https://habr.com/ru/articles/898490/) | Vivado 2022.2 + PetaLinux end-to-end на S9 |
| [iliasam/OpenZynqSDR_HW](https://github.com/iliasam/OpenZynqSDR_HW) | Vivado, схема S9, плата ADC |
| [iliasam/OpenZynqSDRApp](https://github.com/iliasam/OpenZynqSDRApp) | ПО к OpenZynqSDR |

**Dev-плата / документация**

| Ресурс | Что даёт |
|--------|----------|
| [eSnyne / Hackaday.io](https://hackaday.io/project/193160-esnyne-a-powerful-fpgasoc-developpement-board) | Таблица boot jumpers, schematic PDF, что купить (UART, JTAG) |
| [MelodyCoin/eSnyne](https://github.com/MelodyCoin/eSnyne) | Концепт dev-платы, сравнение с PYNQ-Z1 |
| [D-Central: SD recovery](https://d-central.tech/sd-card-firmware-recovery-guide/) | Восстановление штатной прошивки майнера (не dev, но полезно для понимания SD boot) |

**Мета-ссылки**

- [trebisky: заметки по Antminer S9](http://cholla.mmto.org/zynq/antminer/)
- [trebisky: заметки (зеркало)](http://kofa.mmto.arizona.edu/zynq/antminer/)
- [U-Boot DTS bitmain-antminer-s9](https://github.com/Xilinx/u-boot-xlnx/blob/master/arch/arm/dts/bitmain-antminer-s9.dts) — см. [Linux — ресурсы](Linux%20-%20ресурсы.md)
