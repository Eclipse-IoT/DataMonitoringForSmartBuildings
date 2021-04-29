# Data Monitoring For Smart Buildings

“Buildings of the future must connect the various pieces in an integrated, dynamic and functional way”

## Use Cases

#### What is the problem?
>“The first buildings ever constructed were primitive shelters made from stones, sticks, animal skins and other natural materials”. But as technology becomes smarter, so do our buildings! 

The goal for the future are “Smart Buildings”, which are buildings that can collect data from a variety of sensors to provide various services for its occupants at the lowest environmental cost. “Smart buildings use information technology during operation to connect a variety of subsystems, which typically operate independently, so that these systems can share information to optimize total building performance”. 

The problem is that many modern buildings already contain complex mechanical devices with separate control systems that involve machine-to machine communication, but since “data is general in nature and the communication protocols have been proprietary”, the collection of data from all the subsystems of the building may be difficult. For a smart building to be as effective as possible, it requires connectivity between all the equipment and systems in the building. 


#### What is the solution?
The purposes of the demo is to showcase the aggregation of sensor data from multiple different communication protocols. Some of the sensor data will be communicated from an nRF52 Feather to Leshan server with CoAP, while some of the sensor data will be communicated from a separate Raspberry Pi using MQTT. Although these data streams are of different communication protocols, both streams can be easily aggregated to a single dashboard using Eclipse Streamsheets, so that all of the sensor data can be easily viewed in real-time.

#### *Why is this important?*
Modern buildings may already have plenty of sensors that communicate independently of one another, but the goal of a smart building is to connect and collect this information. With Streamsheets we can ensure that all of this data is aggregated to one place, regardless of the differing communication protocol. Streamsheets also require little-to-no coding experience to use, so a building manager can easily analyze the data that is collected. 

**Keywords/Verticals:** 

## Architecture

#### What components is the demo using?

###### Software Components
- **[Streamsheets](https://docs.cedalo.com/gettingstarted.html)**
  - Aggregate and visualize datastreams
- **[Eclipse Leshan](https://www.eclipse.org/leshan/)**
  - LWM2M Server implementation
- **[Zephyr RTOS](https://docs.zephyrproject.org/latest/)**
  - Real-time operating system for our Feather microcontroller 

**Hardware Components**
- Adafruit Feather nRF52840
- DHT22 Temperature and Humidity Sensor
- 10KOhm Resistor
- SEGGER J-LINK EDU JTAG/SWD Programmer
- JTAG to SWD Cable Adapter Board
- Raspberry Pi

## Hardware Assembly

#### 1) Solder the header pins to the Feather board 
If your Feather board didn't come with the header pins pre-soldered, ensure that they are so that it is breadboard compatible.

#### 2) Wire the sensor
Connect pins 1,2, and 4 of the DHT22 to 3.3V, 11, GND pins of the Feather board respectively. Place resistor between pins 1 and 2 of the DHT22 sensor. Pin 3 is not used.

#### 3) Plug in Programmer
Ensure that the adapter board is connected properly between the J-LINK programmer and the Feather board.

## How to run

#### 1) Ensure that you have a Zephyr Development Environment set up 
Follow the Getting Started Guide on the Zephyr documentation for your respective machine: 
https://docs.zephyrproject.org/latest/getting_started/index.html

#### 2) Ensure that you J-Flash installed and added to PATH
Installer can be found from the following link: 
https://www.segger.com/downloads/jlink/

#### 3) Clone repo and drag files into Zephyr environment
Drag the "lwm2m_dht" folder into your Zephyr Development Environment

#### 4) Build the files
Use the following west command to build the app 
```
west build -p -b nrf52_adafruit_feather lwm2m_dht -- -DCONF_FILE="prj.conf overlay-bt.conf"
```

#### 5) Flash to Feather board
Use the following west command to flash to the board with J-LINK programmer
```
west flash --runner jlink
```

#### 6) Download and run Leshan Demo Server
On your Raspberry Pi, run the following commands to download and start the Leshan Demo Server
```
wget https://ci.eclipse.org/leshan/job/leshan/lastSuccessfulBuild/artifact/leshan-server-demo.jar

java -Djava.net.preferIPv6Addresses=true  -jar leshan-server-demo.jar

```

#### 7) Connect the Feather board to the Leshan Server
Open another terminal on your Raspberry Pi and run the following commands to connect the LWM2M client of the Feather board to the Leshan server on the Pi
```
echo "connect C2:FA:8D:93:21:DD 2" > /sys/kernel/debug/bluetooth/6lowpan_control
ip address add 2001:db8::2/64 dev bt0
ping6 2001:db8::1
```

#### 8) Ensure that the devices are connected properly
The Web-UI for the server can be visited at XXX.XX.X.XXX:8080/#/clients where the X's represent the IP address of the Raspberry Pi. You shoudl see the nrf52_adafruit_feather as a connected client.

#### 9) Launch Streamsheets and create dashboard
If you don't already have Streamsheets installed, you can follow the installation guide below:
https://docs.cedalo.com/installation.html

Creating a Streamsheets is as simple as creating a spreadsheet, to get data from the HTTP stream the HTTP.REQUEST function from the Streamsheets Function Wizard is necessary:
https://docs.cedalo.com/functions/streams/httprequest.html

Temperature and Humidity data from the Feather can be viewed from the following links:
- XXX.XX.X.XXX:8080/api/clients/nrf52_adafruit_feather/3303/0/5700
- XXX.XX.X.XXX:8080/api/clients/nrf52_adafruit_feather/3304/0/5700




