# Репозитории vs этапы туториала Zynq-7010

Сводка: что из [ZYNQ7000 — туториал](ZYNQ7000%20-%20туториал.md) реализовано в каждом репозитории из [repos.md](repos.md).

**Условные обозначения:** ✓ — есть (исходники или готовые образы) · ◐ — частично · — — не применимо (bare-metal / только PL) · · — нет

| Репозиторий | Плата | Стек (этап 2) | 0 | 1 | 3 | 4 | 5 | 6 | Комментарий |
|---|---|---|:---:|:---:|:---:|:---:|:---:|:---:|---|
| [Elrori/EBAZ4205](https://github.com/Elrori/EBAZ4205) | EBAZ | — | ✓ | ◐ | ◐ | ◐ | ◐ | · | KiCad EBAZEXT, XDC, архив DTS/uEnv/ps7_init; нет Vivado-проекта и RTL |
| [xjtuecho/EBAZ4205](https://github.com/xjtuecho/EBAZ4205) | EBAZ | штатный Linux | ✓ | ◐ | · | · | · | ◐ | Лучшая HW-документация: KiCad, `/proc`, boot log; XDC закомментирован |
| [blkf2016/ebaz4205](https://github.com/blkf2016/ebaz4205) | EBAZ | **Buildroot** | ◐ | ✓ | ✓ | ✓ | ◐ | ◐ | Почти полный BSP: FSBL, TCL/XDC, DTS, U-Boot/kernel, genimage; без `.img` |
| [nightseas/ebit_z7010](https://github.com/nightseas/ebit_z7010) | EBAZ | Linux BSP | ◐ | ✓ | ✓ | ✓ | ✓ | ◐ | **Готовые boot-образы** + Vivado BD + FSBL + DTS/configs |
| [trebisky/ebaz4205_miner](https://github.com/trebisky/ebaz4205_miner) | EBAZ | **bare-metal** | ◐ | ✓ | ✓ | ◐ | ◐ | ✓ | GPIO, таймеры, AMP, blink в PL; U-Boot только для TFTP |
| [trebisky/Zynq](https://github.com/trebisky/Zynq) | EBAZ/S9 | bare-metal | ◐ | ◐ | ◐ | ◐ | ◐ | ◐ | Часть проектов перенесена из `ebaz4205_miner` |
| [Stavros/ebaz4205](https://github.com/Stavros/ebaz4205) | EBAZ | **PYNQ** | ◐ | ✓ | ✓ | ✓ | ✓ | ✓ | Готовый `.img` (Google Drive): PetaLinux 2019.1 + Vivado + Jupyter |
| [KeitetsuWorks/EBAZ4205](https://github.com/KeitetsuWorks/EBAZ4205) | EBAZ | **PetaLinux** | ◐ | ✓ | ✓ | ✓ | ◐ | ◐ | Полный Vivado + PetaLinux toolchain (база для wavelet2) |
| [wavelet2/EBAZ4205](https://github.com/wavelet2/EBAZ4205) | EBAZ | **PetaLinux/EDF** | ✓ | ✓ | ✓ | ✓ | ◐ | ◐ | Ветки 2021.1–2025.2; Vivado + Yocto/PetaLinux; инструкции сборки |
| [har-in-air/EBAZ4205_PETALINUX](https://github.com/har-in-air/EBAZ4205_PETALINUX) | EBAZ | **PetaLinux 2022.2** | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | Подробный walkthrough: HW-доработки, XSA, JTAG/SD boot, `BOOT.BIN` |
| [tomorrow56/EBAZ4205_tutorial](https://github.com/tomorrow56/EBAZ4205_tutorial) | EBAZ | PL + Vitis | ✓ | ✓ | ◐ | — | ◐ | ✓ | Учебные проекты: blink, HDMI, PS+PL; Linux не основной путь |
| [Leungfung/ebaz4205_hw](https://github.com/Leungfung/ebaz4205_hw) | EBAZ | — | ✓ | · | · | · | · | · | Только HW-документация и схемы |
| [XyleMora/EBAZ4205](https://github.com/XyleMora/EBAZ4205) | EBAZ | — | ◐ | · | · | · | · | · | Схемы, board files; PetaLinux BSP заявлен как TBU |
| [Elrori/openwrt-auto](https://github.com/Elrori/openwrt-auto) | EBAZ | **OpenWrt** | ◐ | ◐ | ✓ | ✓ | ✓ | ✓ | Готовые releases; `system.bit` копируется на FAT вручную |
| [Muhammad-Yunus/EBAZ4205_PROJECT](https://github.com/Muhammad-Yunus/EBAZ4205_PROJECT) | EBAZ | смешанный | ◐ | ◐ | ◐ | ◐ | ◐ | ◐ | Агрегатор community-проектов (SDR, PYNQ и т.д.) |
| [gameltb/EBAZ4205](https://github.com/gameltb/EBAZ4205) | EBAZ | — | · | · | · | · | · | · | README почти пустой |
| [KarolNi/S9miner_sample](https://github.com/KarolNi/S9miner_sample) | S9 | **bare-metal** | ◐ | ✓ | ✓ | — | ◐ | ✓ | P01–P04: UART, GPIO, SD/FatFs, SD-boot; PL пустой |
| [polprog/antminer_zynq](https://github.com/polprog/antminer_zynq) | S9 | PetaLinux *(план)* | ✓ | · | · | ◐ | · | ◐ | U-Boot DTS + boot log; Vivado/XSA/PetaLinux — в планах |
| [kangyuzhe666/zynq7010-pynq-2.5](https://github.com/kangyuzhe666/zynq7010-pynq-2.5) | S9 | **PYNQ 2.5** | ◐ | ✓ | ✓ | ✓ | ✓ | ✓ | Полная инструкция sdbuild; готовый образ на облаке |
| [guannan-he/Antminer_s9_pynq](https://github.com/guannan-he/Antminer_s9_pynq) | S9 | **PYNQ overlays** | · | ◐ | · | · | · | ✓ | Overlays (base, GPIO, resizer…) поверх `s9_pynq`; образ — в kangyuzhe |
| [Muhammad-Yunus/Antminer-S9-PYNQ](https://github.com/Muhammad-Yunus/Antminer-S9-PYNQ) | S9 | **PYNQ 2.5** | ◐ | ◐ | ◐ | ◐ | ◐ | ◐ | Альтернативный PYNQ-порт S9 |
| [MelodyCoin/eSnyne](https://github.com/MelodyCoin/eSnyne) | S9/T9+/E3 | — | ✓ | · | · | · | · | · | Концепт dev-платы, boot jumpers, сравнение с PYNQ-Z1 |
| [astranome/Astra_S9_FPGA](https://github.com/astranome/Astra_S9_FPGA) | S9 | — | ◐ | · | · | · | · | · | Экосистема: ссылки на T9+, daughter board, LCD |
| [astranome/Astra_S9_FPGA_Base](https://github.com/astranome/Astra_S9_FPGA_Base) | S9 | — | ✓ | ◐ | · | · | · | · | Плата расширения: HDMI, I2S, GPIO (PL); не полный BSP |
| [astranome/MicroBlaze-on-Zynq-7010](https://github.com/astranome/MicroBlaze-on-Zynq-7010) | S9 | MicroBlaze | · | ◐ | ◐ | — | ◐ | ◐ | Blink на MicroBlaze; **не** типичная Zynq-цепочка FSBL→Linux |
| [iliasam/OpenZynqSDR_HW](https://github.com/iliasam/OpenZynqSDR_HW) | S9 | Linux + PL | ✓ | ✓ | ◐ | ◐ | ◐ | ✓ | Vivado 2022.2, SDR в PL, схема ADC; ПО — в OpenZynqSDRApp |
| [iliasam/OpenZynqSDRApp](https://github.com/iliasam/OpenZynqSDRApp) | S9 | приложение | · | · | · | · | · | ✓ | ПО к OpenZynqSDR (этапы 0–5 в HW-репо) |
| [cropinghigh/zynq_a9_mb](https://github.com/cropinghigh/zynq_a9_mb) | S9 | — | ✓ | · | · | · | · | · | KiCad/Gerber самодельной платы из компонентов S9 |
| [trebisky/Antminer](https://github.com/trebisky/Antminer) | S9 | **bare-metal** | ◐ | ✓ | ✓ | ◐ | ◐ | ✓ | Аналог `ebaz4205_miner`, адаптирован под Antminer |
| [opensatellite/antminer_t9](https://github.com/opensatellite/antminer_t9) | T9 | **PYNQ** (хак) | ✓ | ◐ | ✓ | ✓ | ✓ | ✓ | Quick start: образ PYNQ-Z1 + патч `boot/`; MIO/BD описаны |
| [p8p671/trying-to-make-T9-works](https://github.com/p8p671/trying-to-make-T9-works) | T9/T9+/S9 | **OpenWrt** | ◐ | ◐ | ✓ | ✓ | ✓ | ✓ | Prebuilt OpenWrt; нужны `u-boot.bin`+`system.bit` из Braiins |

## Расшифровка колонок этапов

| Колонка | Этап туториала | Содержание |
|---------|----------------|------------|
| **0** | Подготовка платы | Схема/pinout, boot log, инструкции по UART/JTAG/SD |
| **1** | Vivado | BD, bitstream, XSA, XDC |
| **3** | FSBL / BOOT.BIN | Первая стадия загрузки |
| **4** | U-Boot + Linux | Kernel, DTS, rootfs *(пропускается для bare-metal)* |
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

Пять репозиториев (`Elrori`, `xjtuecho`, `blkf2016`, `KarolNi`, `polprog`) разобраны детально в локальных клонах и в [Репозитории.md](Репозитории.md); остальные — по README и структуре на GitHub.

См. также: [repos.md](repos.md), [ZYNQ7000 — туториал](ZYNQ7000%20-%20туториал.md), [Практическая карта](Практическая%20карта.md).
