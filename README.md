# Semiconductor_Engineering
Project Description: 

I have designed and coded a Finite State Machine (FSM) for a timed traffic light controller that has the following behaviors:

1) N/S direction traffic has a green light for 64 clocks. E/W direction has a red light.
2) N/S direction traffic has a yellow light for 4 clocks. E/W directionhas a red light.
3) E/W direction traffic has a green light for 64 clocks. N/S direction has a red light.
4) E/W direction traffic has a yellow light for 4 clocks. N/S direction has a red light.

The FSM should have an asynchronous active low reset input, and reset the FSM to starting with a green light in the N/S direction.

The FSM should drive six output signals name: ns_green, ns_yellow, ns_red, ew_green, ew_yellow and ew_red.
