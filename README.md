# RESTORING_DIVISION
module restoring_division (
  input wire clk,
  input wire rst,
  input [7:0] dividend,
  input [3:0] divisor,
  output reg [7:0] quotient,
  output reg [3:0] remainder,
  output reg done
);

  reg [7:0] temp_dividend;
  reg [3:0] temp_divisor;
  reg [7:0] temp_quotient;
  reg [3:0] temp_remainder;
  reg [4:0] count;

  always @(posedge clk or posedge rst) begin
    if (rst) begin
      temp_dividend <= 8'b0;
      temp_divisor <= 4'b0;
      temp_quotient <= 8'b0;
      temp_remainder <= 4'b0;
      count <= 5'b0;
      done <= 1'b0;
    end else if (count < 5) begin
      temp_dividend <= {temp_remainder, dividend};
      temp_divisor <= {temp_remainder[3:0], divisor};
      if (temp_dividend >= temp_divisor) begin
        temp_dividend <= temp_dividend - temp_divisor;
        temp_quotient[count] <= 1'b1;
      end else begin
        temp_quotient[count] <= 1'b0;
      end
      temp_remainder <= temp_dividend[7:4];
      count <= count + 1;
    end else begin
      done <= 1'b1;
    end
  end

endmodule

//MODULE_TESTBENCH
module tb_restoring_division;

reg clk, rst;
reg [7:0] dividend;
reg [3:0] divisor;
wire [7:0] quotient;
wire [3:0] remainder;
wire done;

restoring_division uut (
  .clk(clk),
  .rst(rst),
  .dividend(dividend),
  .divisor(divisor),
  .quotient(quotient),
  .remainder(remainder),
  .done(done)
);

initial begin
  clk = 0;
  rst = 1;
  dividend = 8'b10110101;
  divisor = 4'b1010;

  #10 rst = 0;
  #100;
  clk = 1;
  #100 clk = 0;
  #100;
  dividend = 8'b11011011;
  divisor = 4'b1101;
  #100;
  $stop;
end

always #5 clk = ~clk;

endmodule
