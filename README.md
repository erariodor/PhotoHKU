# PhotoHKU
Instructables voor een foto stellage

Code voor de sensor en het infra rood led:

//Analog pin 1 for reading in the analog voltage from the MaxSonar device.
//This variable is a constant because the pin will not change throughout execution of this code.
#include <multiCameraIrControl.h>
const int analogPin = 0;
unsigned long time;

//variables needed to store values
long distanceValue, inches, cm;
int sum = 0; //the variable so you can sum up all values
int averageRange = 60;//Quantity of values to average (sample size)
int counter = 0;
Canon D5(5);

void setup() {
  //This opens up a serial connection to shoot the results back to the PC console
  Serial.begin(9600);
}
void loop() {
  //MaxSonar Analog reads are known to be very sensitive
  //A simple fix is to average out a sample of n readings to get a more consistant reading
  //This loop gets 60 reads and averages them

  //Distance sensor
  for(int i = 0; i < averageRange ; i++)
  {
    //Used to read in the analog voltage output that is being sent by the MaxSonar device.
    //Scale factor is (Vcc/512) per inch. A 5V supply yields ~9.8mV/in
    //Arduino analog pin goes from 0 to 1024, so the value has to be divided by 2 to get the actual inches
    distanceValue = analogRead(analogPin) / 2;
    sum += distanceValue;
    delay(10);
  }  
  inches = sum / averageRange;
  cm = inches * 2.54;
  Serial.print(inches);
  Serial.print("in, ");
  Serial.print(cm);
  Serial.print("cm");
  Serial.println();
  //reset sample total
  sum = 0;
  //delay(500);
  
  //Timer for the camera
  Serial.print("Time: ");
  //time = millis(); //old time code
  time = (millis()/100);
  //prints time since program started
  Serial.println(time);
  //wait a second so it won't send too massive amounts of data at once
  if (cm > 20 && cm < 70) //Checks if value exceeds 20 or 65, triggers a counter if it does not
    {
    counter++;
    Serial.print("Counter: ");
    Serial.print(counter);
    Serial.print("\n");
      if (counter == 3)
        {
        D5.shutterNow();
        delay(3000);
        counter = 0;
        }
    }
  else //resets counter
    {
    counter = 0;
    }
  delay(1000);
}
