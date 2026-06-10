# BSP в PetaLinux (Zynq-7010)

Практическая инструкция по формированию BSP под **EBAZ4205**, **Antminer S9** и аналоги на XC7Z010.

**Эталонные репозитории:** [wavelet2/EBAZ4205](https://github.com/wavelet2/EBAZ4205) · [KeitetsuWorks/EBAZ4205](https://github.com/KeitetsuWorks/EBAZ4205) · [har-in-air/EBAZ4205_PETALINUX](https://github.com/har-in-air/EBAZ4205_PETALINUX) (walkthrough)

---

## Что такое BSP в PetaLinux

BSP — **PetaLinux-проект** с импортированным аппаратным описанием (XSA) и правками в `project-spec/meta-user/`:

| Компонент | Где |
|-----------|-----|
| Аппаратура (из Vivado) | `project-spec/hw-description/system.xsa` |
| Конфигурация | `project-spec/configs/config`, `rootfs_config` |
| Правки под плату | `project-spec/meta-user/` |
| Артефакты сборки | `images/linux/` |

Готовый Xilinx `.bsp` для EBAZ/S9 **нет** — создаёте сами или клонируете чужой проект.

**Важно:** версия PetaLinux = версии Vivado (2021.2 + 2021.2, 2022.2 + 2022.2).

---

## Предварительные требования

| Компонент | Пример |
|-----------|--------|
| ОС хоста | Ubuntu 20.04/22.04 (64-bit) |
| Vivado | 2022.2 (или ветка репо) |
| PetaLinux | та же версия |
| Диск | 100+ GB свободно |
| Память | 16+ GB RAM |

---

## Структура BSP (готовый проект в git)

Пример [wavelet2/EBAZ4205](https://github.com/wavelet2/EBAZ4205) (ветка `2023.1`):

```
EBAZ4205/
├── vivado/
│   ├── ebaz4205.tcl
│   └── export/...
└── linux/ebaz4205/                    # PetaLinux-проект
    ├── project-spec/
    │   ├── configs/
    │   │   ├── config
    │   │   └── rootfs_config
    │   ├── hw-description/
    │   │   ├── system.xsa
    │   │   ├── ebaz4205_wrapper.bit
    │   │   └── ps7_init*.c
    │   └── meta-user/
    │       ├── recipes-bsp/device-tree/files/
    │       │   ├── ebaz4205.dtsi
    │       │   └── system-user.dtsi
    │       ├── recipes-bsp/u-boot/files/
    │       │   ├── platform-top.h
    │       │   └── bsp.cfg
    │       └── recipes-kernel/linux/
    │           └── linux-xlnx_%.bbappend
    ├── make_BOOT.BIN.sh
    └── images/linux/                  # после petalinux-build
```

---

## Шаг 1. Vivado — аппаратура

1. `xc7z010clg400-1`, Block Design PS7.
2. Настроить по схеме: DDR, UART1, SDIO0, GEM0, NAND (опционально).
3. EMIO для Ethernet/GPIO — если сигналы идут через PL.
4. **Generate Bitstream** → **Export Hardware** (Include bitstream) → `*.xsa`.

Для **PS-only**: BD без PL-IP; bitstream может быть минимальным.

---

## Шаг 2. Создать или открыть PetaLinux-проект

### Новый проект

```bash
source /path/to/petalinux/settings.sh
petalinux-create -t project -n ebaz4205-plnx --enable-binary-components
cd ebaz4205-plnx
petalinux-config --get-hw-description=/path/to/design_wrapper.xsa
```

### Готовый из репо

```bash
cd wavelet2/EBAZ4205/linux/ebaz4205
# опционально обновить XSA:
petalinux-config --get-hw-description=../../vivado/ebaz4205
```

---

## Шаг 3. Настроить BSP (`petalinux-config`)

Интерактивное меню. Для SD-boot (har-in-air / типичный flow):

| Раздел | Настройка |
|--------|-----------|
| **Image Packaging Configuration** | Root filesystem: **ext4 (sd)**; не initrd |
| **DTG settings** | Bootargs: `console=ttyPS0,115200 root=/dev/mmcblk0p2 rw rootwait` |
| **Yocto settings** | sstate-cache, premirror (ускорение сборки) |

Сохранить и выйти.

---

## Шаг 4. Правки в `meta-user/` (обязательно для кастомной платы)

### Device Tree — `recipes-bsp/device-tree/files/system-user.dtsi`

Дополнения к автогенерированному DTS:

```dts
/ {
    chosen {
        bootargs = "earlycon console=ttyPS0,115200 root=/dev/mmcblk0p2 rw rootwait";
    };
};

&gem0 {
    phy-mode = "mii";   /* EBAZ4205 */
    /* phy-handle, local-mac-address */
};
```

Отдельный `ebaz4205.dtsi` — LED, кнопки S2/S3 (см. wavelet2).

**Баг wavelet2:** в `system-conf.dtsi` заменить `ps7_nand_0` → `nfc0` после `petalinux-build -c device-tree -x do_configure`.

### U-Boot — `recipes-bsp/u-boot/`

- `platform-top.h` — консоль, bootcmd
- `bsp.cfg` — фрагменты Kconfig
- `u-boot-xlnx_%.bbappend` — подключение файлов

```bash
petalinux-config -c u-boot   # Boot media: SD Card
```

### Ядро — `recipes-kernel/linux/linux-xlnx_%.bbappend`

Фрагменты `.cfg` или patch для драйверов.

### Rootfs

```bash
petalinux-config -c rootfs
# или правка project-spec/configs/rootfs_config
```

Пакеты: openssh, e2fsprogs, iproute2, gpio-demo (har-in-air).

---

## Шаг 5. Сборка

```bash
petalinux-build
```

Первая сборка — несколько часов.

---

## Шаг 6. Упаковка boot-образов

```bash
cd images/linux
petalinux-package --boot \
  --fsbl zynq_fsbl.elf \
  --fpga system.bit \
  --u-boot u-boot.elf \
  -o BOOT.BIN --force
```

**PS-only без PL:** можно опустить `--fpga` (если bitstream не нужен).

Проверка:

```bash
bootgen -arch zynq -read BOOT.BIN
```

---

## Шаг 7. SD-карта

Разметка (типичная):

| Раздел | Тип | Содержимое |
|--------|-----|------------|
| 1 | FAT32 | `BOOT.BIN`, `boot.scr`, `image.ub` |
| 2 | ext4 | `rootfs.ext4` (`dd if=rootfs.ext4 of=/dev/sdX2`) |

```bash
sudo fdisk /dev/sdX
sudo mkfs.msdos -n BOOT /dev/sdX1
sudo mkfs.ext4 -L rootfs /dev/sdX2
cp BOOT.BIN boot.scr image.ub /media/BOOT/
sudo dd if=rootfs.ext4 of=/dev/sdX2
sudo resize2fs /dev/sdX2
sync
```

---

## Шаг 8. Отладка

```bash
# JTAG (без SD)
petalinux-boot --jtag --kernel

# UART 115200 — FSBL → U-Boot → Linux
```

---

## Экспорт BSP для повторного использования

```bash
petalinux-package --bsp -p ./ebaz4205-plnx \
  --hwsource ./vivado \
  --output ebaz4205.bsp
```

Импорт:

```bash
petalinux-create -t project -s ebaz4205.bsp
```

---

## Артефакты после сборки

| Файл | Назначение |
|------|------------|
| `zynq_fsbl.elf` | FSBL |
| `u-boot.elf` / `u-boot.bin` | Загрузчик |
| `system.bit` | Bitstream PL |
| `system.dtb` | Device Tree |
| `image.ub` | FIT: kernel + dtb (+ ramdisk) |
| `boot.scr` | Автозагрузка U-Boot |
| `rootfs.ext4` | Корневая ФС |
| **`BOOT.BIN`** | Образ для Boot ROM |

---

## Чек-лист

- [ ] Vivado и PetaLinux одной версии
- [ ] XSA импортирован без ошибок
- [ ] `system-user.dtsi` / `ebaz4205.dtsi` описывают реальное железо
- [ ] Rootfs type = ext4, не initrd (для постоянной SD)
- [ ] `petalinux-build` успешен
- [ ] `BOOT.BIN` собран
- [ ] SD размечена, boot mode = SD
- [ ] Login / ssh работает

---

## Репозитории по ролям

| Репозиторий | Что даёт |
|-------------|----------|
| **wavelet2** | Полный проект в git, `meta-user`, how-to-build |
| **KeitetsuWorks** | Vivado 2021.2 + PetaLinux, gpio-demo |
| **har-in-air** | Подробный README, аппаратные доработки платы; проект создаёте сами |

---

## См. также

- [BSP - Buildroot](BSP%20-%20Buildroot.md) · [BSP - PYNQ](BSP%20-%20PYNQ.md) · [BSP - Yocto](BSP%20-%20Yocto.md)
- [UG1165 — Embedded Design Tutorial](https://docs.amd.com/r/en-US/ug1165-zynq-embedded-design-tutorial)
- [EBAZ4205 — ресурсы](../links/EBAZ4205%20-%20ресурсы.md)
