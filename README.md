# PWM input using the Raspberry Pi Pico PIO 

Most microcontrollers have hardware to produce Pulse Width Modulation (PWM) signals. But sometimes it is useful to be able to read PWM signals and determine the period, pulse width and duty cycle.

Based on the method to measure pulses with PIO code as described [here](https://github.com/GitJer/HC-SR04), a PWM Input can be made.

## Algorithm

In pseudo-code the algorithm is as follows:

```
    loop:
       reset the 'timer'
       loop: 
          decrement timer
          test for falling edge 
       record timer value as pulse width (actually, (0xFFFFFFFF - x)*2*1/125MHz is the pulse width)
       loop:
          decrement timer
          test for rising edge
       record the timer value as period (actually, ... its complicated, see below)
```

## 'Timer' for reading 0 and 1

Because the PIO `jmp` instruction makes the jump if the argument is true, there is a difference between testing for a 0 and testing for a 1. In the PIO code this results in testing for 1 (the pulse width) with 2 pio clock cycles, while testing for 0 (the period) can be done in 3 pio clock cycles. For the pulse width this is all fine, but for the period its not: it is partly measured with the 2 clock cycles and partly with 3 clock cycles per loop. In the C/C++ code this is taken care of.
