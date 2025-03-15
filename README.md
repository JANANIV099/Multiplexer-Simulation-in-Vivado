# SIMULATION AND IMPLEMENTATION OF MULTIPLEXER

## AIM
To design and simulate a 4:1 Multiplexer (MUX) using Verilog HDL in four different modeling styles—Gate-Level, Data Flow, Behavioral, and Structural—and to verify its functionality through a testbench using the Vivado 2023.1 simulation environment. The experiment aims to understand how different abstraction levels in Verilog can be used to describe the same digital logic circuit and analyze their performance.

## APPARATUS REQUIRED
- **Vivado 2023.1**

## Procedure
### Launch Vivado
- Open Vivado 2023.1 from the Start menu or desktop icon.

### Create a New Project
1. Click **"Create Project"**.
2. Enter the project name (e.g., `Mux4_to_1`) and choose a location. Click **Next**.
3. Select **RTL Project** and click **Next**.
4. Add Verilog files (e.g., `mux4_to_1_gate.v`, `mux4_to_1_dataflow.v`) and enable **"Copy sources into project"**. Click **Next**.
5. Skip the Constraints step and proceed.
6. Select a default FPGA part (e.g., `xc7a35ticsg324-1L`). Click **Next**, then **Finish**.

### Add Source Files (if needed)
- In the Sources window, right-click on **"Design Sources"** → **Add Sources**.
- Add Verilog files and the testbench (`mux4_to_1_tb.v`).

### Check Syntax
- In the Flow Navigator, under **Synthesis**, click **"Run Synthesis"**.
- Correct any errors or warnings, then re-run the synthesis.

### Simulate the Design
- In the Flow Navigator, under **Simulation**, click **"Run Behavioral Simulation"**.

### Analyze Simulation Results
- View waveform signals (e.g., `S1`, `S0`, `A`, `B`, `C`, `D`, etc.).
- Use the waveform controls to zoom, scroll, and analyze results.

### Adjust Simulation Time
- Click **"Simulation"** → **"Simulation Settings"** to modify the run time (e.g., 1000ns).

### Generate Report
- Right-click the simulation window → **"Export Simulation Results"**.

### Save and Document
- Click **File** → **Save Project**.
- Capture screenshots of the waveform window for your lab report.

### Close Simulation
- Go to **"Simulation"** → **"Close Simulation"** when finished.

---

## Logic Diagram
![image](https://github.com/user-attachments/assets/d4ab4bc3-12b0-44dc-8edb-9d586d8ba856)

---

## Truth Table
![image](https://github.com/user-attachments/assets/c850506c-3f6e-4d6b-8574-939a914b2a5f)

---

**CONCLUSION**
In this experiment, a 4:1 Multiplexer was successfully designed and simulated using Verilog HDL across four different modeling styles: Gate-Level, Data Flow, Behavioral, and Structural. The simulation results verified the correct functionality of the MUX, with all implementations producing identical outputs for the given input conditions.

## Verilog Code

### 4:1 MUX Gate-Level Implementation
```verilog
module mux4_to_1_gate (
    input wire A,
    input wire B,
    input wire C,
    input wire D,
    input wire S0,
    input wire S1,
    output wire Y
);
    wire not_S0, not_S1;
    wire A_and, B_and, C_and, D_and;

    not (not_S0, S0);
    not (not_S1, S1);

    and (A_and, A, not_S1, not_S0);
    and (B_and, B, not_S1, S0);
    and (C_and, C, S1, not_S0);
    and (D_and, D, S1, S0);

    or (Y, A_and, B_and, C_and, D_and);
endmodule
OUTPUT:
![Screenshot 2025-03-13 092231](https://github.com/user-attachments/assets/b4413fbc-abc3-4e61-a535-6fd1432952f6)


**4:1 MUX Data Flow Implementation**

module mux4_to_1_dataflow (
    input wire A,
    input wire B,
    input wire C,
    input wire D,
    input wire S0,
    input wire S1,
    output wire Y
);
    assign Y = (~S1 & ~S0 & A) |
               (~S1 & S0 & B) |
               (S1 & ~S0 & C) |
               (S1 & S0 & D);
endmodule
OUTPUT:
![Screenshot 2025-03-13 092232](https://github.com/user-attachments/assets/5183dc84-7184-4f0a-8e5c-8ab5c56c5f9e)


**4:1 MUX Behavioral Implementation**

module mux4_to_1_behavioral (
    input wire A,
    input wire B,
    input wire C,
    input wire D,
    input wire S0,
    input wire S1,
    output reg Y
);
    always @(*) begin
        case ({S1, S0})
            2'b00: Y = A;
            2'b01: Y = B;
            2'b10: Y = C;
            2'b11: Y = D;
            default: Y = 1'bx;
        endcase
    end
endmodule
OUTPUT:
![Screenshot 2025-03-13 092233](https://github.com/user-attachments/assets/2ac22b4e-e84f-41c1-ac1b-d384b1e07307)


**4:1 MUX Structural Implementation**

module mux2_to_1 (
    input wire A,
    input wire B,
    input wire S,
    output wire Y
);
    assign Y = S ? B : A;
endmodule

module mux4_to_1_structural (
    input wire A,
    input wire B,
    input wire C,
    input wire D,
    input wire S0,
    input wire S1,
    output wire Y
);
    wire mux_low, mux_high;

    mux2_to_1 mux0 (.A(A), .B(B), .S(S0), .Y(mux_low));
    mux2_to_1 mux1 (.A(C), .B(D), .S(S0), .Y(mux_high));

    mux2_to_1 mux_final (.A(mux_low), .B(mux_high), .S(S1), .Y(Y));
endmodule
OUTPUT:
![Screenshot 2025-03-13 092234](https://github.com/user-attachments/assets/aa47722b-62b7-4683-b0ed-3a3e062cf196)


**Testbench Implementation**

`timescale 1ns / 1ps

module mux4_to_1_tb;
    reg A, B, C, D, S0, S1;
    wire Y_gate, Y_dataflow, Y_behavioral, Y_structural;

    mux4_to_1_gate uut_gate (.A(A), .B(B), .C(C), .D(D), .S0(S0), .S1(S1), .Y(Y_gate));
    mux4_to_1_dataflow uut_dataflow (.A(A), .B(B), .C(C), .D(D), .S0(S0), .S1(S1), .Y(Y_dataflow));
    mux4_to_1_behavioral uut_behavioral (.A(A), .B(B), .C(C), .D(D), .S0(S0), .S1(S1), .Y(Y_behavioral));
    mux4_to_1_structural uut_structural (.A(A), .B(B), .C(C), .D(D), .S0(S0), .S1(S1), .Y(Y_structural));

    initial begin
        A = 0; B = 0; C = 0; D = 0; S0 = 0; S1 = 0;

        #10 {S1, S0, A, B, C, D} = 6'b00_0001;
        #10 {S1, S0, A, B, C, D} = 6'b01_0010;
        #10 {S1, S0, A, B, C, D} = 6'b10_0100;
        #10 {S1, S0, A, B, C, D} = 6'b11_1000;
        #10 $stop;
    end

    initial begin
        $monitor("Time=%0t | S1=%b S0=%b | Y_gate=%b | Y_dataflow=%b | Y_behavioral=%b | Y_structural=%b",
                 $time, S1, S0, Y_gate, Y_dataflow, Y_behavioral, Y_structural);
    end
endmodule

**SAMPLE OUTPUT**

Time=0 | S1=0 S0=0 | Y_gate=0 | Y_dataflow=0 | Y_behavioral=0 | Y_structural=0
Time=10 | S1=0 S0=1 | Y_gate=0 | Y_dataflow=0 | Y_behavioral=0 | Y_structural=0
Time=20 | S1=1 S0=0 | Y_gate=0 | Y_dataflow=0 | Y_behavioral=0 | Y_structural=0

**CONCLUSION**
In this experiment, a 4:1 Multiplexer was successfully designed and simulated using Verilog HDL
across four different modeling styles: Gate-Level, Data Flow, Behavioral, and Structural.
The simulation results verified the correct functionality of the MUX,
with all implementations producing identical outputs for the given input conditions.

