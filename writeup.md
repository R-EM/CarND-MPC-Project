Student describes their model in detail. This includes the state, actuators and update equations.

Below you fill find an image describing the update equations of the model used for this project:
![alt text](https://github.com/R-EM/CarND-MPC-Project/blob/master/MPC_model.png)

The state is described by the position x and y, as well as the orientation of the vehicle psi and the velocity of the vehicle v.
The actuators are described by the acceleration a and the steering angle delta.
The update equations are the equations shown in the image above. For example x_(t+1) = x_t + v_t*cos(psi_t)*dt is the update equation for the x position.



Student discusses the reasoning behind the chosen N (timestep length) and dt (elapsed duration between timesteps) values. Additionally the student details the previous values tried.

The values used for N and dt were 10 and 0.1 respecively. These values were the provided values by Udacity's walkthrough video. Changing these did not seem necessary once I was finished tuning the weights for my controller to function correctly. But what I can do is provide an explanation as to why they were not changed. 

The purpose of the Model Predictive Control (MPC) is to determine how the vehicle will work at N samples from the current sample, and adjust the next time sample values accordingly. By reducing the time step length, we are effectively reducing how far ahead we are looking into the future for determining the next values. Reducing this value could be a good idea if the model is highly inaccurate, since we cannot confidently rely on it. Increasing the time step length would be the equivalent of looking further ahead into the future and adjusting our values accordingly. This would be a good idea if our model is highly accurate, but this comes at the cost of higher computational power, which may not always be possible. 

The tradeoffs are as follow:
  Lower N to reduce computational requirements, but also reduce accuracy, given that the model is accurate.
  Raise N to increase computational demans and increase accuracy, given that the model is accurate.

This can also be done by changing the value of the elapsed duration between timesteps. Since the time step length and duration between timesteps together determine how far into the future we are looking. So 10 time steps, with 0.1 seconds between each timestep would equate to 1 second. Which means we are looking 1 second into the future based on our model. Reducing the time between timesteps without changing the time steps would reduce how far ahead we are looking. So setting N = 10 and dt = 0.05, would mean that we are now looking 0.5 seconds into the future.

In conclusion, looking 1 second into the future for predicting the next values gave satisfactory performance for the controller.



A polynomial is fitted to waypoints.If the student preprocesses waypoints, the vehicle state, and/or actuators prior to the MPC procedure it is described.
This is done in rows 104 to 112 in main.cpp. The waypoints are transformed into the car's perspective, making things a bit easier for us in terms of calculations.




The student implements Model Predictive Control that handles a 100 millisecond latency. Student provides details on how they deal with latency.

Seeing how the duration between timesteps is already 100ms, all we need to do is use the sample before the current one for the throttle and the steering angle. This has been implemented in MPC.cpp in lines 121-126, and by using the variable called "delay", we can also vary the delay. Implementing this change has not cause much trouble for the MPC controller because of the weights I've used. Although the vehicle oscillates a little more compared to the controller without the delay, the vehicle still successfully navigates the entire route without any trouble. As for the weights that I've chosen, I began by using the initial weights provided by Udacity's walkthrough video. However, these values did not provide a satisfactory result for the controller. Initially, the car would just drive straight out of the course, and that alone is reason enough to dismiss these weights. The provided values were as following:

w_cte = 2000;
w_epsi = 2000;
w_d1 = 5;
w_a1 = 5;
w_d2 = 200;
w_a2 = 10;

I began by increasing the values of the steering angle and the acceleration.

w_cte = 2000;
w_epsi = 2000;
w_d1 = 50;
w_a1 = 50;
w_d2 = 200;
w_a2 = 200;

I still had issues with oversteering, which is why I decided to heavily penalise changes in steering angle and throttle. The change in weights happened as follows:

w_cte = 2000;
w_epsi = 2000;
w_d1 = 100;
w_a1 = 100;
w_d2 = 500;
w_a2 = 500;

This change did not provide a satisfactory improvement for the performance. The steering angle still needed to be penalised somewhat more heavily that the throttle, which lead to the following change:

w_cte = 2000;
w_epsi = 2000;
w_d1 = 100;
w_a1 = 100;
w_d2 = 10000;
w_a2 = 1000;

This provided some improvements, but after changing w_d2 to 100000 is when my controller's performance became satisfactory. However, while this controller fulfilled the rubric's requirements, there was still an annoying oscillation that was taking place, which is why cte and epsi were lowered somewhat. The final weights are as follow;

w_cte = 1000;
w_epsi = 1000;
w_d1 = 100;
w_a1 = 100;
w_d2 = 100000;
w_a2 = 1000;
