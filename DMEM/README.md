# Data Memory (DMEM)

Implement the Data Memory for the single-cycle RISC-V processor.

### Module Specification

```SystemVerilog
`timescale 1ns / 1ps
//////////////////////////////////////////////////////////////////////////////////
// Company: 
// Engineer: 
// 
// Create Date: 15.10.2026 20:04:15
// Design Name: 
// Module Name: dmem
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


module dmem(
    input  logic        clk,
    input  logic        we,
    input  logic [31:0] a,   // Address
    input  logic [31:0] wd,  // Write data
    output logic [31:0] rd   // Read data
);

    logic [31:0] mem [0:63];

    // Combinational read
    always_comb begin
        rd = mem[a[7:2]];
    end

    // Synchronous write on the rising edge
    always_ff @(posedge clk) begin
        if (we)
            mem[a[7:2]] <= wd;
    end

endmodule

```

### Guidelines

- Addressing is **byte-addressable CPU addressing**: `a` is a byte address.
- Memory stores **32-bit words** and contains **64 words**.
- For aligned accesses, the word index is `a / 4`.
- Writes occur on the **rising edge of `clk`** when `we` (write enable) is high.
- Reads are **combinational**.

### Interface

> [!IMPORTANT]
> Your module, port and parameter names are up to you. Your design must have the same number of ports, with the same directions and widths, as shown above, and must behave as specified. Map your names to this spec in `rnr-stage.yaml` (see the assignment handout).
