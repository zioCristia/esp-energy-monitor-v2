# Esp energy monitor - Version 2
Esp32 based module, 220V ac powered, energy monitor with three ct sensors (for solar power, home power and air conditioning power), and an LCD I2C display for visualization. Integrated with ESPhome in my IoT house based in Home Assistant.
Any possibility of improvement is accepted.

The board's design allows it to be inserted into the rail of the home's breaker box by printing a 3D component.

### Added features
This is an update of the previous [version](https://github.com/zioCristia/esp-energy-monitor-v1) where I added a third sensor for reading the consumption of air conditioners and the possibility of adding additional boards for future ideas.
[Here](https://github.com/zioCristia/smart-irrigation-control-unit) you can find a compatible board to add smart garden irrigation functionality to the board.

The board's design also allows it to be inserted into the rail of the home's breaker box by printing a 3D component.

![alt text](/images/finalPcbTop.jpg)
![alt text](/images/finalPcbBot.jpg)

## Table of contents
* [General info](#general-info)
* [Hardware](#hardware)
* [Software](#software)
* [Pcb](#pcb)
* [Compatible shields](#compatible-shields)
* [Contribution](#contribution)

# General info
This is the second version of a simple energy monitor I built for my home. It has three ct sensors that control the current of the solar panels production, the house consumption and the air conditioning. These sensors are controlled by an esp32 board for connection via wi-fi to the home assistant thanks to the Esphome software. 

The board could be powered with a 220V ac, so no external transformer is needed, but for extensive use, especially when connected to some board extensions, it is recommended to use an external one and plug it directly into the 5V holes.

I have also added a 16x2 lcd so that I can have an immediate output in my electrical service panel.

The total cost for a board with three CT sensors (included) is around 25/30€.

# Hardware
The module is composed of:

* esp32
* sct 013 030 30A/1V as ct sensor, x3
* 16x2 lcd display with i2c interface
* 3.5mm jack connector with 5 pin, x3
* 10k ohm resistor, x6
* 10uF capacitor, x4
* 75ohm resistor, x3 (not mandatory, depending on the CT sensor)

A list with the main components and links could be found [here](https://docs.google.com/spreadsheets/d/1uGH7hN6ZSQ5MoKFjuWFCn1lEjyLJ1oxg2DWpdToigKc/edit#gid=1313427219).
The burden resistor (75ohm) should be added depending on the CT sensor. In that case, to have the best readings, it should be changed based on the max current you are going to measure with the ct sensors, according to the following formula present in [openenergymonitor](https://docs.openenergymonitor.org/electricity-monitoring/ct-sensors/interface-with-arduino.html) page:

```
Burden Resistor (ohms) = (AREF * CT TURNS) / (2√2 * max primary current)
```

# Software
The software I have used is EspHome in order to integrate the sensor with my HomeAssistant server but you may as well use another software to your liking.
You can see [here](https://github.com/zioCristia/esp-energy-monitor-v2/blob/main/energy_monitor.yaml.exemple) an example of the software uploaded on the board.

The filter section is used to obtain the right value of current from the sensor. I've done it confronting the values read with a well know source (like a phone or an electric heater).
Moreover I've added a part to read zero values when we are near zero due to some interference that never allows to have values equal to zero.
```
filters:
      - calibrate_linear:
          # Measured value of 0.0065 maps to 0A
          - 0.0065 -> 0
          # Known load: 13.783A
          # Value shown in logs: 0.2348A
          - 0.2348 -> 13.783
      - lambda: |-
          if (x > 0.2) return x;
          else return 0;
```

I've added a software sensor which gives me the difference between the in power and the out power with which I do some automation in home assistant and allows me to maximize the usage of current produced by the solar panels.

The toGridEnergy and the toGridPower is used to interface with the new energy management feature in home assistant.

The lcd prints the solar power as IN, home power as OUT, the hour in the top right and below the difference between IN-OUT, so the differencePower.

# Pcb
The pcb layout is extremely simple with enough space for all the parts. The components are all through hole and the female jack connector is with 5 pins. Anyone can make this pcb at home with just a soldering iron.

You can find the whole eagle project in [this folder](https://github.com/zioCristia/esp-energy-monitor-v2/tree/main/esp-energy-monitor-v2).

The board's design allows it to be inserted into the rail of the home's breaker box by printing a 3D component.

![alt text](/images/circuitSchema.png)
![alt text](/images/pcbLayout.png)

# Compatible shields
* [Irrigation control unit](https://github.com/zioCristia/smart-irrigation-control-unit)
  
# Contribution
Feel free to report any bugs or feature requests.
