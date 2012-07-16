---
layout: post
title: "Using HD44780 type two row OLED display via SPI bus"
description: "How to use HD44780 type OLED display with Aery32 Software Framework."
author: Kim Blomqvist
author-email:

tags:
- spi
- hd44780

categories:
- article

level: beginner

tweet-text: "Using HD44780 type two row OLED display with #Aery32 Software Framework #aery32devzone"

summary: "In this article it's quickly described how to connect OLED display to Aery32 devboard using serial peripheral interface bus (SPI). Only 6 pieces of jumper wires are needed, 4 for the SPI bus and 2 for the power (3.3V and GND)"
---

<a href="/images/hello_aery32_devs_hires.JPG" title="OLED display connected to Aery32">
<img class="pull-right" itemprop="image" src="{{ site.url }}/images/hello_aery32_devs_lores.jpg" alt="OLED display connected to Aery32" /></a>

<span class="label label-info">Update!</span> Article updated to be consistent with the framework version 0.2.

In this article I will quickly describe how to connect OLED display to Aery32 devboard using serial peripheral interface bus (SPI). Only 6 pieces of jumper wires are needed, 4 for the SPI bus and 2 for the power (3.3V and GND). For the display module I chose [NHD‐0220DZW‐AG5](http://www.newhavendisplay.com/specs/NHD-0220DZW-AG5.pdf) (2 lines x 20 characters) that has Hitachi HD44780 LCD controller. The selection criterion was that [HD44780 display controllers](http://en.wikipedia.org/wiki/Hitachi_HD44780_LCD_controller) are very common and that the display was working at 3.3V supply voltage.

By default NHD‐0220DZW‐AG5 MPU interface is set to work in parallel mode, so I had to change the jumper setting at the bottom side of the display module to enable SPI interface. Hereby, I removed the jumper resistors marked with red rectangles, jumped JSC with 0R resistor (marked by light green) and changed the position of the H_PS_L jumper to the position marked by the orange ractangle. Afterwards I noticed that jumper J68 would not have to be removed, so you can spare your soldering iron for that.

![NHD‐0220DZW‐AG5 jumper selection for SPI](/images/nhd-0220dzw-bottom-serial-selection.png "NHD‐0220DZW‐AG5 jumper selection for SPI")

<table>
	<caption>Connections table</caption>
	<thead>
		<tr>
			<th>Aery32</th>
			<th>Dipslay module</th>
		</tr>
	</thead>
	<tr>
		<td>PA10, CS</td>
		<td>PIN16</td>
	</tr>
	<tr>
		<td>PA11, MISO</td>
		<td>PIN13</td>
	</tr>
	<tr>
		<td>PA12, MOSI</td>
		<td>PIN14</td>
	</tr>
	<tr>
		<td>PA13, SCK</td>
		<td>PIN12</td>
	</tr>
</table>


Initialize the SPI bus
----------------------

First SPI bus has to be initialized, so let's include [SPI module](http://aery32.readthedocs.org/en/latest/module_functions.html#serial-peripheral-bus-spi-include-aery32-spi-h) into our `main.cpp`. We will also need `aery32/delay.h`

<pre class="prettyprint lang-c">
#include "board.h"
#include &lt;aery32/gpio.h&gt;
#include &lt;aery32/spi.h&gt;
#include &lt;aery32/delay.h&gt;
</pre>

I have chosen to connect the display to the SPI0 with NPCS0 (slave select 0), so I have to initialize those GPIO pins before initializing SPI0 itself. The SPI pins are PA10, 11, 12 and 13, that have to be assinged to peripheral function A. This I checked from the [UC3A1's datasheet](http://www.atmel.com/Images/doc32058.pdf) page 45.

<pre class="prettyprint lang-c">
using namespace aery;

#define SPI0_GPIO_MASK      ((1 &lt;&lt; 10) | (1 &lt;&lt; 11) | (1 &lt;&lt; 12) | (1 &lt;&lt; 13))

#define DISPLAY_SPI         spi0
#define DISPLAY_SPI_NPCS    0
#define DISPLAY_SPI_MODE    SPI_MODE3

int main(void)
{
	init_board();
	gpio_init_pin(LED, GPIO_OUTPUT);
	gpio_init_pins(porta, SPI0_GPIO_MASK, GPIO_FUNCTION_A);

	// ...
</pre>

Then init and enable SPI0 as a master, and its NPCS0 pin as it was defined above.

<pre class="prettyprint lang-c">
spi_init_master(DISPLAY_SPI);
spi_setup_npcs(DISPLAY_SPI, DISPLAY_SPI_NPCS, DISPLAY_SPI_MODE, 10);
spi_enable(DISPLAY_SPI);
</pre>

Show strings on the display
---------------------------

I would like to write to the display with `puts()` like function, so let's make a function for that

<pre class="prettyprint lang-c">
int display_puts(const char *buf)
{
	int i = 0;
	for (; *(buf+i); i++)
		display_putc(*(buf+i));
	return i;
}
</pre>

That was easy, but it needs `display_putc()` function that we do not have yet. How do we write characters to the display? Yes, by writing bytes to the SPI bus that is connected to the display.

<pre class="prettyprint lang-c">
void display_putc(char c)
{
	display_wrbyte((uint8_t) c);
}
</pre>

Ok, that includes another undeclared function, but don't worry that's easy too :) At this point Aery32 Software Framework comes in help.

<pre class="prettyprint lang-c">
void display_wrbyte(uint8_t byte)
{
	display_wait();
	aery_spi_transmit(DISPLAY_SPI, DISPLAY_SPI_NPCS, 0x200|byte, true);
} 
</pre>

If you were wondering, the transmitted byte is bitwise OR'ed to set RS and R/W bits appropriately. But the cool thing here is that we can now write to the display like this

<pre class="prettyprint lang-c">
display_puts("Hello Aery32 devs!");
</pre>

Instructing the display
-----------------------

Above we created the `display_puts()` function and were very happy with that, but forgot to start our display&ndash;Oops! Somehow we have to be able to instruct the display. The HD44780 display driver has moderately large instruction set and its initialization sequence is also several lines long. But we won't let that discouraged us from making this work! So here's the instruction sequence to be placed after the SPI0 initialization in `main()`.

<pre class="prettyprint lang-c">
display_instruct(HD44780_DL8BIT|HD44780_FONTBL_WE1);
display_instruct(HD44780_DISPLAY_OFF);
display_instruct(HD44780_CLEAR_DISPLAY);
display_instruct(HD44780_RETURN_HOME);
display_instruct(HD44780_EMODE_INCREMENT);
display_instruct(HD44780_CURSOR_ONBLINK);
</pre>

And here comes the large paste that shows the defined instructions and the `display_instruct()` function. Hopefully you are ready for this ;)

<pre class="prettyprint lang-c">
#define HD44780_CLEAR_DISPLAY       0x01
#define HD44780_RETURN_HOME         0x02

#define HD44780_EMODE_INCREMENT     0x06
#define HD44780_EMODE_DECREMENT     0x04
#define HD44780_EMODE_INCRNSHIFT    0x07
#define HD44780_EMODE_DECRNSHIFT    0x05

#define HD44780_DISPLAY_ON          0x0C
#define HD44780_DISPLAY_OFF         0x08
#define HD44780_CURSOR_ON           0x0E
#define HD44780_CURSOR_ONBLINK      0x0F

#define HD44780_LSHIFT_CURSOR       0x10
#define HD44780_RSHIFT_CURSOR       0x14
#define HD44780_LSHIFT_DISPLAY      0x18
#define HD44780_RSHIFT_DISPLAY      0x1C

#define HD44780_DL4BIT              0x28
#define HD44780_DL8BIT              0x38
#define HD44780_FONTBL_ENJA         0x28
#define HD44780_FONTBL_WE1          0x29
#define HD44780_FONTBL_ENRU         0x2A
#define HD44780_FONTBL_WE2          0x2B

#define HD44780_DDRAM_ADDR          0x80
#define HD44780_CGRAM_ADDR          0x40

#define HD44780_BUSYBIT_MASK        0x80

bool display_isbusy(void)
{
	uint16_t rd; /* read data */

	aery_spi_transmit(DISPLAY_SPI, DISPLAY_SPI_NPCS, 0x100, false);
	rd = aery_spi_transmit(DISPLAY_SPI, DISPLAY_SPI_NPCS, 0x100, true);
	return ((HD44780_BUSYBIT_MASK &lt;&lt; 2) &amp; rd) != 0;
}

void display_wait(void)
{
	while (display_isbusy()) {
		delay_us(600);
	}
}

void display_instruct(uint16_t instruction)
{
	display_wait();
	aery_spi_transmit(DISPLAY_SPI, DISPLAY_SPI_NPCS, instruction, true);
}
</pre>

Btw. here is [the entire program](https://gist.github.com/2815704). &#10065;
