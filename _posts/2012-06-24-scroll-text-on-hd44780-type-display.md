---
layout: post
title: "Scroll text on HD44780 type display"
description: "Every now and then it's fun to play with HD44780 type displays."
author: "Kim Blomqvist"
author-email:

tags:
- hd44780

categories:
- article

level: beginner

tweet-text: "Scroll text on HD44780 type display #aery32devzone"

summary: "Every now and then it's fun to play with displays &ndash; this time scrolling text to infinity"
---

<div style="position:relative;padding-bottom: 56.25%;height:0;margin-bottom:20px;">
<iframe src="http://www.youtube.com/embed/54DsvyTVbbk?showinfo=0;wmode=transparent;theme=light;color=white;autohide=0" style="position:absolute;height:100%;width:100%;top:0;left:0;" frameborder="0"></iframe>
</div>

<span class="label label-info">Update!</span> Article updated to be consistent with the framework version 0.4.1.

Every now and then it's fun to play with displays. This time I was curious to scroll text to infinity, see the video above. To get started let's copy the [HD44780 example code](https://raw.github.com/aery32/aery32/master/examples/displays/hd44780.c) over <code>main.cpp</code>. This example code works on SPI bus, as it has been described in previous post, [Using HD44780 type two row OLED display via SPI bus](http://devzone.aery32.com/2012/05/27/using-hd44780-type-two-row-oled-display-via-spi-bus/). To make the display scroll from right to left, we have change its entry mode to <code>HD44780_EMODE_INCRNSHIFT</code>. It's also good idea to disable the cursor from blinking. With these changes the display initialization sequence should look like this

<pre class="prettyprint lang-c">
/* Display initialization sequence */
delay_ms(2);
display_instruct(HD44780_DL8BIT|HD44780_FONTBL_WE1);
display_instruct(HD44780_DISPLAY_OFF);
display_instruct(HD44780_CLEAR_DISPLAY);
display_instruct(HD44780_RETURN_HOME);
display_instruct(HD44780_EMODE_INCRNSHIFT);
display_instruct(HD44780_DISPLAY_ON);
</pre>

Now let's define the string what we want to scroll on the display. This piece of code comes at the top of <code>main()</code> function.

<pre class="prettyprint lang-c">
int main(void)
{
	char buf[] = "Find this demo from devzone.aery32.com... ";
	int len = strlen(buf);
</pre>

Next remove the greeting message below the line where the LED has been switched on. Then edit the main loop, upload the program to your Aery32 board and see what happens.

<pre class="prettyprint lang-c">
for(;;) {
	for (int i = 0; i &lt; len; i++) {
		display_putchar(buf[i]);
		delay_ms(200);		
	}
}
</pre>

First nothing seems to happen. Then suddenly the text appears and scrolls nicely from right to left &ndash; mission accomplished \o/ Ooops... but what happens now? Now there are text also in the second row. To understand this, you have to understand where's the display's home in its DDRAM. When the entry mode is incremental and shift, the display's home is at the top right side of the DDRAM, and that's the place where the first written character will appear.

![NHD‐0220 DDRAM table](/images/nhd-0220_ddram.png "NHD‐0220 DDRAM table")

To see characters sooner we have to fill this hidden buffer before hand. We also have to take care of setting the DDRAM pointer to point back to the first index (home) just before it's about to change the row. So let's introduce a new function for this

<pre class="prettyprint lang-c">
void display_scrolleft(const char *buf, int n, uint8_t rowlen, uint8_t offset)
{
	static bool initialized = false;
	static int i = 0, j = 0;

	// Fill the buffer, only at the first call of this function
	if (!initialized) {
		j = rowlen - offset;
		for (int k = j; k &gt; 0; k--) {
			display_putchar(buf[i++]);
			if (i == n) i = 0;
		}
		initialized = true;
		return;
	}

	// Take care of indexes
	if (i == n) i = 0;
	if (j == rowlen) {
		display_instruct(HD44780_RETURN_HOME);
		j = 0;
	}

	display_putchar(buf[i++]);
	j++;
}
</pre>

Now we can use this in the main loop. Try it with different offsets to see how it acts.

<pre class="prettyprint lang-c">
for(;;) {
	display_scrolleft(buf, len, 0x40, 20);
	delay_ms(200);
}
</pre>

&#10065;