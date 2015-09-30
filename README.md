#Lab 0 Submission
###Nur Shlapobersky, Sarah Walters, David Abrahams

##Circuit Design

![Circuit design](/images/circuit_image.jpg)

When adding together multiple bit numbers, you start at the least significant bit and add them together. Then add together the next most significant bit of the numbers and the carryout of the first addition. You then repeat this process until you have reached the most significant bit. We designed a circuit to follow this process by putting four single-bit full adders together, connecting the carryout from one adder to the carryin of the next one, and outputting each result to a bit of our `sum`.

Overflow can never occur when adding numbers with different signs. Overflow occurs when the sign bit of the result does not match the sign of the two inputs. Therefore, you can calculate overflow by checking if `a[3]` and `b[3]` are the same (an XNOR), and `sum[3]` is different from `a[3]` or `b[3]` (an XOR)).

##Waveforms

Below is our waveform propagation for inputs A and B, and outputs Sum, Carryout, and Overflow. There are a number of times when Sum rapidly switches between values, because it is outputting values before all four adders have finished propagating their values. This causes temporary fluctuations in the values of our carryout and overflow. The worst delay of our circuit was for the overflow output, which for one test has a delay of 450 time units. There was a delay on most of our sum outputs, with a maximum of 350 time units.
![Waveform Propagation](/images/4badderWave.png)

##Testing

We chose tests to cover the four possible carryout/overflow combinations. We separated our tests into:

* No Carryout, No Overflow cases. This occurs when adding two positive numbers (and not overflowing), or when adding a positive and negative and obtaining a negative.
* Carryout, no Overflow. This occurs when adding two negative numbers (and not overflowing), or when adding a positive and negative and obtaining a positive.
* No Carryout, Overflow: This occurs when adding two positive numbers and overflowing.
* Carryout, Overflow: This occurs when adding two negative numbers and overflowing.

We created four tests for each combination. Running

```
iverilog adder.v -o add
./add
```

####No Carryout, No Overflow cases
A | B |  Sum | Cout | O | Ex Sum | Ex Cout | Ex O
---|---|---|---|---|---|---|---
0000 | 0000 |  0000 | 0 | 0 | 0000 | 0 | 0
0001 | 0001 |  0010 | 0 | 0 | 0010 | 0 | 0
1110 | 0001 |  1111 | 0 | 0 | 1111 | 0 | 0
1001 | 0100 |  1101 | 0 | 0 | 1101 | 0 | 0

####Carryout, No Overflow cases
A | B |  Sum | Cout | O | Ex Sum | Ex Cout | Ex O
---|---|---|---|---|---|---|---
0110 | 1101 |  0011 | 1 | 0  | 0011 | 1 | 0
1111 | 1001 |  1000 | 1 | 0  | 1000 | 1 | 0
1100 | 1101 |  1001 | 1 | 0  | 1001 | 1 | 0
1111 | 1111 |  1110 | 1 | 0  | 1110 | 1 | 0

####No Carryout, Overflow cases
A | B |  Sum | Cout | O | Ex Sum | Ex Cout | Ex O
---|---|---|---|---|---|---|---
0101 | 0110 | 1011 | 0 | 1 | 1011 | 0 | 1
0111 | 0011 | 1010 | 0 | 1 | 1010 | 0 | 1
0001 | 0111 | 1000 | 0 | 1 | 1000 | 0 | 1
0111 | 0010 | 1001 | 0 | 1 | 1001 | 0 | 1

####Carryout, Overflow cases
A | B |  Sum | Cout | O | Ex Sum | Ex Cout | Ex O
---|---|---|---|---|---|---|---
1110 | 1001 | 0111 | 1 | 1 | 0111 | 1 | 1
1000 | 1101 | 0101 | 1 | 1 | 0101 | 1 | 1
1011 | 1010 | 0101 | 1 | 1 | 0101 | 1 | 1
1001 | 1001 | 0010 | 1 | 1 | 0010 | 1 | 1

##FPGA Implementation

In order to run our 4-bit adder on the FPGA, we added  `lab0_wrapper.v` and `adder.v` to our Vivado as sources. We added `ZYBO_master.xdc` as a constraint, then uncommented the lines pertaining to the four switches, buttons, and LEDs. We tested our FPGA implementation by running each of the above 16 test cases.

We took photos of the first of the test cases with both carryout and overflow (b1110 + b1001 = b0111, carryout 1, overflow 1).

![First input, b1110](/images/00_input0.jpg)
![Second input, b1001](/images/01_input1.jpg)
![Resultant sum, b0111](/images/02_sum.jpg)
![Carryout 1 and overflow 1](/images/03_carryout_overflow.jpg)

##Summary Statistics

![Resource utilization](/images/resource_utilization.png)

When implemented on the FPGA, the design uses nine flip-flops, nine lookup tables, thirteen inputs and outputs, and one global buffer.

![Power summary](/images/power_summary.png)
![Power on chip](/images/power_onchip.png)

The design requires 0.113 W of power in total. Only 8% of the power is dynamic; of the dynamic power, the vast majority (91%) is used for I/O.

![Setup timing](/images/timing_setup.png)
![Hold timing](/images/timing_hold.png)
![Pulsewidth timing](/images/timing_pulsewidth.png)

The worst negative slack within the design is 6.908ns. This means that the path which is closest to failing still passes by 6.908ns -- so, all of the paths pass. Accordingly, the total negative slack is 0 ns. Similarly, the worst hold slack is 0.293ns, and total hold slack is accordingly 0ns; worst pulse width slack is 3.5ns, and total pulse width negative slack is 0ns. Because none of these numbers are negative, we know the timing within the design is working out.
