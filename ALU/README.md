`timescale 1ns / 1ps
//////////////////////////////////////////////////////////////////////////////////
// Company: 
// Engineer: 
// 
// Create Date: 27.10.2026 19:08:29
// Design Name: 
// Module Name: alu
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



module alu(
    input  logic [31:0] a,          // Operand A
    input  logic [31:0] b,          // Operand B
    input  logic [2:0]  alucontrol, // Operation selector
    output logic [31:0] result      // Operation result
);

    always_comb begin
        case (alucontrol)
            3'b000: result = a + b;  // ADD
            3'b001: result = a - b;  // SUB
            3'b010: result = a & b;  // AND
            3'b011: result = a | b;  // OR
            3'b101: result = ($signed(a) < $signed(b))
                             ? 32'd1 : 32'd0; // SLT

            default: result = 32'b0;
        endcase
    end

endmodule
