
# BabySoc Functional Modelling

The VSDBabySoC is a compact System-on-Chip (SoC) design that integrates a RISC-V processor (rvmyth), a Phase-Locked Loop (PLL), and a Digital-to-Analog Converter (DAC) module. This project showcases the integration of these IP cores and focuses on simulating and verifying the system’s functionality through both pre-synthesis and post-synthesis simulations.

---

## 📂 Repository Structure

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
<img width="1920" height="923" alt="Screenshot from 2025-10-04 19-24-28" src="https://github.com/user-attachments/assets/1e9b0164-7627-4022-a24f-7206c2d33b69" />


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


## 📚 Summary

The **VSDBabySoC** provides a hands-on example of integrating digital and analog IP cores in a single SoC.
It helps learners understand how a **processor**, **clock generator**, and **DAC module** interact in a unified design, verified through both **pre-synthesis** and **post-synthesis** simulations.

