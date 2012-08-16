---
layout: post
title: "Performance testing for fun with AVR32_COUNT system register"
description: "Naive performance testing for how many clock cycles functions take to execute"
author: "Kim Blomqvist"
author-email:

tags:
- "performance testing"
- AVR32_COUNT

categories:
- article

level: advanced

tweet-text: "Performance testing for fun with AVR32_COUNT system register #aery32devzone"

summary: "The cycle counting register, AVR32_COUNT, within the AVR32 UC3 microprosessors can be used for naive performance testing of functions' execution time"
---

The cycle counting register, AVR32_COUNT, within the AVR32 UC3 microprosessors can be used for naive performance testing of functions' execution time. Just reset the AVR32_COUNT before the function call to be tested and read the count value right after the function returns, like this

<pre class="prettyprint lang-c">
#include &lt;avr32/io.h&gt;
uint32_t count = 0;

__builtin_mtsr(AVR32_COUNT, 0);
myfunction();
count = __builtin_mfsr(AVR32_COUNT);
</pre>

For a demonstration I have tested how long it takes for `aery::dtoa()` to complete. This function converts double type value to string. In my test I used it to convert pi in eight decimals precision. The result I got was 3681 CPU cycles, which is approximately 56 us with 66 MHz clock frequency.

<pre class="prettyprint lang-c">
#include &lt;aery/string.h&gt;

char str[20] = "";
char str2[20] = "";
uint32_t count = 0;

__builtin_mtsr(AVR32_COUNT, 0);
aery::dtoa(3.14159265, 8, str);
count = __builtin_mfsr(AVR32_COUNT);
aery::itoa(count, str2);
</pre>

<center><img itemprop="image" src="{{ site.url }}/images/aery32_benchmark_dtoa_with_avr32_count.png" alt="DFU programming" width="350" height="234"></center>