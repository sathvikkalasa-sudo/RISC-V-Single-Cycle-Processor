# ALU Decoder

Design the ALU Control Unit that generates the ALU operation signals based on instruction fields and `ALUOp`.

### Module Specification

```SystemVerilog
`timescale 1ns / 1ps
//////////////////////////////////////////////////////////////////////////////////
// Company: 
// Engineer: 
// 
// Create Date: 16.10.2026 14:11:59
// Design Name: 
// Module Name: aludec
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


module aludec(
    input  logic        opb5,      // instruction bit [5] of opcode
    input  logic [2:0]  funct3,    // funct3 field of instruction
    input  logic        funct7b5,  // bit 5 of funct7 field
    input  logic [1:0]  ALUOp,     // high-level ALU operation
    output logic [2:0]  ALUControl // ALU operation code
);

    always_comb begin
        case (ALUOp)

            // ALUOp = 00: Addition (LW, SW, ADDI, etc.)
            2'b00: ALUControl = 3'b000;

            // ALUOp = 01: Branch instructions (BEQ)
            2'b01: ALUControl = 3'b001;

            // ALUOp = 10: R-type and I-type arithmetic/logical instructions
            2'b10: begin
                case (funct3)

                    3'b000: begin
                        // SUB when funct7[5] = 1 and opcode bit [5] = 1
                        if (opb5 && funct7b5)
                            ALUControl = 3'b001; // SUB
                        else
                            ALUControl = 3'b000; // ADD / ADDI
                    end

                    3'b111: ALUControl = 3'b010; // AND / ANDI
                    3'b110: ALUControl = 3'b011; // OR / ORI
                    3'b010: ALUControl = 3'b101; // SLT / SLTI

                    default: ALUControl = 3'b000;

                endcase
            end

            // Default: ADD
            default: ALUControl = 3'b000;

        endcase
    end

endmodule


```

### Working

- Determines the **ALU operation** (`ALUControl`) based on the instruction fields and `ALUOp`.
- Supports **R-type, I-type arithmetic, and branch instructions**.
- Must correctly differentiate between **add/subtract** for R-type instructions.

### ALUControl Mapping (Locked Baseline ISA)

The table below defines the required `ALUControl` value for every baseline instruction that reaches the ALU:

| Instruction | ALUControl |
|---|---|
| ADD, ADDI, LW, SW | 000 |
| SUB, BEQ | 001 |
| AND, ANDI | 010 |
| OR, ORI | 011 |
| SLT, SLTI | 101 |

### Interface

> [!IMPORTANT]
> Your module, port and parameter names are up to you. Your design must have the same number of ports, with the same directions and widths, as shown above, and must behave as specified. Map your names to this spec in `rnr-stage.yaml` (see the assignment handout).