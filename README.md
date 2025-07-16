-PIPELINE-PROCESSOR-DESIGN

COMPANY NAME : CODTECH IT SOLUTIONS

NAME : ANURAG KUMAR

INTERN ID : CT06DF628

DOMAIN : VLSI

DURATION : 6 WEEKS

MENTOR : NEELA SANTOSH

-----DESCRIPTION----

Project Description:
As part of my VLSI internship at Codtech IT Solutions, my fourth major task involved the design and simulation of a 4-stage pipelined processor capable of executing basic arithmetic and memory operations such as ADD, SUB, and LOAD. The primary objective of this project was to understand how pipelining improves instruction throughput in a processor and to gain hands-on experience by implementing this concept using Verilog Hardware Description Language (HDL).
This task not only deepened my knowledge of digital design and processor architecture but also helped me grasp the practical intricacies of creating a pipelined system where multiple instructions are executed in a staggered, overlapped manner across different stages.

Introduction:
The concept of pipelining is similar to an assembly line in a factory, where different stages of instruction execution work in parallel. The 4 stages designed in this project are:
1. IF (Instruction Fetch) – Fetch the instruction from instruction memory.
2. ID (Instruction Decode) – Decode the fetched instruction and read source registers.
3. EX (Execute) – Perform ALU operations like ADD and SUB or calculate memory addresses.
4. MEM (Memory Access / Write-Back) – Access data memory (for LOAD) and write the result back to the register file.
This architecture allows the processor to work on multiple instructions simultaneously, with each instruction being at a different stage of execution. This increases overall efficiency and throughput.

Verilog Code - 

module pipeline_processor (
    input clk,
    input reset
);

    // Opcodes
    parameter ADD = 4'b0001;
    parameter SUB = 4'b0010;
    parameter LOAD = 4'b0011;

    // Instruction memory
    reg [15:0] instr_mem[0:7];
    reg [7:0] pc;

    // Register file
    reg [7:0] regfile[0:15];
    reg [7:0] memory[0:15];

    // Pipeline registers
    reg [15:0] IF_ID, ID_EX;
    reg [7:0] EX_MEM_result;
    reg [3:0] EX_MEM_dest;
    reg [3:0] EX_MEM_opcode;

    // IF stage
    always @(posedge clk or posedge reset) begin
        if (reset)
            pc <= 0;
        else
            IF_ID <= instr_mem[pc++];
    end

    // ID stage
    reg [3:0] opcode, dest, src1, src2;
    reg [7:0] op1, op2;

    always @(posedge clk) begin
        opcode <= IF_ID[15:12];
        dest   <= IF_ID[11:8];
        src1   <= IF_ID[7:4];
        src2   <= IF_ID[3:0];

        op1 <= regfile[src1];
        op2 <= regfile[src2];

        ID_EX <= IF_ID;
    end

    // EX stage
    always @(posedge clk) begin
        EX_MEM_opcode <= opcode;
        EX_MEM_dest <= dest;

        case (opcode)
            ADD: EX_MEM_result <= op1 + op2;
            SUB: EX_MEM_result <= op1 - op2;
            LOAD: EX_MEM_result <= memory[op1]; // address from src1
            default: EX_MEM_result <= 0;
        endcase
    end

    // MEM / Write-back stage
    always @(posedge clk) begin
        if (EX_MEM_opcode != 4'b0000)
            regfile[EX_MEM_dest] <= EX_MEM_result;
    end

    // Initialize instruction memory
    initial begin
        // Example program:
        // ADD R1, R2, R3
        // SUB R4, R1, R3
        // LOAD R5, [R2]
        instr_mem[0] = {ADD, 4'd1, 4'd2, 4'd3};
        instr_mem[1] = {SUB, 4'd4, 4'd1, 4'd3};
        instr_mem[2] = {LOAD, 4'd5, 4'd2, 4'd0}; // src2 unused for LOAD

        regfile[2] = 10;  // R2
        regfile[3] = 5;   // R3
        memory[10] = 99;  // For LOAD
    end

endmodule

Testbench code - 

module tb_pipeline;
    reg clk = 0;
    reg reset = 1;

    pipeline_processor uut (.clk(clk), .reset(reset));

    always #5 clk = ~clk; // Clock with 10 time unit period

    initial begin
        // Initialize VCD file for EPWave
        $dumpfile("pipeline.vcd");      // Name of the VCD file
        $dumpvars(0, tb_pipeline);      // Dump all variables in this testbench and its submodules

        #10 reset = 0;   // Release reset
        #200 $finish;    // Run simulation for 200 time units
    end
endmodule

Implementation in Verilog:
The processor was implemented in Verilog using a modular and behavioral design approach. The instruction format was 16 bits, divided into fields for opcode, destination register, and two source registers. The supported instructions were:

* `ADD R1, R2, R3` – R1 = R2 + R3
* `SUB R1, R2, R3` – R1 = R2 - R3
* `LOAD R1, [R2]` – R1 = Memory\[R2]

Instruction memory, register files, and data memory were modeled using Verilog arrays. Intermediate values between pipeline stages were stored in pipeline registers (`IF_ID`, `ID_EX`, `EX_MEM`) to simulate real processor behavior.
Each pipeline stage was coded in a sequential `always @(posedge clk)` block to replicate clocked logic behavior, and a testbench module was created to simulate and observe the processor’s functionality.

Simulation and Waveform Verification:
To verify the working of the pipeline, a detailed testbench was written. It included clock generation, reset logic, and waveform dumping using `$dumpfile` and `$dumpvars` to generate a `.vcd` file. This allowed me to observe internal signals in EPWave (on EDA Playground), where the movement of each instruction across pipeline stages could be visually tracked.
The simulation successfully demonstrated the parallelism and pipelining efficiency, as multiple instructions could be seen at various stages at the same time. This visual proof of the pipelined execution was one of the most rewarding aspects of the project.

Output1-

  ![Image](https://github.com/user-attachments/assets/7a35d4a2-45c0-41fa-acab-5d18ffa9bbce)

Output2-

  ![Image](https://github.com/user-attachments/assets/0b98554d-c70c-4165-820d-d0343667c9b2)

Learning and Performance Insight:
Throughout the project, I learned the importance of instruction synchronization, hazard management (conceptually), and the use of pipeline registers to isolate each stage. Although this basic processor doesn't implement advanced features like forwarding or hazard detection, it provides a strong foundation for further exploration in computer architecture.
This project also strengthened my understanding of:
* Writing and organizing complex Verilog code
* Implementing arithmetic logic at the hardware level
* Visualizing and verifying multi-stage digital systems
* Theoretical and practical aspects of instruction pipelinine

Conclusion:
Designing a 4-stage pipelined processor from scratch using Verilog was a highly enriching experience. It allowed me to apply both my theoretical knowledge of digital electronics and my hands-on skills in HDL programming. The working simulation demonstrated my understanding of how real-world processors execute instructions efficiently. This task marks a significant step in my growth as a digital design and VLSI enthusiast.


