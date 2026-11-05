# Intro to Firmware

Firmware is the software that runs directly on a microcontroller. At UAARG we
write firmware for **STM32** microcontrollers, a family of ARM Cortex-M chips
made by STMicroelectronics.

Firmware is very low level, and involves interfacing directly with hardware.

This guide goes through things you should be familiar with, how to set up a
firmware development environment, and the typical workflow for developing
firmware.

## Prerequisites

### C Programming

Almost all STM32 firmware is written in C. You don't need to be an expert, but
you should be comfortable with:

- **Basic syntax:** variables, functions, control flow, arrays and structs
- **Fixed-width integer types:** `uint8_t`, `uint16_t`, `uint32_t`, etc. from
  `<stdint.h>`
- **Header and source files:** how `.h` and `.c` files fit together, and how
  `#include` and `#define` work

Good resources for learning C:

- [Beej's Guide to C Programming](https://beej.us/guide/bgc/) (free)
- *The C Programming Language* by Kernighan and Ritchie ("K&R")

### Embedded Systems Basics

You should also have a basic understanding of how microcontrollers work:

- **GPIO:** configuring pins as inputs or outputs
- **Clocks:** the microcontroller and each peripheral run off a clock, and
  peripherals usually have to be enabled before they can be used
- **Interrupts:** how hardware events pause normal execution to run a handler
- **Communication protocols:** UART, I2C, SPI and CAN at a high level

You will also need to be able to understand **PCB Schematics** and **Datasheets**.
These will be the documents which will allow you to understand the hardware 
which you are writing firmware for. If you are ever confused about a 
schematic or a datasheet, don't hesitiate to ask the PCB designer or an 
electrical lead for clarification. 

Basic electronics knowledge (voltage, current, pull-up resistors, and using a
multimeter) also helps a lot when debugging.

## Development Environment

The recommended setup is **STM32CubeIDE**, ST's free all-in-one IDE. It
includes:

- **STM32CubeMX**, a graphical tool for configuring pins, clocks and
  peripherals, which generates the setup code for you
- The **ARM GCC compiler** (`arm-none-eabi-gcc`)
- A **debugger** that connects to the board through an **ST-Link**

You will need an STM32 board and a debug probe to flash and debug it. We use:

- **ST Nucleo development boards**, which have an **ST-Link** debug probe
  built in, so only a USB cable is needed. These are the best place to start.
- **Pico debug probes** (the Raspberry Pi Debug Probe, or a Pico running the
  `debugprobe` firmware) for our custom hardware. These are used through
  [OpenOCD](./firmware-openocd.md).

### Windows

1. Download STM32CubeIDE for Windows from the [ST
   website](https://www.st.com/en/development-tools/stm32cubeide.html). You
   will need to create a free ST account.
2. Run the installer and keep the default options. When asked, allow it to
   install the **ST-Link drivers**.
3. Plug in your board. It should appear in Device Manager as an ST-Link debug
   probe, along with a "Virtual COM Port" for serial output.
4. Install a serial terminal for viewing output from the board, such as
   [PuTTY](https://www.putty.org/). STM32CubeIDE also has a built-in serial
   console.

### Linux

These instructions are for Ubuntu 22.04+ (or similar).

1. Download STM32CubeIDE for Linux from the [ST
   website](https://www.st.com/en/development-tools/stm32cubeide.html). Pick
   the Debian/Ubuntu package.
2. Unzip the download and run the installer:

   ```
   chmod +x st-stm32cubeide_*.sh
   sudo ./st-stm32cubeide_*.sh
   ```

   Accept the licence agreements, and accept when asked to install the ST-Link
   udev rules. These let you access the board without `sudo`.
3. Add your user to the `dialout` group so you can open serial ports, then log
   out and back in:

   ```
   sudo usermod -aG dialout $USER
   ```
4. Plug in your board and check that it is detected:

   ```
   lsusb | grep -i st-link
   ```
5. Install a serial terminal for viewing output from the board:

   ```
   sudo apt install minicom
   minicom -D /dev/ttyACM0 -b 115200
   ```

### Command-Line Toolchain (Optional)

If you prefer working in your own editor (e.g. VSCode), you can install the
command-line tools instead of, or as well as, STM32CubeIDE. You will still want
**STM32CubeMX** to generate the project configuration.

- **Windows:** install
  [STM32CubeCLT](https://www.st.com/en/development-tools/stm32cubeclt.html),
  which bundles the compiler, debugger and flashing tools.
- **Linux:**

  ```
  sudo apt install gcc-arm-none-eabi gdb-multiarch cmake make openocd stlink-tools
  ```

ST also publishes an official STM32 extension for VSCode that works with these
tools.

For more detail, see [Building and Flashing with
OpenOCD](./firmware-openocd.md) and [Debugging with
STM32CubeCLT](./firmware-cubeclt.md).

### Verifying Your Setup

The classic first program is "blinky":

1. In STM32CubeIDE, go to **File → New → STM32 Project** and select your board
   (e.g. your Nucleo model) in the **Board Selector** tab.
2. Accept the default peripheral configuration. On most Nucleo boards, this
   sets up the onboard LED pin as a GPIO output.
3. In `main.c`, inside the `while (1)` loop, add:

   ```c
   /* USER CODE BEGIN 3 */
   HAL_GPIO_TogglePin(LD2_GPIO_Port, LD2_Pin);
   HAL_Delay(500);
   /* USER CODE END 3 */
   ```

   The LED name (`LD2` here) depends on your board. Check the pin labels in the
   `.ioc` file.
4. Click **Run** (or **Debug**) to build the project and flash it to the board.

If the LED blinks, your environment is working.

## Development Workflow

A typical firmware workflow looks like this:

1. **Configure the hardware.** Open the project's `.ioc` file in STM32CubeMX
   and set up the pins, clocks and peripherals you need. This is determined
   from the PCB schematic.
2. **Generate code.** CubeMX generates initialization code using ST's **HAL**
   (Hardware Abstraction Layer) library.
3. **Write your code.** Only write code between `/* USER CODE BEGIN */` and
   `/* USER CODE END */` comments in generated files. Anything outside these
   blocks is overwritten every time code is regenerated. Larger features
   should go in their own `.c`/`.h` files.
4. **Build.** Compile the project and fix any errors or warnings.
5. **Flash.** Upload the firmware to the board through the ST-Link.
6. **Debug.** Use the debugger to set breakpoints, step through code and watch
   variables live. Printing over UART to a serial terminal is also useful for
   checking what the firmware is doing.


If you get stuck, ask the Software or Electrical leads.
