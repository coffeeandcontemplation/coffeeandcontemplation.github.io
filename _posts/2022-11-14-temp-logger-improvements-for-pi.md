---
layout: post
title: Temperature logger improvements
comments: true
tags: [hacks, raspberry, pi, pico]
---

[Part1](/2022/11/06/temperature-monitoring-with-rp-pico/)  
[Part2](/2022/11/09/plotting-temp-timeseries/)

## Using the on-board LED to monitor sensor reads
The raspberry pi pico based temperature logger "hung" at times. It didn't write anything to the file at all. But, it wasn't obvious that it had stopped working. Initially, I had plans to turn on the on-board LED when writing to a file. But, more experienced programmers suggested that `sensor_temp.read_u16()` is more likely to be the blocking call. So turning on the LED before reading the sensor and turning it off after would give a clear indication. The LED will remain on if the call is blocked.

```python
led = Pin(25, Pin.OUT)

def get_temperature():
    led.on()
    reading = sensor_temp.read_u16() * conversion_factor 
    temperature = 27 - (reading - 0.706)/0.001721
    led.off()
    return temperature
```

## Logging time along with temperature
It looks like the pico has a [real time clock](https://docs.micropython.org/en/latest/library/machine.RTC.html?highlight=rtc#machine.RTC.datetime) and can be accessed with `machine.RTC()`. When running the code using Thonny, it initializes the RTC with the current time. But when running outside of thonny, the RTC should get set to midnight of Jan 1, 2015. We'll write the time along with the temperature to get a sense of when(/if) the pico fails to write to the file.


```python
rtc=machine.RTC()
now = rtc.datetime()
file.write("{hh:02d}:{mm:02d}:{ss:02d}, {temperature:.2f}\n"
    .format(hh=now[4], mm=now[5], ss=now[6], temperature=temperature))
```
