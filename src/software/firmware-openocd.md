# Building and Flashing with OpenOCD

[OpenOCD](https://openocd.org/) (Open On-Chip Debugger) is an open-source tool
for flashing and debugging microcontrollers through a debug probe. It lets you
build and flash STM32 firmware from the command line, without STM32CubeIDE.

These instructions are for **Linux** (Ubuntu 22.04+ or similar). This page
assumes you have an STM32CubeMX project. See [Intro to Firmware](./firmware.md)
for the basics.

## Debug Probes

We use two kinds of debug probe:

| Probe | Used for | OpenOCD interface file |
| --- | --- | --- |
| **ST-Link** (built into Nucleo boards) | Nucleo development boards | `interface/stlink.cfg` |
| **Pico debug probe** (Raspberry Pi Debug Probe, or a Pico running the `debugprobe` firmware) | Our custom hardware | `interface/cmsis-dap.cfg` |

### ST-Link (Nucleo)

The ST-Link is built into the Nucleo board, so just connect the board to your
computer with a USB cable.

### Pico Debug Probe (Custom Hardware)

The Pico debug probe connects to the board's **SWD** pins:

| Signal | Raspberry Pi Debug Probe ("D" port) | Pico with `debugprobe` firmware |
| --- | --- | --- |
| SWCLK | Orange (SC) | GP2 |
| SWDIO | Yellow (SD) | GP3 |
| GND | Black | Any GND pin |

1. Wire SWCLK, SWDIO and GND to the matching pins on the board (check the
   board's schematic for where they are).
2. Power the board from its own supply. The probe does **not** power the board.
3. Connect the probe to your computer with a USB cable.

The probe also has a UART connection ("U" port on the Debug Probe, or GP4 (TX)
and GP5 (RX) on a Pico) that you can wire to the board's UART pins for serial
output. It shows up as a serial port such as `/dev/ttyACM0`.

## Installation

Install OpenOCD and the ARM toolchain:

```
sudo apt install openocd gcc-arm-none-eabi gdb-multiarch cmake ninja-build make
```

Check the installation:

```
openocd --version
arm-none-eabi-gcc --version
```

> NOTE: The Pico debug probe needs **OpenOCD 0.12 or newer**. Ubuntu 22.04
> ships with 0.11, so if `openocd --version` shows an older version, download
> the latest Linux release of [xPack
> OpenOCD](https://github.com/xpack-dev-tools/openocd-xpack/releases) instead,
> extract it (e.g. to `~/tools/openocd`), and add its `bin` folder to your
> `PATH`.

## Configuring OpenOCD

OpenOCD needs two configuration files:

- An **interface** file for the debug probe (see [Debug
  Probes](#debug-probes)).
- A **target** file for the microcontroller family, e.g.
  `target/stm32f4x.cfg` for an STM32F4.

| Chip family | Target file |
| --- | --- |
| STM32F0 | `target/stm32f0x.cfg` |
| STM32F1 | `target/stm32f1x.cfg` |
| STM32F3 | `target/stm32f3x.cfg` |
| STM32F4 | `target/stm32f4x.cfg` |
| STM32F7 | `target/stm32f7x.cfg` |
| STM32G4 | `target/stm32g4x.cfg` |
| STM32H7 | `target/stm32h7x.cfg` |
| STM32L4 | `target/stm32l4x.cfg` |

The full list is in `/usr/share/openocd/scripts/target` (or
`scripts/target` in the xPack download).

### Project Configuration File

Create a file called `openocd.cfg` in the root of your project. When you run
`openocd` with no `-f` options, it automatically uses this file from the
current directory. Use the version for your probe, with your chip's target
file.

**ST-Link (Nucleo):**

```
source [find interface/stlink.cfg]
source [find target/stm32f4x.cfg]
```

**Pico debug probe:**

```
source [find interface/cmsis-dap.cfg]
transport select swd
adapter speed 5000
source [find target/stm32f4x.cfg]
```

If you use both (e.g. Nucleo for development, then custom hardware), keep two
files, such as `openocd-nucleo.cfg` and `openocd-probe.cfg`, and pick one with
`-f`:

```
openocd -f openocd-probe.cfg
```

Commit these files so everyone on the project uses the same configuration.

### Testing the Connection

With the board connected and powered, run:

```
openocd
```

(or `openocd -f <your config file>`). If it connects, OpenOCD prints
information about the chip and waits for a debugger. Press `Ctrl+C` to stop
it.

## Building

To build from the command line, STM32CubeMX must generate a CMake or Makefile
project instead of an STM32CubeIDE project.

1. Open the project's `.ioc` file in STM32CubeMX.
2. In the **Project Manager** tab, set **Toolchain / IDE** to **CMake** (or
   **Makefile**).
3. Click **Generate Code**.

### CMake

From the root of the project:

```
cmake --preset Debug
cmake --build --preset Debug
```

The firmware is built to `build/Debug/<project name>.elf`. Use the `Release`
preset for an optimized build.

### Makefile

From the root of the project:

```
make -j
```

The firmware is built to `build/<project name>.elf`.

## Flashing

To flash the firmware, verify it, and reset the board to start running it:

```
openocd -c "program build/Debug/<project name>.elf verify reset exit"
```

This uses the `openocd.cfg` file in your project. To use a different config
file, add `-f <config file>` before `-c`.

> TIP: Combine building and flashing into one command so you don't forget to
> rebuild:
>
> ```
> cmake --build --preset Debug && openocd -c "program build/Debug/<project name>.elf verify reset exit"
> ```

## Debugging

OpenOCD can also act as a GDB server. This is how to debug with the Pico debug
probe, since ST's debugging tools only support ST-Links. Start OpenOCD with no
commands:

```
openocd
```

Then, in a second terminal, connect GDB on port `3333`:

```
gdb-multiarch build/Debug/<project name>.elf
(gdb) target extended-remote localhost:3333
(gdb) monitor reset halt
(gdb) load
```

Common GDB commands are covered in [Debugging with
STM32CubeCLT](./firmware-cubeclt.md#common-gdb-commands).

## Troubleshooting

| Problem | Things to try |
| --- | --- |
| `Error: open failed`, `LIBUSB_ERROR_ACCESS` or `unable to find a matching CMSIS-DAP device` | OpenOCD can't access the probe. Check the USB cable, and that the probe shows up in `lsusb`. |
| `init mode failed (unable to connect to the target)` or `Error connecting DP` | Check the target file matches your chip. For the Pico debug probe, check the SWD wiring and that the board is powered, and try a lower speed (e.g. `adapter speed 1000`). Holding the board's reset button while OpenOCD starts can also help. |
| Pico debug probe not recognized at all | Check your OpenOCD version is 0.12 or newer. |
| `unknown device` or wrong flash size | You are probably using the wrong target file. |
| Flash succeeds but the code doesn't run | Make sure `reset` is in the `program` command, or press the board's reset button. |
| ST-Link firmware warnings | Update the ST-Link firmware using STM32CubeProgrammer. |
