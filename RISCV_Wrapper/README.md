# RISC-V Single-Cycle Wrapper

This module wraps together the controller and datapath to form a complete single-cycle RISC-V processor.

### Module Specification

module riscvsingle(
    input  logic        clk,
    input  logic        reset,
    output logic [31:0] PC,
    input  logic [31:0] Instr,
    output logic        MemWrite,
    output logic [31:0] ALUResult,
    output logic [31:0] WriteData,
    input  logic [31:0] ReadData
);

    logic [1:0] ResultSrc;
    logic       PCSrc;
    logic       ALUSrc;
    logic       RegWrite;
    logic       Jump;
    logic [1:0] ImmSrc;
    logic [2:0] ALUControl;
    logic       Zero;

    controller controller_unit (
        .op         (Instr[6:0]),
        .funct3     (Instr[14:12]),
        .funct7b5   (Instr[30]),
        .Zero       (Zero),
        .ResultSrc  (ResultSrc),
        .MemWrite   (MemWrite),
        .PCSrc      (PCSrc),
        .ALUSrc     (ALUSrc),
        .RegWrite   (RegWrite),
        .Jump       (Jump),
        .ImmSrc     (ImmSrc),
        .ALUControl (ALUControl)
    );

    datapath datapath_unit (
        .clk        (clk),
        .reset      (reset),
        .ResultSrc  (ResultSrc),
        .PCSrc      (PCSrc),
        .ALUSrc     (ALUSrc),
        .RegWrite   (RegWrite),
        .ImmSrc     (ImmSrc),
        .ALUControl (ALUControl),
        .Zero       (Zero),
        .PC         (PC),
        .Instr      (Instr),
        .ALUResult  (ALUResult),
        .WriteData  (WriteData),
        .ReadData   (ReadData)
    );

endmodule

>[!TIP]
>Think what could "wrap" mean here :D

### Interface

> [!IMPORTANT]
> Your module, port and parameter names are up to you. Your design must have the same number of ports, with the same directions and widths, as shown above, and must behave as specified. Map your names to this spec in `rnr-stage.yaml` (see the assignment handout).