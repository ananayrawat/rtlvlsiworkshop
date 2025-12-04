Day 2: Timing Libraries, Synthesis Approaches, and Flip-Flop RTL Design

Day 2 covers three important concepts:

Understanding the SKY130 timing library file (sky130_fd_sc_hd__tt_025C_1v80.lib)

Comparing hierarchical and flattened synthesis methods

Writing clean, synthesis-friendly flip-flop RTL

Timing Libraries
SKY130 PDK Overview

The SKY130 PDK is an open-source 130 nm CMOS process design kit from SkyWater.
It provides:

Device models

Standard-cell libraries

Timing, power, and variation data

All of this information is essential for accurate digital IC design and synthesis.

Naming Breakdown: tt_025C_1v80

tt → Typical process corner

025C → Characterized at 25°C

1v80 → Nominal supply voltage is 1.8 V

This naming scheme describes the exact PVT (Process–Voltage–Temperature) conditions used to generate the timing data in the .lib file.

Opening and Inspecting the .lib File

Install a GUI editor if needed:

sudo apt install gedit


Open the Liberty timing file:

gedit sky130_fd_sc_hd__tt_025C_1v80.lib


Inside the file you will find:

Standard-cell names and logic functions

Pin directions and load capacitances

Setup/hold timing tables

Power and timing arcs

Hierarchical vs. Flattened Synthesis
Hierarchical Synthesis

Definition: Preserves the module structure of the RTL design.

Characteristics:

Synthesizes each module while keeping boundaries intact

Easy to debug and trace signals back to RTL

Faster on large designs

Great for modularity and IP reuse

Limitations:

Less cross-module optimization

Some full-chip metrics require extra configuration

Flattened Synthesis

Definition: Removes hierarchy and produces a single-level netlist.

Characteristics:

Global optimization across all modules

Better timing and logic optimization opportunities

Limitations:

Longer runtime

Debugging is more difficult since structure is lost

Netlist can become very large

Key Differences
Aspect	Hierarchical	Flattened
Hierarchy	Preserved	Removed
Optimization	Module-level	Full design
Runtime	Faster for large designs	Slower for large designs
Debugging	Easier	Harder
Output	Modular netlist	Single large netlist
Best for	Modularity, analysis	Maximum optimization
Flip-Flop RTL Coding Styles

Flip-flops form the core of synchronous logic. Clean RTL ensures proper inference by synthesis tools.

Asynchronous Reset D Flip-Flop
module dff_asyncres ( input clk, input async_reset, input d, output reg q );
  always @(posedge clk or posedge async_reset) begin
    if (async_reset)
      q <= 1'b0;
    else
      q <= d;
  end
endmodule


Reset overrides everything and forces q = 0 immediately

Data d is sampled on the rising edge of clk

Asynchronous Set D Flip-Flop
module dff_async_set ( input clk, input async_set, input d, output reg q );
  always @(posedge clk or posedge async_set) begin
    if (async_set)
      q <= 1'b1;
    else
      q <= d;
  end
endmodule


When async_set is asserted, q is forced to 1

Synchronous Reset D Flip-Flop
module dff_syncres ( input clk, input sync_reset, input d, output reg q );
  always @(posedge clk) begin
    if (sync_reset)
      q <= 1'b0;
    else
      q <= d;
  end
endmodule


Reset is sampled only on clock edges (synchronous behavior)

Simulation and Synthesis Workflow
Icarus Verilog Simulation

Compile:

iverilog dff_asyncres.v tb_dff_asyncres.v


Run:

./a.out


View waveform:

gtkwave tb_dff_asyncres.vcd

Synthesis with Yosys and SKY130

Start Yosys:

yosys


Inside the Yosys prompt:

Load Liberty file:

read_liberty -lib /path/to/sky130_fd_sc_hd__tt_025C_1v80.lib


Load RTL:

read_verilog /path/to/dff_asyncres.v


Run generic synthesis:

synth -top dff_asyncres


Map flip-flops:

dfflibmap -liberty /path/to/sky130_fd_sc_hd__tt_025C_1v80.lib


Technology mapping + optimization:

abc -liberty /path/to/sky130_fd_sc_hd__tt_025C_1v80.lib


View synthesized design:

show


This flow ensures your flip-flop RTL is correctly mapped to SKY130 standard-cell flip-flops and produces a timing-accurate gate-level netlist.
