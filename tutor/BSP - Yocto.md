# BSP в Yocto / OpenEmbedded (Zynq-7010)

Практическая инструкция по формированию BSP под **EBAZ4205**, **Antminer S9** и аналоги на XC7Z010 через **meta-xilinx**.

PetaLinux внутри основан на Yocto; прямой Yocto нужен для полного контроля без обёртки Xilinx.

**Слои:** [meta-xilinx](https://github.com/Xilinx/meta-xilinx) · poky

---

## Что такое BSP в Yocto

BSP — набор **meta-слоя** + **machine config** + рецепты, описывающие плату:

| Компонент | Где |
|-----------|-----|
| Machine | `conf/machine/<плата>.conf` |
| Device Tree | `recipes-bsp/device-tree/` или `linux-yocto` |
| U-Boot | `recipes-bsp/u-boot/` |
| Kernel | `recipes-kernel/linux/` |
| Образ | `recipes-core/images/<image>.bb` |
| Аппаратура (опционально) | XSA → через `xilinx-tools` или вручную DTS |

Нет единого `.bsp`-файла Xilinx — вы собираете **meta-layer** под плату.

---

## Предварительные требования

| Компонент | Рекомендация |
|-----------|--------------|
| ОС | Ubuntu 20.04/22.04 LTS |
| Диск | 100+ GB |
| RAM | 16+ GB |
| Git | клонировать poky + meta-xilinx |

Версия **meta-xilinx** должна соответствовать версии Vivado (см. ветки/tags в репо Xilinx).

---

## Структура своего meta-слоя

Создайте `meta-ebaz4205/` (или `meta-antminer-s9/`):

```
meta-ebaz4205/
├── conf/
│   └── layer.conf
├── conf/machine/
│   └── ebaz4205-zynq7.conf
├── recipes-bsp/
│   ├── device-tree/
│   │   ├── device-tree.bbappend
│   │   └── files/
│   │       └── zynq-ebaz4205.dts
│   └── u-boot/
│       ├── u-boot-xlnx_%.bbappend
│       └── files/
│           └── bsp.cfg
├── recipes-kernel/
│   └── linux/
│       ├── linux-xlnx_%.bbappend
│       └── files/
│           └── bsp.cfg
└── recipes-core/images/
    └── ebaz4205-image.bb
```

---

## Шаг 1. Инициализация build environment

```bash
# Пример: ветка meta-xilinx под вашу версию инструментов
git clone -b <release> https://github.com/Xilinx/meta-xilinx.git
git clone -b <release> git://git.yoctoproject.org/poky
git clone <your-meta-ebaz4205>

source poky/oe-init-build-env build-ebaz4205
```

---

## Шаг 2. `bblayers.conf`

Добавить слои:

```
BBLAYERS += " \
  /path/to/meta-xilinx/meta-xilinx-core \
  /path/to/meta-xilinx/meta-xilinx-bsp \
  /path/to/meta-xilinx/meta-xilinx-tools \
  /path/to/meta-ebaz4205 \
"
```

Точный набор слоев зависит от версии meta-xilinx (см. README слоя).

---

## Шаг 3. Machine config

`conf/machine/ebaz4205-zynq7.conf` (упрощённо):

```bitbake
#@TYPE: Machine
#@NAME: EBAZ4205 Zynq-7010
#@SOC_FAMILY: "zynq"

require conf/machine/include/soc-zynq7.inc
require conf/machine/include/machine-xilinx-zynq7.inc

MACHINE_FEATURES += "usbhost vfat alsa"

SERIAL_CONSOLES = "115200;ttyPS0"

KERNEL_DEVICETREE = "zynq-ebaz4205.dtb"

UBOOT_MACHINE = "xilinx_zynq_virt_defconfig"
# или кастомный defconfig через bbappend

IMAGE_BOOT_FILES = "BOOT.BIN boot.scr uImage"
```

Для S9: другой DTS, DDR 1 GB, RGMII вместо MII — отдельный `antminer-s9-zynq7.conf`.

---

## Шаг 4. Device Tree

`recipes-bsp/device-tree/files/zynq-ebaz4205.dts` — тот же контент, что для Buildroot/PetaLinux:

- GEM, SDHCI, UART, GPIO
- Ориентир: [blkf2016](https://github.com/blkf2016/ebaz4205), [wavelet2 ebaz4205.dtsi](https://github.com/wavelet2/EBAZ4205)

`device-tree.bbappend`:

```bitbake
FILESEXTRAPATHS:prepend := "${THISDIR}/files:"
SRC_URI += "file://zynq-ebaz4205.dts"
```

---

## Шаг 5. U-Boot и kernel

`u-boot-xlnx_%.bbappend`:

```bitbake
FILESEXTRAPATHS:prepend := "${THISDIR}/files:"
SRC_URI += "file://bsp.cfg"
```

`linux-xlnx_%.bbappend` — фрагменты `bsp.cfg` с `CONFIG_*`.

Патчи U-Boot для board DTS — аналог `uboot.patch` в blkf2016.

---

## Шаг 6. Образ (image recipe)

`recipes-core/images/ebaz4205-image.bb`:

```bitbake
require recipes-core/images/core-image-minimal.bb

IMAGE_INSTALL += " \
    openssh \
    e2fsprogs \
    iproute2 \
    bash \
"
```

Или наследовать `petalinux-image-minimal` если используете meta-xilinx-tools.

---

## Шаг 7. `local.conf`

```bitbake
MACHINE = "ebaz4205-zynq7"
DL_DIR ?= "${TOPDIR}/downloads"
SSTATE_DIR ?= "${TOPDIR}/sstate-cache"

# Ускорение
BB_NUMBER_THREADS = "8"
PARALLEL_MAKE = "-j 8"
```

---

## Шаг 8. Сборка

```bash
bitbake ebaz4205-image
```

Или стандартный:

```bash
bitbake core-image-minimal
```

Первая сборка — **несколько часов**.

---

## Шаг 9. BOOT.BIN и FSBL

В Yocto/meta-xilinx FSBL и `BOOT.BIN` часто собираются через:

- `xilinx-bootbin` / `xilinx-fsbl` recipes (зависит от версии meta-xilinx)
- или **внешне** через Vivado/Vitis + `bootgen` (как в Buildroot)

Проверьте `meta-xilinx-tools` для вашей версии: `petalinux-image` vs ручная упаковка.

Артефакты:

```
tmp/deploy/images/ebaz4205-zynq7/
├── BOOT.BIN
├── boot.scr
├── image.ub          # или uImage + dtb
├── rootfs.ext4
└── *.wic             # если включён wic
```

---

## Шаг 10. SD-карта

### Вариант A: `.wic` образ

```bash
sudo dd if=core-image-minimal-ebaz4205-zynq7.wic of=/dev/sdX bs=4M status=progress
```

### Вариант B: вручную (как PetaLinux)

FAT: `BOOT.BIN`, `boot.scr`, `image.ub`  
ext4: `rootfs.ext4` через `dd`

---

## Связь с Vivado / XSA

| Подход | Описание |
|--------|----------|
| **Ручной DTS** | Как Buildroot — XSA только для справки |
| **meta-xilinx-tools** | Импорт XSA в workflow (ближе к PetaLinux) |
| **Экспорт из PetaLinux** | `petalinux-package --bsp` → адаптация слоёв |

Для кастомных майнерских плат обычно начинают с **ручного DTS** + machine.conf.

---

## Сравнение с PetaLinux

| | Yocto напрямую | PetaLinux |
|---|----------------|-----------|
| Импорт XSA | Через tools или вручную | `petalinux-config --get-hw-description` |
| Конфигурация | `.bb`, `.bbappend`, `local.conf` | `petalinux-config`, `meta-user` |
| Кривая обучения | Высокая | Средняя |
| Гибкость | Максимальная | Ограничена обёрткой |

---

## Чек-лист

- [ ] meta-xilinx версия = Vivado
- [ ] `meta-<плата>` в `bblayers.conf`
- [ ] `MACHINE` в `local.conf`
- [ ] DTS описывает реальное железо
- [ ] U-Boot и kernel bbappend подключены
- [ ] `bitbake <image>` успешен
- [ ] `BOOT.BIN` есть в `deploy/images`
- [ ] SD записана, boot mode = SD
- [ ] UART / сеть работают

---

## Репозитории и материалы

| Источник | Заметка |
|----------|---------|
| [wavelet2/EBAZ4205](https://github.com/wavelet2/EBAZ4205) | README упоминает Yocto + PetaLinux |
| [meta-xilinx](https://github.com/Xilinx/meta-xilinx) | Официальный слой |
| [Yocto Project Manual](https://docs.yoctoproject.org/) | Общая документация |
| blkf2016 DTS/configs | Переносимы в meta-layer |

Для **S9** готового Yocto-BSP в [repos.md](../repo/repos.md) нет — собирать meta-layer с нуля или портировать DTS из polprog/Xilinx.

---

## См. также

- [BSP - PetaLinux](BSP%20-%20PetaLinux.md) — обёртка Xilinx над Yocto
- [BSP - Buildroot](BSP%20-%20Buildroot.md) · [BSP - PYNQ](BSP%20-%20PYNQ.md)
- [Linux — ресурсы](../links/Linux%20-%20ресурсы.md)
