# DAY-45_100_Days_Of_RTL
//GCD_EUCLIDIAN_ALGORITHM


module GCD_Calculator(
    input clk,           // Clock signal
    input rst,           // Reset signal
    input start,         // Start signal
    input [3:0] A, B,    // 4-bit inputs
    output reg [3:0] GCD, // GCD Output
    output reg done      // Done flag
);

    reg [3:0] x, y;      // Internal registers to store A and B
    reg [1:0] state;     // State register

    parameter IDLE = 2'b00, COMPUTE = 2'b01, DONE = 2'b10;

    always @(posedge clk or posedge rst) begin
        if (rst) begin
            x <= 0;
            y <= 0;
            GCD <= 0;
            done <= 0;
            state <= IDLE;
        end 
        else begin
            case (state)
                IDLE: begin
                    if (start) begin
                        x <= A;
                        y <= B;
                        state <= COMPUTE;
                        done <= 0;
                    end
                end
                
                COMPUTE: begin
                    if (x == y) begin
                        GCD <= x;
                        state <= DONE;
                    end 
                    else if (x > y) begin
                        x <= x - y;  // Euclidean Algorithm step
                    end 
                    else begin
                        y <= y - x;  // Euclidean Algorithm step
                    end
                end

                DONE: begin
                    done <= 1;
                    state <= IDLE;  // Reset to IDLE for next computation
                end
            endcase
        end
    end
endmodule
///////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////

module GCD_Calculator_tb;

    reg clk, rst, start;
    reg [3:0] A, B;
    wire [3:0] GCD;
    wire done;

    // Instantiate the GCD module
    GCD_Calculator uut (
        .clk(clk),
        .rst(rst),
        .start(start),
        .A(A),
        .B(B),
        .GCD(GCD),
        .done(done)
    );

    // Clock generation
    always #5 clk = ~clk;

    initial begin
        // Initialize signals
        clk = 0; rst = 1; start = 0; A = 0; B = 0;
        #10 rst = 0;

        // Test Case 1: GCD(12, 8) = 4
        A = 4'b1100;  // 12
        B = 4'b1000;  // 8
        start = 1;
        #10 start = 0;

        wait(done);
        #10;

        // Test Case 2: GCD(9, 6) = 3
        A = 4'b1001;  // 9
        B = 4'b0110;  // 6
        start = 1;
        #10 start = 0;

        wait(done);
        #10;

        // Test Case 3: GCD(15, 5) = 5
        A = 4'b1111;  // 15
        B = 4'b0101;  // 5
        start = 1;
        #10 start = 0;

        wait(done);
        #10;

        $finish;
    end

endmodule

