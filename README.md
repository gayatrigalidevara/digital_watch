# Design and Implementation of a 24-Hour Digital Watch with Stopwatch and Alarm Using Verilog HDL

A modular, hardware-synthesizable digital watch system designed entirely in Verilog HDL. This system incorporates a real-time 24-hour clock, an independent stopwatch with sub-second resolution capability, and a programmable comparator-driven hardware alarm on a unified display bus.

---

## ⚡ Interactive Hardware Mockup
Click the link below to open and interact with the digital watch UI simulation mockup:

👉 **[Launch Digital Watch Interactive Mockup Panel (index.html)](index.html)**

---

## 🛠️ Hardware Button Control Mappings

| Button | Target Action / Operational Behavior |
| :--- | :--- |
| **`MODE`** | Cycles cleanly through modules: `Clock (0)` $\rightarrow$ `Stopwatch (1)` $\rightarrow$ `Alarm View (2)` $\rightarrow$ loops back. |
| **`START`** | Controls stopwatch execution intervals (Run/Pause) or silences an active alarm trigger. |
| **`RESET`** | Formats and zeros the active module register block context currently mounted to the display bus. |

---

## 📐 Modular System Architecture

The design balances complex operational scaling by segregating roles across eight decoupled synthesizable RTL blocks under a single top-level scheduler:

### Module Responsibilities Breakdown

*   **`integration_of_digitital_watch`**: Houses the 2-bit state tracking register (`count`), orchestrates top-level display-bus multiplexing, and controls asynchronous 100 Hz cathode anode digit switching to achieve hardware **Persistence of Vision (POV)**.
*   **`digital_clock_24hrs`**: Drives standard `HH:MM:SS` sequential ripple-carry counting loop with instant roll-back at `23:59:59`. Contains a baseline bitwise hardware comparator comparing real-time values to alarm configurations.
*   **`stopwatch`**: Gated by an enable flip-flop (`en`). Handles timing calculations while remaining structurally distinct from real-world temporal scaling loops.
*   **`debounce`**: Mitigates real-world switch bouncing by utilizing a saturating validation counter over multiple consecutive stable rising edges before pulsing `clean_button` lines.
*   **`display`**: Implements a dedicated active-low case statement matrix to translate BCD vectors into matching standard `A-B-C-D-E-F-G-DP` segment buses.

---

## 📈 RTL Implementation Details & Constraints
*   **Target Oscillator Input:** Designed against a nominal 50 MHz structural oscillator board reference. 
*   **Display Logic Configuration:** Decoded for common-cathode layouts, mapped using a 4:1 multiplexed scanning routing algorithm to conserve critical FPGA board I/O pin allocations.
*   **Resource Allocation Safety:** Synthesizes cleanly into standard Look-Up Tables (LUTs) and synchronous flip-flops without creating hazardous combinational latches or unsafe gate logic loops.

---
## 🔬 Simulation and Verification

### Testbench Optimization Principle
To prevent multimillion-cycle loop hanging during structural simulation runs, always override the internal frequency scaling factors inside your local testbenches:
```verilog
// Simulation Parameter Overrides Example
defparam uut.clk_divider_instance.max_count = 32'd5; // Truncates 50MHz scaling step down to 5 cycles for faster waveform validation
```

### Anticipated Simulation Waveforms
1.  **`clk_1hz`**: Steady, uninterrupted square wave pulse validation.
2.  **`digital_clock_24hrs`**: Sequential cascade rollover (`59` sec $\rightarrow$ `+1` min; `23:59:59` $\rightarrow$ `00:00:00`).
3.  **`alarm` / `buzzer`**: High logic level triggered on exact baseline comparison match, pulsing the `buzzer` pins at the source master clock rate until a `push_stop` mask is read.

---
### Project Attribution
*   **Author:** Gayatri Galidevara
*   **Laboratory:** Digital System Design Laboratory Technical Report Documentation
