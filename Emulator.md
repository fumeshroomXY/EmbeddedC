# Simulator
A simulator models the **logical behavior** of a system, **not its exact hardware**.

Characteristics
| Feature                | Simulator                   |
| ---------------------- | --------------------------- |
| Instruction execution  | Approximated                |
| Timing accuracy        | ❌ (usually inaccurate)      |
| Peripheral behavior    | Simplified                  |
| CPU registers          | Often abstracted            |
| Real hardware fidelity | Low–medium                  |
| Speed                  | Fast                        |
| Use case               | Learning, algorithm testing |

Example
- Simulating a UART:
```text
send byte → print to terminal
```


Tools:
- Proteus (logic simulation)
- MATLAB/Simulink
- Instruction-level CPU simulators


# Emulator
An emulator reproduces the **real hardware behavior** as closely as possible.

Characteristics
| Feature               | Emulator                 |
| --------------------- | ------------------------ |
| Instruction execution | Exact                    |
| Timing accuracy       | ✅ (often cycle-accurate) |
| Peripheral behavior   | Realistic                |
| CPU registers         | Exact                    |
| Hardware fidelity     | High                     |
| Speed                 | Slower                   |
| Use case              | Firmware debugging       |

Example
- CPU executes real instruction encodings
- Memory-mapped registers behave like real MCU
- Interrupt latency modeled

Tools:
- QEMU
- Renode
- Keil μVision

Debugger hardware devices like J-Link, ST-Link are **NOT** emulators. They:  
- Run code on real hardware
- Provide debug access (halt, step, read registers)

This is called in-circuit debugging.



