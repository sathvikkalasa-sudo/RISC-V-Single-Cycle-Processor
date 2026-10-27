# 2:1 Multiplexer

Design an 8-bit parameterized 2:1 Multiplexer.

### Module Specification

module mux2 #(parameter WIDTH = 8)(
    input  logic [WIDTH-1:0] d0,
    input  logic [WIDTH-1:0] d1,
    input  logic             s,
    output logic [WIDTH-1:0] y
);

    always_comb begin
        y = s ? d1 : d0;
    end

endmodule
### Interface

> [!IMPORTANT]
> Your module, port and parameter names are up to you. Your design must have the same number of ports, with the same directions and widths, as shown above, and must behave as specified. Map your names to this spec in `rnr-stage.yaml` (see the assignment handout).