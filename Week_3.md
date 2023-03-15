# Week3 Boid

Panopto:https://ual.cloud.panopto.eu/Panopto/Pages/Viewer.aspx?id=5a0f4e7a-cdb3-4498-8a0d-afc5016d4b81

<img width="808" alt="截屏2023-03-14 21 48 23" src="https://user-images.githubusercontent.com/119190967/225148000-5cce9e93-fc33-471e-b073-927cdc0b52df.png">


 Panopto:[https://youtu.be/m0k39LrJlCk](https://ual.cloud.panopto.eu/Panopto/Pages/Viewer.aspx?id=ee15eb51-e6ca-40da-8593-afc5016d3913)

## Code
boid.cpp
```
/*
 *  boid.cpp
 *  boids
 *
 *  Created by Marco Gillies on 05/10/2010.
 *  Copyright 2010 Goldsmiths, University of London. All rights reserved.
 *
 */

#include "boid.h"
#include "ofMain.h"

Boid::Boid()
{
	separationWeight = 1.0f;
	cohesionWeight = 0.2f;
	alignmentWeight = 0.1f;
	
	separationThreshold = 15;
	neighbourhoodSize = 100;
	
	position = ofVec3f(ofRandom(0, 200), ofRandom(0, 200));
	velocity = ofVec3f(ofRandom(-2, 2), ofRandom(-2, 2));
}

Boid::Boid(ofVec3f &pos, ofVec3f &vel)
{
	separationWeight = 0.6f;
	cohesionWeight = 0.8f;
	alignmentWeight = 0.1f;
	
	separationThreshold = 15;
	neighbourhoodSize = 100;
	
	position = pos;
	velocity = vel;
}

Boid::~Boid()
{
	
}

float Boid::getSeparationWeight()
{
	return separationWeight;
}
float Boid::getCohesionWeight()
{
	return cohesionWeight;
}

float Boid::getAlignmentWeight()
{
	return alignmentWeight;
}


float Boid::getSeparationThreshold()
{
	return separationThreshold;
}

float Boid::getNeighbourhoodSize()
{
	return neighbourhoodSize;
}


void Boid::setSeparationWeight(float f)
{
	separationWeight = f;
}
void Boid::setCohesionWeight(float f)
{
	cohesionWeight = f;
}

void Boid::setAlignmentWeight(float f)
{
	alignmentWeight = f;
}


void Boid::setSeparationThreshold(float f)
{
	separationThreshold = f;
}

void Boid::setNeighbourhoodSize(float f)
{
	neighbourhoodSize = f;
}


ofVec3f Boid::getPosition()
{
	return position;
}



ofVec3f Boid::getVelocity()
{
	return velocity;
}

ofVec3f Boid::separation(std::vector<Boid *> &otherBoids)
{
    //分离
	// finds the first collision and avoids that
	// should probably find the nearest one
	// can you figure out how to do that?
	for (int i = 0; i < otherBoids.size(); i++)
	{	
		if(position.distance(otherBoids[i]->getPosition()) < separationThreshold)
		{
			ofVec3f v = position -  otherBoids[i]->getPosition();
			v.normalize();
			return v;
		}
	}
}

ofVec3f Boid::cohesion(std::vector<Boid *> &otherBoids)
{
    //聚合
	ofVec3f average(0,0,0);
	int count = 0;
	for (int i = 0; i < otherBoids.size(); i++)
	{
		if (position.distance(otherBoids[i]->getPosition()) < neighbourhoodSize)
		{
			average += otherBoids[i]->getPosition();
			count += 1;
		}
	}
	average /= count;
	ofVec3f v =  average - position;
	v.normalize();
	return v;
}

ofVec3f Boid::alignment(std::vector<Boid *> &otherBoids)
{
    //对齐
	ofVec3f average(0,0,0);
	int count = 0;
	for (int i = 0; i < otherBoids.size(); i++)
	{
		if (position.distance(otherBoids[i]->getPosition()) < neighbourhoodSize)
		{
			average += otherBoids[i]->getVelocity();
			count += 1;
		}
	}
	average /= count;
	ofVec3f v =  average - velocity;
	v.normalize();
	return v;
}

void Boid::update(std::vector<Boid *> &otherBoids, ofVec3f &min, ofVec3f &max)
{
	velocity += separationWeight*separation(otherBoids);
	velocity += cohesionWeight*cohesion(otherBoids);
	velocity += alignmentWeight*alignment(otherBoids);
	
	walls(min, max);
	position += velocity;
}

void Boid::walls(ofVec3f &min, ofVec3f &max)
{
	if (position.x < min.x){
		position.x = min.x;
		velocity.x *= -1;
	} else if (position.x > max.x){
		position.x = max.x;
		velocity.x *= -1;
	}
	
	if (position.y < min.y){
		position.y = min.y;
		velocity.y *= -1;
	} else if (position.y > max.y){
		position.y = max.y;
		velocity.y *= -1;
	}
	//定义边界线
	
}

void Boid::draw()
{
	ofSetColor(100, 100, 255);
	ofCircle(position.x, position.y, 5);
    ofRect(position.x,position.y,10,10);
    //ofRectangle(mouse.x, mouse.y, 10, 8);
    ofLine(position.x,position.y, position.x-2, position.y+position.x);
}//画图
void Boid::draw_1()
{
    ofSetColor(0, 100, 255);
    //ofCircle(position.x, position.y, 5);
    ofRect(position.x,position.y,10,10);
    //ofRectangle(mouse.x, mouse.y, 10, 8);
    //ofLine(position.x,position.y, position.x-2, position.y+position.x);
}//画图

```


boid.h
```
/*

 *
 */

#ifndef _BOID
#define _BOID
#include <vector>
#include "ofMain.h"

class Boid
{
// all the methods and variables after the
// private keyword can only be used inside
// the class
private:	
	ofVec3f position;
	ofVec3f velocity;
   
	
	float separationWeight;
	float cohesionWeight;
	float alignmentWeight;
	
	float separationThreshold;
	float neighbourhoodSize;
	
	ofVec3f separation(std::vector<Boid *> &otherBoids);
	ofVec3f cohesion(std::vector<Boid *> &otherBoids);
	ofVec3f alignment(std::vector<Boid *> &otherBoids);
	
// all the methods and variables after the
// public keyword can only be used by anyone
public:	
	Boid();
	Boid(ofVec3f &pos, ofVec3f &vel);
	
	~Boid();//～分构函数：释放Boid的所使用的资源
	
	ofVec3f getPosition();
	ofVec3f getVelocity();
   
	
	
	float getSeparationWeight();//分离
	float getCohesionWeight();//聚合
	float getAlignmentWeight();//对齐
	
	float getSeparationThreshold();
	float getNeighbourhoodSize();
	
	void setSeparationWeight(float f);
	void setCohesionWeight(float f);
	void setAlignmentWeight(float f);
	
	void setSeparationThreshold(float f);
	void setNeighbourhoodSize(float f);
	
	void update(std::vector<Boid *> &otherBoids, ofVec3f &min, ofVec3f &max);
	
	void walls(ofVec3f &min, ofVec3f &max);
	
	void draw();
        void draw_1();
};

#endif
```

testApp.cpp
```
#include "testApp.h"

testApp::~testApp()
{
	for (int i = 0; i < boids.size(); i++)
	{
		delete boids[i];
	}
    for (int i = 0; i < boids2.size(); i++)
    {
        delete boids2[i];
    }
}

//--------------------------------------------------------------
void testApp::setup(){
	
	//测试boid
	int screenW = ofGetScreenWidth();
	int screenH = ofGetScreenHeight();
//
    std::vector<Boid*> testboids = std::vector<Boid*>();
    boids = testboids;
//
////    //背景
	ofBackground(0,0,0);
//
//    std::cout << "boids.size()" << boids.size() << std::endl;
//    std::cout << "boid2s.size()" << boids2.size() << std::endl;
//
////	// set up the boids
  for (int i = 0; i < 10; i++)
    {
//        boids_1.push_back(new Boid());
        boids.push_back(new Boid());
    }
////
    for (int i = 0; i < 100; i++)
    {
        boids2.push_back(new Boid());
    }
        
  //  for (int j = 0; j < 10; j++)
     //   boids_1.push_back(new Boid());
   

}


//--------------------------------------------------------------
void testApp::update(){
	
    ofVec3f min(10, 10);
	ofVec3f max(ofGetWidth(), ofGetHeight());
//
    ofVec3f min_1(400, 400);
    ofVec3f max_1(ofGetWidth(), ofGetHeight());
    
	for (int i = 0; i < boids.size(); i++)
	{
		boids[i]->update(boids, min, max);
	}
    //
    for (int i = 0; i < boids2.size(); i++)
    {
        boids2[i]->update(boids2, min_1, max_1);
    }
}

//--------------------------------------------------------------
void testApp::draw(){

	for (int i = 0; i < boids.size(); i++)
	{
		boids[i]->draw();
	}
//
//    //
    for (int i = 0; i < boids2.size(); i++)
    {
        boids2[i]->draw_1();
    }

}


//--------------------------------------------------------------
void testApp::keyPressed(int key){
 
}

//--------------------------------------------------------------
void testApp::keyReleased(int key){
    
}

//--------------------------------------------------------------
void testApp::mouseMoved(int x, int y ){
    
}

//--------------------------------------------------------------
void testApp::mouseDragged(int x, int y, int button){
    
}

//--------------------------------------------------------------
void testApp::mousePressed(int x, int y, int button){
	
}

//--------------------------------------------------------------
void testApp::mouseReleased(int x, int y, int button){
    
}

//--------------------------------------------------------------
void testApp::windowResized(int w, int h){
    
}

```

testApp.h
```
#ifndef _TEST_APP
#define _TEST_APP


#include "ofMain.h"
#include <vector>
#include "boid.h"

class testApp : public ofBaseApp{
	
public:
    ~testApp();//析构函数
	
    void setup();
    void update();
    void draw();
    
    void keyPressed(int key);
    void keyReleased(int key);
    void mouseMoved(int x, int y );
    void mouseDragged(int x, int y, int button);
    void mousePressed(int x, int y, int button);
    void mouseReleased(int x, int y, int button);
    void windowResized(int w, int h);
    
    
    std::vector<Boid*> boids2;
    
    std::vector<Boid *> boids;
    std::vector<Boid *> boids_1; //创建存储空间

	
};

#endif	

```
