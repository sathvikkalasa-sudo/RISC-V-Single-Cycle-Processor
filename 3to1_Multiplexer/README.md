# 3:1 Multiplexer

Design an 8-bit parameterized 3:1 Multiplexer.

### Module Specification

module mux3 #(parameter WIDTH = 8)(
    input  logic [WIDTH-1:0] d0,
    input  logic [WIDTH-1:0] d1,
    input  logic [WIDTH-1:0] d2,
    input  logic [1:0]       s,
    output logic [WIDTH-1:0] y
);

    always_comb begin
        case (s)
            2'b00:   y = d0;
            2'b01:   y = d1;
            2'b10:   y = d2;
            default: y = '0;
        endcase
    end

endmodule

### Interface

> [!IMPORTANT]
> Your module, port and parameter names are up to you. Your design must have the same number of ports, with the same directions and widths, as shown above, and must behave as specified. Map your names to this spec in `rnr-stage.yaml` (see the assignment handout).