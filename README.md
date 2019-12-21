# Lychee Bot - Group 36 EE106A Final Project

[Link](https://lychee-bot.github.io/project/)

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

## How People Move
Based on primliminary analysis with 10 video takes in the Motion Capture room of 2 people moving. Human motion is dynamic and highly context dependent, however, we wanted tried to infer simple patterns in the motion.
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

## Trajectory Planning


## Challenges

## Future Work
1. Research in smoothing the path (move in curves)
2. Perform online multi-human motion prediction and add to Turtlebot.
3. Add Computer Vision based planner. Test it on Berkeley Campus. 
4. Make Kiwi bot autonomous, then start our own self-driving delivery service :)


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
