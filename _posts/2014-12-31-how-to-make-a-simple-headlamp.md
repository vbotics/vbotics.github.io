---
layout:     post
title:      How to make a simple headlamp?
date:       2014-12-31 10:59:48
author:     Vishal Raveendranathan
summary:    A basic DIY to get your hands dirty with embedded system...
categories: blog
thumbnail:  heart
tags:
 - robotics
 - diy
 - embedded c
 - electronics
---

I would like to share you a basic embedded project that I designed.

**What does this Headlamp do?**
  
This is an automatic headlamp which can be turned on/off by just waving the hand above the lamp. That is, it does not need to be touched or there is no need to operate any switch to turn the headlamp on/off. This can be used by persons who work in an environment where their hands are dirty and cannot operate the headlamp with their unclean hands.

**Things needed:**
  
I have made it by using a microcontroller to control the sensor that is attached with the headlamp to detect the gesture.
  
Microcontroller: ATTiny45 would be great if available, but unfortunately I couldn’t get it over here. So I used ATMega8. It was a bit bulky. I wanted to design the headlamp very compact.
  
Battery: 9V Li-ion battery. (Can use any)
  
Sensor: IR sensors. (I tried using PIR motion detection sensors but it was bulky and it was too sensitive even after tweaking a lot with the resistance value. It was very sensitive and could not use it for this purpose because in a headlamp the head always moves and is not stationary. So the PIR thinks that there happened to be a wave by the hand and hence keep fluctuating the output.)
  
Miscellaneous: Elastic band, cute plastic box (to fit in the circuit), copper board, solder, iron, thin wires, voltage regulator, cutters etc.

**How is this made?**
  
The circuit is very simple. Hope you are aware of the basic microcontroller circuit. In that small tweaks will make this headlamp ready.

![Basic ATMega8 connection circuit](https://i.ibb.co/KWbqvMM/atmega-circuit-schematic.png)

Connect the IR sensors in such a way that the output (data pin) from the sensor is connected to the input pin of the microcontroller (say ATMega8, PB0 or PB1 etc). Then take output from the ATMega8 (PB2 or PB3 etc) to one of the terminals of the LED which is connected to the circuit and then neutralize it. That is the basic logic of the circuit.

Since we used a microcontroller here, it is our necessity to code the microcontroller. It is written in embedded C, which is no big different from the fundamental C programming. Also this is a very basic code and it will not be hard to understand. Do let me know if you find any of it hard.

Code:

```c
/*
 * final-headlamp.c
 *
 * Created: 14-May-14 1:18:46 AM
 * Author: Vishal
 */

#define F_CPU 16000000L

#include <avr/io.h>
#include <util/delay.h>
#include <avr/iom8.h>

int main(void)
{
    DDRB = 0x00;    // port B input PB1
    //PORTB = 0x00; // tri-state enable

    DDRD = 0x07; // LED on PD0,PD1,PD2
    PORTD = 0x00; // Output initially low

    //DDRB &= ~(1 << PB1); // set portB PB1 input

    unsigned char flag=0;
    while(1)
    {
        PINB &=0b00000010;
        //TODO:: Please write your application code 
        if(PINB == 0b00000010) // PB1 is high NO OBJ
        {
            if(flag==0)
            {
                PORTD=0x00; //LED OFF
                flag=0;
                _delay_ms(100);
            }
            else
            if(flag==1) // LIGHT ON CONTINUE
            {
                PORTD=0x07; //LED ON LATCH
                //flag=2;
            }

        }
        //PINB &=0b00000010;

        if(PINB == 0b00000000) //PB1 is low OBJ DETECTED
        {
            if(flag==0)
            {
                PORTD =0x07; //LED ON
                flag=1;
                _delay_ms(100);
            }
            else
                if(flag==1) // Second wave LIGHT OFF
                {
                    PORTD=0x00; //LED OFF LATCH
                    flag=0;
                    _delay_ms(100);
                }
        }
        //_delay_ms(5);

    }
}
```

For programming, you can use [AVR Studio][1] which is a wonderful software by Atmel. Now compile the code and burn the code in the microcontroller with a USB programmer.

![Things needed for prototyping](https://i.ibb.co/D99wYjT/things-needed-for-prototype.jpg)

![Building up the Circuit](https://i.ibb.co/vHQbcYN/building-up-the-circuit.jpg)

![Almost set! That is a powerful 3V LED](https://i.ibb.co/mtVxVNm/3v-led.jpg)

Now half of the project is done. Make the prototype of the circuit in a breadboard and test your circuit properly. Use the multimeter to keep a track of all the voltages are being the same as you expect it to be. Especially check the IR sensor outputs and the microcontroller input/output. Once everything is fine then move on to the copper board for soldering the circuit to make it a solid one. Make sure you design the circuit in such a way that the LED, IR sensor and Switch are exactly in the position where you want it to be in the plastic casing. This is the trickiest part because once you have soldered it and things are not fitting in the right places it can make things worse. De-soldering it not fun at all.

![The next stage- inserting the components in the right places](https://i.ibb.co/6nGZbPg/getting-it-placed.jpg)

![Soldering the circuit](https://i.ibb.co/D4TdK6k/soldering-the-circuit.jpg)

![Fitting up the circuit inside the casing](https://i.ibb.co/cY96SY4/fitting-the-circuit.jpg)

![Finally the headlamp is packed and ready!](https://i.ibb.co/1Q0J5yW/headlamp-ready.jpg)

The picture shown above is the one I have designed. It can be made more fancy and cool.

You can watch me testing the circuit [here][2]

Well so hope you had a good read and do share with me your experience of making simple things. As Dr. John Cohn says, “Play!!!” Play and explore your creativity with the basic science and engineering which you learn. Engineering is fun if you play with it! ☺

Vishal

[1]: http://www.atmel.com/microsite/atmel_studio6/
[2]: http://youtu.be/N5r0FB97ikA