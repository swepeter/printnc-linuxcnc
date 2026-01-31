# PrintNC Configuration Details

This file contains the detailed technical specifications and pinout configurations for the PrintNC LinuxCNC setup.

## Axis Specifications

| Axis | Joint | Drive System | Scale (step/mm) | Max Vel (mm/min) | Max Accel (mm/s²) | Homing Seq |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **X** | 0 | 2010 Ball Screw | 160.0 | 4000 | 750 | 1 |
| **Y** | 1 | 2010 Ball Screw | 160.0 | 4000 | 750 | -2 |
| **Y** | 2 | 2010 Ball Screw | 160.0 | 4000 | 750 | -2 |
| **Z** | 3 | 1610 Ball Screw | 400.0 | 3000 | 250 | 0 (First) |
| **A** | 4 | Rotary | 8.88~ | 21600 (deg) | 1200 | None |

*Note: Y axis is tandem (Joints 1 & 2). A axis is rotary.*

## Mesa 7i96 Pinout

### StepGens (Output)
- **StepGen 0**: X Axis
- **StepGen 1**: Y1 Axis
- **StepGen 2**: Y2 Axis
- **StepGen 3**: Z Axis
- **StepGen 4**: A Axis

### Inputs (GPIO)
These are configured as standard inputs in `printnc.hal`:

| Pin | Function | Signal Name | Logic |
| :--- | :--- | :--- | :--- |
| **Input 0** | X Home Switch | `home-x` | `in_not` (Active Low/NC) |
| **Input 1** | Y1 Home Switch | `home-y` | `in_not` (Active Low/NC) |
| **Input 2** | Y2 Home Switch | `home-y2` | `in_not` (Active Low/NC) |
| **Input 3** | Z Home Switch | `home-z` | `in_not` (Active Low/NC) |
| **Input 4** | Tool Length Sensor | `length-in` | `in_not` (Active Low/NC) |
| **Input 5** | Touch Probe | `touch-probe` | `in_not` (Active Low/NC) |

*Note: The Tool Length Sensor and Touch Probe are combined via logic gates into `motion.probe-input`.*
*Note: `touch-probe` is also connected to `motion.digital-in-00`.*

## Final Configuration Settings (Verified)

### YL620 VFD Settings (Modbus RTU)
*   **Driver**: `mb2hal`
*   **Port**: `/dev/ttyUSB0`
*   **Baud Rate**: **9600** (Set P03.00 = 3 on VFD)
*   **Slave ID**: **10** (Set P03.02 = 10 on VFD)
*   **Protocol**: 8N1
*   **Registers**:
    *   **Control (Write)**: `8192` (0x2000) - Start/Stop/Fwd/Rev
    *   **Frequency (Write)**: `8193` (0x2001) - Set Speed
    *   **Frequency (Read)**: `8203` (0x200B) - Actual Speed
    *   **Functions**: Uses Function 16 (Write Multiple) and Function 03 (Read Holding).

### Axis Configuration
| Axis | Max Limit | Home Offset | Scale | Invert Output? |
| :--- | :--- | :--- | :--- | :--- |
| **X** | 1335.0 | 1335.0 | 160.0 | **TRUE** |
| **Y** | 1420.0 | 0.0 (Y1=1.0) | 160.0 | False |
| **Z** | 0.01 (Min -140) | 0.0 | 400.0 | False |
| **A** | N/A | N/A | 8.88 | **TRUE** |

### Signal Wiring & Logic
| Function | Physical Pin | Enabled By | LinuxCNC Signal |
| :--- | :--- | :--- | :--- |
| **Spindle VFD** | RS485 (USB) | Always | `YL620...` |
| **Laser PWM** | P1-03 (PWM0) | `halui.machine.is-on` | `hm2...pwmgen.00` |
| **Spindle Release** | SSR 00 | `M64 P0` | `spindle-release` |
| **Spindle Lock** | SSR 01 | `M64 P1` | `spindle-lock` |
| **Spindle Clean** | SSR 02 | `M64 P2` | `spindle-clean` |
| **Tool Rack** | SSR 03 | `M64 P3` | `tool-rack` |
| **Probe Enable** | Internal Logic | `M64 P04` (Tool) / `P05` (Top) | `probe-enable` |
| **Tool Sensor** | Input 04 | Always Active (Gated) | `motion.probe-input` |
| **Touch Probe** | Input 05 | Gated by `probe-enable` | `motion.probe-input` |
