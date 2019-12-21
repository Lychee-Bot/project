# Lychee Bot - Group 36 EE106A Final Project

[Link](https://lychee-bot.github.io/project/)

## Overview:

Autonomous navigation in an environment with humans is challenging but can be highly useful for applications such as food delivery (like our inspiration Kiwi bot) and navigating in homes or offices. Our research project focused on modelling human-human interaction and robotic autonomous navigation amongst humans.

Navigating amongst humans is challenging primarily because of the difficulty in predicting human motion and their interactions with others. For an external agent such as a robot that does not know the goal state of the human or other prior context, it is even more challenging to predict human motion, and navigate around them to reach the goal. We aimed to experiment with various approaches and create a prototype to achieve this goal. 

This research project has three main parts: human modeling, path planning and robot control. For modelling the human-human interaction, we analyze the takes we recorded in the Cory motion capture room to infer patterns, and apply the Maximum Likelihood Constraint Inference for Inverse Reinforcement Learning paper to determine constraints in human interaction. We also developed and experimented with an MCMC based approach to predict human motion. 
For the planning component, we created a constraint optimization based planner. Finally, we developed a controller for the Turtlebot to navigate around obstacles detected by the OptiTrack motion capture system. 

## Design:

As it is a research project, an important goal was to experiment with different approaches while making sure to have useful results. At a high level, our project incorporates sensing, planning, actuation and learning. We aimed to modularize our design so we can work on it parallely, while some aspects such as the Turtlebot, was not available in the motion capture room. 

Since the goal is to make a robot navigate to its goal around humans and our research goal was to experiment with methods to model humans, we made design choices to focus more on these novel problems. Choosing the OptiTrack motion capture system to locate agents and obstacles, instead of an on-board camera, allowed us to have a comprehensive localization and mapping system, while allowing us to focus on the research problems.     


We decided to use the OptiTrack motion capture system rather than use computer vision to detect humans and other obstacles to get more accurate object positions and  a comprehensive map. Initially, we had used the Turtlebots’ Kinect sensor to detect and avoid obstacles but we quickly realized that it would an ineffective way to address the primary research questions (since it needs to move around to generate the map, account for errors, detect occluded humans). 

## Motivation
- Navigating in a dynamic urban environment is hard!
- Moving around humans is challenging since the motion is non-deterministic and human’s goal is unknown.
- Goal: Research and experiment with methods to infer human motion behavior and navigate around humans. 
- Real-time motion planning around humans: active research area.

## Research Goal

1. Understand how humans interact with static obstacles as well as other humans.
2. Path plan a trajectory from current state state to goal navigating around static and moving obstacles. 
3. Model a human’s trajectory and incorporate its behavior into our path planning algorithm.
4. Create a controller to receive commands from a master algorithm and actuate.

## Data Collection
We wanted our data collection to be hollistic so that it would provide us with a good overview of how humans navigated around obstacles. Thats why we did 6 different collisions with different subjects and multiple trials. 
[Description of different paths](https://github.com/Lychee-Bot/project/blob/master/Project%20Idea.pdf)

Collecting this data was essential in the human modelling and to accomplish this we used the optitrack system. There were several challenges with data collection though the primary ones being:
1. We had to learn how to create rigid bodies (of the caps that were used to track the particpants) and add them to the optitrack system
2. Formatting and null values of the data - the data provided was extremely poorly formatted and to automate graph generations we had to build a pipeline to clean (remove null values or fill wherever possible!)

### add videos and pictures^^^

## Analysis of human motion

Based on primliminary analysis with 20 video takes in the Motion Capture room of 2 people moving. Human motion is dynamic and highly context dependent, however, we wanted tried to infer simple patterns in the motion.
Here are a few observations from the data:
- Only one person tends to move away from the other when there is a head-on collision. 
- When moving at higher speeds the movement away from the person is closer to the time of collision but the deviation is much higher. 
- People prefer maintaining a longer backward distance than a sideway distance when walking next to people.

![Image](https://github.com/Lychee-Bot/project/blob/master/img1.png)	
-This image shows how the horizontal distance between two people changed as the collison was about to occur. You can clearly see how the horizontal distance in mantained until the time of the collision until both the participants turn away from each other drastically. 
- You can also notice how there was some hesitance on who was going to move out of whose way at around 1.3 seconds, but ultimately they deviated away from each other before the collision which occured at 1.8 seconds as seen in the graph below.

This analysis also helped us calculate useful metrics across the samples: the median minimum distance between people is: 0.469 m, standard deviation: 0.195. The median minimum distance is used in our current path planning algorithm as a hard constraint. 

![Image](https://github.com/Lychee-Bot/project/blob/master/img2.png)	

## Human Model

We read various papers on human motion modelling and we experimented with the approach that our mentor Dexter Scobee had described in the paper [Maximum Likelihood Constraint Inference for Inverse Reinforcement Learning (D.Scobee, S. Shastry)](https://arxiv.org/abs/1909.05477).  This paper reformulates the Inverse Reinforcement Learning problem to describe the observed behavior with a simple reward and a set of hard constraints. 
We used this paper and Dexter's repository to identify constraints for moving humans. 

Another approach that we read about what social spheres also known as proxemics. In this area of research the leading school of thought is that humans maintain a relatively fixed set of distances when navigating through urban environments. Even though this set of distances is unique to humans, research has shown a general trend in that these proxemics are usually ellipsoids. This is somewhat consistent with our analysis of how humans move where we found that people tended to maintain a longer vertical distance than a horizaontal one. Ultimately we didnt decide to go down this approach as we didnt have enough data samples, especially un-tainted ones (not from a lab setting) to create such a generalization and it would be extremely complicated to do so. 

In the input example we used, when two humans start by facing each other, and move across to the other side, they are close to each other towards the middle of the room. The other human's position near the potential head on collision time is a constraint that we want this IRL constraint inference model to identify. 

There are two ways in which constraints inference is extremely useful to us -
1. It allows us to infer static obstacles such as puddles or broken glass on the floor that a robot may not be able to detect. By observing the human's movement, we could potentially detect these constraints while path planning.
2. Robots have limited range of vision and may not be able to detect constraints far away. By looking at the movement of the human and sudden changes in its path (for example when two humans are having a head on collison), can allow us to potentially detect the other moving obstacle.

To get the re-inforcement alogrithm to work we had to - 
1. Create a MDP of the Cory room with states, actions, relevant time discounted rewards (we took Dexter's help in formalizing our MDP problem)
2. Discretization of the data we get so that we could fit it to the MDP and track the movement of the person through the grid. We used several techniques such as moving averages to get a much smoother path when transitiong from one state to another so that we could get a realistic path.This would also make our path generation more robust to optitrack errors!
3. To generate (state, action) pairs we had to write a script that given the layout of an MDP and a list of states, it would generate the corresponding actions.
4. After discretizing our data, and putting the it into relevant (state,action) pairs we were able to use the MDP to infer constraints.


Key Facts about the MDP and model:
1. num_states: 81
2. Transitions allowed: allow_diagonal_transitions = true; num_actions = 8; up = 1; down = 2; left = 3; right = 4; up_left = 5; down_left = 6; up_right = 7; down_right = 8
3. Goal and end state got from discretization of the data
4. Max path length: 15
5. Discount factor 0.9

## Trajectory Planning



## Actuation

We have thought about two differnt approaches to achieve our goals. Approach 1 leverages existing turtlebot pacakge, but we soon found out a major drawback that prevent us using approach 1, so we switch to approach 2. In Approach 2, we set up our own master server that compute the path based on human modeling constraints.

### Approach 1: Map Update
The first time we thought about "turtelbot avoids pedestrain and goes to a specific location on map" problem, we were confident that there must be online solutions, since the turtlebot is very popular. Indeed, there is one path planning package called ```actionlib``` which takes a stacic map and a goal (inspired by [Learn Turtlebot and ROS](https://github.com/markwsilliman/turtlebot/)). It has a built-in function that will compute the path for turtlebot, and moves the turtlebot to desired location. However, this method doesn't do well in our goal: 

* The map is static. In our case, pedestrain is always moving. A static map doesn't work.
* The computed path may not be our desired path. From our human modeling, we find the optimal threshold of distance turtlebot has to keep from pedestrain. The path generated by ```actionlib``` may not obey this rule.

We then come up with a modifeid solution to use ```actionlib``` package: considering pedestrain as a moving obstacle (circle, with the radius as our distance threshold) [insert picture here, caption: we write a python script to generate pgm map. The ] in the map, and each timesteps we will update the map by moving the circle. The steps are:

1. Create a map of the mocap room, use ```actionlib``` package to generate a path.
2. Add pedestrain as a moving circular obstacle, and update (generate new map) the circle every time-step. Call ```actionlib``` again to update our path
3. Turtlebot moves based on current path command.
4. Repeat step 2 and step 3 untill turtlebot reaches goal location.

We test our approach first by running ```rosrun map_server map_saver``` to create a layour map (pgm image), and then modify the map with image editing tools to add walls (black region in the map). 


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
