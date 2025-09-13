# Exp-No: 01 - 4:1 Multiplexer using Verilog HDL (Gate-Level, Dataflow, Behavioural, and Structural Modelling)

**Aim:** <br>
<br>
&emsp;&emsp;To design and simulate a 4:1 Multiplexer (MUX) using Verilog HDL in four different modeling styles—Gate-Level, Data Flow, Behavioral, and Structural and to verify its functionality through a testbench using the Vivado 2023.1 simulation environment. The experiment aims to understand how different abstraction levels in Verilog can be used to describe the same digital logic circuit and analyze their performance.<br>
<br>
**Apparatus Required:** <br>
<br>
&emsp;&emsp;Vivado 2023.1<br>
<br>
**Procedure:** <br>
<br>
1. Launch Vivado Open Vivado 2023.1 by double-clicking the Vivado icon or searching for it in the Start menu.<br>
2. Create a New Project Click on "Create Project" from the Vivado Quick Start window. In the New Project Wizard: Project Name: Enter a name for the project (e.g., Mux4_to_1). Project Location: Select the folder where the project will be saved. Click Next. Project Type: Select RTL Project, then click Next. Add Sources: Click on "Add Files" to add the Verilog files (e.g., mux4_to_1_gate.v, mux4_to_1_dataflow.v, etc.). Make sure to check the box "Copy sources into project" to avoid any external file dependencies. Click Next. Add Constraints: Skip this step by clicking Next (since no constraints are needed for simulation).
Default Part Selection: You can choose a part based on the FPGA board you are using (if any). If no board is used, you can choose any part, for example, xc7a35ticsg324-1L (Artix-7). Click Next, then Finish.<br>
3. Add Verilog Source Files In the "Sources" window, right-click on "Design Sources" and select Add Sources if you didn't add all files earlier. Add the Verilog files (mux4_to_1_gate.v, mux4_to_1_dataflow.v, etc.) and the testbench (mux4_to_1_tb.v).<br>
4. Check Syntax Expand the "Flow Navigator" on the left side of the Vivado interface. Under "Synthesis", click "Run Synthesis". Vivado will check your design for syntax errors. If any errors or warnings appear, correct them in the respective Verilog files and re-run the synthesis.<br>
5. Simulate the Design In the Flow Navigator, under "Simulation", click on "Run Simulation" → "Run Behavioral Simulation". Vivado will open the Simulations Window, and the waveform window will show the signals defined in the testbench.<br>
6. View and Analyze Simulation Results The simulation waveform window will display the signals (S1, S0, A, B, C, D, Y_gate, Y_dataflow, Y_behavioral, Y_structural). Use the time markers to verify the correctness of the 4:1 MUX output for each set of inputs. You can zoom in/out or scroll through the simulation time using the waveform viewer controls.<br>
7. Adjust Simulation Time To run a longer simulation or adjust timing, go to the Simulation Settings by clicking "Simulation" → "Simulation Settings".
Under "Simulation", modify the Run Time (e.g., set to 1000ns).<br>
8. Generate Simulation Report Once the simulation is complete, you can generate a simulation report by right-clicking on the simulation results window and selecting "Export Simulation Results". Save the report for reference in your lab records.<br>
9. Save and Document Results Save your project by clicking File → Save Project. Take screenshots of the waveform window and include them in your lab report to document your results. You can include the timing diagram from the simulation window showing the correct functionality of the 4:1 MUX across different select inputs and data inputs.<br>
10. Close the Simulation Once done, by going to Simulation → "Close Simulation<br>
<br>

**Logic Diagram:** <br>
<br>

![368836171-d4ab4bc3-12b0-44dc-8edb-9d586d8ba856](https://github.com/user-attachments/assets/335554b2-6dde-4d52-b34d-d1c711e330ad)

<br>

**Truth Table:** <br>
<br>

![368836230-c850506c-3f6e-4d6b-8574-939a914b2a5f](https://github.com/user-attachments/assets/ef7b428a-a74c-4ee4-9681-d89e5ad2ec35)

<br>

**Verilog Code:** <br>
4:1 MUX Gate-Level Implementation:
```
module mux4to1(I0,I1,I2,I3,S1,S2,Y);
    input I0,I1,I2,I3,S1,S2;
    output Y;
    wire p,q,a,b,c,d;
    not (p,S1);
    not (q,S2);
    and (a,I0,p,q);
    and (b,I1,p,S2);
    and (c,I2,S1,q);
    and (d,I3,S1,S2);
    or (Y,a,b,c,d);    
endmodule
```
4:1 MUX Data Flow Implementation:
```
module mux4to1_df(A,B,C,D,S1,S0,Y);
    input A,B,C,D,S1,S0;
    output Y;
    
    assign Y =   (S1 == 0 && S0 == 0) ? A:
                 (S1 == 0 && S0 == 1) ? B:
                 (S1 == 1 && S0 == 0) ? C: 
                 (S1 == 1 && S0 == 1) ? D: 1'b0;
                                         
endmodule
```
4:1 MUX Behavioral Implementation:
```
module mux4to1_bhv(I,S,Y);
    input wire [0:3] I;
    input wire [1:0] S;
    output reg Y;
    always @(*) begin
        case (S)
            2'b00: Y = I[0];
            2'b01: Y = I[1];
            2'b10: Y = I[2];
            2'b11: Y = I[3];
            default: Y = 1'b0;
        endcase
    end
endmodule
```
4:1 MUX Structural Implementation:
```
module mux2to1(A,B,S,Y);
    input A,B,S;
    output Y;    
    assign Y = (S) ? B : A;
endmodule
module mux4to1_str(I,S,Y);
    input [0:3] I;      
    input [1:0] S;     
    output Y;           
    wire y1, y2;
    mux2to1 m1(.A(I[0]), .B(I[1]), .S(S[0]), .Y(y1));
    mux2to1 m2(.A(I[2]), .B(I[3]), .S(S[0]), .Y(y2));
    mux2to1 m3(.A(y1), .B(y2), .S(S[1]), .Y(Y));
endmodule
```   
Testbench Implementation:
4:1 MUX Gate-Level Test bench Implementation
```
module mux4to1_tb;
    reg I0_t,I1_t,I2_t,I3_t,S1_t,S2_t;
    wire Y_t;
    mux4to1 dut(.I0(I0_t),.I1(I1_t),.I2(I2_t),.I3(I3_t),.S1(S1_t),.S2(S2_t),.Y(Y_t));
    initial 
    begin
        I0_t = 1'b1;
        I1_t = 1'b0;
        I2_t = 1'b1;
        I3_t = 1'b0;
        S1_t = 1'b0;
        S2_t = 1'b0;
        #100
        I0_t = 1'b1;
        I1_t = 1'b0;
        I2_t = 1'b1;
        I3_t = 1'b0;
        S1_t = 1'b0;
        S2_t = 1'b1;
        #100
        I0_t = 1'b1;
        I1_t = 1'b0;
        I2_t = 1'b1;
        I3_t = 1'b0;
        S1_t = 1'b1;
        S2_t = 1'b0;
        #100
        I0_t = 1'b1;
        I1_t = 1'b0;
        I2_t = 1'b1;
        I3_t = 1'b0;
        S1_t = 1'b1;
        S2_t = 1'b1;
    end
endmodule
```

4:1 MUX Data Flow Test bench Implementation
```
module mux4to1_df_tb;
    reg a, b, c, d;
    reg s1, s0;
    wire y;
    mux4to1_df dut (
        .A(a), .B(b), .C(c), .D(d),
        .S1(s1), .S0(s0), .Y(y)
    );initial 
    begin
        a = 1'b0;
        b = 1'b1;
        c = 1'b0;
        d = 1'b1;
        s1 = 1'b0; s0 = 1'b0; #100; 
        s1 = 1'b0; s0 = 1'b1; #100; 
        s1 = 1'b1; s0 = 1'b0; #100; 
        s1 = 1'b1; s0 = 1'b1; #100; 
        a = 1'b1; b = 1'b1; c = 1'b0; d = 1'b0;
        s1 = 1'b0; s0 = 1'b0; #100; 
        s1 = 1'b0; s0 = 1'b1; #100; 
        s1 = 1'b1; s0 = 1'b0; #100; 
        s1 = 1'b1; s0 = 1'b1; #100; 
    end
endmodule
  
```
4:1 MUX Behavioral Test bench Implementation
```
module mux4to1_bhv_tb;
    reg [0:3] I;
    reg [1:0] S;
    wire Y;
    mux4to1_bhv dut (
        .I(I),
        .S(S),
        .Y(Y)
    );
    initial begin
        
        I = 4'b1010;
        S = 2'b00; #50; 
        S = 2'b01; #50;
        S = 2'b10; #50; 
        S = 2'b11; #50; 
        I = 4'b1100;
        S = 2'b00; #50; 
        S = 2'b01; #50; 
        S = 2'b10; #50; 
        S = 2'b11; #50; 
        I = 4'b0110;
        S = 2'b00; #50; 
        S = 2'b01; #50;
        S = 2'b10; #50; 
        S = 2'b11; #50; 
        end
endmodule
```
4:1 MUX Structural Test bench Implementation
```
module mux4to1_str_tb;
    reg [0:3] I;
    reg [1:0] S;
    wire Y;
    mux4to1_str dut(.I(I), .S(S), .Y(Y));
    initial begin
        I = 4'b1010; S = 2'b00; #100;
        I = 4'b1010; S = 2'b01; #100;
        I = 4'b1010; S = 2'b10; #100;
        I = 4'b1010; S = 2'b11; #100;
        I = 4'b1100; S = 2'b00; #100;
        I = 4'b1100; S = 2'b01; #100;
        I = 4'b1100; S = 2'b10; #100;
        I = 4'b1100; S = 2'b11; #100;
    end
endmodule
```

Output waveform
Gate-Level:

<img width="1920" height="1200" alt="image" src="https://github.com/user-attachments/assets/2465da8b-a010-4de4-a95d-7263bacb2072" />

Data Flow:
<img width="1920" height="1200" alt="image" src="https://github.com/user-attachments/assets/b0b83e0d-dae1-4985-aabd-e98ec9e30fd3" />


Behavioral:
<img width="1920" height="1200" alt="image" src="https://github.com/user-attachments/assets/2fbc5f4a-7ffb-4bd9-8cbd-3a489dca12a0" />


Structural:
<img width="1920" height="1200" alt="image" src="https://github.com/user-attachments/assets/f91ea1be-dfc5-4102-a7f6-159c11001d90" />


**Conclusion:** <br>
<br>
&emsp;&emsp;In this experiment, a 4:1 Multiplexer was successfully designed and simulated using Verilog HDL across four different modeling styles: Gate-Level, Data Flow, Behavioral, and Structural. The simulation results verified the correct functionality of the MUX, with all implementations producing identical outputs for the given input conditions.



  
