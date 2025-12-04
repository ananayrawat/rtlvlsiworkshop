**Day 2: Timing Libraries, Synthesis Styles & Flip-Flop RTL**

**Day 2** covers three essential concepts in digital design using the SKY130 PDK:

Understanding the SKY130 timing library (sky130_fd_sc_hd__tt_025C_1v80.lib)

Comparing hierarchical and flat synthesis strategies

Writing clean, synthesis-friendly flip-flop RTL

1. Timing Libraries
SKY130 PDK Overview

The SKY130 PDK is an open-source 130 nm CMOS platform from SkyWater.
It provides:

Standard cell libraries

Device and timing models

Power/area/variation data

These files are used by synthesis and STA tools for accurate timing analysis.

Decoding tt_025C_1v80

The naming describes the PVT corner:

tt → Typical process

025C → Characterized at 25 °C

1v80 → Operating at 1.8 V

This corner represents "typical" device behavior under nominal conditions.

2. Exploring the .lib Timing File

To view the Liberty file:

Install a GUI editor
sudo apt install gedit

Open the library
gedit sky130_fd_sc_hd__tt_025C_1v80.lib


Inside the .lib, you’ll find:

Standard-cell names and logic functions

Pin directions & input capacitances

Setup/Hold timing tables

Power and leakage models

Internal delays and timing arcs

This file is essential for technology-mapped synthesis.

3. Hierarchical vs. Flattened Synthesis
Hierarchical Synthesis

Definition: RTL module boundaries are retained during synthesis.

Pros

Faster on large designs

Easier debugging (module names preserved)

Great for modular/structured design

Supports IP reuse and incremental builds

Cons

Limited cross-module optimizations

Some reports require more navigation

Flattened Synthesis

Definition: The entire design is collapsed into a single synthesis unit.

Pros

Global optimization across all modules

Can achieve better timing or area

Produces one uniform netlist

Cons

Longer runtime, more memory usage

Harder to debug — hierarchy is lost

Netlist size increases significantly

Key Comparison
Aspect	Hierarchical	Flattened
RTL Structure	Preserved	Removed
Optimization	Per-module	Global
Runtime	Faster on large designs	Slower on large designs
Debugging	Easy	Harder
Netlist	Modular	Single large file
Best For	Analysis, reuse	Maximum optimization
4. Flip-Flop Coding Styles

Clean RTL ensures proper inference of sequential elements.

Asynchronous Reset DFF
module dff_asyncres(input clk, input async_reset, input d, output reg q);
    always @(posedge clk or posedge async_reset) begin
        if (async_reset)
            q <= 1'b0;
        else
            q <= d;
    end
endmodule


Reset is immediate and has priority.

Asynchronous Set DFF
module dff_async_set(input clk, input async_set, input d, output reg q);
    always @(posedge clk or posedge async_set) begin
        if (async_set)
            q <= 1'b1;
        else
            q <= d;
    end
endmodule


Output jumps to 1 as soon as set is asserted.

Synchronous Reset DFF
module dff_syncres(input clk, input sync_reset, input d, output reg q);
    always @(posedge clk) begin
        if (sync_reset)
            q <= 1'b0;
        else
            q <= d;
    end
endmodule


Reset is evaluated only on the clock edge.

5. Simulation Flow (Icarus Verilog)
Compile
iverilog dff_asyncres.v tb_dff_asyncres.v

Run
./a.out

View Waveform
gtkwave tb_dff_asyncres.vcd

6. Synthesis Flow (Yosys + SKY130)

Open yosys:

yosys


Inside Yosys:

Load the library
read_liberty -lib /path/to/sky130_fd_sc_hd__tt_025C_1v80.lib

Read RTL
read_verilog dff_asyncres.v

Generic synthesis
synth -top dff_asyncres

Map flip-flops to SKY130 cells
dfflibmap -liberty /path/to/sky130_fd_sc_hd__tt_025C_1v80.lib

Technology map + optimize
abc -liberty /path/to/sky130_fd_sc_hd__tt_025C_1v80.lib

View netlist
show


This produces a gate-level netlist mapped to real SKY130 library cells—ready for STA or physical design.
