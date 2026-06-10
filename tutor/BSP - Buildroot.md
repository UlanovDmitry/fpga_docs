# BSP в Buildroot (Zynq-7010)

Практическая инструкция по формированию BSP под **EBAZ4205**, **Antminer S9** и аналоги на XC7Z010. См. также [ZYNQ7000 — туториал](ZYNQ7000%20-%20туториал.md), [SoC — термины](SoC%20-%20термины.md).

**Эталонный репозиторий:** [blkf2016/ebaz4205](https://github.com/blkf2016/ebaz4205) · [embed-me/ebaz4205_buildroot](https://github.com/embed-me/ebaz4205_buildroot)

---

## Что такое BSP в Buildroot

У Buildroot **нет** файла `.bsp` как у Xilinx. BSP — это:

1. **`configs/<плата>_defconfig`** — манифест сборки (ядро, U-Boot, пакеты, скрипты).
2. **`board/<плата>/`** — платформенные файлы (DTS, конфиги, overlay, genimage).
3. **Вне Buildroot:** FSBL, `BOOT.BIN`, Vivado TCL/XDC (Buildroot их не собирает).

Связь с Vivado **ручная**: XSA не импортируется автоматически.

---

## Предварительные требования

| Компонент | Версия (пример blkf2016) |
|-----------|--------------------------|
| Хост ОС | Linux (Ubuntu 18.04/20.04) или WSL2 |
| Buildroot | 2019.05 (или версия из репо) |
| Vivado | 2019.1 (для FSBL/bitstream, опционально) |
| Инструменты | `git`, `make`, `gcc`, `bc`, `flex`, `bison` |

**Аппаратура:** SD-карта, UART 115200, boot mode = SD.

---

## Структура BSP (оверлей)

```
buildroot/                          # или BR2_EXTERNAL
├── configs/
│   └── zynq-ebaz4205_defconfig     # главный конфиг
└── board/ebaz4205/
    ├── zynq-ebaz4205.dts           # Device Tree для ядра
    ├── linux.config                # CONFIG_* ядра
    ├── uboot.config                # конфиг U-Boot
    ├── uboot.patch                 # DTS в дерево U-Boot
    ├── uboot-boot-script.txt       # → boot.scr
    ├── rootfs_overlay/             # /etc/network, dnsmasq…
    ├── genimage.cfg                # разметка SD
    ├── post-build.sh
    ├── post-image.sh
    └── boot.bin                    # BOOT.BIN (вне make, копируется в post-build)
```

Корень репозитория blkf2016 дополнительно содержит:

- `main.c`, `uboot_loader.c` — модифицированный FSBL
- `ebaz4205.tcl`, `ebaz4205.xdc` — Vivado

---

## Шаг 1. Аппаратура (Vivado)

1. Проект `xc7z010clg400-1`, Block Design PS7: DDR, UART1, SDIO0, GEM0 (MII/RGMII по схеме).
2. Для **PS-only без PL**: только `processing_system7_0`, без пользовательского RTL.
3. Export Hardware → XSA (для справки; в Buildroot не импортируется).
4. Собрать FSBL + `BOOT.BIN` (Vitis/SDK или кастом FSBL blkf2016).
5. Положить `boot.bin` в `board/ebaz4205/boot.bin`.

---

## Шаг 2. Device Tree

Создайте `board/<плата>/<плата>.dts` по схеме платы:

- `&gem0` — Ethernet (MII для EBAZ4205)
- `&sdhci0` — SD
- `&uart1` — консоль
- `gpio-keys`, `gpio-leds` — кнопки/LED (опционально)
- `&nfc0` — NAND (часто `status = "disabled"` при boot с SD)

Ориентиры: DTS из [Elrori/EBAZ4205](https://github.com/Elrori/EBAZ4205), [polprog/antminer_zynq](https://github.com/polprog/antminer_zynq) (S9).

---

## Шаг 3. defconfig

Скопируйте и адаптируйте `zynq-ebaz4205_defconfig`. Ключевые опции:

```makefile
BR2_arm=y
BR2_cortex_a9=y
BR2_LINUX_KERNEL_CUSTOM_VERSION_VALUE="5.1.5"
BR2_LINUX_KERNEL_CUSTOM_CONFIG_FILE="board/ebaz4205/linux.config"
BR2_LINUX_KERNEL_CUSTOM_DTS_PATH="board/ebaz4205/zynq-ebaz4205.dts"
BR2_TARGET_UBOOT_CUSTOM_CONFIG_FILE="board/ebaz4205/uboot.config"
BR2_TARGET_UBOOT_PATCH="board/ebaz4205/uboot.patch"
BR2_TARGET_UBOOT_BOOT_SCRIPT_SOURCE="board/ebaz4205/uboot-boot-script.txt"
BR2_ROOTFS_OVERLAY="board/ebaz4205/rootfs_overlay"
BR2_ROOTFS_POST_BUILD_SCRIPT="board/ebaz4205/post-build.sh"
BR2_ROOTFS_POST_IMAGE_SCRIPT="board/ebaz4205/post-image.sh"
BR2_TARGET_ROOTFS_EXT2_SIZE="80M"
BR2_PACKAGE_HOST_GENIMAGE=y
```

Пакеты (openssh, bash, dnsmasq…) добавляйте в defconfig или через `make menuconfig`.

---

## Шаг 4. U-Boot boot script

Пример `uboot-boot-script.txt` (blkf2016 — ядро на ext4, раздел 2):

```
ext4load mmc 0:2 0x2000000 /boot/zImage
ext4load mmc 0:2 0x1f00000 /boot/zynq-ebaz4205.dtb
setenv bootargs "root=/dev/mmcblk0p2 rootwait"
bootz 0x2000000 - 0x1f00000
```

Альтернатива: ядро и DTB на FAT (раздел 1) — измените `fatload` и пути.

---

## Шаг 5. rootfs overlay

```
board/ebaz4205/rootfs_overlay/
└── etc/
    ├── network/interfaces      # статический IP
    └── dnsmasq.conf
```

`post-build.sh` — ssh-ключи, копирование `boot.bin` в `output/images/`.

---

## Шаг 6. genimage (разметка SD)

Пример `genimage.cfg`:

```
image boot.vfat {
  vfat {
    files = { "boot.bin", "u-boot.bin", "boot.scr" }
  }
  size = 8M
}

image sdcard.img {
  hdimage { align = 1M }
  partition boot {
    partition-type = 0xC
    bootable = "true"
    image = "boot.vfat"
  }
  partition rootfs {
    partition-type = 0x83
    image = "rootfs.ext4"
    size = 256M
  }
}
```

---

## Шаг 7. Сборка

### Вариант A: оверлей внутри дерева Buildroot

```bash
wget https://buildroot.org/downloads/buildroot-2019.05.tar.gz
tar xf buildroot-2019.05.tar.gz
cp -r ebaz4205/buildroot/* buildroot-2019.05/
cd buildroot-2019.05
make zynq-ebaz4205_defconfig
make
```

### Вариант B: BR2_EXTERNAL

```bash
make BR2_EXTERNAL=/path/to/ebaz4205/buildroot zynq-ebaz4205_defconfig
make
```

Сборка занимает 30–90+ минут (первая).

---

## Шаг 8. Результат

| Артефакт | Путь |
|----------|------|
| `zImage`, `.dtb` | `output/images/` |
| `u-boot.bin`, `boot.scr` | `output/images/` |
| `rootfs.ext4` | `output/images/` |
| `boot.bin` | `output/images/` (из post-build) |
| **`sdcard.img`** | `output/images/` |

Запись: `dd if=sdcard.img of=/dev/sdX bs=4M status=progress` или Win32 Disk Imager.

---

## Цепочка загрузки (blkf2016)

```
Boot ROM → BOOT.BIN (FSBL) → u-boot.bin с FAT
         → boot.scr → zImage + dtb с ext4 → Linux root=/dev/mmcblk0p2
```

---

## Чек-лист

- [ ] DTS соответствует схеме (Ethernet PHY, SD, UART MIO)
- [ ] `linux.config` включает драйверы GEM, SDHCI, GPIO
- [ ] U-Boot patch добавляет board DTS
- [ ] `boot.scr` адреса RAM не конфликтуют (256 MB на EBAZ)
- [ ] `boot.bin` в `board/` и копируется в post-build
- [ ] Boot mode платы = SD
- [ ] UART: FSBL → U-Boot → Linux

---

## Типичные проблемы

| Симптом | Проверить |
|---------|-----------|
| Тишина на UART | boot mode, `BOOT.BIN`, питание |
| U-Boot есть, ядро нет | `boot.scr`, пути ext4load, разметка SD |
| Ethernet не работает | DTS `phy-mode`, MII vs RGMII |
| Сборка падает на U-Boot | версия/commit, patch |

---

## См. также

- [BSP - PetaLinux](BSP%20-%20PetaLinux.md) · [BSP - PYNQ](BSP%20-%20PYNQ.md) · [BSP - Yocto](BSP%20-%20Yocto.md)
- [Практическая карта](Практическая%20карта.md) — `blkf2016` для EBAZ4205
