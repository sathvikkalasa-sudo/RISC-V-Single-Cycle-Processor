# Control Unit

Design the Processor Control Unit that coordinates all datapath operations for a single-cycle RISC-V processor.

### Module Specification

```SystemVerilog
`timescale 1ns / 1ps
//////////////////////////////////////////////////////////////////////////////////
// Company: 
// Engineer: 
// 
// Create Date: 16.10.2026 14:54:11
// Design Name: 
// Module Name: controller
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


module controller(
    input  logic [6:0]  op,         // opcode field of instruction
    input  logic [2:0]  funct3,     // funct3 field
    input  logic        funct7b5,   // bit 5 of funct7 field
    input  logic        Zero,       // Zero flag from ALU
    output logic [1:0]  ResultSrc,  // selects data to write to register
    output logic        MemWrite,   // memory write enable
    output logic        PCSrc,      // controls PC update
    output logic        ALUSrc,     // ALU input select
    output logic        RegWrite,  // register write enable
    output logic        Jump,       // jump signal
    output logic [1:0]  ImmSrc,     // immediate generator select
    output logic [2:0]  ALUControl  // ALU operation code
);

    logic Branch;
    logic [1:0] ALUOp;

    // Main Decoder
    always_comb begin

        // Default control signals
        ResultSrc = 2'b00;
        MemWrite  = 1'b0;
        ALUSrc    = 1'b0;
        RegWrite  = 1'b0;
        Jump      = 1'b0;
        Branch    = 1'b0;
        ImmSrc    = 2'b00;
        ALUOp     = 2'b00;

        case (op)

            // R-type: ADD, SUB, AND, OR, SLT
            7'b0110011: begin
                RegWrite = 1'b1;
                ALUSrc   = 1'b0;
                ResultSrc = 2'b00;
                ALUOp    = 2'b10;
            end

            // I-type arithmetic: ADDI, ANDI, ORI, SLTI
            7'b0010011: begin
                RegWrite = 1'b1;
                ALUSrc   = 1'b1;
                ResultSrc = 2'b00;
                ImmSrc   = 2'b00;
                ALUOp    = 2'b10;
            end

            // Load word: LW
            7'b0000011: begin
                RegWrite = 1'b1;
                ALUSrc   = 1'b1;
                ResultSrc = 2'b01;
                ImmSrc   = 2'b00;
                ALUOp    = 2'b00;
            end

            // Store word: SW
            7'b0100011: begin
                MemWrite = 1'b1;
                ALUSrc   = 1'b1;
                ImmSrc   = 2'b01;
                ALUOp    = 2'b00;
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
                RegWrite = 1'b1;
                ResultSrc = 2'b10;
                Jump     = 1'b1;
                ImmSrc   = 2'b11;
            end

            // Unsupported instruction: retain safe defaults
            default: begin
                // All control signals remain at their defaults
            end

        endcase
    end

    // ALU Decoder
    aludec alu_decoder (
        .opb5      (op[5]),
        .funct3    (funct3),
        .funct7b5  (funct7b5),
        .ALUOp     (ALUOp),
        .ALUControl(ALUControl)
    );

    // Next-PC selection: jump or taken branch
    always_comb begin
        PCSrc = Jump | (Branch & Zero);
    end

endmodule

```

### Behavior

- Generates all **control signals** required by the datapath.
- Uses **Main Decoder** and **ALU Decoder** to determine the correct signal values.    
- Combines instruction fields and ALU results (e.g., `Zero`) to produce control outputs.
- Must handle **arithmetic, memory, branch, and jump instructions** correctly.

### PC Source Selection

- `PCSrc` selects the next `PC` value between **PC+4** and the **branch/jump target address**.
- `PCSrc = Jump | (Branch & Zero)`, where `Jump` and `Branch` are the corresponding flags produced by the Main Decoder instantiated internally within this module for the current instruction.
- `Jump` is asserted for **JAL**.
- `Branch` is asserted for **BEQ**.
- `Zero` is the datapath's ALU-result-zero indication, used to determine whether a **BEQ** branch is taken.

### Interface

> [!IMPORTANT]
> Your module, port and parameter names are up to you. Your design must have the same number of ports, with the same directions and widths, as shown above, and must behave as specified. Map your names to this spec in `rnr-stage.yaml` (see the assignment handout).