# Resettable Flip-Flop

Design a 8-bit parameterized flip-flop with asynchronous reset.

### Module Specification

```SystemVerilog
`timescale 1ns / 1ps
//////////////////////////////////////////////////////////////////////////////////
// Company: 
// Engineer: 
// 
// Create Date: 15.10.2026 20:40:37
// Design Name: 
// Module Name: flopr
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


module flopr #(parameter WIDTH = 8)(
    input  logic [WIDTH-1:0] d,     // Data input
    input  logic              clk,   // Clock
    input  logic              reset, // Asynchronous reset
    output logic [WIDTH-1:0] q      // Data output
);

    always_ff @(posedge clk or posedge reset) begin
        if (reset)
            q <= '0;
        else
            q <= d;
    end

endmodule

```

### Working

- Asynchronous reset: sets `q` to `0` when `reset` is high.
- Clocked behavior: updates output `q` with input `d` on the rising edge of `clk`.

> [!TIP] 
> Ensure the reset takes priority over normal data capture in the `always_ff` block.

### Interface

> [!IMPORTANT]
> Your module, port and parameter names are up to you. Your design must have the same number of ports, with the same directions and widths, as shown above, and must behave as specified. Map your names to this spec in `rnr-stage.yaml` (see the assignment handout).