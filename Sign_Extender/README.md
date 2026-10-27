# Sign Extender

This module generates a 32-bit immediate value from instruction fields, depending on the type of instruction (I-type, S-type, B-type, or J-type).

### Module Specification

```SystemVerilog
`timescale 1ns / 1ps
//////////////////////////////////////////////////////////////////////////////////
// Company: 
// Engineer: 
// 
// Create Date: 15.10.2026 20:54:04
// Design Name: 
// Module Name: extend
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


module extend(
    input  logic [31:7] instr,     // Instruction bits
    input  logic [1:0]  immsrc,    // Immediate type selector
    output logic [31:0] immext    // Sign-extended immediate
);

    always_comb begin
        case (immsrc)
            // I-type immediate
            2'b00: immext = {{20{instr[31]}}, instr[31:20]};

            // S-type immediate
            2'b01: immext = {{20{instr[31]}}, instr[31:25], instr[11:7]};

            // B-type: encode the immediate without the low zero bit
2'b10: immext = {{20{instr[31]}},
                 instr[31], instr[7],
                 instr[30:25], instr[11:8]};

// J-type: encode the immediate without the low zero bit
2'b11: immext = {{12{instr[31]}},
                 instr[31], instr[19:12],
                 instr[20], instr[30:21]};

            default: immext = 32'b0;
        endcase
    end

endmodule

```

### Working

- `immsrc = 2'b00` → **I-type**    
- `immsrc = 2'b01` → **S-type**
- `immsrc = 2'b10` → **B-type**
- `immsrc = 2'b11` → **J-type** 

>[!TIP]  
> Think about which bits of the instruction correspond to each immediate type and how to extend the **sign bit** to fill 32 bits.

### Notes

- `immext` is always **sign-extended to 32 bits**, regardless of immediate type.
- **B-type** and **J-type** immediates are used as PC-relative offsets: they are shifted left by 1 when forming the branch/jump target address (`PC + (ImmExt << 1)`).

### Interface

> [!IMPORTANT]
> Your module, port and parameter names are up to you. Your design must have the same number of ports, with the same directions and widths, as shown above, and must behave as specified. Map your names to this spec in `rnr-stage.yaml` (see the assignment handout).