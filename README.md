# MAD-ARL-paper-review



Review of [Adversarial Deep Reinforcement Learning for Improving the Robustness of Multi-agent Autonomous Driving Policies](https://arxiv.org/abs/2112.11937) paper



Research proposes the method for improving the reinforcement learning models robustness of Autonomous Cars (ACs) in training with adversarial (malicious) agents who exacerbate road conditions.

## Introduction
When training AC models - an exhaustive list of road test scenarios is needed, thus related works mainly consist of methods for generating realistic synthetic test scenarios using testing frameworks such as:
- Udacity testing framework
- OpenStreetMap traffic simulator SUMO
- industrial-grade autonomous driving platform Baidu Apollo
- Carla-based urban driving simulation

One interesting example of AC trainig is "Deepxplore: Automated whitebox testing of deep learning systems":
They provide a whitebox method for testing ACs by triggering as many neurons in the driving model as possible for finding failure scenarios. 
They pose an optimization problem and apply gradient **ascent** over the results of test inputs in order to maximize the chance of finding corner cases


Authors claim that or all of the provided related works research is either:
a) does not address the problem of AC testing in a realistic **multi-agent** driving environment OR
b) partial multi-agent environment with one AC under test driving alongside **non-AC** traffic, as non-players. OR
c) Work [24] includes training more than one adversarial RL agent against one rule-based driving model 
approach only exposes the trained ACs to a single non-AI model as well as lacks complex adversarial driving scenarios, such as T-intersection, (which authors target in our work)
d) Work [25] besides lacking multi-agent environment - it uses adversarial perturbations as noise in the simulation model itself. 
In contrast, our work adds perturbations by the adversarial car’s policy, thus adding adversarial actions as example trajectories for improving AC’s driving policies


Training ACs usually consists of the following steps:
1. Generating test scenarios for discovering errors
2. Finding corner cases
3. Adding environment exacerbations (rain, snow, visibility, or interfering objects for LiDAR -based models)
4. Adding other non-AC agents which simulate human-driving
5. Adding adversarial exacerbations - adversary agents intentionally worsening road conditions (unpredictable driving, offroad steering, targeting collisions, etc.)
6. Iteratively trainig models on these scenarios

## MAD-ARL FORMULATION
Multi-Agent Driving with Adversarial Reinforcement Learning MAD-ARL
The adversarial agent is trained to take actions such to create observations that appear natural to the victim agents while being adversarial in nature. As an example, the adversary is learning to steer offroad most of the time while crossing the intersection. Such unusual behavior will act as an adversarial noise to the visual observations of victim ACs

2 steps:
1. Finding Failures in AC Driving Policies
2. Improving the Robustness of AC Policies by Retraining

### Finding Failures in AC Driving Policies
<img width="417" alt="Screenshot 2023-06-18 at 18 10 33" src="https://github.com/Mikkicon/MAD-ARL-paper-review/assets/29481776/cbb910a4-0f9b-4096-88b5-fa5dfbe54a4b">

model our problem as a 2-player Markov game with independent non-communicating competitive players: 2 victims and 1 adversary

<img width="1332" alt="Screenshot 2023-06-18 at 18 13 30" src="https://github.com/Mikkicon/MAD-ARL-paper-review/assets/29481776/bdfca232-b699-4c0d-81b9-16da21fb3f14">

### Reward Functions
<img width="960" alt="Screenshot 2023-06-18 at 18 15 54" src="https://github.com/Mikkicon/MAD-ARL-paper-review/assets/29481776/6361cf49-9583-4fe8-a44b-4946251cafaf">

<img width="659" alt="Screenshot 2023-06-18 at 18 15 21" src="https://github.com/Mikkicon/MAD-ARL-paper-review/assets/29481776/89b40de6-0b9a-4023-a4d3-73a196c82745">

## EXPERIMENTS
Step 1: Finding Failure States
Freeze victims weights to train adversary
<img width="647" alt="Screenshot 2023-06-18 at 18 29 08" src="https://github.com/Mikkicon/MAD-ARL-paper-review/assets/29481776/c8cba05d-e14c-4304-9e69-20defe83b345">

Step2: Retraining Victim ACs for Improved Robustness
unfreeze the weights of the victim AC agents to retrain their end-to-end driving policies by keeping the adversarial agent in the same environment

Setup:
- RLlib [46] from Ray framework which is an open source project providing a very fine-tuned and scalable RL implementation interface. 
- Carla [38] urban driving simulation framework for training, testing, and validating ACs. 
- Carla and Open AI’s Gym toolkit integration - we utilize open source platform Macad-gym [45].
- Tensorflow [47] version 2.1.0 within the RLlib library for creating DRL based model architectures

## Results
<img width="1393" alt="Screenshot 2023-06-18 at 18 19 28" src="https://github.com/Mikkicon/MAD-ARL-paper-review/assets/29481776/9ccd9a7c-2821-43cb-8a5b-3823e81ebcde">
<img width="426" alt="Screenshot 2023-06-18 at 18 25 12" src="https://github.com/Mikkicon/MAD-ARL-paper-review/assets/29481776/3158d534-7c8b-468e-8e08-e32f82f66a68">

# Cons
## Introduction
1. Basically a small extension of related work [24] with T-intersection
2. "Another work [22] uses RL to stress-test ACs in a simulated environment" - with no explanation of why they mention it.
3. Why specifically offroad and colision, and why only them, without stopping in the center which is seen vastly in cases of stupor for novice drivers? (output layer of PPO (Proximal Policy Optimization), we have nine discrete values as the action space which are used by each driving agent to make control decisions. All of the discrete actions can be summed into three main actions: **Steer**, **Throttle**, and **Brake** )?
4. Biggest con - Since there are two different adversarial reward-based policies involved, the retraining of the victim ACs is done twice separately
- Meaning that victim models would have to be re-trained for every adversary policies (offroad-steering & collision in our case)

