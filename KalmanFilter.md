# Lab 7
**Not finished
## Overview
The purpose of this lab is to implement a Kalman filter that takes data from a ToF sensor and combines it with a motion model to calculate a more accurate measurement of my car's position as it drives toward a wall. 
## Data collection
I first drove the car toward a wall using a fixed PWM value of 100 (out of 255 max) because I wanted the car to be fast enough to reach steady state before hitting the wall, but not so fast that it would smash itself to pieces. Note that since the motor response is not identical, I actually used 100 PWM for the left motor and 105 PWM for the right so the car could travel in a straight line. That doesn't have any effect on the Kalman filter result, so it isn't a problem. I created two arrays of 100 elements each, one to store the current time (by calling the millis() function) and one to store the current ToF sensor output. Below is a plot of the ToF sensor output versus distance. I omitted the data points that occurred after the car made contact with the wall. 

![ToFvsTime](https://user-images.githubusercontent.com/71809396/164991541-1f12b7db-a57d-4bab-885e-8405f692b33e.png)

Below is a plot of the car's velocity over the same time period. 

![VelocityvsTime](https://user-images.githubusercontent.com/71809396/164992494-d13a7b32-578f-4a7d-ad8f-4a542a188b9c.png)

The next step in the process is determining the A and B matrices. I used my velocity graph to determine the steady-state speed (the flat portion of the velocity graph), which was ~103 cm/s. The 90% rise time was calculated by determining the amount of time it took the robot to get up to 0.9 * steady-state speed. For my robot, the 90% rise time was ~1.4s. These values gave me an A matrix of [[0,1],[0,-d/m]] where d is the reciprocal of the steady-state speed and m = -d * 90% rise time / ln(0.1). This gave me a matrix A = [[0,1],[0,-1.6447]]. Matrix B is of the form [[0], [1/m]] which for my robot is [[0], [169]]. 

## Kalman Filter Implementation

The first step in the Kalman fitler implementation is determing how much we trust our sensors vs our motion model. If we trust the sensors too much, the Kalman filter will be essentially useless, since it will output a PWM value similar to the PID without a Kalman filter; however, trusting the motion model too much may cause the robot to become lost if the motion model is inaccurate. My covariance matrices are of the following form:

sig_u=np.array([[sigma_1**2,0],[0,sigma_2**2]]) //We assume uncorrelated noise, therefore a diagonal matrix works.
sig_z=np.array([[sigma_4**2]])

where sigma_1 and sigma_2 place trust in the sensor data and sigma_4 places trust in the model. Thus a larger sigma_4 relative to sigma_1 and sigma_2 places more trust in the model than the sensor data. My C matrix is C = np.array([[-1,0]]), since I'm measuring the distance to the wall while driving toward it (thus the negative sign). 

**Insert Kalman filter plot from python sanity check with sigma values in plots

## Kalman filter on the Robot

** code snippet here
