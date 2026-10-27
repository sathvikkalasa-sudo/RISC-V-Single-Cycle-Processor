# Main Decoder

The Main Decoder generates control signals for the processor's ID stage based on the instruction opcode. This module determines which operations the datapath and ALU should perform for each instruction type.

### Module Specification

```SystemVerilog
`timescale 1ns / 1ps
//////////////////////////////////////////////////////////////////////////////////
// Company: 
// Engineer: 
// 
// Create Date: 27.10.2026 19:10:49
// Design Name: 
// Module Name: maindec
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



module maindec(
    input  logic [6:0]  op,         // Opcode of the instruction
    output logic [1:0]  ResultSrc,  // Selects ALU, memory, or PC+4
    output logic        MemWrite,   // Data memory write enable
    output logic        Branch,     // Branch instruction flag
    output logic        ALUSrc,     // ALU source select
    output logic        RegWrite,   // Register file write enable
    output logic        Jump,       // Jump instruction flag
    output logic [1:0]  ImmSrc,     // Immediate type selector
    output logic [1:0]  ALUOp       // High-level ALU operation
);

    always_comb begin
        // Safe defaults for unsupported instructions
        ResultSrc = 2'b00;
        MemWrite  = 1'b0;
        Branch    = 1'b0;
        ALUSrc    = 1'b0;
        RegWrite  = 1'b0;
        Jump      = 1'b0;
        ImmSrc    = 2'b00;
        ALUOp     = 2'b00;

        case (op)

            // R-type: ADD, SUB, AND, OR, SLT
            7'b0110011: begin
                ResultSrc = 2'b00;
                MemWrite  = 1'b0;
                Branch    = 1'b0;
                ALUSrc    = 1'b0;
                RegWrite  = 1'b1;
                Jump      = 1'b0;
                ALUOp     = 2'b10;
            end

            // I-type arithmetic: ADDI, ANDI, ORI, SLTI
            7'b0010011: begin
                ResultSrc = 2'b00;
                ALUSrc    = 1'b1;
                RegWrite  = 1'b1;
                ImmSrc    = 2'b00;
                ALUOp     = 2'b10;
            end

            // Load word: LW
            7'b0000011: begin
                ResultSrc = 2'b01;
                ALUSrc    = 1'b1;
                RegWrite  = 1'b1;
                ImmSrc    = 2'b00;
                ALUOp     = 2'b00;
            end

            // Store word: SW
            7'b0100011: begin
                MemWrite  = 1'b1;
                ALUSrc    = 1'b1;
                ImmSrc    = 2'b01;
                ALUOp     = 2'b00;
            end

            // Branch if equal: BEQ
            7'b1100011: begin
                Branch = 1'b1;
                ALUSrc = 1'b0;
                ImmSrc = 2'b10;
                ALUOp  = 2'b01;
            end

            // Jump and link: JAL
            7'b1101111: begin
                ResultSrc = 2'b10;
                RegWrite  = 1'b1;
                Jump      = 1'b1;
                ImmSrc    = 2'b11;
            end

            // Unsupported opcode: safe defaults remain active
            default: begin
            end

        endcase
    end

endmodule


```

### Baseline Behavior (Locked ISA)

- `Branch` is asserted for **BEQ**.
- `Jump` is asserted for **JAL**.
- The baseline processor supports exactly **BEQ** for branch instructions and **JAL** for jump instructions. No other RV32I branch or jump instructions (e.g. BNE, BLT, JALR) are supported.

### Control Signal Table (Locked Baseline ISA)

The table below gives the required control-signal values for each baseline instruction category. "Don't care" means the datapath does not use that signal's value for the given category.

`ResultSrc` encoding: `00` = ALU result, `01` = memory read data, `10` = PC+4.
`ImmSrc` encoding (per Sign_Extender): `00` = I-type, `01` = S-type, `10` = B-type, `11` = J-type.
`ALUOp` encoding: `00` = always add (memory address calculation), `01` = always subtract (branch comparison), `10` = determined by `funct3`/`funct7b5` (see ALU_Decoder).

| Category | ResultSrc | MemWrite | Branch | ALUSrc | RegWrite | Jump | ImmSrc | ALUOp |
|---|---|---|---|---|---|---|---|---|
| R-type | 00 | 0 | 0 | 0 | 1 | 0 | Don't care | 10 |
| I-type ALU | 00 | 0 | 0 | 1 | 1 | 0 | 00 | 10 |
| LW | 01 | 0 | 0 | 1 | 1 | 0 | 00 | 00 |
| SW | Don't care | 1 | 0 | 1 | 0 | 0 | 01 | 00 |
| BEQ | Don't care | 0 | 1 | 0 | 0 | 0 | 10 | 01 |
| JAL | 10 | 0 | 0 | Don't care | 1 | 1 | 11 | Don't care |

### Hint for Students

>[!TIP]
>We have the following instruction types: `R-Type`, `I-Type`, `Load`, `Store`, `B-Type`, `Jump`.
> Think about which control signals are relevant for each type of instruction:       

Students should reason about the signal values for each instruction type instead of hardcoding them from external sources.

### Interface

> [!IMPORTANT]
> Your module, port and parameter names are up to you. Your design must have the same number of ports, with the same directions and widths, as shown above, and must behave as specified. Map your names to this spec in `rnr-stage.yaml` (see the assignment handout).