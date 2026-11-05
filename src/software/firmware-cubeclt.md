# Debugging with STM32CubeCLT

[STM32CubeCLT](https://www.st.com/en/development-tools/stm32cubeclt.html)
(STM32Cube Command Line Tools) is ST's toolset for building, flashing and
debugging STM32 firmware from the command line. It contains the same tools
STM32CubeIDE uses, without the IDE, so you can use any editor (e.g. VSCode).

This page assumes you have an STM32CubeMX project generated for **CMake**. See
[Intro to Firmware](./firmware.md) for the basics, and [Building and Flashing
with OpenOCD](./firmware-openocd.md#building) for how to generate a CMake
project.

> NOTE: ST's flashing and debugging tools (`STM32_Programmer_CLI` and
> `ST-LINK_gdbserver`) only work with **ST-Links**, such as the one built into
> Nucleo boards. To flash or debug our custom hardware with a **Pico debug
> probe**, use [OpenOCD](./firmware-openocd.md) instead. You can still use
> CubeCLT's compiler and GDB with it, and everything in [Debugging with
> GDB](#debugging-with-gdb) from step 2 onwards applies; just connect to
> OpenOCD's port (`target extended-remote localhost:3333`) instead of
> `localhost:61234`.

## What's Included

| Tool | Command | Purpose |
| --- | --- | --- |
| GNU Tools for STM32 | `arm-none-eabi-gcc`, `arm-none-eabi-gdb` | Compiler and debugger |
| ST-Link GDB server | `ST-LINK_gdbserver` | Connects GDB to the board through an ST-Link |
| STM32CubeProgrammer | `STM32_Programmer_CLI` | Flashes, erases and reads the chip |
| CMake and Ninja | `cmake`, `ninja` | Build tools |
| SVD files | – | Describe every peripheral register, so debuggers can show them by name |

## Installation

### Windows

1. Download STM32CubeCLT for Windows from the [ST
   website](https://www.st.com/en/development-tools/stm32cubeclt.html). You
   will need a free ST account.
2. Run the installer and keep the default options, including the ST-Link
   drivers. It installs to `C:\ST\STM32CubeCLT_<version>\`.

### Linux

1. Download STM32CubeCLT for Linux (the Debian/Ubuntu package) from the [ST
   website](https://www.st.com/en/development-tools/stm32cubeclt.html).
2. Unzip the download and run the installer:

   ```
   chmod +x st-stm32cubeclt_*.sh
   sudo ./st-stm32cubeclt_*.sh
   ```

   Accept the licence agreements and the ST-Link udev rules. It installs to
   `/opt/st/stm32cubeclt_<version>/`.

### Adding the Tools to Your PATH

If the commands below aren't found, add these folders (inside the install
folder) to your `PATH`:

- `GNU-tools-for-STM32/bin`
- `STLink-gdb-server/bin`
- `STM32CubeProgrammer/bin`
- `CMake/bin` and `Ninja/bin`

On Linux, for example, add this to your `~/.bashrc` (with your version number):

```
export CLT=/opt/st/stm32cubeclt_<version>
export PATH=$CLT/GNU-tools-for-STM32/bin:$CLT/STLink-gdb-server/bin:$CLT/STM32CubeProgrammer/bin:$CLT/CMake/bin:$CLT/Ninja/bin:$PATH
```

Check the installation by opening a new terminal and running:

```
arm-none-eabi-gcc --version
STM32_Programmer_CLI --version
```

## Building for Debugging

Always debug a **Debug** build. Release builds are optimized, which reorders
and removes code, so stepping through it and inspecting variables becomes
unreliable.

```
cmake --preset Debug
cmake --build --preset Debug
```

The firmware is built to `build/Debug/<project name>.elf`.

## Flashing

To flash the firmware, verify it, and reset the board:

```
STM32_Programmer_CLI -c port=SWD -w build/Debug/<project name>.elf -v -rst
```

- `-c port=SWD` connects through the ST-Link using SWD
- `-w` writes the file to flash
- `-v` verifies the write
- `-rst` resets the board so the new firmware starts running

## Debugging with GDB

Debugging needs two terminals: one running the **GDB server**, which talks to
the board, and one running **GDB**, which you type commands into.

### 1. Start the GDB Server

```
ST-LINK_gdbserver -p 61234 -d -e -cp <CubeCLT install folder>/STM32CubeProgrammer/bin
```

- `-p 61234` is the port GDB connects to
- `-d` uses the SWD debug interface
- `-e` keeps the server running after GDB disconnects
- `-cp` is the path to STM32CubeProgrammer, which the server needs to flash
  the chip

Leave this terminal open.

### 2. Connect GDB

In a second terminal, from the root of your project:

```
arm-none-eabi-gdb build/Debug/<project name>.elf
```

Then, at the `(gdb)` prompt:

```
target remote localhost:61234
load
monitor reset
break main
continue
```

This connects to the board, flashes the firmware, resets the chip, sets a
breakpoint at `main`, and runs until it is reached.

### Common GDB Commands

| Command | Short form | Description |
| --- | --- | --- |
| `break <function or file:line>` | `b` | Set a breakpoint, e.g. `b main.c:120` |
| `info breakpoints` | `i b` | List breakpoints |
| `delete <number>` | `d` | Remove a breakpoint |
| `continue` | `c` | Run until the next breakpoint |
| `next` | `n` | Run the next line, stepping over function calls |
| `step` | `s` | Run the next line, stepping into function calls |
| `finish` | | Run until the current function returns |
| `Ctrl+C` | | Pause the running program |
| `print <expression>` | `p` | Print a variable or expression, e.g. `p sensor.value` |
| `display <expression>` | | Print an expression every time the program stops |
| `watch <variable>` | | Stop whenever a variable changes |
| `backtrace` | `bt` | Show the chain of function calls that led here |
| `info registers` | `i r` | Show CPU registers |
| `x/<n>xw <address>` | | Show `n` words of memory in hex, e.g. `x/4xw 0x40020000` |
| `monitor reset` | | Reset the chip |
| `load` | | Flash the current `.elf` file again |
| `quit` | `q` | Exit GDB |

After changing your code, rebuild in another terminal, then run `load` and
`monitor reset` in GDB. There's no need to restart GDB or the server.

## Debugging a HardFault

If the firmware crashes, it usually ends up stuck in `HardFault_Handler` (in
`stm32xxxx_it.c`). To find out why:

1. Set a breakpoint on the handler before running: `break HardFault_Handler`
2. When it stops there, run `backtrace` to see which function caused it.
3. Check the fault status register to see what kind of fault it was:

   ```
   x/wx 0xE000ED28
   ```

   Look up the bits of this register (the CFSR) in the ARM Cortex-M
   documentation. Common causes are dereferencing a null or invalid pointer,
   a stack overflow, and dividing by zero.

## Using VSCode

Typing GDB commands works anywhere, but a graphical debugger is often easier.
In VSCode, you can use either of these extensions:

- **STM32 VS Code Extension** (official, from ST), which uses STM32CubeCLT
  directly and can import STM32CubeMX CMake projects.
- **Cortex-Debug**, a general debugger for ARM microcontrollers. Set its
  `servertype` to `stlink` (or `openocd`). Point its `svdFile` setting at your
  chip's SVD file from the CubeCLT install to view peripheral registers by name.

Both give you breakpoints, stepping, variable watches and register views from
inside the editor.
