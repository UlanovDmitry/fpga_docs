# BSP через PYNQ sdbuild (Zynq-7010)

Практическая инструкция: как PetaLinux-BSP формируется **внутри сборки образа PYNQ** для кастомной платы (**EBAZ4205**, **Antminer S9**).

**Эталоны:** [kangyuzhe666/zynq7010-pynq-2.5](https://github.com/kangyuzhe666/zynq7010-pynq-2.5) (S9) · [Stavros/ebaz4205](https://github.com/Stavros/ebaz4205) (готовый `.img`)

Документация: [PYNQ SD Card](https://pynq.readthedocs.io/en/latest/pynq_sd_card.html)

---

## Что такое BSP в PYNQ

PYNQ **не заменяет** PetaLinux. `sdbuild` автоматически:

1. Создаёт или распаковывает **PetaLinux BSP** из XSA/HDF или готового `.bsp`.
2. Собирает boot-цепочку (FSBL, U-Boot, kernel, DTB).
3. Накатывает **PREBUILT rootfs** + пакеты PYNQ (Jupyter, Python, overlays).
4. Выдаёт **монолитный `.img`** на SD.

Вы **не** проходите `petalinux-config` вручную (кроме отладки); BSP настраивается через **board directory** и `petalinux_bsp/meta-user/`.

---

## Предварительные требования

| Компонент | PYNQ 2.5 (пример S9) |
|-----------|----------------------|
| ОС | Ubuntu 18.04 |
| Vivado | **2019.1** |
| PetaLinux | **2019.1** |
| SDK | 2019.1 |
| PYNQ | `git checkout v2.5` |
| PREBUILT rootfs | `bionic.arm.2.5.img` с [pynq.io](http://www.pynq.io/board.html) |

**Все версии Xilinx должны совпадать с версией PYNQ.**

---

## Структура board directory (ваш BSP)

```
PYNQ/boards/s9_pynq/
├── s9_pynq.spec                      # главный конфиг
├── base/
│   └── base.bit                      # bitstream при загрузке
└── petalinux_bsp/
    ├── hardware_project/
    │   └── system.hdf                # или .xsa (из Vivado)
    └── meta-user/                    # правки BSP (как в PetaLinux)
        └── recipes-bsp/device-tree/files/system-user.dtsi
```

Опционально:

- `notebooks/` — Jupyter-ноутбуки
- `packages/` — доп. пакеты sdbuild
- overlays с `.bit` + `.hwh`

---

## Шаг 1. Vivado — минимальный BD

1. `xc7z010clg400-1`, PS7: DDR, UART1, SDIO0, GEM0.
2. **Bank voltages** по схеме (S9: bank500=3.3V, bank501=2.5V).
3. DDR: для S9 1 GB — две планки 256M×16, 32-bit (см. kangyuzhe666).
4. PL может быть пустым на первом этапе.
5. **Generate Bitstream** → **Export Hardware** (Include bitstream).

Переименовать:

- `design_1_wrapper.bit` → `boards/<плата>/base/base.bit`
- `design_1_wrapper.hdf` → `boards/<плата>/petalinux_bsp/hardware_project/system.hdf`

(В новых версиях — `.xsa` вместо `.hdf`.)

---

## Шаг 2. Файл `.spec`

Минимум (kangyuzhe666, S9):

```makefile
ARCH_s9_pynq := arm
BSP_s9_pynq :=
BITSTREAM_s9_pynq := base/base.bit
```

Расширенный (официальная Pynq-Z1):

```makefile
ARCH_Pynq-Z1 := arm
BSP_Pynq-Z1 :=
BITSTREAM_Pynq-Z1 := base/base.bit
FPGA_MANAGER_Pynq-Z1 := 1
STAGE4_PACKAGES_Pynq-Z1 := pynq ethernet boot_leds pynq_peripherals
```

| Переменная | Назначение |
|------------|------------|
| `ARCH_*` | `arm` (Zynq-7000) или `aarch64` (ZynqMP) |
| `BSP_*` | Путь к `.bsp` или **пусто** → создать из HDF/XSA |
| `BITSTREAM_*` | `.bit` при старте |
| `FPGA_MANAGER_*` | `1` — `Overlay()` из Python |
| `STAGE4_PACKAGES_*` | `pynq`, `ethernet`, `jupyter`… |

---

## Шаг 3. Кастомизация BSP (`petalinux_bsp/meta-user/`)

Если автогенерации из XSA недостаточно — те же правки, что в PetaLinux:

- `system-user.dtsi` — Ethernet PHY, DDR, кнопки
- `recipes-bsp/u-boot/files/platform-top.h`
- `recipes-kernel/linux/linux-xlnx_%.bbappend`

`sdbuild/scripts/create_bsp.sh` при сборке:

```bash
petalinux-create --type project --template zynq --name <board>
petalinux-config --get-hw-description=hardware_project/ --silentconfig
# копирует meta-user/
petalinux-package --bsp -p <project> --output <board>.bsp
```

---

## Шаг 4. Подготовка хоста

```bash
git clone https://github.com/Xilinx/PYNQ.git
cd PYNQ && git checkout v2.5
git checkout -b myboard-2.5

cd sdbuild/scripts/
./setup_host.sh

source /tools/Xilinx/petalinux/settings.sh
source /tools/Xilinx/Vivado/2019.1/settings64.sh
export PATH=/opt/qemu/bin:/opt/crosstool-ng/bin:$PATH
petalinux-util --webtalk off
```

Скачать PREBUILT: `bionic.arm.2.5.img`.

---

## Шаг 5. Сборка образа

```bash
cd PYNQ/sdbuild
make BOARDS=s9_pynq PREBUILT=bionic.arm.2.5.img
```

Внешний board repo:

```bash
make BOARDDIR=/abs/path/to/my-boards BOARDS=s9_pynq PREBUILT=bionic.arm.2.5.img
```

Результат: `sdbuild/output/s9_pynq-2.5.img`

---

## Шаг 6. Запись на SD

```bash
sudo dd if=s9_pynq-2.5.img of=/dev/sdX bs=4M status=progress
sync
```

Boot mode = SD. UART / Jupyter: обычно `xilinx`/`xilinx`, порт **9090** (kangyuzhe666).

---

## Что внутри `.img` (vs классический PetaLinux)

| Компонент | PetaLinux вручную | PYNQ sdbuild |
|-----------|-------------------|--------------|
| BOOT.BIN, U-Boot, kernel | `images/linux/` | Внутри `.img` |
| rootfs | Собирается с нуля | PREBUILT + пакеты |
| Jupyter, PYNQ API | Нет | Да |
| Разметка SD | Ручная fdisk | Уже в `.img` |

---

## Overlays (после базового образа)

[guannan-he/Antminer_s9_pynq](https://github.com/guannan-he/Antminer_s9_pynq) — overlays поверх `s9_pynq`:

```python
from pynq import Overlay
ol = Overlay('base.bit')  # или design.bit
```

Не требуют пересборки всего Linux — только bitstream + `.hwh`.

---

## Готовый образ без сборки

| Плата | Репозиторий |
|-------|-------------|
| EBAZ4205 | [Stavros/ebaz4205](https://github.com/Stavros/ebaz4205) — `.img` на Google Drive |
| S9 | kangyuzhe666 — Baidu Pan в README |

---

## Хак без sdbuild (T9)

[opensatellite/antminer_t9](https://github.com/opensatellite/antminer_t9): образ **PYNQ-Z1** + замена файлов в `boot/` — не полноценный BSP, быстрый старт.

---

## Чек-лист

- [ ] Vivado = PetaLinux = версия PYNQ
- [ ] BD соответствует схеме (DDR, bank voltages)
- [ ] `boards/<плата>/<плата>.spec` создан
- [ ] HDF/XSA в `petalinux_bsp/hardware_project/`
- [ ] `base.bit` на месте
- [ ] PREBUILT rootfs скачан
- [ ] `setup_host.sh` выполнен
- [ ] `make BOARDS=...` завершился успешно
- [ ] `.img` записан, boot mode = SD
- [ ] Jupyter / SSH доступен

---

## Типичные проблемы

| Проблема | Решение |
|----------|---------|
| Ethernet не поднимается | Скопировать `sdbuild/packages/ethernet/eth0` → `/etc/network/interfaces.d/` |
| Неверная DDR | Проверить конфиг DDR в Vivado (512M vs 1G на S9) |
| Долгая сборка | sstate-cache Xilinx для PetaLinux |
| EBAZ/S9 не в официальном списке | Нужен полный порт (board directory) |

---

## См. также

- [BSP - PetaLinux](BSP%20-%20PetaLinux.md) — ручной flow (без Jupyter)
- [BSP - Buildroot](BSP%20-%20Buildroot.md) · [BSP - Yocto](BSP%20-%20Yocto.md)
- [Практическая карта](Практическая%20карта.md)
