
# BabySoc Functional Modelling

The VSDBabySoC is a compact System-on-Chip (SoC) design that integrates a RISC-V processor (rvmyth), a Phase-Locked Loop (PLL), and a Digital-to-Analog Converter (DAC) module. This project showcases the integration of these IP cores and focuses on simulating and verifying the system’s functionality through both pre-synthesis and post-synthesis simulations.

---

## 📂 Structure

```
VSDBabySoC/
├── src/
│   ├── include/        # Header files (*.vh) with macros and parameter definitions
│   │   ├── sandpiper.vh
│   │   └── other header files...
│   │
│   ├── module/         # Verilog modules for each SoC component
│   │   ├── vsdbabysoc.v   # Top-level SoC integration module
│   │   ├── rvmyth.v       # RISC-V core
│   │   ├── avsdpll.v      # PLL module
│   │   ├── avsddac.v      # DAC module
│   │   └── testbench.v    # Testbench for simulation
│
├── output/             # Simulation waveforms, logs, or synthesized results
│
└── compiled_tlv/       # Optional: Intermediate compiled files from TL-Verilog/SandPiper
```

---


---

## ⚙️ Requirements

Before running simulations, make sure you have the following tools installed:

- **Icarus Verilog** – for compiling and running simulations  
- **GTKWave** – for viewing waveform files  
- **Unix-like OS (Linux/macOS)** – recommended environment

### Install on Ubuntu:
```bash
sudo apt install iverilog gtkwave
```
### 🚀 Step-by-Step Simulation Guide
### 1️⃣ Setup and Preparation

Clone or set up the project directory as shown above.
```bash
# Clone the VSDBabySoC repository
git clone https://github.com/manili/VSDBabySoC.git

# Change directory into the cloned repository
cd VSDBabySoC

```
### 2️⃣ Module Descriptions

### vsdbabysoc.v — Top-Level SoC Module

Integrates the rvmyth, pll, and dac modules into a single SoC system.
```bash
Inputs

reset — Resets the processor
VCO_IN, ENb_CP, ENb_VCO, REF — PLL control signals
VREFH — Reference voltage for DAC

Outputs

OUT — Analog output from DAC
CLK — Clock signal from PLL

Internal Connections

RV_TO_DAC — 10-bit bus connecting RISC-V core output to DAC input
```
### rvmyth.v — RISC-V Core

Implements a simple RISC-V processor that generates a 10-bit digital signal for DAC conversion.
```bash

Inputs

CLK — Clock from PLL
reset — Resets the core

Output

OUT — 10-bit digital output signal
```
 ### avsdpll.v — PLL Module

Generates a stable clock signal for the RISC-V core and other modules.
```bash
Inputs

VCO_IN, ENb_CP, ENb_VCO, REF

Output

CLK — Stable clock signal
```
### avsddac.v — DAC Module

Converts the 10-bit digital output from the RISC-V core into an analog voltage signal.
```bash
Inputs

D — 10-bit digital input
VREFH — Reference voltage

Output

OUT — Analog output signal
```
### 3️⃣ Testbench

The testbench.v module verifies the complete SoC by:

Initializing signals
Generating clock and reset
Dumping waveform data for simulation results

Generated Output Files

pre_synth_sim.vcd — Pre-synthesis waveform
post_synth_sim.vcd — Post-synthesis waveform

## Simulation Steps
### 🔹 Pre-Synthesis Simulation

Run:
```bash
iverilog -o output/pre_synth_sim/pre_synth_sim.out -DPRE_SYNTH_SIM \
    -I src/include -I src/module \
    src/module/testbench.v src/module/vsdbabysoc.v

cd output/pre_synth_sim
./pre_synth_sim.out

```
### Explanation:

-DPRE_SYNTH_SIM defines a macro for conditional compilation.

The simulation generates pre_synth_sim.vcd, viewable in GTKWave.

### View waveform:
<img width="1920" height="923" alt="pre_synth_sim" src="https://github.com/user-attachments/assets/e1b8a7d8-b94a-4cf5-90dd-8866b5bf65dc" />


This GTKWave snapshot shows the **pre-synthesis simulation results** of the VSDBabySoC testbench.

- The **input signals** (`CLK`, `reset`) and **output signals** (`OUT[9:0]`) are traced.  
- The digital signals from the **RISC-V core** propagate correctly, driving the **DAC outputs**.  
- The waveform confirms **functional correctness before synthesis**, ensuring that the RTL matches the expected behavior.

## Possible Causes and Debugging Steps

### 1. Clock Signal Issues
**Possible Cause:**  
The clock signal may not be properly defined or generated, leading to missing or irregular clock pulses.

**Debugging Steps:**  
- Ensure that the clock signal is correctly defined and toggling continuously in the testbench.  
- Verify the clock frequency and confirm that it meets the design’s timing requirements.  
- Inspect the waveform to confirm the presence of a stable clock signal throughout the simulation.  

---

### 2. Reset Signal Issues
**Possible Cause:**  
If the reset signal remains asserted (active) for the entire simulation, the design may stay in reset mode and produce no output.

**Debugging Steps:**  
- Verify that the reset signal is only asserted during initialization and deasserted afterward.  
- Check the waveform to ensure the reset is inactive after the setup period.  

---

### 3. Enable Signal Configuration
**Possible Cause:**  
Some designs use enable signals to control circuit operation. If the enable signal is active for only one clock cycle, the output will also be limited to that single cycle.

**Debugging Steps:**  
- Identify any enable signals in the design that control output activation.  
- Make sure the enable signal remains active for the desired duration.  
- Observe the enable signal in the waveform viewer to verify correct timing and duration.  

---

### 4. Simulation Time Limit
**Possible Cause:**  
If the simulation run time is too short, it may only include a single clock cycle of activity.

**Debugging Steps:**  
- Increase the simulation time to capture multiple clock cycles and observe extended behavior.  
- Ensure that the simulation stop time allows sufficient duration for complete testing.  

---

### 5. Waveform Viewer Settings
**Possible Cause:**  
The waveform viewer might be set to show a narrow time window, giving the impression that output occurs for only one cycle.

**Debugging Steps:**  
- Expand the visible time range in the waveform viewer.  
- Confirm that all relevant signals are displayed for the entire simulation duration.  

### 🔹 Post-Synthesis Simulation

Run:
```bash
iverilog -o output/post_synth_sim/post_synth_sim.out -DPOST_SYNTH_SIM \
    -I src/include -I src/module \
    src/module/testbench.v output/synthesized/vsdbabysoc.synth.v

cd output/post_synth_sim
./post_synth_sim.out

```
### synthesis:
<img width="1920" height="923" alt="Screenshot from 2025-10-04 22-14-05" src="https://github.com/user-attachments/assets/63233909-b684-4db7-b7b6-f04464d4bef0" />

### post synthesis waveform:
<img width="1920" height="923" alt="post_synth_sim" src="https://github.com/user-attachments/assets/f1e72bf6-8bf1-4329-8c29-b2ef6c72fde9" />

This schematic represents the **post-synthesis netlist** of the VSDBabySoC design, generated using **Yosys**.

- The **PLL (avsdpll)** takes inputs (`ENb_CP`, `ENb_VCO`, `REF`, `VCO_IN`) and generates a clock (`CLK`).  
- The **RISC-V core (rvmyth)** receives the clock and reset signals, producing digital output (`RV_TO_DAC`).  
- The **DAC (avsddac)** converts the core’s digital output into an analog signal (`OUT`) with reference voltage (`VREFH`).  
- Finally, all modules are integrated at the **top-level SoC (vsdbabysoc)**, clearly showing the **data/control flow** between IPs.

## 📚 Summary

The **VSDBabySoC** provides a hands-on example of integrating digital and analog IP cores in a single SoC.
It helps learners understand how a **processor**, **clock generator**, and **DAC module** interact in a unified design, verified through both **pre-synthesis** and **post-synthesis** simulations.

