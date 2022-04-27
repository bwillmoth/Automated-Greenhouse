# Automated Greenhouse
## Project Summary
Our Project was to design a mechanical system deemed appropriate by Dr. Dvorak and use the semester to successfully construct it while using/learning the Scrum model. The project we chose was a scaled down automated greenhouse that had a certain number of features that would allow for plant growth with little to no human intervention. The Automated Greenhouse’s primary features include an LCD displaying Temperature and Humidity, A cooling system using a fan motor, and a soil moisture sensor that regulates a submersion pump for watering the plant. The Greenhouse is capable of holding a medium sized pot and a small storage vessel for water that the submersion pump can use to water the soil as needed. On the sides of the Greenhouse are a Mega Arduino controller and two breadboards that run the master code stored on the Arduino so long as its two power inputs are connected to an outlet. The Greenhouse itself is composed of polycarbonate walls and roofing to provide insulation while its construction leaves some access to outside air for increased ventilation.  

Overall, the resulting Greenhouse was a success; with all features included in the product backlog being created and functioning as intended. The LCD does display both Temperature and Humidity 24/7 regardless of if both the Fan/Watering system is on or only one. The Fan checks the Temperature every 3 minutes and if the targeted temperature is reached, will turn on and then follow suit and turn off once the temperature is below the target value. Similarly, the code will check, at regular intervals, the soil moisture level, and once it is below the target value provide the submersion pump will turn on, providing the soil with water. Water may be added to the soil every 3 minutes until the soil moisture content around the plant exceeds the threshold. With all this successfully working, the Greenhouse can maintain a set amount of plants, provided they fit, and does model a larger scale Automated Greenhouse. 
## Design Description
The automated greenhouse measures 13 by 17 inches, allowing ample space to fit multiple plants while growing. It is composed of polycarbonate sheets and supported in all corners with wood framing. The standard wall heaight is 15 inches, and reaches 24 inches at the peak. Structural components, and sensors, are connected using hot glue as an adhesive. Two sensors are housed within the greenhouse: one in the soil to sense moisture, and one mounted on the inside of the greenhouse to detect both temperature and humidity.  The sensors are connected to pins as shown in the schematic below.  The LCD displays the temperature and humidity percentage inside of the greenhouse.  If the soil moisture sensor detects below a certain value, the water pump turns on and waters the plants.  If the temperature/humidity sensor detects a temperature level too high for the plants, a DC motor fan is turned on to circulate cooler, fresh air in to the greenhouse. 
### Circuit Schematic
![image](https://user-images.githubusercontent.com/102317706/165352077-04833366-79a6-4dd3-8340-3d751659f54e.png)

### CAD Drawings
A model of the greenhouse structure, and the location of the different components within the automated greenhouse can be seen in the two figures below. 
#### Structure with Automated Components

![image](https://user-images.githubusercontent.com/102317706/165413048-f69a8ef8-a889-48ad-b2b0-4d182666df73.png)

#### Polycarbonate Structure

![image](https://user-images.githubusercontent.com/102317706/165413078-181d49cf-5bbb-45dd-9e5d-03c6d515b792.png)

### Code

```c
//Libraries
#include <DHT.h>
#include <LiquidCrystal.h>                  //the liquid crystal library contains commands for printing to the display
LiquidCrystal lcd(42, 40, 38, 36, 34, 32);    // tell the RedBoard what pins are connected to the display

//Constants
#define DHTPIN 7     // what pin we're connected to
#define DHTTYPE DHT22   // DHT 22  (AM2302)

DHT dht(DHTPIN, DHTTYPE); //// Initialize DHT sensor for normal 16mhz Arduino

//Variables
int chk;
float hum;  //Stores humidity value
float temp; //Stores temperature value
float btemp; //stores last temperature value read for error
int tempf;
int soilM = 0;
int soilPin = A0;
int soilPower = 3;
int pump=6;
int fan=5;
int i=0;

void setup()
{
  Serial.begin(9600);
  pinMode(pump, OUTPUT);
  pinMode(fan, OUTPUT);
  pinMode(soilPower, OUTPUT);

  digitalWrite(soilPower, LOW);
  
  dht.begin();
  
  lcd.begin(16, 2);                         //tell the lcd library that we are using a display that is 16 characters wide and 2 characters high
  lcd.clear();                              //clear the display
  lcd.setCursor(0, 0);                      //set the cursor to the top left position
  lcd.print("Degrees F: ");                 //print a label for the data
  lcd.setCursor(0, 1);                      //set the cursor to the lower left position
  lcd.print("Humidity: ");                 //Print a label for the data
}

int readSoil()
{
  digitalWrite(soilPower, HIGH);
  delay(10);
  soilM = analogRead(soilPin);
  digitalWrite(soilPower, LOW);
  return soilM;
}

void loop()
{
   
    //Read data and store it to variables hum and temp
    hum = dht.readHumidity();
    temp= dht.readTemperature();
  
    tempf=(temp*9/5)+32;
  
 
if (tempf!= 0){
  lcd.setCursor(11, 0);                      //set the cursor to the top left position
  lcd.print(tempf);                            //print the degrees Celsius
  lcd.print("   ");

  lcd.setCursor(10, 1);                      //set the cursor to the lower left position
  lcd.print(hum);                   //print the degrees Fahrenheit
  lcd.print(" ");
}

  delay(2000);                        //set timing of each loop to 2 seconds
  
  if (tempf >= 75){
    if (i==0) {
    digitalWrite(fan,HIGH);
    }
  }
  if (tempf < 75){
    if (i==85){
    digitalWrite(fan, LOW);
  }
  }
 
  if (soilM < 350){
    if(i==10){
    digitalWrite(pump, HIGH);
    }
    if(i==12) {
      digitalWrite(pump, LOW);
    }
  }
  if (soilM >= 350){
    if(i==12){
    digitalWrite(pump, LOW);
    }
  }
  
  i=i+1;
  
  if (i==93) {
    i=0;
  }
  }
```

## Design Decisions
In designing an automated greenhouse, there are many different features that could be included.  Among those discussed by the team were components like opening doors for ventilation, cooling wet pads for temperature control, a dehumidifier for drying the air, and timer-initiated grow lamps. All of these components needed to be considered when determining the complexity of the greenhouse, and the target market/price range if the system was optimized and manufactured for produciton.  For the greenhouse ventilation door, a servo motor would be operated by the Arduino when the temperature/humidity sensor read a value above a certain threshold.  After considering that the door would need to reseal somehow when it was closed, we opted to use another method to lower temperature and humidity within the greenhouse.  For the evaporative cooling pads to be effective, we would have to use multiple small fans or an exhaust fan.  In our small scale, model greenhouse, we considered this to be an ineffective, expensive, and an unnecessary method for cooling, as the fan or fans would take up most of the space within the greenhouse. In conjunction with our discussion about the evaporative cooling, we also discussed a dehumidifier or humidifier within the structure to help combat any problems created by the evaporative cooling pad. The size, and added cost, of humidifiers and dehumidifiers also posed problems that outweighed the benefits. 

After much analysis of components, we decided to include a water pump to water the plant, a ventilation fan to circulate air in order to lower temperature and humidity within the structure, and sensors that detected each variable to make the system completely automated.  In order to minimize costs, while not affecting function, we decided to cut a hole in the side of our greenhouse structure directly in front of a DC motor fan to vent the greenhouse.  The fan was coded to turn on if the greenhouse inside temperature was 75˚F or higher, and set to run until the inside temperature is lower than 75˚F.  The ventilation hole in front of the fan has a 3 ½ inch diameter, allowing heat and excess moisture to escape the structure.  The hole is covered by a piece of fabric that closes the hole when the fan is not on, but is light enough to be moved out of the way by the fan; allowing air to escape when the fan is turned on.   
## Materials
The final design consists of the following: 
  - Soil moisture sensor (SparkFun SEN-13637) 
  - Temperature and humidity sensor (HiLetGo DHT22) 
  - LCD display (SparkFun ADM1602K) 
  - DC motor fan () 
  - Water pump (Gikfun EK1374) 
  - Polycarbonate Structure
## Testing
In order to test our completed design, we placed a curly wurly corkscrew rush plant into the greenhouse that had not been watered in several days.  After inserting the soil moisture sensor into the soil of the pot, we plugged our Arduino in to a 12V power source.  Since we were indoors, in a lab, we had to heat up the temperature sensor inorganically in order to make the fan turn on.  To do this, we used a cigarette lighter to heat the sensor up until it was 75˚F.  In order to test the soil moisture sensor multiple times, we ran the ¼-inch airplane tubing from the water source back into the water source.  We did this so there would not be moisture added to our soil during testing that would disrupt future tests, but we were still able to ensure the pump was working.  We were also able to visually test our LCD by comparing the display to a digital thermometer.   
## Testing Results
While we were constructing our final product, we performed preliminary testing.  During this testing, we encountered an issue with our LCD and water pump drawing too much power for the 5V output from the arduino, before we added any other passive elements to our circuit.  As an attempt to remedy this, we added a transistor to the circuit.  Even with a transistor, while the pump was running, the LCD displayed strange characters instead of the temperature and humidity values.  We noticed this was happening as soon as the pump turned on, so we were able to deduce that the pump was drawing more power than the arduino could supply.  We further remedied this before adding any other elements to our circuit by trading the battery pack for a 5V wall outlet power supply, and connecting it to the water pump and the DC motor. The motor and pump, which created noise in the voltage signal, were seperated onto their own breadboard with their own voltage supplies. The circuit containing the LCD and sensors were connected to the arduino, and ran by another power supply. When the fan and motor were running, issues with the LCD displaying random characters was still occurring. An oscilloscope was used to look at the signals coming from the voltage source, motor, pump, and digital pins. Here we saw that the noise through these signals was most likely the cause of the LCD functioning improperly. Capacitors were added across the voltage input pin for the LCD, across the voltage input for the sensors, and across both the motor and pump. These capacitors acted as high pass filters, only allowing the DC component, which is of higher frequency, to pass. Implementing these capacitors fixed the issues occuring with the LCD display. 

Once the issues with the circuit was fixed, the code was tested with the obtained data being displayed below. All components worked together just as the code was written. 

![image](https://user-images.githubusercontent.com/102317706/165510901-e770242a-f471-4625-a275-a331a53dbde2.png)

## Results Discussion
During our testing we placed our finger over the temperature/humidity sensor to artificially raise our temperature readings. The bold numbers, above, are the readings obtained when we first did this. Because the temperature of the sensor could not physically drop back down to the temperature of the room immediately, we observed a slow lowering of the temperature back down to the room temperature. Logically, we inferred that the temperature reading aspect of the sensor was working as well as the humidity sensor. For the soil moisture sensor, we removed the sensor from the soil so that it would be below the 350-set limit for the soil to be considered dry. At this point, the submersion pump would turn on, pushing water through the tube and to the base of the plant. The LCD displayed accurate readings of both temperature and humidity for extended periods of time, without fault, with both the fan on and off.  

The limitations of the devices are that it is a scaled model and only has room for a single pot. The height of the structure also limits the size of the plant that can be grown. The system also has no way to provide heat for the greenhouse; relying solely on the plant and insulation of the greenhouse. This limits the range of climates at which this greenhouse can be used to successfully grow a plant. A colder climate would hinder the ability to grow cold intolerant plants. The system also has no way to simulate sunlight and thus the greenhouse would need to be placed in a well-lit area for it to function properly. The most effective way to expand upon this design would be to add a heat source, and grow light, making it completely self sustaining in any climate or light conditions. Without these functions, the model still functions as a greenhouse, but requires more human inputs, and care for the plant.  
