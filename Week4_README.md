# Week4  Arduino & Open Framework


Youtube：https://youtu.be/55TE7uJFdBw


![IMG_1777](https://user-images.githubusercontent.com/119190967/225134997-6f7ee746-1af0-4e7c-bc3f-b06cd28b17b5.jpg)

## Arduino Code
```
int ButtonPin= 12;
int LedPin= 7;
int PotPin=0;
int a=0;
int Res=0;
int voltage =0;
int ButtonState;

void setup() {
  // put your setup code here, to run once:
  Serial.begin(9600);
  pinMode(ButtonPin, INPUT);
  pinMode(LedPin, OUTPUT);
  pinMode(PotPin,INPUT);
//  Change();
}

void loop() {
   Res=analogRead(PotPin);//val = 0 ~ 1023
   voltage = map (Res,0,1023,0,2550);
   
   analogWrite(LedPin,voltage);
   
   if (Serial.available()) 
   { 
    char inByte = Serial.read();
    //voltage = map (Res,0,1023,0,2000);

    analogWrite(LedPin,voltage);
    Serial.print("val = ");
    Serial.print(Res);
    Serial.print(";   voltage = ");
    Serial.println(voltage);
   // analogWrite(LedPin,voltage);
    int ButtonState =digitalRead(12);
    
  
  if(a==0)
  {
    if(ButtonState == HIGH)
    {
    delay(500);
    //digitalWrite(LedPin,HIGH);
   
    a++;
    }
  }
  else
   if(a==1) {
    if( ButtonState==HIGH ){
    delay(500);
      digitalWrite(LedPin,LOW);
      a--;
      }
    }
   }
   Serial.write(Res);
   Serial.write(ButtonState);
}
```

## OpenFrameWork Code
