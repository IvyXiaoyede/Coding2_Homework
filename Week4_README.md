# Week4  Arduino & Open Framework

For this homework, I have combined Arduino and OpenFramework to adjust the brightness of the lamp for the display of the screen.


Panopto：https://ual.cloud.panopto.eu/Panopto/Pages/Viewer.aspx?id=5a0f4e7a-cdb3-4498-8a0d-afc5016d4b81



<img width="998" alt="截屏2023-03-14 21 41 57" src="https://user-images.githubusercontent.com/119190967/225145921-c01845ab-ef2f-4350-b085-884fc5a87139.png">

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
<img width="592" alt="image" src="https://user-images.githubusercontent.com/119190967/225146394-cc8351a5-232e-4296-8e4e-e22b61cf1629.png">

## OpenFrameWork Code

ofApp.cpp
```
#include "ofApp.h"

void ofApp::setup(){
    ofSetVerticalSync(true);
    ofSetLogLevel(OF_LOG_VERBOSE);
    
    serial.listDevices();
    vector <ofSerialDeviceInfo> deviceList = serial.getDeviceList();
    
    int baud =9600;
    
    serial.setup("/dev/cu.usbmodem14401", baud);
    

    ofEnableDepthTest();
    //glEnable(GL_POINT_SMOOTH); // use circular points instead of square points
    ofLogo.load("Subject.png");
    ofEnableNormalizedTexCoords();
    ofSetLineWidth(1);

}

//--------------------------------------------------------------
void ofApp::update(){
    if(serial.available()<0)
    {
        SensorValue ="Arduino Error";
    }
    else
    {
        while(serial.available()>0)
        {
            byteData = serial.readByte();
            ButtonState = serial.readByte();
      //      printf("w:",byteData);
            SensorValue = "value:" + ofToString(byteData);
        }
    }
    cout << SensorValue <<endl;
   
}

//--------------------------------------------------------------
void ofApp::draw(){
    ofBackgroundGradient(ofColor::gray, ofColor::black, OF_GRADIENT_CIRCULAR);
    
    float movementSpeed = .1;
    float cloudSize = ofGetWidth() / 2;
    float maxSphereSize = 100;
    float spacing = 1;
    int SphereCount = 100;
    
    cam.begin();

        for(int i = 0; i < SphereCount; i++) {
            ofPushMatrix();

            float t = (ofGetElapsedTimef() + i * spacing) * movementSpeed;
           
            glm::vec3 pos(
                ofSignedNoise(t-101, 0, 0),
                ofSignedNoise(0, t+100, 0),
                ofSignedNoise(0, 0, t));

            float SphereSize = maxSphereSize * ofNoise(pos.x, pos.y, pos.z);

            pos *= cloudSize;
            ofTranslate(pos);
            ofRotateXDeg(pos.x);
            ofRotateYDeg(pos.y);
            ofRotateZDeg(pos.z);

            ofLogo.bind();
            ofFill();
            ofSetColor(byteData*10);
            ofDrawBox(SphereSize);
            ofLogo.unbind();

            ofNoFill();
            ofSetColor(ofColor::fromHsb(sinf(t) * byteData + 128, 0, 0));

            ofDrawSphere(SphereSize);

            ofPopMatrix();
        }

        cam.end();
    
    
    
    
   

    
//    timeValue =byteData;
//   float time =ofGetElapsedTimef();
//    for(int x=1;x<20;x++){
//        for(int i=0;i<100;i+=5){
//            // ofDrawCircle(100*sin(i*0.01+timeValue+x+300), 50+i, 10*cos(i*0.01+timeValue));
//             ofDrawCircle(sin(i)*1000, 50+i, 50+40*sin(i*0.005 +time+ timeValue));
//
//           // ofDrawCircle(200, 200, timeValue+100);
//        }
//
//    }
}

//--------------------------------------------------------------
void ofApp::keyPressed(int key){

}

//--------------------------------------------------------------
void ofApp::keyReleased(int key){

}

//--------------------------------------------------------------
void ofApp::mouseMoved(int x, int y ){

}

//--------------------------------------------------------------
void ofApp::mouseDragged(int x, int y, int button){

}

//--------------------------------------------------------------
void ofApp::mousePressed(int x, int y, int button){

}

//--------------------------------------------------------------
void ofApp::mouseReleased(int x, int y, int button){

}

//--------------------------------------------------------------
void ofApp::mouseEntered(int x, int y){

}

//--------------------------------------------------------------
void ofApp::mouseExited(int x, int y){

}

//--------------------------------------------------------------
void ofApp::windowResized(int w, int h){

}

//--------------------------------------------------------------
void ofApp::gotMessage(ofMessage msg){

}

//--------------------------------------------------------------
void ofApp::dragEvent(ofDragInfo dragInfo){

}

```
ofApp.h
```
#pragma once

#include "ofMain.h"

class ofApp : public ofBaseApp{

    public:
        void setup();
        void update();
        void draw();

        void keyPressed(int key);
        void keyReleased(int key);
        void mouseMoved(int x, int y );
        void mouseDragged(int x, int y, int button);
        void mousePressed(int x, int y, int button);
        void mouseReleased(int x, int y, int button);
        void mouseEntered(int x, int y);
        void mouseExited(int x, int y);
        void windowResized(int w, int h);
        void dragEvent(ofDragInfo dragInfo);
        void gotMessage(ofMessage msg);
        
        string SensorValue;
        ofSerial serial;
        int byteData;
        int timeValue;
    bool ButtonState;

    
            ofImage ofLogo; // the OF logo
            ofLight light; // creates a light and enables lighting
            ofEasyCam cam; // add mouse controls for camera movement
};

```
