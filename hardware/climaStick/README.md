# Thinger.io ClimaStick Quick Start Gide

This board is a complete Internet of Things development kit, that integrates WiFi connectivity along with a set of powerful sensors to provide environmental and motion sensing. This way, it is possible to create several connected projects easily. It is fully compatible with the Thinger.io cloud infrastructure, and provides easy to use libraries that can be used in the Arduino IDE. 

it is ready to:
- Meassure environmetral variables such as Temperature, relative humidity, barometric pressure or luminic radiation.
- Take inertial measurements like vibrations, accelerations, angles and compass.
- Export all data for bucket storage or monitoricing process.

<p align="center">
<img src="assets/i0.PNG?raw=true"   width="600" height="350" />
</p>


## GETTING STARTED
#### RUNNING STOCK EXAMPLE CODE:
> 1. Power up the board with a micro USB-B 5V supply, like a cell phone charger. 
2. Sign-up on thinger.io
3. Using a Smartphone, look for and connect to “Thinger device” LAN.
4. Open your web browser and enter your Thinger.io account name, and your WiFi credentials.
5. Get out of “thinger device” WiFi Access point and connect to your home WiFi.
6. Open your browser on thinger.io > dashboards > ClimaStick_Example

#### START PROGRAMING
1- Download and install the working environements:
 - CP2102 drivers from silicon labs web site:
http://www.silabs.com/products/mcu/pages/usbtouartbridgevcpdrivers.aspx
 - Arduino IDE v1.6.13 or newer:
 https://www.arduino.cc/en/main/software
 
2- Configure Arduino IDE, opening next interfaces:
  > 1. On File > Preferences > Additional_Boards_URL_Manager to include the "ESP8266 boards manager link" that you can retrieve from Github community project: https://github.com/esp8266/Arduino
<p align="center">
<img src="assets/i4.PNG?raw=true"   width="600" height="500" />
</p>
2. On Tools>Board>Board_manager and look for ESP8266 community board firmware, and install the last version.
<p align="center">
<img src="assets/i5.PNG?raw=true"   width="600" height="400" />
</p>
3. Now you can program almost any ESP8266 directly from the Arduino IDE. From the Tools > Boards you should see now the new ESP8266 boards installed. To program the Thinger.io ClimaStick you should select **NODE_MCU V1.0 (ESP-12E Module)**.
<p align="center">
<img src="assets/i6.png?raw=true"   width="600" height="550" />
</p>
4. On Edit > include_libraries > libraries_manager, look for the last version of **Thinger.io** libraries. In this step we are dowloading all thinger.io libraries sourcecode, and examples.
5. Repeat step 4, to download the ClimaStick libraries and example codes.
6. Connect the ClimaStick to your computer and select its serial communication port number on: Tools > port
7. Now you can start developing with Thinger.io ClimaStic! Check out the source code examples opening Arduino IDE: File > Examples > Thinger.io > ClimaStick Basic

#### UPLOADING FIRMWARE 
**Before programming check out process**
- Be sure that your USB wire allows data transmission.
- Verify that your operative system recognised the CP2102 serial port interface.
- Check out the selected serial COM number on Arduino IDE: Tools>port

- &#9888; **Flash boot mode:** If you follow "uploading firmware steps", and there is any problem to stablish the communication with the board, you can force a flash boot up keeping pressed USR button and making reset (pressing RST button).
 
**Running the example code**

Now you are ready to open an example code and upload it to your ClimaStick. On Arduino Ide, File > Examples > ClimaStick you will find some examples that explains most usefull routines from ClimaStick.h, divided on diferente subjects like IMU, Battery, CLima and RGB led. The ClimaStick_Auto example code, is a little sketch that implements all ClimaStick.h functions and create Thinger resources to update all data to the server:
 
 ```cpp
#define _batterySwProtection_ //This parameter enables the auto-sleep mode when battery voltage flows down 3.65V
#include <ClimaStick_Auto.h>
 
#define USERNAME "your_user_name"
#define DEVICE_ID "your_device_id"
#define DEVICE_CREDENTIAL "your_device_credential"

#define SSID "your_wifi_ssid"
#define SSID_PASSWORD "your_wifi_ssid_password"

ThingerESP8266 thing(USERNAME, DEVICE_ID, DEVICE_CREDENTIAL);
 
void setup() {
  thing.add_wifi(SSID, SSID_PASSWORD);
  init_resources(thing);  
  
}

void loop() { 
  thing.handle(); 
}
 ```
 
 
## CLIMA_MOVE LIBRARY
#### EASY FUNCTIONS
ClimaStick.h contains some routines designed to simplify data retrieving and programing process. This functions returns all reads from sensors into complex structs that you can use like in the example below:
```cpp
 struct accelerometer accelgyro = get_acceleration();  //refresh accel and gyro variables
 struct compass myCompass = get_compass();             //refresh compass variables
 struct magnet myMagnet = get_magnet();                //refres  magnet variables
 struct environmental myClima = get_clima();            //fill all climatologic variables from clima struct
 struct time myTime = get_time();                       //starts NTP client and refresh time variables
 float voltage = get_batteryVoltage();                  //returns main voltage into a float value
 float remainBattery = get_batteryLoad();               //returns remain battery load porcentage
```
#### STRUCTS
After executing this functions, you can access to each data calling it and saving into your local variable. Next examples shows how to extract all this variables:
```cpp
//Accelerometer:
    uint16_t ax = accelgyro.ax; 
    uint16_t ay = accelgyro.ay;
    uint16_t az = accelgyro.az;
    uint16_t gx = accelgyro.gx;
    uint16_t gy = accelgyro.gy;
    uint16_t gz = accelgyro.gz;
 
 //Magnetometer:
    float mx = myMagnet.x;
    float my = myMagnet.y;
    float mz = myMagnet.z; 
    float normaliced_x = myMagnet.nx;
    float normaliced_y = myMagnet.ny;
    float normaliced_z = myMagnet.nz; 
 
 //Compass:
    float heading = myCompass.heading;
    float headingDegrees = myCompass.headingDegrees;
 
 //Environmental:
    float temperature = myClima.temperature;
    float humidity = myClima.humidity;
    float pressure = myClima.pressure;
    float altitude = myClima.altitude;
    float luminosity = myClima.luminosity;
    float lux = myClima.lux;
 
 ```
#### EMBEDED RGB LED


```cpp
 rgb(String "colorName");
 rgb(int red, int green, int blue);
```

#### ENVIRONMENTAL SAMPLING PROTOCOL
Due to low power dissipation capacity, climaSticks gets hot after a few seconds of working, so it is not possible to make high-rate real time temperature reads. For reading temperature process, it is necessary to hibernate the processor, and wait a few minutes before to executing a high precision temperature read. This function is available using ESP.deepsleep(<-milliseconds->). during specified time period, the running process will be stopped,  and processor will not respond to any inquiry or WiFi transmissions. When the countdown is over, the device will  make a hard reset and the process will start from new.


```cpp
#include <Clima_Move.h>

#define USERNAME "your_user_name"
#define DEVICE_ID "your_device_id"
#define DEVICE_CREDENTIAL "your_device_credential"

#define SSID "your_wifi_ssid"
#define SSID_PASSWORD "your_wifi_ssid_password"

void setup() {
  thing.add_wifi(SSID, SSID_PASSWORD);
  climaMove(thing);   
} 
 
void loop(){
  thing.handle();
  
  thing.stream(thing["environment"]);
  
  if(sleepMode==1){
      ESP.deepSleep(5*60*1000000);      //Sleeping 5 minutes until reset 
  }

}
```

>**&#9888; DEEPSLEEP CONSIDERATIONS:** 

> - During the deepSleep mode, it is not possible to flash code. To change the program you will need to make a forced flash mode boot up.
> - Note that, when the processor makes a hard reset, all dynamic variables will lost its values. 

## OTHER CONSIDERATIONS
##### GENERAL ADVERTISEMENT

-  It should be powered with a 5V and 250 to 1000mah USB power supply.
-  This board has a low heat dissipation capacity, so it is normal that it keeps hot on high transmission processes. The temperature sensor could takes values up to 70ºC on full duplex communication process.
-  If you use the VIN power header, be careful to connect it in the correct position, as it is showed on image2. Not following this directive could damage the protection diod.
<p align="center">
<img src="assets/i1.PNG?raw=true"   width="200" height="180" />
</p>
-  This device is developed like a software testing platform and it is not protected to support hard weather conditions without the appropriate cover case.
- Do not touch the components Surfaces to grab the device, it will sport the electrical contacts, producing shortcuts and wrong working. keep holding from lateral edges like in the illustration below:
<p align="center">
<img src="assets/i2.PNG?raw=true"   width="200" height="300" />
</p>

- If necessary, clean the circuit using a non-damaging contact cleaner like Isopropyl alcohol and soft brush. 
-  Store in a cool, dry place. Protected from dust.

#### BATTERY POWERING
- Using BAT power header, be careful to wire up correctly, as it is showed on image below:
<p align="center">
<img src="assets/i3.PNG?raw=true"   width="200" height="180" />
</p>
- BAT header is connected to a lithium battery charger that can manage 3.7Vdc, 500mah Li-Po / li-ion batteries charge and discharge process. 

&#9888; if you are ussing a different battery, plug it on VIN connector.

&#9888; if cell voltage flows under 3.6V, an authomatic battery protection circuit will power off the system. 

 - To load a battery, connect it on BAT heather and power on the ClimaStick through USB / VIN connectors. The battery charger will manage the charging voltage to increase life battery and stops charging cycle when voltage ups to 4.2Vdc.

 - Never use a computer USB port to charge batteries, it could damage your computer’s circuitry. 


#### EXENCION OF RESPONSABILITY
- This device its commercialized by Thinger.io platform like a software development kit, so it is not subject to commerce homologation rules. The owner is liable for all injuries to third parties and damage to their properties. 
