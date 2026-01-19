**MCU** — Microcontroller Unit   
A **single** chip that integrates CPU + memory + peripherals for control-oriented tasks.

**MPU** — Microprocessor Unit  
A CPU-centric chip that relies on **external** memory and peripherals, designed for high-performance systems.

MCU vs MPU Summary Table
| Aspect                       | **MCU (Microcontroller Unit)**                  | **MPU (Microprocessor Unit)**                      |
| ---------------------------- | ----------------------------------------------- | -------------------------------------------------- |
| Primary purpose              | Control-oriented embedded tasks                 | High-performance application processing            |
| Integration                  | CPU + Flash + RAM + peripherals on one chip     | Mainly CPU; external memory & peripherals required |
| Typical CPU cores            | ARM Cortex-M                                    | ARM Cortex-A, x86                                  |
| Clock speed                  | Low–medium (tens to hundreds of MHz)            | High (hundreds of MHz to GHz)                      |
| Memory                       | On-chip Flash & SRAM                            | External DRAM, Flash                               |
| MMU (Memory Management Unit) | ❌ Usually no                                  | ✅ Yes                                              |
| MPU (Memory Protection Unit) | Sometimes                                       | Sometimes                                          |
| Virtual memory               | ❌ No                                            | ✅ Yes                                              |
| Cache                        | Small or none                                   | Large, multi-level caches                          |
| Operating system             | Bare-metal, RTOS (FreeRTOS, Zephyr)             | Linux, Android                                     |
| Real-time determinism        | High                                            | Lower                                              |
| Boot time                    | Very fast (ms)                                  | Slower (seconds)                                   |
| Power consumption            | Very low                                        | High                                               |
| Cost                         | Low                                             | Higher                                             |
| PCB complexity               | Simple                                          | Complex                                            |
| Typical development          | Flash firmware directly                         | Bootloader + OS + applications                     |
| Example products             | STM32, AVR, MSP430                              | i.MX6/8, AM335x, Raspberry Pi SoC                  |
| Typical applications         | IoT, motor control, appliances, automotive ECUs | Gateways, routers, HMIs, smart devices             |
