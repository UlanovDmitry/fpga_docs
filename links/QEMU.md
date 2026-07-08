# QEMU — Zynq-7000 (XC7Z010 / Zynq-7010 и др.)

Материалы по эмуляции и отладке **Zynq-7000** в QEMU: bare-metal, Linux, FSBL/U-Boot, GDB/XSDB. Для Zynq-7010 отдельных гайдов почти нет — используется та же линейка, что ZC702/ZC706 (`xilinx-zc702`, `zc702-zynq7`). Общие ресурсы по Zynq-7000 — в [ZYNQ7000 — ресурсы](ZYNQ7000%20-%20ресурсы.md).

**Типы машин QEMU для Zynq-7000:**

| Тип | Где используется |
|-----|------------------|
| `arm-generic-fdt-7series` | AMD/Xilinx QEMU (PetaLinux, Vitis) |
| `xilinx-zynq-a9` | Mainline QEMU (`qemu-system-arm`) |

**Отладка:** `-gdb tcp::port -S` → `target remote localhost:port` (GDB) или `gdbremote connect :port` (XSDB). В PetaLinux GDB-сервер обычно на `localhost:9000`.

---

## Официальная документация AMD/Xilinx

| Документ | URL | Зачем |
|----------|-----|-------|
| **UG1169** — Xilinx Quick Emulator User Guide | [docs.amd.com](https://docs.amd.com/v/u/en-US/ug1169-xilinx-qemu) | Главный гайд: запуск, boot-образы, GDB stub, монитор QEMU, DTB |
| **UG1393** — Vitis Application Acceleration (раздел QEMU) | [docs.amd.com](https://docs.amd.com/r/2022.1-English/ug1393-vitis-application-acceleration/QEMU) | Обзор QEMU в Vitis, ссылка на UG1169 |
| **UG1702** — Zynq 7000 PS Arguments for QEMU | [docs.amd.com](https://docs.amd.com/r/en-US/ug1702-vitis-accelerated-reference/Zynq-7000-PS-Arguments-for-QEMU) | Параметры `qemu_args.txt`: `-M arm-generic-fdt-7series`, SLCR, UART, U-Boot |

---

## Wiki AMD/Xilinx — QEMU User Documentation

Оглавление: [QEMU User Documentation](https://xilinx-wiki.atlassian.net/wiki/spaces/A/pages/821395464/QEMU+User+Documentation)

| Глава | URL | Зачем |
|-------|-----|-------|
| Chapter 1 — Introduction to QEMU | [wiki](https://xilinx-wiki.atlassian.net/wiki/spaces/A/pages/821428302/Chapter+1+-+Introduction+to+QEMU) | Введение, поддерживаемые платформы |
| Chapter 2 — Building and Running QEMU | [wiki](https://xilinx-wiki.atlassian.net/wiki/spaces/A/pages/818708631/Chapter+2+-+Building+and+Running+QEMU) | PetaLinux, Yocto, сборка из исходников, bare-metal |
| Chapter 3 — Developing with QEMU Virtual Machines | [wiki](https://xilinx-wiki.atlassian.net/wiki/spaces/A/pages/821952548/Chapter+3+-+Developing+with+QEMU+Virtual+Machines) | Boot-параметры, монитор, GDB/XSDB, загрузка приложений |
| Chapter 4 — Advanced | [wiki](https://xilinx-wiki.atlassian.net/wiki/spaces/A/pages/862879867/Chapter+4+-+Advanced) | Продвинутые темы (co-sim и др.) |
| Chapter 5 — Troubleshooting and Known Issues | [wiki](https://xilinx-wiki.atlassian.net/wiki/spaces/A/pages/861274368/Chapter+5+-+Troubleshooting+and+Known+Issues) | Типичные ошибки, FSBL hang, GDB segfault |
| Chapter 6 — Additional Resources | [wiki](https://xilinx-wiki.atlassian.net/wiki/spaces/A/pages/863043724/Chapter+6+-+Additional+Resources+and+References) | Ссылки, глоссарий, mainline QEMU |

---

## Запуск QEMU на Zynq-7000

| Ресурс | URL | Зачем |
|--------|-----|-------|
| What is QEMU | [wiki](https://xilinx-wiki.atlassian.net/wiki/spaces/A/pages/822247454/What+is+QEMU) | Зачем QEMU, отладка, co-simulation; Zynq-7000 — single-arch |
| QEMU Supported Platforms | [wiki](https://xilinx-wiki.atlassian.net/wiki/spaces/A/pages/821821485/QEMU+Supported+Platforms) | ZC702 (`xilinx-zc702` / `zc702-zynq7`), ZC706; перечень периферии |
| Launching QEMU Using PetaLinux tools | [wiki](https://xilinx-wiki.atlassian.net/wiki/spaces/A/pages/821985321) | `petalinux-boot --qemu --prebuilt 3`, `--qemu-args` |
| Launching QEMU Using Yocto toolchains | [wiki](https://xilinx-wiki.atlassian.net/wiki/spaces/A/pages/821854264/Launching+QEMU+Using+Yocto+toolchains) | `MACHINE=zc702-zynq7 runqemu ...` |
| Building and Running QEMU from Source Code | [wiki](https://xilinx-wiki.atlassian.net/wiki/spaces/A/pages/822312999/Building+and+Running+QEMU+from+Source+Code) | `git clone`, configure, сборка DTB из qemu-devicetrees |
| QEMU Options and Commands | [wiki](https://xilinx-wiki.atlassian.net/wiki/spaces/A/pages/821428366/QEMU+Options+and+Commands) | `-boot`, `-d`, `-S`, `-hw-dtb`, loader options; LQSPI для Zynq-7000 |
| Running Bare Metal Applications on QEMU | [wiki](https://xilinx-wiki.atlassian.net/wiki/spaces/A/pages/821854273/Running+Bare+Metal+Applications+on+QEMU) | Раздел **Zynq7000**: `arm-generic-fdt-7series`, DTB из PetaLinux-проекта |

---

## Отладка в QEMU

| Ресурс | URL | Зачем |
|--------|-----|-------|
| Debugging with QEMU and GDB | [wiki](https://xilinx-wiki.atlassian.net/wiki/spaces/A/pages/821624963/Debugging+Guest+Applications+with+QEMU+and+GDB) | Kernel-intrusive / non-intrusive debug, `-gdb tcp::port -S` |
| Debugging with QEMU, XSDB, and XSCT | [wiki](https://xilinx-wiki.atlassian.net/wiki/spaces/A/pages/821985347/Debugging+Guest+Applications+with+QEMU) | `gdbremote connect`, `memmap -file`, breakpoints в XSDB |
| Example Development Flow | [wiki](https://xilinx-wiki.atlassian.net/wiki/spaces/A/pages/821985356/Example+Development+Flow) | Сборка → QEMU → GDB; module debug printing |
| QEMU Module Debug Printing | [wiki](https://xilinx-wiki.atlassian.net/wiki/spaces/A/pages/862978153/QEMU+Module+Debug+Printing) | `-d`, включение debug-макросов в исходниках QEMU |
| Troubleshooting | [wiki](https://xilinx-wiki.atlassian.net/wiki/spaces/A/pages/862748822/Troubleshooting) | CPU stall, socket errors, `-hw-dtb: invalid option` (нужен AMD QEMU) |
| Known Issues (FSBL hangs) | [wiki](https://xilinx-wiki.atlassian.net/wiki/spaces/A/pages/862421130/Known+Issues) | Зависание FSBL: DDR, `psu_init`; workaround |

---

## Co-simulation (PS в QEMU + модель PL)

| Ресурс | URL | Зачем |
|--------|-----|-------|
| Co-simulation | [wiki](https://xilinx-wiki.atlassian.net/wiki/spaces/A/pages/862421112/Co-simulation) | Remote-Port, DTB с `zynq-pl-remoteport.dtsi` для Zynq-7000 |
| libsystemctlm-soc | [GitHub](https://github.com/Xilinx/libsystemctlm-soc) | SystemC/TLM-обёртка PS Zynq-7000 |
| systemctlm-cosim-demo | [GitHub](https://github.com/Xilinx/systemctlm-cosim-demo) | Демо `zynq_demo` для Zynq-7000 |

---

## Исходники и репозитории

| Ресурс | URL | Зачем |
|--------|-----|-------|
| Xilinx QEMU (fork) | [GitHub](https://github.com/Xilinx/qemu) | Официальный форк; теги `xilinx-v20xx.x` под версию PetaLinux |
| qemu-devicetrees | [GitHub](https://github.com/Xilinx/qemu-devicetrees) | DTB для ZynqMP; для Zynq-7000 DTB — из PetaLinux-проекта |
| Mainline QEMU — xilinx-zynq-a9 | [qemu.org](https://www.qemu.org/docs/master/system/arm/xlnx-zynq.html) | Документация машины; boot Buildroot ZC702 |
| qemu-discuss: Zynq A9 baremetal | [lists.nongnu.org](https://lists.nongnu.org/archive/html/qemu-discuss/2020-02/msg00027.html) | GDB + UART/semihosting на `xilinx-zynq-a9` |

---

## Сторонние обзоры и практические примеры

| Ресурс | Зачем |
|--------|-------|
| [Xilinx QEMU: FPGA Emulation & Virtual Development Guide](https://pcbsync.com/xilinx-qemu/) | Обзор: `xilinx-zynq-a9`, GDB, отладка FSBL/U-Boot/ядра, сборка из исходников |
| [Linux Kernel Module Cheat — QEMU bare-metal xilinx-zynq-a9](http://www.androidbugfix.com/2022/08/linux-kernel-module-cheat-qemu.html) | Bare-metal + GDB на Zynq-7000 (PYNQ), Global Timer |

---

## Ограничения (важно для Zynq-7010)

- **PL (логика FPGA) не эмулируется** как на реальной плате — только PS и стандартные периферии по DTB.
- **DTB для Zynq-7000** не в репозитории qemu-devicetrees — берётся из PetaLinux-проекта под вашу плату.
- **FSBL** может зависать на инициализации DDR/clock — см. [Known Issues](https://xilinx-wiki.atlassian.net/wiki/spaces/A/pages/862421130/Known+Issues).
- Для AMD-специфичных опций (`-hw-dtb`, co-sim) нужен **Xilinx/AMD QEMU**, не mainline из дистрибутива.
