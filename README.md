# PrintNC LinuxCNC Configuration

This directory contains the configuration files for a PrintNC CNC machine running on a Raspberry Pi 5 with a Mesa 7i96 Ethernet card.

> [!NOTE]
> For detailed axis specifications, pinout tables, and feature implementation details, please see **[AGENTS.md](AGENTS.md)**.

## Hardware Config
- **Controller**: Raspberry Pi 5
- **Interface Card**: Mesa 7i96 (IP: `10.10.10.10`)
- **Machine Type**: PrintNC (XYYZA Kinematics)

## Files
- `printnc.ini`: Main configuration file.
- `printnc.hal`: Hardware Abstraction Layer file.
- `custom.hal`: Placeholder for user customisations.
- `postgui_call_list.hal`: Placeholder for post-GUI logic.

## Usage
To start the machine configuration:
1.  **Prerequisite**: Ensure your user is in the `dialout` group for VFD access:
    ```bash
    sudo usermod -a -G dialout $USER
    # Reboot required
    ```
2.  **Prerequisite**: Create the G-code storage directory:
    ```bash
    mkdir -p ~/linuxcnc/nc_files
    ```
3.  **Launch**:
    ```bash
    linuxcnc printnc.ini
    ```

## Known Hardware Settings
*   **VFD**: The YL620 VFD **MUST** be set to Slave ID **10** (`P03.02`) and Baud **9600** (`P03.00=3`).
*   **Wiring**: If VFD fails to connect, try swapping the A+ and B- wires on the USB adapter.

See [AGENTS.md](AGENTS.md) for full pinout and axis configuration settings.
