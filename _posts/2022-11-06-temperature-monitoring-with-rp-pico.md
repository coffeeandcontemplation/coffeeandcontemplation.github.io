---
layout: post
title: Temperature monitoring with raspberry pi
comments: true
tags: [contemplation, hacks, TIL]
---

We bought a new Air conditioner for the house. It doesn't feel like the AC is able to regulate the temperature to what we set. But, humans are often wrong about these kind of things. A simple thermometer would have done the trick - but we need time-series temperature data to verify the AC function.

Enter - the [Raspberry pi pico](https://www.raspberrypi.com/documentation/microcontrollers/raspberry-pi-pico.html). It has a built-in ambient temperature sensor and supports micropython. It also has 2MB of storage. So, logging the temperature to a file should be easy.

*This is the "finished" project:*
![](/public/pico.jpg "finished project")

The OLED screen displays the latest temperature reading. Interfacing with the display was very easy. Tom's hardware has a [very good guide](https://www.tomshardware.com/how-to/oled-display-raspberry-pi-pico) explaining this. [Reading the temperature](https://how2electronics.com/read-temperature-sensor-value-from-raspberry-pi-pico/) was trivial too.

The dirty code I put together looked like this

```python
import machine
import utime
from machine import Pin, I2C
from ssd1306 import SSD1306_I2C

i2c=I2C(0,sda=Pin(0), scl=Pin(1), freq=400000)
oled = SSD1306_I2C(128, 64, i2c)
 
sensor_temp = machine.ADC(4)
conversion_factor = 3.3 / (65535)

file = open("temp_log.csv","a")

def get_temperature():
    reading = sensor_temp.read_u16() * conversion_factor 
    temperature = 27 - (reading - 0.706)/0.001721
    return temperature

while True:
    temperature = get_temperature()
    print(temperature)
    oled.fill(0)
    oled.text("Temp: %.2f" % temperature, 10, 10)
    oled.show()
    file.write("%.2f\n" % temperature)
    file.flush()
    utime.sleep(20)
```

The code worked most of the time. But, there were instances when the temperature reading just wouldn't change. Also, nothing was written to the file. Some debugging is required to find and fix the issue.

### Random learnings
- If you name the file as `main.py`, it will run when the pico is powered on.
- `"%0.2f"` will convert a float to a string and round off to 2 decimal places.
- A power bank is good enough to power the pico away from other power sources.
