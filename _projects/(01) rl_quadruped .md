---
name: Quadruped Walking with Reinforcement Learning
tools: [Python, PyTorch, Deep Reinforcement Learning]
image: https://scferro.github.io/assets/deep_rl_main.gif
description: Used the Soft Actor-Critic algorithm to teach a quadruped robot to walk in simulation. 
---

# Quadruped Walking with Reinforcement Learning
<br>

### Overview
In this project, I applied reinforcement learning to teach a simulated ant to walk in the Gym Ant environment, focusing on the Soft Actor-Critic (SAC) algorithm. SAC, an off-policy method excelling in continuous action spaces, uses a maximum entropy framework to balance exploration and exploitation. I aimed to achieve both quick learning and high performance in the ant's walking behavior. The project involved fine-tuning algorithm aspects and customizing the reward function. Testing on both the Inverted Double Pendulum and Ant environments showed successful outcomes, with notable improvements in the ant's walking smoothness using a tailored reward function. This work demonstrates the effectiveness of modern reinforcement learning in tackling complex robotic control problems and highlights the importance of careful algorithm selection and reward function design in achieving desired behaviors.



### Results and Observations
* **Training Efficiency**: The SAC demonstrated rapid learning capabilities, significantly improving the agent's performance in navigating the complex Ant environment.
* **Algorithmic Insights**: The project underscored the importance of parameter tuning and algorithmic adjustments to address specific challenges such as the exploration-exploitation trade-off and policy stability.


### Technical Approach
* **Algorithm:** Utilization of the Soft Actor-Critic, a model-free, off-policy algorithm that operates on the principles of maximizing entropy for robust learning.
* **Environment Setup:** Configurations included defining intricate state and action spaces, tuning parameters for optimal performance, and managing the replay buffer for experience replay.
* **Network Design:**
    1. Policy Network (Actor): Comprises three hidden layers with 128 neurons each, leading to outputs for action mean and log standard deviation, essential for the stochastic policy behavior in SAC.
    2. Critic Network (Q-Network): Integral for estimating the Q-values necessary for the critic aspect of SAC, using a multi-layer design to process state-action pairs.


### Conclusions and Future Work
The project not only showcased the SAC’s potential in a complex simulation but also highlighted the critical aspects of implementing advanced reinforcement algorithms in environments with large state and action spaces. It sets the stage for further explorations into more sophisticated environments and more complex tasks in reinforcement learning.

Future directions include scaling the implementation to other challenging environments, exploring the impacts of different hyperparameters on learning efficiency, and integrating multi-agent dynamics for broader applicability.


<p class="text-center">
{% include elements/button.html link="https://github.com/scferro/deep_rl_quadruped_walking" text="GitHub" %}
</p>
