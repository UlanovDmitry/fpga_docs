# Документация по ПЛИС / FPGA / Zynq-7010

Сборник заметок, туториалов и ссылок по FPGA и дешёвым отладочным платам на **XC7Z010 (Zynq-7010)** — в первую очередь **EBAZ4205** и **Antminer S9** (перепрофилированные control board майнеров).

Локальные копии репозиториев — в `c:\code\`.

---

## Структура каталога

```
fpga_docs/
├── README.md                 ← этот файл
├── Платы.md                  маркировка чипов Zynq на платах
│
├── tutor/                    практические руководства и туториалы
│   ├── ZYNQ7000 - туториал.md
│   ├── ZYNQ7000 - чек-лист прошивки.md
│   ├── SoC - термины.md
│   ├── Практическая карта.md
│   ├── Первый запуск.md
│   ├── BSP - Buildroot.md
│   ├── BSP - PetaLinux.md
│   ├── BSP - PYNQ.md
│   └── BSP - Yocto.md
│
├── repo/                     каталог GitHub-репозиториев
│   ├── repos.md
│   └── repos-vs-tutorial.md
│
└── links/                    внешние ссылки и закладки
    ├── ZYNQ7000 - ресурсы.md
    ├── Linux - ресурсы.md
    ├── EBAZ4205 - ресурсы.md
    ├── Antminer - ресурсы.md
    ├── FPGA.md
    └── Yosys.md
```

---

## С чего начать

| Шаг | Документ | Зачем |
|-----|----------|-------|
| 1 | [Первый запуск](tutor/Первый%20запуск.md) | Boot mode, UART, boot log (S9; применимо к этапу 0) |
| 2 | [ZYNQ7000 — туториал](tutor/ZYNQ7000%20-%20туториал.md) | Полный цикл: Vivado → boot → Linux → SD |
| 3 | [Практическая карта](tutor/Практическая%20карта.md) | Какой репозиторий / стек выбрать под задачу |
| 4 | [SoC — термины](tutor/SoC%20-%20термины.md) | Глоссарий: PS/PL, FSBL, DTS, BSP… |
| 5 | Ресурсы по своей плате | [EBAZ4205](links/EBAZ4205%20-%20ресурсы.md) или [Antminer](links/Antminer%20-%20ресурсы.md) |

Цепочка загрузки Zynq: **Boot ROM → FSBL → [bitstream] → U-Boot → [Linux / приложение]**.

---

## tutor/ — руководства

### Общие

| Документ | О чём |
|----------|-------|
| [ZYNQ7000 — туториал](tutor/ZYNQ7000%20-%20туториал.md) | Этапы 0–6: действия, артефакты, Buildroot / PetaLinux / PYNQ / Yocto |
| [ZYNQ7000 — чек-лист прошивки](tutor/ZYNQ7000%20-%20чек-лист%20прошивки.md) | Развёрнутый чек-лист: аппаратура, Vivado, boot, Linux, flash |
| [SoC — термины](tutor/SoC%20-%20термины.md) | Словарь терминов из туториала и репозиториев |
| [Практическая карта](tutor/Практическая%20карта.md) | Куда идти за готовым BSP, репо или walkthrough |
| [Первый запуск](tutor/Первый%20запуск.md) | Antminer S9: джамперы boot, UART, снятие boot log |

### Формирование BSP

| Документ | Стек |
|----------|------|
| [BSP - Buildroot](tutor/BSP%20-%20Buildroot.md) | defconfig, `board/<плата>/`, genimage; эталон blkf2016 |
| [BSP - PetaLinux](tutor/BSP%20-%20PetaLinux.md) | XSA, `project-spec/meta-user/`; wavelet2, KeitetsuWorks |
| [BSP - PYNQ](tutor/BSP%20-%20PYNQ.md) | sdbuild, board `.spec`; kangyuzhe666, Stavros |
| [BSP - Yocto](tutor/BSP%20-%20Yocto.md) | meta-layer, machine.conf, meta-xilinx |

---

## repo/ — GitHub-репозитории

| Документ | О чём |
|----------|-------|
| [repos.md](repo/repos.md) | Расширенный каталог проектов по EBAZ4205 и Antminer S9/T9 |
| [repos-vs-tutorial.md](repo/repos-vs-tutorial.md) | Сводка: что из каждого репо покрывает этапы туториала |

---

## links/ — внешние ресурсы

### Zynq и Linux

| Документ | О чём |
|----------|-------|
| [ZYNQ7000 — ресурсы](links/ZYNQ7000%20-%20ресурсы.md) | UG1165, Zynq Book, Habr, trebisky, официальные туториалы |
| [Linux — ресурсы](links/Linux%20-%20ресурсы.md) | U-Boot DTS, UG585, Bootgen, PetaLinux, download center |

### По платам

| Плата | Документ |
|-------|----------|
| **EBAZ4205** (256 MB DDR, 100M Ethernet, NAND) | [EBAZ4205 — ресурсы](links/EBAZ4205%20-%20ресурсы.md) |
| **Antminer S9** (SD, JTAG, больше GPIO) | [Antminer — ресурсы](links/Antminer%20-%20ресурсы.md) |
| Маркировка чипа на корпусе | [Платы.md](Платы.md) |

### Общее (не Zynq)

| Документ | О чём |
|----------|-------|
| [FPGA](links/FPGA.md) | Обучающие материалы, Марсоход, OpenCores |
| [Yosys](links/Yosys.md) | Открытый синтез, OSS CAD Suite, fpga-toolchain |

---

## Быстрый выбор под задачу

| Задача | Куда смотреть |
|--------|----------------|
| Понять этапы разработки | [туториал](tutor/ZYNQ7000%20-%20туториал.md) |
| Собрать Linux самому | [BSP Buildroot](tutor/BSP%20-%20Buildroot.md) или [BSP PetaLinux](tutor/BSP%20-%20PetaLinux.md) |
| Jupyter + Python на PL | [BSP PYNQ](tutor/BSP%20-%20PYNQ.md) · [repos.md](repo/repos.md) |
| Готовый образ без сборки | [Практическая карта](tutor/Практическая%20карта.md) → nightseas, Stavros, kangyuzhe666 |
| Bare-metal | KarolNi, trebisky — [repos-vs-tutorial](repo/repos-vs-tutorial.md) |
| Что уже сделано в репо | [repos-vs-tutorial.md](repo/repos-vs-tutorial.md) |
