# Репозитории vs этапы туториала Zynq-7010

Сводка: что из [ZYNQ7000 — туториал](../tutor/ZYNQ7000%20-%20туториал.md) реализовано в каждом репозитории из [repos.md](repos.md).

Пустая ячейка обозначена прочерком **—**. Этап 4 для bare-metal-проектов не применяется — в ячейке указано «не нужен».

## EBAZ4205

| Репозиторий | Стек | 0 · Подготовка | 1 · Vivado | 3 · FSBL / BOOT.BIN | 4 · U-Boot + Linux | 5 · Носитель | 6 · Верификация |
|---|---|---|---|---|---|---|---|
| [Elrori/EBAZ4205](https://github.com/Elrori/EBAZ4205) | — | KiCad EBAZEXT-V2, pinout `xc7z010clg400pkg.txt`, XDC, архив SD-boot | XDC для PL/MII/LED; `ps7_init_gpl.c`; **нет** BD, bitstream, RTL | `ps7_init`, фрагменты цепочки SD-boot | DTS `zynq-ebaz4205.dts`, `uEnv.txt`; **нет** сборки kernel/U-Boot | Архивные файлы boot-сценария; **нет** готового образа | — |
| [xjtuecho/EBAZ4205](https://github.com/xjtuecho/EBAZ4205) | штатный Linux | KiCad V004, PDF-схема, wiki; инструкции UART/JTAG/SD/boot; дампы `/proc`, boot log | Шаблон XDC (пины закомментированы); **нет** BD/bitstream | — | Документация штатного u-boot/Linux/cgminer; **нет** своей сборки | — | Описание эксплуатации штатной прошивки (сеть, cgminer) |
| [blkf2016/ebaz4205](https://github.com/blkf2016/ebaz4205) | Buildroot | XDC/TCL как эталон пинов; boot mode SD описан в README | `ebaz4205.tcl` (PS7+EMIO Ethernet), XDC, bitstream через синтез | Модифицированный FSBL (`uboot_loader.c`), BIF-логика | Buildroot 2019.05: DTS, U-Boot/kernel configs, `boot.scr`, genimage, rootfs overlay (IP, dnsmasq) | Схема FAT+ext4 в genimage; **нет** готового `.img` в репо | Минимальный Linux rootfs; сеть через overlay |
| [nightseas/ebit_z7010](https://github.com/nightseas/ebit_z7010) | Linux BSP | Минимально; опирается на чужую HW-доку | Vivado BD (PS+EMIO Ethernet/GPIO), SDK-проект FSBL | FSBL, **pre-built boot images** | DTS и configs для U-Boot и kernel, env variables | **Готовые boot-образы** в репозитории | Reference design; проверка через готовые образы |
| [trebisky/ebaz4205_miner](https://github.com/trebisky/ebaz4205_miner) | bare-metal | Master XDC, заметки на сайте cholla.mmto.org | XDC; проекты blink/AXI GPIO в PL; BD в подпроектах | FSBL + app.elf в примерах SD-boot | U-Boot настроен для TFTP-boot (`setup`); **полного Linux нет** | TFTP через U-Boot; SD-boot в примерах | GPIO, кнопки, printf, таймеры, AMP (2 ядра), blink PL |
| [trebisky/Zynq](https://github.com/trebisky/Zynq) | bare-metal | Часть материалов из `ebaz4205_miner` | Перенесённые проекты (bootrom, secret и др.) | Частично в перенесённых проектах | — | — | Те же bare-metal эксперименты, что в miner |
| [Stavros/ebaz4205](https://github.com/Stavros/ebaz4205) | PYNQ | Ссылки на HW-доку; фото платы | Pre-built Vivado 2019.1 (PS+PL) | Внутри готового образа | PetaLinux 2019.1 + PYNQ; U-Boot/kernel/rootfs в `.img` | **Готовый `.img`** на Google Drive | Jupyter, U-Boot prompt; проверено автором |
| [KeitetsuWorks/EBAZ4205](https://github.com/KeitetsuWorks/EBAZ4205) | PetaLinux | — | Vivado-проект под плату | FSBL через PetaLinux | PetaLinux-проект (U-Boot, kernel, rootfs) | Инструкции сборки; **образы — у пользователя** | Базовый Linux на SD |
| [wavelet2/EBAZ4205](https://github.com/wavelet2/EBAZ4205) | PetaLinux / EDF | HW-доработки в README (SD, R2577, D24, S2/S3) | Папка `vivado/`; ветки 2021.1–2025.2 | Через PetaLinux/EDF | PetaLinux + Yocto; `docs/how-to-build.md` | Инструкции сборки и разметки SD | Linux с `dnf`; исправлены кнопки S2/S3 |
| [har-in-air/EBAZ4205_PETALINUX](https://github.com/har-in-air/EBAZ4205_PETALINUX) | PetaLinux 2022.2 | Подробно: SD-слот, кварцы Y3/X5, boot-switch, D24, S3 | TCL в `hw/`, bitstream, XSA export | `petalinux-package --boot` → BOOT.BIN | Полный walkthrough: kernel, U-Boot, DTS, ext4 rootfs, JTAG boot | Разметка SD (FAT+ext4), копирование BOOT.BIN/image.ub | JTAG и SD boot; gpio-demo, peekpoke в rootfs |
| [tomorrow56/EBAZ4205_tutorial](https://github.com/tomorrow56/EBAZ4205_tutorial) | PL + Vitis | `documents/`: схемы, TRM, boot images, PHY, JTAG | 01–06: blink, HDMI, PS+PL (Vitis); merged XDC | Boot images в `documents/TF_boot_image/` | не нужен (нет Linux-стека) | TF boot images в документации | Blink, HDMI pattern/gradation, PS UART, PS LCD test |
| [Leungfung/ebaz4205_hw](https://github.com/Leungfung/ebaz4205_hw) | — | HW-документация, схемы, ссылки (CN) | — | — | — | — | — |
| [XyleMora/EBAZ4205](https://github.com/XyleMora/EBAZ4205) | — | Схемы, board files, discussions | — | — | — | — | — |
| [Elrori/openwrt-auto](https://github.com/Elrori/openwrt-auto) | OpenWrt | Ссылки на статьи; зависимости сборки | `system.bit` — копируется на FAT вручную | В составе release-образа | OpenWrt-сборка (скрипт `make-ebaz420x-v4.14.sh`) | **Готовые releases** на GitHub | Рабочий OpenWrt с LuCi |
| [Muhammad-Yunus/EBAZ4205_PROJECT](https://github.com/Muhammad-Yunus/EBAZ4205_PROJECT) | смешанный | Агрегатор community-проектов | Отдельные проекты (SDR, PYNQ) — разрозненно | Зависит от подпроекта | Зависит от подпроекта | Зависит от подпроекта | SDR, PYNQ и др. — без единого BSP |
| [gameltb/EBAZ4205](https://github.com/gameltb/EBAZ4205) | — | — | — | — | — | — | — |

## Antminer S9 / T9+ / E3

| Репозиторий | Плата | Стек | 0 · Подготовка | 1 · Vivado | 3 · FSBL / BOOT.BIN | 4 · U-Boot + Linux | 5 · Носитель | 6 · Верификация |
|---|---|---|---|---|---|---|---|---|
| [KarolNi/S9miner_sample](https://github.com/KarolNi/S9miner_sample) | S9 | bare-metal | PDF-схема control board | 4 проекта Vivado BD (PS7, MIO); PL пустой | FSBL + BIF в P04_SD_BOOT | не нужен | SD-boot в P04; **нет** готового BOOT.BIN в репо | P01 UART Hello; P02 GPIO; P03 SD/FatFs; P04 SD-boot + LED |
| [polprog/antminer_zynq](https://github.com/polprog/antminer_zynq) | S9 | PetaLinux *(план)* | Фото платы, boot log штатной прошивки | — *(Vivado/XSA в планах)* | — | U-Boot DTS `bitmain-antminer-s9`; **нет** kernel DTS и сборки | — *(образы в планах)* | Boot log bmminer как эталон штатной прошивки |
| [kangyuzhe666/zynq7010-pynq-2.5](https://github.com/kangyuzhe666/zynq7010-pynq-2.5) | S9 | PYNQ 2.5 | Описание DDR 1 GB в инструкции | Vivado minimal BD, export XSA+bit | Внутри sdbuild-образа | PetaLinux 2019.1 + PYNQ sdbuild; board `s9_pynq` | **Готовый `.img`** (облако Baidu) | Jupyter на порту 9090; образ проверен |
| [guannan-he/Antminer_s9_pynq](https://github.com/guannan-he/Antminer_s9_pynq) | S9 | PYNQ overlays | — | Overlays: base, resizer, antminerGPIO, AD7606 | — | — | — | Overlays поверх `s9_pynq`; GPIO, resizer, AD7606 demos |
| [Muhammad-Yunus/Antminer-S9-PYNQ](https://github.com/Muhammad-Yunus/Antminer-S9-PYNQ) | S9 | PYNQ 2.5 | Частично | Частично | Частично | Альтернативный PYNQ-порт | Образы community | PYNQ на S9 |
| [MelodyCoin/eSnyne](https://github.com/MelodyCoin/eSnyne) | S9/T9+/E3 | — | Концепт dev-платы, boot jumpers, сравнение с PYNQ-Z1 | — | — | — | — | — |
| [astranome/Astra_S9_FPGA](https://github.com/astranome/Astra_S9_FPGA) | S9 | — | Ссылки на экосистему (T9+, LCD, MicroBlaze) | — | — | — | — | — |
| [astranome/Astra_S9_FPGA_Base](https://github.com/astranome/Astra_S9_FPGA_Base) | S9 | — | Схема платы расширения (HDMI, I2S, GPIO) | Constraints/PL-разводка daughter board | — | — | — | — |
| [astranome/MicroBlaze-on-Zynq-7010](https://github.com/astranome/MicroBlaze-on-Zynq-7010) | S9 | MicroBlaze | — | Blink на MicroBlaze; XDC под S9 | Свой boot для soft-core | не нужен (не PS-Linux) | Bitstream для PL | Мигание LED |
| [iliasam/OpenZynqSDR_HW](https://github.com/iliasam/OpenZynqSDR_HW) | S9 | Linux + PL | PDF-схема S9; плата ADC (KiCad) | Vivado 2022.2, HLS DMA, SDR в PL | Зависит от companion-репо | Linux-стек в связке с App | Сборка пользователем | SDR-приёмник; статья на Habr |
| [iliasam/OpenZynqSDRApp](https://github.com/iliasam/OpenZynqSDRApp) | S9 | приложение | — | — | — | — | — | ПО SDR-приёмника (этапы 0–5 в HW-репо) |
| [cropinghigh/zynq_a9_mb](https://github.com/cropinghigh/zynq_a9_mb) | S9 | — | KiCad/Gerber самодельной платы из компонентов S9 | — | — | — | — | — |
| [trebisky/Antminer](https://github.com/trebisky/Antminer) | S9 | bare-metal | Аналог miner: XDC, сайт | XDC, PL-проекты под S9 | FSBL + bare-metal apps | U-Boot TFTP (как ebaz4205_miner) | TFTP / SD | GPIO, blink, bare-metal тесты |
| [opensatellite/antminer_t9](https://github.com/opensatellite/antminer_t9) | T9 | PYNQ (хак) | Pinout, boot jumpers, MIO/PHY/LED таблицы | Quick config BD (текстом в README) | Патч `boot/` поверх образа PYNQ-Z1 | Образ PYNQ-Z1 + модификации | Quick start: запись SD, джамперы JP1–4 | SSH/Jupyter xilinx/xilinx; DHCP Ethernet |
| [p8p671/trying-to-make-T9-works](https://github.com/p8p671/trying-to-make-T9-works) | T9/T9+/S9 | OpenWrt | Описание совместимости плат Bitmain | `system.bit` из Braiins OS | В составе OpenWrt-образа | Портированный DTS; OpenWrt kernel 5.10 | **Prebuilt OpenWrt** + инструкция Rufus | Ethernet работает (проверено 2023) |

## Расшифровка колонок

| Колонка | Этап туториала | Содержание |
|---------|----------------|------------|
| **0** | Подготовка платы | Схема/pinout, boot log, инструкции по UART/JTAG/SD |
| **1** | Vivado | BD, bitstream, XSA, XDC |
| **3** | FSBL / BOOT.BIN | Первая стадия загрузки |
| **4** | U-Boot + Linux | Kernel, DTS, rootfs *(для bare-metal — «не нужен»)* |
| **5** | Носитель | Разметка SD / готовые образы |
| **6** | Верификация | GPIO, сеть, overlays, SDR и прикладное ПО |

## Краткие выводы

### EBAZ4205 — полная цепочка Linux (этапы 1→5)

- **Готовый образ без сборки** → `nightseas/ebit_z7010`, `Stavros/ebaz4205`
- **Своя сборка** → `blkf2016` (Buildroot), `har-in-air` / `wavelet2` / `KeitetsuWorks` (PetaLinux)

### EBAZ4205 — без Linux

- `trebisky/ebaz4205_miner`, `tomorrow56/EBAZ4205_tutorial` (PL/Vitis)

### EBAZ4205 — только подготовка (этап 0)

- `xjtuecho`, `Elrori`, `Leungfung`

### Antminer S9 — полный Linux/PYNQ

- `kangyuzhe666` (+ overlays в `guannan-he`)

### Antminer S9 — bare-metal (этапы 1, 3, 6)

- `KarolNi/S9miner_sample`, `trebisky/Antminer`

### Antminer — заготовки

- `polprog` — только U-Boot DTS (этап 4 частично)

### Serious PL

- `iliasam/OpenZynqSDR_HW` — единственный «тяжёлый» PL + приложение

---

Пять репозиториев (`Elrori`, `xjtuecho`, `blkf2016`, `KarolNi`, `polprog`) разобраны детально в локальных клонах; остальные — по README и структуре на GitHub.

См. также: [repos.md](repos.md), [ZYNQ7000 — туториал](../tutor/ZYNQ7000%20-%20туториал.md), [Практическая карта](../tutor/Практическая%20карта.md).
