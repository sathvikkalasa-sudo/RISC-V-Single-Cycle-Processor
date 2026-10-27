# Resettable Flip-Flop with Enable

Design a 8-bit parameterized flip-flop with synchronous enable and asynchronous reset.

### Module Specification

```SystemVerilog
`timescale 1ns / 1ps
//////////////////////////////////////////////////////////////////////////////////
// Company: 
// Engineer: 
// 
// Create Date: 15.10.2026 20:37:15
// Design Name: 
// Module Name: flopenr
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


module flopenr #(parameter WIDTH = 8)(
    input  logic              clk,   // Clock
    input  logic              reset, // Asynchronous reset
    input  logic              en,    // Enable signal
    input  logic [WIDTH-1:0]  d,     // Data input
    output logic [WIDTH-1:0]  q      // Data output
);

    always_ff @(posedge clk or posedge reset) begin
        if (reset)
            q <= '0;
        else if (en)
            q <= d;
    end

endmodule

```

### Working

- Asynchronous reset: sets `q` to `0` when `reset` is high. 
- Enable (`en`): updates output `q` only when `en` is high.

> [!TIP] 
> Consider the order of reset and enable in the `always_ff` block to ensure correct behavior.

### Interface

> [!IMPORTANT]
> Your module, port and parameter names are up to you. Your design must have the same number of ports, with the same directions and widths, as shown above, and must behave as specified. Map your names to this spec in `rnr-stage.yaml` (see the assignment handout).