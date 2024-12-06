# Semiconductor_Engineering
Project Description: 

I have designed and coded a Finite State Machine (FSM) for a timed traffic light controller that has the following behaviors:

1) N/S direction traffic has a green light for 64 clocks. E/W direction has a red light.
2) N/S direction traffic has a yellow light for 4 clocks. E/W directionhas a red light.
3) E/W direction traffic has a green light for 64 clocks. N/S direction has a red light.
4) E/W direction traffic has a yellow light for 4 clocks. N/S direction has a red light.

The FSM should have an asynchronous active low reset input, and reset the FSM to starting with a green light in the N/S direction.

The FSM should drive six output signals name: ns_green, ns_yellow, ns_red, ew_green, ew_yellow and ew_red.


Testbennch and Verilog source codes

module traffic_light_fsm_tb;

    // Inputs
    reg clk;
    reg reset_n;

    // Outputs
    wire ns_green, ns_yellow, ns_red;
    wire ew_green, ew_yellow, ew_red;

    // Instantiate the FSM module
    traffic_light_fsm uut (
        .clk(clk),
        .reset_n(reset_n),
        .ns_green(ns_green),
        .ns_yellow(ns_yellow),
        .ns_red(ns_red),
        .ew_green(ew_green),
        .ew_yellow(ew_yellow),
        .ew_red(ew_red)
    );

    // Clock generation
    initial clk = 0;
    always #5 clk = ~clk; // 10 ns clock period

    // Test sequence
    initial begin
        // Reset the FSM
        reset_n = 0;
        #15;
        reset_n = 1;

        // Run simulation for a few cycles
        #500;

        // End simulation
        $finish;
    end

    // Monitor outputs
    initial begin
        $monitor("Time=%0d | NS: Green=%b, Yellow=%b, Red=%b | EW: Green=%b, Yellow=%b, Red=%b",
            $time, ns_green, ns_yellow, ns_red, ew_green, ew_yellow, ew_red);
    end
  
    initial 
    begin 
    $vcdpluson;
    end 

endmodule


Verilog codes are as below;

module traffic_light_fsm (
    input clk,
    input reset_n, // Asynchronous active low reset
    output reg ns_green, ns_yellow, ns_red,
    output reg ew_green, ew_yellow, ew_red
);

    // State encoding
    parameter STATE_NS_GREEN  = 2'b00;
    parameter STATE_NS_YELLOW = 2'b01;
    parameter STATE_EW_GREEN  = 2'b10;
    parameter STATE_EW_YELLOW = 2'b11;

    reg [1:0] current_state, next_state; // Current and next states
    reg [6:0] timer; // Timer for counting clock cycles (0-127)

    // State transition and timer logic
    always @(posedge clk or negedge reset_n) begin
        if (!reset_n) begin
            current_state <= STATE_NS_GREEN;
            timer <= 7'd0;
        end else begin
            current_state <= next_state;
            timer <= (timer == 7'd63 || timer == 7'd3) ? 7'd0 : timer + 1;
        end
    end

    // Next state logic
    always @(*) begin
        case (current_state)
            STATE_NS_GREEN:
                next_state = (timer == 7'd63) ? STATE_NS_YELLOW : STATE_NS_GREEN;
            STATE_NS_YELLOW:
                next_state = (timer == 7'd3) ? STATE_EW_GREEN : STATE_NS_YELLOW;
            STATE_EW_GREEN:
                next_state = (timer == 7'd63) ? STATE_EW_YELLOW : STATE_EW_GREEN;
            STATE_EW_YELLOW:
                next_state = (timer == 7'd3) ? STATE_NS_GREEN : STATE_EW_YELLOW;
            default:
                next_state = STATE_NS_GREEN;
        endcase
    end

    // Output logic
    always @(*) begin
        // Default all outputs to off
        ns_green = 0; ns_yellow = 0; ns_red = 0;
        ew_green = 0; ew_yellow = 0; ew_red = 0;

        case (current_state)
            STATE_NS_GREEN: begin
                ns_green = 1;
                ns_red = 0;
                ew_red = 1;
            end
            STATE_NS_YELLOW: begin
                ns_yellow = 1;
                ns_red = 0;
                ew_red = 1;
            end
            STATE_EW_GREEN: begin
                ns_red = 1;
                ew_green = 1;
                ew_red = 0;
            end
            STATE_EW_YELLOW: begin
                ns_red = 1;
                ew_yellow = 1;
                ew_red = 0;
            end
        endcase
    end

endmodule

