# Lychee Bot - Group 36 EE106A Final Project

[Link](https://lychee-bot.github.io/project/)

## Overview:

Our research project focused on modelling human-human interaction and robotic autonomous navigation amongst humans. Autonomous navigation in an environment with humans is challenging but can be highly useful for applications such as food delivery (like our inspiration Kiwi bot) and navigating in homes or offices. 

This research project has three main parts: human modeling, path planning and robot control. For modelling the human-human interaction, we analyze the takes we recorded in the Cory motion capture room to infer patterns, and apply the Maximum Likelihood Constraint Inference for Inverse Reinforcement Learning paper to determine constraints in human interaction. We also developed and experimented with an MCMC based approach to predict human motion. 
For the planning component, we created a constraint optimization based planner. Finally, we developed a controller for the Turtlebot to navigate around obstacles detected by the OptiTrack motion capture system. 

## Motivation
- Navigating in a dynamic urban environment is hard. 
- Moving around humans is challenging since the motion is non-deterministic and human’s goal is unknown.
- Goal: Research and experiment with methods to infer human motion behavior and navigate around humans. 
- Real-time motion planning around humans: active research area.

## Research Goal

1. Understand how humans interact with static obstacles as well as other humans.
2. Path plan a trajectory from current state state to goal navigating around static and moving obstacles. 
3. Model a human’s trajectory and incorporate its behavior into our path planning algorithm.
4. Create a controller to receive commands from a master algorithm and actuate.

## Data Collection
We wanted our data collection to be hollistic so that it would provide us with a good overview of how humans navigated around obstacles. Thats why we did 6 different collisions with different subjects and multiple trials. Collecting this data was essential to understanding the human modelling and we had to use the optitrack system in the lab to collect the data.

There were several challenges with data collection though the primary ones being:
1. We had to learn how to create rigid bodies and add them to the optitrack system
2. Formatting and null values of the data - the data provided was extremely poorly formatted and to automate graph generations we had to build a pipeline to clean (remove null values or fill wherever possible!)


### add videos and pictures^^^

## How People Move
Based on primliminary analysis with 20 video takes in the Motion Capture room of 2 people moving. Human motion is dynamic and highly context dependent, however, we wanted tried to infer simple patterns in the motion.
Here are a few observations from the data:
- Only one person tends to move away from the other when there is a head-on collision. 
- When moving at higher speeds the movement away from the person is closer to the time of collision but the deviation is much higher. 
- People prefer maintaining a longer backward distance than a sideway distance when walking next to people.

![Image](https://github.com/Lychee-Bot/project/blob/master/img1.png)	
-This image shows how the horizontal distance between two people changed as the collison was about to occur. You can clearly see how the horizontal distance in mantained until the time of the collision until both the participants turn away from each other drastically. 
- You can also notice how there was some hesitance on who was going to move out of whose way at around 1.3 seconds, but ultimately they deviated away from each other before the collision which occured at 1.6 seconds as seen in the graph below.


![Image](https://github.com/Lychee-Bot/project/blob/master/img2.png)	

## Human Model

We read various papers on human motion modelling and we experimented with the approach that our mentor Dexter Scobee had described in the paper [Maximum Likelihood Constraint Inference for Inverse Reinforcement Learning (D.Scobee, S. Shastry)](https://arxiv.org/abs/1909.05477).  This paper reformulates the Inverse Reinforcement Learning problem to describe the observed behavior with a simple reward and a set of hard constraints. 
We used this paper and Dexter's repository to identify constraints for moving humans. 

In the input example we used, when two humans start by facing each other, and move across to the other side, they are close to each other towards the middle of the room. The other human's position near the potential head on collision time is a constraint that we want this IRL constraint inference model to identify. 

There are two ways this is extremely useful to us -
1. It allos us to infer static obstacles such as puddles or broken glass on the floor that a robot may not be able to detect. By observing the humans movement, we could potentially detect these constraints while path planning.
2. Robots have limited range of vision and may not be able to detect constraints faw away. By looking at the movement of the human and sudden changes in its path (similar to when two humans are having a head on collison), can allow it to potentially infer another moving obstacle that is coming towards it.

To get the re-inforcement alogrithm to work we had to - 
1. Create a MDP of the Cory room with states, actions, relevant time discounted rewards (we took Dexter's help in formalizing our MDP problem)
2. Discretization of the data we get so that we could fit it to the MDP and track the movement of the person through the grid. We used several techniques such as moving averages to get a much smoother path when transitiong from one state to another so that we could get a realistic path.This would also make our path generation more robust to optitrack errors!
3. After discretizing our data, and putting it into relavant (state,action) pairs we were able to use the MDP to infer constraints.

Key Facts about the MDP and model:
1. num_states: 81
2. Transitions allowed: allow_diagonal_transitions = true; num_actions = 8; up = 1; down = 2; left = 3; right = 4; up_left = 5; down_left = 6; up_right = 7; down_right = 8
3. Goal and end state got from discretization of the data

## Trajectory Planning


## Challenges
1. Turtlebot access in mocap room only three days before!!! This hindered us from implementing the design that we had orginally forseen at the beginning of the project. Primarily not being able to connect the optimized path planning to the  bots.
2. Existing path planning package doesn’t work with Optitrack system
3. Optitrack and turtlebot Odometry system has two different coordinates
4. ROS_MASTER_URI conflict between turtlebot and optitrack
5. No way besides SLAM to update the map and do path planning efficiently

## Future Work
1. Research in smoothing the path (move in curves)
2. Perform online multi-human motion prediction and add to Turtlebot.
3. Add Computer Vision based planner. Test it on Berkeley Campus. 
4. Make Kiwi bot autonomous, then start our own self-driving delivery service :)

## Team

1. Nmae - Bio - what you did in the project
2.
3.
4.
5.

## Github Repo with Codebase and Jupyter Notebooks

### Header 3

- Bulleted
- List

1. Numbered
2. List

**Bold** and _Italic_ and `Code` text

[Link](url) and ![Image](src)

[Link](url) and ![Image](src)
```

For more details see [GitHub Flavored Markdown](https://guides.github.com/features/mastering-markdown/).

### Jekyll Themes

Your Pages site will use the layout and styles from the Jekyll theme you have selected in your [repository settings](https://github.com/Lychee-Bot/project/settings). The name of this theme is saved in the Jekyll `_config.yml` configuration file.

### Support or Contact

Having trouble with Pages? Check out our [documentation](https://help.github.com/categories/github-pages-basics/) or [contact support](https://github.com/contact) and we’ll help you sort it out.
