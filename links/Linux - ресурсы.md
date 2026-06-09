# Linux — ресурсы

## U-Boot DTS

**Antminer S9** — официальный device tree в Xilinx U-Boot:

- [bitmain-antminer-s9.dts](https://github.com/Xilinx/u-boot-xlnx/blob/master/arch/arm/dts/bitmain-antminer-s9.dts) — полезен вместе с [polprog/antminer_zynq](https://github.com/polprog/antminer_zynq)

---

## Официальная документация AMD/Xilinx

Откуда скачивать инструменты и откуда берутся загрузчики (FSBL генерируется из вашего Block Design; U-Boot и kernel — из PetaLinux или исходников Xilinx).

| Документ | URL | Зачем |
|----------|-----|-------|
| **Vivado / Vitis / PetaLinux** | [xilinx.com/support/download.html](https://www.xilinx.com/support/download.html) | Среды сборки; FSBL, U-Boot, kernel |
| **UG585** TRM Zynq-7000 | [PDF](https://www.xilinx.com/support/documentation/user_guides/ug585-Zynq-7000-TRM.pdf) | Boot modes, SLCR, OCM, MIO |
| **UG821** Software Dev Guide | [FSBL](https://docs.amd.com/r/en-US/ug821-zynq-7000-swdev/First-Stage-Bootloader) | Порядок партиций в BOOT.BIN |
| **UG1283** Bootgen | [PDF](https://www.xilinx.com/support/documents/sw_manuals/xilinx2022_2/ug1283-bootgen-user-guide.pdf) | BIF, boot header, `bootgen -arch zynq` |
| **UG1144** PetaLinux | через [download center](https://www.xilinx.com/support/download.html) | `petalinux-build`, `petalinux-package --boot` |
| **UG1165** Embedded Design Tutorial | [AMD docs](https://docs.amd.com/r/en-US/ug1165-zynq-embedded-design-tutorial) | Полный цикл Vivado → Vitis → PetaLinux → SD |
| **embeddedsw** (исходники FSBL) | [github.com/Xilinx/embeddedsw](https://github.com/Xilinx/embeddedsw) | FSBL без Vitis |
| **u-boot-xlnx** | [github.com/Xilinx/u-boot-xlnx](https://github.com/Xilinx/u-boot-xlnx) | U-Boot с DTS плат (в т.ч. Antminer S9) |
| **Pinout XC7Z010CLG400** | [package file](https://www.xilinx.com/support/packagefiles/z7packages/xc7z010clg400pkg.txt) | Справочник BGA-пинов для XDC |
