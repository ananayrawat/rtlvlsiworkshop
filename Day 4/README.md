Day 4: Gate-Level Simulation (GLS), Blocking vs Non-Blocking, Synthesis Mismatches

Day 4 focuses on gate-level verification, Verilog assignment semantics, and debugging synthesis–simulation mismatches with hands-on labs.

📘 Table of Contents

1. Gate-Level Simulation (GLS)

2. Synthesis-Simulation Mismatch

3. Blocking vs Non-Blocking Assignments

3.1 Blocking (=)

3.2 Non-Blocking (<=)

3.3 Comparison

4. Labs

5. Key Takeaways

1. Gate-Level Simulation (GLS)

Gate-Level Simulation verifies that a synthesized netlist behaves exactly like RTL and checks timing behavior using real gate delays.

✔ Purpose

Validate synthesis correctness

Ensure timing closure (setup/hold)

Verify DFT structures (scan, BIST)

✔ When to Run GLS

After synthesis

Before PnR

After signoff timing

✔ GLS Modes
Mode	Purpose
Zero-delay / Unit-delay	Pure functional verification
SDF Back-annotated	Full timing verification
2. Synthesis-Simulation Mismatch

A mismatch occurs when RTL simulation ≠ Gate-level simulation ≠ Hardware.

✔ Common Causes

Non-synthesizable constructs (initial, delays, file I/O)

Incomplete sensitivity lists

Missing else or latch inference

Race conditions

Tool interpretation differences

✔ Fix

Write clean, synthesizable RTL using proper templates.

3. Blocking vs Non-Blocking Assignments
3.1 Blocking (=)
always @(*) begin
    y = a & b; // Immediate update
end


✔ Sequential execution
✔ Use for combinational logic only

3.2 Non-Blocking (<=)
always @(posedge clk) begin
    q <= d; // Scheduled update
end


✔ Parallel, hardware-like behavior
✔ Use for sequential (flip-flops)

3.3 Comparison
Aspect	Blocking (=)	Non-Blocking (<=)
Execution	Immediate	Scheduled (end of timestep)
Behavior	Sequential (like C)	Parallel (like hardware)
Use Case	Combinational logic	Sequential logic
Failure Risk	Latches, race issues	Very low
4. Labs
🔬 Lab 1: Ternary MUX
RTL
module ternary_mux (input i0, i1, sel, output y);
assign y = sel ? i1 : i0;
endmodule

Yosys Flow
read_liberty sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog ternary_mux.v
synth -top ternary_mux
abc -liberty sky130_fd_sc_hd__tt_025C_1v80.lib
write_verilog -noattr ternary_operator_mux_netlist.v

iverilog ../my_lib/verilog_model/primitives.v \
         ../my_lib/verilog_model/sky130_fd_sc_hd.v \
         ternary_mux.v tb_ternary_mux.v
./a.out
gtkwave tb_ternary_mux.vcd

Before Synthesis

<img width="831" height="198" alt="Screenshot 2025-12-04 111517" src="https://github.com/user-attachments/assets/6bb8c69e-b0cd-44fe-8969-08189cf68013" />

After Synthesis

<img width="619" height="488" alt="Screenshot 2025-12-04 103559" src="https://github.com/user-attachments/assets/1e464d72-3aa4-432e-8f35-ce2d28e83ffd" />

🔬 Lab 2: Bad MUX (Incomplete Sensitivity List)
RTL
module bad_mux (input i0, i1, sel, output reg y);
always @(sel) begin // INCORRECT — missing i0, i1
    if (sel) y <= i1;
    else     y <= i0;
end
endmodule

Yosys Flow
read_liberty sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog bad_mux.v
synth -top bad_mux
abc -liberty sky130_fd_sc_hd__tt_025C_1v80.lib
write_verilog -noattr bad_mux_netlist.v

iverilog ../my_lib/verilog_model/primitives.v \
         ../my_lib/verilog_model/sky130_fd_sc_hd.v \
         bad_mux.v tb_bad_mux.v
./a.out
gtkwave tb_bad_mux.vcd

Before Synthesis
<img width="1008" height="443" alt="Screenshot 2025-12-04 104156" src="https://github.com/user-attachments/assets/20c71baf-09d4-4343-8727-3273a6473514" />

After Synthesis
<img width="1241" height="503" alt="image" src="https://github.com/user-attachments/assets/e67f6107-526c-44e1-a77a-63467d80dd86" />
<img width="618" height="202" alt="Screenshot 2025-12-04 111804" src="https://github.com/user-attachments/assets/baa7b15d-02d7-4898-8cfe-3eb4b4f1c72a" />

🔬 Lab 3: Blocking Caveat
RTL
module blocking_caveat (input a, b, c, output reg d);
reg x;
always @(*) begin
    d = x & c; // Uses old x value!
    x = a | b; // Updated too late
end
endmodule

Yosys Flow
read_liberty sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog blocking_caveat.v
synth -top blocking_caveat
abc -liberty sky130_fd_sc_hd__tt_025C_1v80.lib
write_verilog -noattr blocking_caveat_netlist.v

iverilog ../my_lib/verilog_model/primitives.v \
         ../my_lib/verilog_model/sky130_fd_sc_hd.v \
         blocking_caveat.v tb_blocking_caveat.v
./a.out
gtkwave tb_blocking_caveat.vcd

Before Synthesis
<img width="935" height="423" alt="image" src="https://github.com/user-attachments/assets/4d221953-8fc2-4e70-b31a-cc2090d9e5f4" />


After Synthesis
<img width="945" height="388" alt="image" src="https://github.com/user-attachments/assets/752a6942-f3f2-4642-a8ba-d13625788af4" />

5. Key Takeaways

🔑 IMPORTANT

GLS ensures your synthesized netlist matches RTL

Use blocking (=) for combinational logic

Use non-blocking (<=) for sequential logic

Fix incomplete sensitivity lists

Avoid mixing blocking & non-blocking inside the same always block

Always check synthesis warnings
