Day 3: Combinational and Sequential Optimization

Welcome to Day 3 of the VLSI RTL Design & Synthesis Workshop!
Today we explore combinational and sequential optimization techniques used by synthesis tools to improve area, timing, and power.

📘 Table of Contents

1. Constant Propagation

2. State Optimization

3. Cloning

4. Retiming

5. Labs on Optimization

1. Constant Propagation

Constant Propagation is an optimization where the synthesis tool identifies signals driven by constants and pushes them forward, reducing unnecessary logic.
<img width="666" height="382" alt="image" src="https://github.com/user-attachments/assets/ed536fa5-65f5-4207-b05b-79590b90516e" />


✔ How it works

Detect signals tied to 0 or 1

Substitute constants into equations

Remove redundant gates

Simplify logic cones

✔ Benefits

Reduced gate count

Lower power

Better timing

Smaller logic depth

2. State Optimization

State optimization aims to minimize the FSM complexity.


✔ Techniques Used

State Reduction → Remove unreachable or equivalent states

State Encoding → Binary, Gray, One-Hot depending on power/timing/area

Logic Minimization → Reduce next-state/output equations

Power Optimization → Clock gating for idle states

3. Cloning

Cloning duplicates logic to improve timing by reducing fanout or long routing.
<img width="587" height="368" alt="image" src="https://github.com/user-attachments/assets/bff9acb4-94db-45bc-a705-15871bd578f5" />



✔ Workflow

Identify critical paths or high-fanout nets

Duplicate the driver logic

Split loads between clones

Re-run STA to verify improvement

4. Retiming

Retiming shifts registers across combinational logic to balance delays without changing I/O behavior.

✔ Key Concepts

Move FFs forward/backward

Balance logic between pipeline stages

Improve slack and clock frequency

Maintain equivalence with constraints

5. Labs on Optimization

Below are RTL examples demonstrating optimization effects in synthesis.

🔬 Lab 1 — opt_check
module opt_check (input a, input b, output y);
assign y = a ? b : 0;
endmodule


📝 When a = 0, y = 0. Tools simplify the mux.

Add in synthesis script:

opt_clean -purge
<img width="1496" height="649" alt="Screenshot 2025-12-04 200749" src="https://github.com/user-attachments/assets/d4864e2c-bb8e-4b15-a2bf-e54ab2afa79e" />
<img width="1490" height="620" alt="Screenshot 2025-12-04 200815" src="https://github.com/user-attachments/assets/f88ccce6-4cac-49cf-a175-a15f13e947b3" />

<img width="676" height="339" alt="Screenshot 2025-12-04 200841" src="https://github.com/user-attachments/assets/782054f9-6ed6-4911-85b5-1b9fd1b1619a" />




<img width="786" height="236" alt="Screenshot 2025-12-04 200933" src="https://github.com/user-attachments/assets/94c9df37-743d-4392-975d-19a7d23c632c" />



🔬 Lab 2 — opt_check2
module opt_check2 (input a, input b, output y);
assign y = a ? 1 : b;
endmodule


Function:

If a = 1 → y = 1

If a = 0 → y = b

<img width="1441" height="705" alt="Screenshot 2025-12-04 201121" src="https://github.com/user-attachments/assets/141c4c93-5801-4a3d-ab57-caa60cb08ff8" />
<img width="847" height="581" alt="Screenshot 2025-12-04 201142" src="https://github.com/user-attachments/assets/bd5c9514-b977-426c-b3da-fb52bd6221e4" />
<img width="773" height="359" alt="Screenshot 2025-12-04 201203" src="https://github.com/user-attachments/assets/e4191ee9-ece7-4da4-9a77-e388d43cead7" />






🔬 Lab 3 — opt_check3
module opt_check3 (input a, input b, output y);
assign y = a ? 1 : b;
endmodule


Same behavior as Lab 2.

<img width="1448" height="558" alt="Screenshot 2025-12-04 201221" src="https://github.com/user-attachments/assets/a5ccd93a-b0db-4f8b-9cc5-4aab4c392cfd" />
<img width="883" height="458" alt="Screenshot 2025-12-04 201305" src="https://github.com/user-attachments/assets/7adf8393-9b72-488e-9b9f-e24640f902a8" />

<img width="782" height="366" alt="Screenshot 2025-12-04 201316" src="https://github.com/user-attachments/assets/c5d719ff-2130-4835-8713-283e4673a4f5" />




🔬 Lab 4 — opt_check4
module opt_check4 (input a, input b, input c, output y);
assign y = a ? (b ? (a & c) : c) : (!c);
endmodule


📝 Simplified result:

If a = 1 → y = c

If a = 0 → y = ~c

<img width="782" height="366" alt="Screenshot 2025-12-04 201316" src="https://github.com/user-attachments/assets/7232ad9f-799e-4666-b456-78eceeb5171a" />

<img width="847" height="468" alt="Screenshot 2025-12-04 201408" src="https://github.com/user-attachments/assets/3497b400-20eb-4044-8a93-ffcbaae516bf" />

<img width="774" height="348" alt="Screenshot 2025-12-04 201424" src="https://github.com/user-attachments/assets/d90463c9-88ba-48bb-8e5f-bb05cfb66117" />



🔬 Lab 5 — dff_const1
module dff_const1 (input clk, input reset, output reg q);
always @(posedge clk or posedge reset) begin
    if (reset)
        q <= 1'b0;
    else
        q <= 1'b1;
end
endmodule


✦ After reset, flop always loads 1 → tool may simplify.
<img width="1006" height="690" alt="Screenshot 2025-12-04 201533" src="https://github.com/user-attachments/assets/a3b80e20-599c-40c7-b6b2-3c05a351cfc3" />
<img width="706" height="528" alt="Screenshot 2025-12-04 201639" src="https://github.com/user-attachments/assets/023ceb35-7455-49e0-9fad-560a238e632c" />

<img width="601" height="370" alt="Screenshot 2025-12-04 201709" src="https://github.com/user-attachments/assets/69c06046-8d59-42ed-b4c7-625a9264cdbc" />

<img width="790" height="168" alt="Screenshot 2025-12-04 201723" src="https://github.com/user-attachments/assets/585c4a0b-774b-434d-9661-ab6503619830" />
<img width="773" height="561" alt="Screenshot 2025-12-04 201823" src="https://github.com/user-attachments/assets/e9bc1eb8-1cea-4548-9c58-702db10e3eda" />
<img width="1589" height="528" alt="Screenshot 2025-12-04 203032" src="https://github.com/user-attachments/assets/ad72ccde-d1ac-497f-afd2-1e7d505fec3d" />


🔬 Lab 6 — dff_const2
module dff_const2 (input clk, input reset, output reg q);
always @(posedge clk or posedge reset) begin
    if (reset)
        q <= 1'b1;
    else
        q <= 1'b1;
end
endmodule


✔ Output is always 1 → flop is removed and replaced with constant driver.
<img width="991" height="381" alt="Screenshot 2025-12-04 203046" src="https://github.com/user-attachments/assets/3ada79ce-4aec-447b-adc6-9fef1ae7b1d3" />
<img width="768" height="480" alt="Screenshot 2025-12-04 203321" src="https://github.com/user-attachments/assets/47b83afb-d48c-4025-9ae9-2b1666018ec9" />
<img width="954" height="386" alt="Screenshot 2025-12-04 203338" src="https://github.com/user-attachments/assets/099982c4-4131-46a8-88d8-b41b75681aa7" />

<img width="1587" height="550" alt="Screenshot 2025-12-04 203729" src="https://github.com/user-attachments/assets/969202c0-06bc-4957-81da-2c8f35657267" />



Additional Labs
dff_const3

<img width="994" height="417" alt="image" src="https://github.com/user-attachments/assets/efd021ac-7f6d-41ef-b131-2bd0b9e23a31" />
<img width="1277" height="423" alt="image" src="https://github.com/user-attachments/assets/84faff82-a22c-4a30-9df5-5639796e487a" />



dff_const4
<img width="1005" height="404" alt="image" src="https://github.com/user-attachments/assets/a51399a5-1a70-45e4-96fc-35a856b4cb15" />

<img width="609" height="605" alt="image" src="https://github.com/user-attachments/assets/12eeeaa2-d90f-4f60-a6fe-2a4e7f73934e" />



dff_const5
<img width="1001" height="411" alt="image" src="https://github.com/user-attachments/assets/101464fc-3f21-47bb-ae1e-e04bd3fc7a9b" />

<img width="1275" height="312" alt="image" src="https://github.com/user-attachments/assets/289022dd-b566-45dc-a591-eed2f8674255" />



counter_opt
<img width="1278" height="199" alt="image" src="https://github.com/user-attachments/assets/0e8a4e46-5a40-4da3-9236-2751970e63e1" />

📌 Summary
✔ What You Learned Today

Constant Propagation

State Optimization (FSM reduction & encoding)

Cloning for timing improvement

Retiming for sequential balancing

Practical lab examples demonstrating combinational + sequential optimization in synthesis
