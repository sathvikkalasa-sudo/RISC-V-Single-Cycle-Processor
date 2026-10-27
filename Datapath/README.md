# Datapath

Design the datapath module for a single-cycle RISC-V processor. This module integrates various submodules to implement instruction execution.

### Module Specification

```SystemVerilog
`timescale 1ns / 1ps
//////////////////////////////////////////////////////////////////////////////////
// Company: 
// Engineer: 
// 
// Create Date: 27.10.2026 19:12:31
// Design Name: 
// Module Name: datapath
// Project Name: 
// Target Devices: 
// Tool Versions: 
// Description: 
// 
// Dependencies: 
// 
// Revision:
// Revision 0.01 - File Created
// Additional Comments:
// 
//////////////////////////////////////////////////////////////////////////////////



module datapath(
    input  logic        clk,
    input  logic        reset,
    input  logic [1:0]  ResultSrc,
    input  logic        PCSrc,
    input  logic        ALUSrc,
    input  logic        RegWrite,
    input  logic [1:0]  ImmSrc,
    input  logic [2:0]  ALUControl,
    output logic        Zero,
    output logic [31:0] PC,
    input  logic [31:0] Instr,
    output logic [31:0] ALUResult,
    output logic [31:0] WriteData,
    input  logic [31:0] ReadData
);

    // Internal datapath signals
    logic [31:0] PCNext;
    logic [31:0] PCPlus4;
    logic [31:0] PCTarget;
    logic [31:0] ImmExt;
    logic [31:0] RD1;
    logic [31:0] RD2;
    logic [31:0] SrcB;
    logic [31:0] Result;

    // 1. Program Counter register
    flopr #(.WIDTH(32)) pc_register (
        .d     (PCNext),
        .clk   (clk),
        .reset (reset),
        .q     (PC)
    );

    // 2. Calculate PC + 4
    adder pc_adder (
        .a (PC),
        .b (32'd4),
        .y (PCPlus4)
    );

    // 3. Generate the immediate
    extend immediate_extender (
        .instr  (Instr[31:7]),
        .immsrc (ImmSrc),
        .immext (ImmExt)
    );

    // 4. Register file
    regfile register_file (
        .clk  (clk),
        .we3  (RegWrite),
        .a1   (Instr[19:15]),
        .a2   (Instr[24:20]),
        .a3   (Instr[11:7]),
        .wd3  (Result),
        .rd1  (RD1),
        .rd2  (RD2)
    );

    // 5. Select ALU operand B
    mux2 #(.WIDTH(32)) alu_src_mux (
        .d0 (RD2),
        .d1 (ImmExt),
        .s  (ALUSrc),
        .y  (SrcB)
    );

    // 6. Arithmetic Logic Unit
    alu alu_unit (
        .a          (RD1),
        .b          (SrcB),
        .alucontrol (ALUControl),
        .result     (ALUResult)
    );

    // 7. Zero flag for BEQ
    always_comb begin
        Zero = (ALUResult == 32'b0);
    end

    // 8. Data sent to data memory for SW
    always_comb begin
        WriteData = RD2;
    end

    // 9. Calculate branch/jump target
    // This follows the starter repository's convention:
    // PC target = PC + (ImmExt << 1)
    adder target_adder (
        .a (PC),
        .b (ImmExt << 1),
        .y (PCTarget)
    );

    // 10. Select the next PC
    mux2 #(.WIDTH(32)) pc_src_mux (
        .d0 (PCPlus4),
        .d1 (PCTarget),
        .s  (PCSrc),
        .y  (PCNext)
    );

    // 11. Select the register write-back data
    mux3 #(.WIDTH(32)) result_src_mux (
        .d0 (ALUResult),
        .d1 (ReadData),
        .d2 (PCPlus4),
        .s  (ResultSrc),
        .y  (Result)
    );

endmodule


```

### Guidelines

- The datapath should handle **instruction fetch, decode, execution, memory access, and write-back**.    
- Integrate the submodules **without modifying their interfaces**.
- All mux selections, register file writes, and ALU operations should follow the **control signals** provided.
- Ensure **combinational outputs and sequential registers** are used correctly (e.g., PC update).
- Do **not change widths or naming** of input/output signals.

### PC Update and Branch/Jump Target

- The next value of `PC` is selected using `PCSrc` between **PC+4** and the **PC-relative branch/jump target**.
- The target address is computed as: `PC + (ImmExt << 1)`.
- **B-type** immediates (`ImmExt`) are used for **BEQ** targets; **J-type** immediates are used for **JAL** targets.
- `Zero` is asserted when the ALU result is zero.
- For **JAL**, `PC+4` is written to `rd` through the `ResultSrc` write-back mux.

### Interface

> [!IMPORTANT]
> Your module, port and parameter names are up to you. Your design must have the same number of ports, with the same directions and widths, as shown above, and must behave as specified. Map your names to this spec in `rnr-stage.yaml` (see the assignment handout).