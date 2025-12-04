Verilog Synthesis — Preventing Latches, Using Loops, and Building Scalable Hardware

Day 5 focuses on common RTL pitfalls that cause unintended latches, and introduces synthesizable loops and generate constructs for scalable hardware design. The labs walk through both incorrect and correct coding practices.

Table of Contents

1. Conditional Logic

2. Unwanted Latch Creation

3. Conditional Labs

4. For Loops

5. Generate Constructs

6. Ripple Carry Adder Basics

7. Advanced Loop Labs

Summary

1. Conditional Logic

if-else constructs describe decision-making behavior in procedural blocks.

Template
if (cond1) begin
    // Executes when cond1 is true
end else if (cond2) begin
    // Alternate path
end else begin
    // Default assignment
end


🔹 Important: Every signal in a combinational always @(*) block must be assigned under all input conditions.
Failure to do so results in latch inference.

2. Unwanted Latch Creation

Latches occur when signals inside combinational blocks aren’t assigned for certain conditions.

Incorrect
always @(*) begin
    if (enable)
        out = data;   // No assignment when enable=0
end


The compiler infers a latch to “hold” the previous value.

Correct
always @(*) begin
    case (enable)
        1'b1: out = data;
        default: out = 1'b0;  // Ensures output is always driven
    endcase
end

3. Conditional Labs
Lab 1: Missing Assignment Path
module missing_path(input i0, i1, i2, output reg y);
always @(*) begin
    if (i0) y <= i1;   // Missing default → latch inferred
end
endmodule

<img width="1276" height="458" alt="Screenshot 2025-12-04 112046" src="https://github.com/user-attachments/assets/45f0d43f-bfcc-42da-a78c-30d1d6b42a6b" />


Lab 2: Synthesized Result
<img width="609" height="300" alt="Screenshot 2025-12-04 112336" src="https://github.com/user-attachments/assets/51354ecb-10d5-4bfb-96a5-6246a0c9d8b5" />

Lab 3: Incomplete Condition Chain
module chain_missing(input i0,i1,i2,i3, output reg y);
always @(*) begin
    if (i0) y <= i1;
    else if (i2) y <= i3;   // Case missing when (i0=0,i2=0)
end
endmodule
<img width="1276" height="464" alt="Screenshot 2025-12-04 113153" src="https://github.com/user-attachments/assets/16b1eaf9-0448-4ebb-a59f-5d84b6fe2058" />


Lab 4: Synthesized Netlist
<img width="631" height="339" alt="Screenshot 2025-12-04 113437" src="https://github.com/user-attachments/assets/818769e5-1416-4600-b3b4-f60cb6527f6e" />

Lab 5: Fully Specified Case Block
module safe_case(input i0,i1,i2, input [1:0] sel, output reg y);
always @(*) begin
    case(sel)
        2'b00: y = i0;
        2'b01: y = i1;
        default: y = i2;   // Complete coverage
    endcase
end
endmodule
<img width="1004" height="451" alt="Screenshot 2025-12-04 113617" src="https://github.com/user-attachments/assets/79f54163-c13b-412e-a5a9-2b4f8f2d321f" />


Lab 6
<img width="598" height="192" alt="Screenshot 2025-12-04 113734" src="https://github.com/user-attachments/assets/516b5dc2-dfbb-4a17-a02d-b0fc6158a4cf" />

Lab 7: Overlapping Case Patterns
module overlap_case(input i0,i1,i2,i3, input [1:0] sel, output reg y);
always @(*) begin
    case(sel)
        2'b00: y = i0;
        2'b01: y = i1;
        2'b10: y = i2;
        2'b1?: y = i3;   // Wildcard → priority behavior
    endcase
end
endmodule


Lab 8: Selective Assignments (Latch on x)
module selective_assign(input i0,i1,i2, input [1:0] sel, output reg y,x);
always @(*) begin
    case(sel)
        2'b00: begin y=i0; x=i2; end
        2'b01: y = i1;       // x not assigned → latch
        default: begin x=i1; y=i2; end
    endcase
end
endmodule


Partial Case

Bad Case

<img width="973" height="376" alt="Screenshot 2025-12-04 113903" src="https://github.com/user-attachments/assets/6d59f9fb-6032-4484-9ed4-8905936ffd47" />

<img width="606" height="295" alt="Screenshot 2025-12-04 113953" src="https://github.com/user-attachments/assets/6c3e9a2c-f8e1-407c-abc0-687baee739b8" />




4. For Loops

Synthesizable loops allow repeated logic as long as the iteration range is constant.

Example: 4-Input Selector
module mux4_loop(input [3:0] inputs, input [1:0] select, output reg out);
integer idx;
always @(*) begin
    out = 1'b0;
    for (idx=0; idx<4; idx=idx+1)
        if (idx == select) out = inputs[idx];
end
endmodule

5. Generate Constructs

generate blocks create hardware during elaboration, letting you replicate modules easily.

Pattern
genvar i;
generate
    for (i=0; i<WIDTH; i=i+1) begin : adder_array
        full_adder fa_inst (
            .a(a[i]), .b(b[i]), .sum(sum[i])
        );
    end
endgenerate

<img width="1000" height="418" alt="Screenshot 2025-12-04 114737" src="https://github.com/user-attachments/assets/a97ae49a-380c-4f6e-9767-01967c82cb04" />
<img width="599" height="327" alt="Screenshot 2025-12-04 114856" src="https://github.com/user-attachments/assets/ccfc3ee0-7230-4443-bc9c-864798bf60cb" />




6. Ripple Carry Adder Basics

A ripple carry adder chains full adders so that each stage’s carry feeds the next.

FA0 → carry1

FA1(cin = carry1) → carry2

FA2(cin = carry2) → carry3

Continues until MSB


7. Advanced Loop Labs
Lab 9: 4-Way Selector Using Loop
module mux4_param(input i0,i1,i2,i3, input [1:0] sel, output reg y);
wire [3:0] packed = {i3,i2,i1,i0};
integer idx;
always @(*) begin
    for (idx=0; idx<4; idx=idx+1)
        if (idx == sel) y = packed[idx];
end
endmodule

Lab 10: 8-Way Distributor (Case)
module demux8_case(output [7:0] outs, input [2:0] sel, input data);
reg [7:0] result;
assign outs = result;

always @(*) begin
    result = 8'b0;
    case(sel)
        3'd0: result = data;
        3'd1: result = data;
        // ...
        3'd7: result = data;
    endcase
end
endmodule


Lab 11: 8-Way Distributor (Loop)
module demux8_loop(output [7:0] outs, input [2:0] sel, input data);
reg [7:0] result;
assign outs = result;
integer idx;

always @(*) begin
    result = 8'b0;
    for (idx=0; idx<8; idx=idx+1)
        if (idx == sel) result[idx] = data;
end
endmodule
<img width="981" height="422" alt="Screenshot 2025-12-04 115050" src="https://github.com/user-attachments/assets/1984591b-97f8-4cc4-9266-93206b335b32" />
<img width="608" height="350" alt="Screenshot 2025-12-04 115148" src="https://github.com/user-attachments/assets/3b0d512e-b3c7-4609-a64e-496c53cfdfc6" />


Lab 12: 8-Bit Adder Chain (Generate)
module adder_chain(input [7:0] op1, op2, output [8:0] result);
wire [7:0] carries, partial_sums;

full_adder first (.a(op1), .b(op2), .cin(1'b0),
                  .sum(partial_sums), .cout(carries));

genvar bit;
generate
    for (bit=1; bit<8; bit=bit+1) begin : adder_stages
        full_adder stage (
            .a(op1[bit]), .b(op2[bit]),
            .cin(carries[bit-1]),
            .sum(partial_sums[bit]), .cout(carries[bit])
        );
    end
endgenerate

assign result = {carries, partial_sums};
endmodule

module full_adder(input a,b,cin, output sum, cout);
assign {cout, sum} = a + b + cin;
endmodule
<img width="984" height="443" alt="Screenshot 2025-12-04 115540" src="https://github.com/user-attachments/assets/f9fc873e-b4e0-406d-85bd-2891bcfe292b" />

<img width="606" height="389" alt="Screenshot 2025-12-04 115701" src="https://github.com/user-attachments/assets/b705e6f6-d00a-4ef7-b429-6a2818ab09fa" />
