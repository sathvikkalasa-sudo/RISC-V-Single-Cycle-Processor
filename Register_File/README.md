# Register File

Design a 32×32 three-ported register file with two read ports and one write port.

The register file contains **32 registers, each 32 bits wide**.

### Module Specification

```SystemVerilog
`timescale 1ns / 1ps
//////////////////////////////////////////////////////////////////////////////////
// Company: 
// Engineer: 
// 
// Create Date: 15.10.2026 20:32:15
// Design Name: 
// Module Name: regfile
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


module regfile(
    input  logic        clk,   // clock
    input  logic        we3,   // write enable for port 3
    input  logic [4:0]  a1, a2, a3, // read addresses (a1,a2), write address (a3)
    input  logic [31:0] wd3,       // write data for port 3
    output logic [31:0] rd1, rd2   // read data outputs
);

    logic [31:0] rf [0:31];

    // Combinational read ports
    always_comb begin
        rd1 = (a1 == 5'd0) ? 32'b0 : rf[a1];
        rd2 = (a2 == 5'd0) ? 32'b0 : rf[a2];
    end

    // Synchronous write port
    always_ff @(posedge clk) begin
        if (we3 && (a3 != 5'd0))
            rf[a3] <= wd3;
    end

endmodule

```

### Working

- **Read Ports:**
    
    - `rd1` reads `rf[a1]`
        
    - `rd2` reads `rf[a2]`
        
    - Reads are **combinational**, i.e., reflect memory instantly.
        
- **Write Port:**
    
    - `rf[a3] <= wd3` on rising edge of `clk` **if `we3` is high**.
        
- **Register 0:**
    
    - Always hardwired to 0, cannot be written.

### Interface

> [!IMPORTANT]
> Your module, port and parameter names are up to you. Your design must have the same number of ports, with the same directions and widths, as shown above, and must behave as specified. Map your names to this spec in `rnr-stage.yaml` (see the assignment handout).