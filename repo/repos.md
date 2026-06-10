Ниже — подборка GitHub-проектов для **дешёвых отладочных плат на XC7Z010 (Zynq-7010)**: перепрофилированные платы управления майнерами, с документацией, Vivado/PetaLinux/BSP или bare-metal примерами. Сводка по этапам туториала — [repos-vs-tutorial.md](repos-vs-tutorial.md).

---

## EBAZ4205

Плата управления майнером Ebit E9+ — 256 MB DDR3, 128 MB NAND, Ethernet 100M. Подробнее: [EBAZ4205 — ресурсы](../links/EBAZ4205%20-%20ресурсы.md).

| Репозиторий | О чём | Что полезного |
|---|---|---|
| [Elrori/EBAZ4205](https://github.com/Elrori/EBAZ4205) | Документация и аппаратная база | KiCad **EBAZEXT-V2**, XDC (VGA, CMOS, MII), архив SD-boot (DTS, uEnv, ps7_init); pinout Zynq-7010. RTL и готовых образов нет |
| [xjtuecho/EBAZ4205](https://github.com/xjtuecho/EBAZ4205) | Основной репозиторий по плате | [wiki](https://github.com/xjtuecho/EBAZ4205/wiki), KiCad V004, PDF-схема, инструкции штатного Linux/u-boot, дампы `/proc`, шаблон XDC (пины закомментированы) |
| [blkf2016/ebaz4205](https://github.com/blkf2016/ebaz4205) | Linux на SD (Buildroot) | Наиболее полный BSP: FSBL, Buildroot 2019.05, DTS, U-Boot/kernel configs, Vivado TCL/XDC. Готовых `.bin`/`.img` нет |
| [nightseas/ebit_z7010](https://github.com/nightseas/ebit_z7010) | Базовый reference design | Vivado BD (PS+EMIO Ethernet/GPIO), **готовые boot-образы**, DTS для U-Boot/ядра, FSBL. Один из самых «собранных» стартовых BSP |
| [trebisky/ebaz4205_miner](https://github.com/trebisky/ebaz4205_miner) | Bare-metal + PL | XDC, GPIO/кнопки, таймеры, AMP (2 ядра), blink в fabric, TFTP-boot через U-Boot |
| [trebisky/Zynq](https://github.com/trebisky/Zynq) | Общий Zynq-репо | Часть проектов перенесена сюда из `ebaz4205_miner` |
| [Stavros/ebaz4205](https://github.com/Stavros/ebaz4205) | PYNQ | Готовые PS (PetaLinux 2019.1) + PL (Vivado 2019.1) для **PYNQ** |
| [KeitetsuWorks/EBAZ4205](https://github.com/KeitetsuWorks/EBAZ4205) | Vivado + PetaLinux | Полный toolchain под плату |
| [wavelet2/EBAZ4205](https://github.com/wavelet2/EBAZ4205) | Vivado + PetaLinux | Ветки **2021.1 / 2022.2 / 2023.1**, инструкции по сборке |
| [har-in-air/EBAZ4205_PETALINUX](https://github.com/har-in-air/EBAZ4205_PETALINUX) | PetaLinux 2022.2 | Подробно описаны **аппаратные доработки** (SD, кварц PL Y3/X5, boot-switch, D24) |
| [tomorrow56/EBAZ4205_tutorial](https://github.com/tomorrow56/EBAZ4205_tutorial) | Учебные проекты | Blink, HDMI, PS+PL (Vitis), PS LCD test |
| [Leungfung/ebaz4205_hw](https://github.com/Leungfung/ebaz4205_hw) | Аппаратная база (CN) | Сборник HW-документации, схем, ссылок |
| [XyleMora/EBAZ4205](https://github.com/XyleMora/EBAZ4205) | Документация + форум | Схемы, board files, discussions; Petalinux BSP заявлен как TBU |
| [Elrori/openwrt-auto](https://github.com/Elrori/openwrt-auto) | OpenWrt | Сборка OpenWrt для EBAZ4205 (см. также [Elrori/EBAZ4205](https://github.com/Elrori/EBAZ4205)) |
| [Muhammad-Yunus/EBAZ4205_PROJECT](https://github.com/Muhammad-Yunus/EBAZ4205_PROJECT) | Сообщество | Проекты вокруг EBAZ4205 (SDR, PYNQ и т.д.) |
| [gameltb/EBAZ4205](https://github.com/gameltb/EBAZ4205) | Vivado/PetaLinux | Упоминается в сообществе; README почти пустой |

**Практическая карта для EBAZ4205:**

1. **Готовый Linux с образами** → `nightseas/ebit_z7010`
2. **Своя сборка Linux (Buildroot)** → `blkf2016/ebaz4205`
3. **PetaLinux, свежие версии** → `wavelet2/EBAZ4205` или `har-in-air/EBAZ4205_PETALINUX`
4. **PYNQ** → `Stavros/ebaz4205`
5. **Bare-metal / PL** → `trebisky/ebaz4205_miner`
6. **Учебные примеры** → `tomorrow56/EBAZ4205_tutorial`

---

## Antminer S9 / T9+ / E3 (тот же XC7Z010, больше GPIO)

Платы Bitmain ближе к «настоящей» dev-плате: SD, JTAG, UART, джамперы boot, кнопки/LED на плате.

| Репозиторий | Плата | О чём | Что полезного |
|---|---|---|---|
| [KarolNi/S9miner_sample](https://github.com/KarolNi/S9miner_sample) | S9 | Bare-metal примеры | 4 проекта Vivado/SDK: UART, GPIO, SD (FatFs), SD-boot. Только PS7, PL пустой |
| [polprog/antminer_zynq](https://github.com/polprog/antminer_zynq) | S9 | Сборник по control board | U-Boot DTS, boot log штатной прошивки. Vivado/XSA/PetaLinux — в планах, не реализованы |
| [kangyuzhe666/zynq7010-pynq-2.5](https://github.com/kangyuzhe666/zynq7010-pynq-2.5) | S9 (C41 V1.2, 1 GB DDR) | PYNQ 2.5 | Полная инструкция сборки образа PYNQ под S9 |
| [guannan-he/Antminer_s9_pynq](https://github.com/guannan-he/Antminer_s9_pynq) | S9 | PYNQ overlays | Портирование overlay на `s9_pynq` |
| [Muhammad-Yunus/Antminer-S9-PYNQ](https://github.com/Muhammad-Yunus/Antminer-S9-PYNQ) | S9 | PYNQ 2.5 | Альтернативный PYNQ-порт |
| [MelodyCoin/eSnyne](https://github.com/MelodyCoin/eSnyne) | S9 / T9+ / E3 | Концепт dev-платы | Документация по перепрофилированию, boot jumpers, сравнение с PYNQ-Z1/ZedBoard |
| [astranome/Astra_S9_FPGA](https://github.com/astranome/Astra_S9_FPGA) | S9 | Dev-плата + экосистема | Ссылки на T9+, daughter board, LCD, MicroBlaze |
| [astranome/Astra_S9_FPGA_Base](https://github.com/astranome/Astra_S9_FPGA_Base) | S9 | Плата расширения | HDMI, I2S, I2C, SPI, 16×GPIO (PL), аудио, MIDI |
| [astranome/MicroBlaze-on-Zynq-7010](https://github.com/astranome/MicroBlaze-on-Zynq-7010) | S9 | Blink | Простейший blink + constraints под S9 |
| [iliasam/OpenZynqSDR_HW](https://github.com/iliasam/OpenZynqSDR_HW) | S9 | SDR | Vivado 2022.2, схема S9, плата ADC (AD9226+AD8370), [статья на Habr](https://habr.com/ru/articles/898490) |
| [iliasam/OpenZynqSDRApp](https://github.com/iliasam/OpenZynqSDRApp) | S9 | SDR (ПО) | Приложение к OpenZynqSDR |
| [cropinghigh/zynq_a9_mb](https://github.com/cropinghigh/zynq_a9_mb) | S9 → своя плата | KiCad/Gerber | Самодельная dev/main board из компонентов S9 |
| [trebisky/Antminer](https://github.com/trebisky/Antminer) | S9 | Bare-metal | Аналог `ebaz4205_miner`, но под Antminer |
| [opensatellite/antminer_t9](https://github.com/opensatellite/antminer_t9) | **T9** | PYNQ | Быстрый старт PYNQ на T9 (1 GbE), настройки MIO/BD |
| [p8p671/trying-to-make-T9-works](https://github.com/p8p671/trying-to-make-T9-works) | T9/T9+/S9 | OpenWrt | Порт OpenWrt, совместимость плат Bitmain |

**Практическая карта для S9:**

1. **Bare-metal** → `KarolNi/S9miner_sample` + `trebisky/Antminer`
2. **Linux/PetaLinux** → `polprog/antminer_zynq` (заготовка) + [U-Boot DTS](../links/Linux%20-%20ресурсы.md)
3. **PYNQ** → `kangyuzhe666/zynq7010-pynq-2.5` или `guannan-he/Antminer_s9_pynq`
4. **SDR / серьёзный PL** → `iliasam/OpenZynqSDR_HW`
5. **Плата расширения** → `astranome/Astra_S9_FPGA_Base` или `cropinghigh/zynq_a9_mb`

---

## Сводка: что выбрать под задачу

| Задача | Лучший старт |
|---|---|
| Дешевле всего, учиться Zynq | EBAZ4205 + `nightseas/ebit_z7010` или `blkf2016/ebaz4205` |
| Больше GPIO, удобнее boot | Antminer S9 + `KarolNi/S9miner_sample` |
| PYNQ / Jupyter | S9: `kangyuzhe666/zynq7010-pynq-2.5`; EBAZ: `Stavros/ebaz4205` |
| Bare-metal + PL | `trebisky/ebaz4205_miner` / `trebisky/Antminer` |
| OpenWrt | EBAZ: `Elrori/openwrt-auto`; S9/T9: `p8p671/trying-to-make-T9-works` |
| SDR на Zynq-7010 | `iliasam/OpenZynqSDR_HW` (S9) |
| Учебные туториалы | `tomorrow56/EBAZ4205_tutorial` |

