# MAD-ARL-paper-review


Review of the [Adversarial Deep RL for Improving the Robustness of Multi-agent Autonomous Driving Policies](https://arxiv.org/abs/2112.11937) paper

Research proposes the method for improving the reinforcement learning models **Adversarial Robustness** of Autonomous Cars (ACs) in training with adversarial (malicious) agents who exacerbate road conditions.

## BACKGROUND
When training AC models - an exhaustive list of road test scenarios is needed.<br/>
Thus a lot of related works mainly focus of methods for generating realistic synthetic test scenarios and/or discovering error-prone cases.


- Authors in [14] use GANs to generate synthetic images to validate the driving robustness of ACs
<img width="467" alt="Screenshot 2023-06-20 at 12 23 45" src="https://github.com/Mikkicon/MAD-ARL-paper-review/assets/29481776/c34a0c77-41e3-4e90-99d2-fdae5ec5576d">

- Authors in [15] proposes a testing tool for AC models by generating test cases using real-world conditions: rain and lightning conditions
<img width="679" alt="Screenshot 2023-06-20 at 12 25 29" src="https://github.com/Mikkicon/MAD-ARL-paper-review/assets/29481776/3a4046b4-33f7-46fe-bad5-51cd01c12dbf">

- Authors in [9] use knowledge of vehicle dynamics and genetic algorithms to find failure scenarios.
  The experiments are performed in a **partial multi-agent** environment with one AC under test driving alongside **non-AC** traffic, as non-players.
  As a limitation, the work does not address the problem of **improving the robustness**
<img width="634" alt="Screenshot 2023-06-20 at 12 34 55" src="https://github.com/Mikkicon/MAD-ARL-paper-review/assets/29481776/60044d90-1b28-4a7c-8161-26e9d56a01a4">

- Authors in [37] "Deepxplore" try to maximize the chances of finding differential behavior by modifying the input, i.e., the image of the red car, towards maximizing its probability of being classified as a car by one DNN but minimizing corresponding probability of the other DNN
  They pose an optimization problem and apply gradient **ascent** over the results of test inputs in order to maximize the chance of finding corner cases
<img width="839" alt="Screenshot 2023-06-20 at 12 56 17" src="https://github.com/Mikkicon/MAD-ARL-paper-review/assets/29481776/5947c7e0-12bf-4c93-9d04-ef7f3a998b78">

- Authors is [24] include training more than one adversarial RL agent against one rule-based driving model
<img width="1132" alt="Screenshot 2023-06-20 at 13 10 58" src="https://github.com/Mikkicon/MAD-ARL-paper-review/assets/29481776/f4072317-2f77-41e9-844f-307b9c0092bc">

- Authors is [25] encode **domain relevant information** into the search procedure with this modification, the AST method discovers a larger and more expressive subset of the failure space

## NOVELTY

### Neither of these approaches has been tested in an RL-based multi-agent AC environment
Authors claim that or all of the provided related works research is either:
1. doesn't have AC testing in a realistic **multi-agent** driving environment OR
2. multi-agent environment with 1 AC under test with traffic NPCs. OR
3. [24] approach only exposes the trained ACs to a single non-AI model as well as lacks complex adversarial driving scenarios, such as **T-intersection**, (which authors target in our work)
4. [25] besides lacking multi-agent environment - it uses adversarial perturbations as noise in the simulation model itself.
   In contrast, our work adds perturbations by the adversarial car’s policy, thus adding adversarial actions as example trajectories for improving AC’s driving policies

As stated by the authors:
> in the near future, multiple ACs will co-exist on the road, and the more such cars start interacting with each other, as well as with human drivers, the more complex their testing becomes<br/>

### Thus **novelty of the paper** lies in adversarial testing of ACs in a **multi-agent** driving environment for the purpose of
- finding failures in AC driving models
- improving the robustness of AC driving models against these failures

## MAD-ARL FORMULATION
Multi-Agent Driving with Adversarial Reinforcement Learning MAD-ARL
The adversarial agent is trained to take actions such to create observations that _appear natural_ to the victim agents while being adversarial in nature. <br/>
As an example, the adversary is learning to steer offroad most of the time while crossing the intersection. <br/>
Such unusual behavior will act as an adversarial noise to the visual observations of victim ACs
2 steps:
1. Finding Failures in AC Driving Policies
2. Improving the Robustness of AC Policies by Retraining

## Finding Failures in AC Driving Policies
Problem is modeled as a 2-player Markov game with independent **non-communicating** competitive players: 2 victims and 1 adversary

<img width="1332" alt="Screenshot 2023-06-18 at 18 13 30" src="https://github.com/Mikkicon/MAD-ARL-paper-review/assets/29481776/bdfca232-b699-4c0d-81b9-16da21fb3f14">

## REWARD FUNCTIONS

### Victims clearly have to nagvigate as quickly as possible without accidents
<img width="960" alt="Screenshot 2023-06-18 at 18 15 54" src="https://github.com/Mikkicon/MAD-ARL-paper-review/assets/29481776/6361cf49-9583-4fe8-a44b-4946251cafaf">

### Adversary, on the other hand, has to maximize accidents - specifically offroad-steering and collisions
<img width="659" alt="Screenshot 2023-06-18 at 18 15 21" src="https://github.com/Mikkicon/MAD-ARL-paper-review/assets/29481776/89b40de6-0b9a-4023-a4d3-73a196c82745">

## EXPERIMENTS
### Step 1: Finding Failure States
Freeze victims weights to train adversary<br/>

<img width="647" alt="Screenshot 2023-06-18 at 18 29 08" src="https://github.com/Mikkicon/MAD-ARL-paper-review/assets/29481776/c8cba05d-e14c-4304-9e69-20defe83b345">

### Step2: Retraining Victim ACs for Improved Robustness
unfreeze the weights of the victim AC agents to retrain their end-to-end driving policies by keeping the adversarial agent in the same environment

Setup:
- RLlib [46] from Ray framework which is an open source project providing a very fine-tuned and scalable RL implementation interface. 
- Carla [38] urban driving simulation framework for training, testing, and validating ACs. 
- Carla and Open AI’s Gym toolkit integration - we utilize open source platform Macad-gym [45].
- Tensorflow [47] version 2.1.0 within the RLlib library for creating DRL based model architectures

## RESULTS
<img width="1393" alt="Screenshot 2023-06-18 at 18 19 28" src="https://github.com/Mikkicon/MAD-ARL-paper-review/assets/29481776/9ccd9a7c-2821-43cb-8a5b-3823e81ebcde">
<img width="426" alt="Screenshot 2023-06-18 at 18 25 12" src="https://github.com/Mikkicon/MAD-ARL-paper-review/assets/29481776/3158d534-7c8b-468e-8e08-e32f82f66a68">

## CONCLUSION
### Pros
1. Provides novelty in terms of providing unique multi-agent setup where all of the participants are being trained on different steps of the algorithm, similar to GAN - where one agent's gain is another agent's loss - zero sum game.
2. A large amount of related works have been covered to assess theie drawbacks
3. Research tackles the near-future problem of different ACs coexisting on the road - treating victims and adversary not as NPC but as trainable agents.

### Cons
1. Basically an extension of related work [24] with T-intersection and additional victim
2. "Another work [22] uses RL to stress-test ACs in a simulated environment" - with no explanation of why they mention it.
3. Why specifically offroad and colision, and why only them, without stopping in the center which is seen vastly in cases of stupor for novice drivers? (output layer of PPO (Proximal Policy Optimization), we have nine discrete values as the action space which are used by each driving agent to make control decisions. All of the discrete actions can be summed into three main actions: **Steer**, **Throttle**, and **Brake** )?
4. Biggest con - Since there are two different adversarial reward-based policies involved, the retraining of the victim ACs is done twice separately
- Meaning that victim models would have to be re-trained for every adversary policies (offroad-steering & collision in our case)

