---
title: references of DQN paper
tags:
---
这篇来自 DeepMind 的文章将增强学习与神经网络结合，克服了之前增强学习“需要提炼关键信息或者提供明确的、有决定性的变量”这一缺陷，文中的模型可以利用神经网络直接从高维数据（游戏画面）中提炼关键信息，从而避免了 Q-learning 中需要学习记忆的情况太多以至于难以收敛的问题。

自然地，分析这篇文章的原理可以从两个角度出发，即增强学习（文中采用 Q-learning）和深度学习。

## Q-learning
下面的公式是认识 Q-learning 的一个切入点：$$
\begin{align}
Q\_{t+1}(s\_{t},a\_{t}) &= Q\_{t}(s\_{t},a\_{t})+\alpha\cdot(r\_{t}+\gamma\cdot\max\limits\_{a}Q(s\_{t+1},a)-Q\_{t}(s\_{t},a\_{t})) \\\
&= (1-\alpha)\cdot Q\_{t}(s\_{t},a\_{t})+\alpha\cdot(r\_{t}+\gamma\cdot\max\limits\_{a}Q(s\_{t+1},a))
\end{align}$$上式即表示了动作效用函数 $Q$ 的更新方式，由这期行动得到的未来最大回报基于当期回报 $r\_{t}$ 和下一期情形下得到的回报 $\max\limits\_{a}Q(s\_{t+1},a)$。$Q\_{t}(s\_{t},a\_{t})$ 是在情形 $s\_{t}$ 下做出行动 $a\_{t}$ 所获的预期回报，而 $r\_{t}$ 是在情形 $s\_{t}$ 下做出行动 $a\_{t}$ 所获的实际回报，$\max\limits\_{a}Q(s\_{t+1},a)$ 是根据之前训练的记忆，在 $s\_{t+1}$ 下做出选择所能获得最大回报。$\gamma$ 是未来回报的折现因子（discount factor）。$\alpha$ 是学习速率（learning rate），$\alpha$ 越大，保留之前训练的效果就越少。$r\_{t}+\gamma\cdot\max\limits\_{a}Q(s\_{t+1},a)$ 则是每次被学习到的部分。

在学习开始前，$Q$ 是一个人为设定的值，每次 agent 选择一个行动并观察到回报，新的情形也就来自于上一个情形以及当下的行动，从而将 $Q$ 更新。
用伪代码表示即：

```python
initialize Q    // 随机初始化Q
for episode in M:    // 每一局游戏为一个episode
    initialize S    // S为游戏开始的初始情形
    for t in T:    // 直到游戏结束
        choose action a_t    // 选择一个行动，比如以文中的 ε-greedy 策略，以概率 ε 选择随机行动，概率 1-ε 选择当前状态下效用值最大的行动
        get r_t, x_t+1, s_t+1    // 执行 a_t 后，得到对应回报 r_t 和新的画面 x_t+1，情形更新为 s_t+1
        Q(s_t,a_t) = Q(s_t,a_t) + α*[r_t + γ*maxQ(S,a) - Q(s_t,a_t)]    // 在maxQ(S,a)中更新S=s_t+1，得到新的Q
        S = s_t+1    // 情形更新
```


## 神经网络的引入
对于本文的任务，单纯使用 Q-learning 增强学习有两个问题：一个是 Q-learning 需要针对不同的游戏各自设定能刻画情形 $s$ 的指标，即无法用同一个 Q-learning 学习不同的游戏；另一个是如果将 84×84×4 的游戏画面直接作为输入，那么 Q-table 中就有太多太多的情形（对于 256 色的屏幕，则有 $256^{84\times84\times4}$ 种）需要列举学习，这在运算上根本不现实。因此，DeepMind 将深度学习引入原来的增强学习模型，来替代“屏幕输入 - $Q$ 输出”这一过程，神经网络架构如下：
<div align="center"><table width="80%"><tr><th style="text-align:center">Layer</th><th style="text-align:center">Input</th><th style="text-align:center">Filtersize</th><th style="text-align:center">Stride</th><th style="text-align:center">Numfilters</th><th style="text-align:center">Activation</th><th style="text-align:center">Output</th></tr><tr><td style="text-align:center">conv1</td><td style="text-align:center">84×84×4</td><td style="text-align:center">8×8</td><td style="text-align:center">4</td><td style="text-align:center">32</td><td style="text-align:center">ReLU</td><td style="text-align:center">20×20×32</td></tr><tr><td style="text-align:center">conv2</td><td style="text-align:center">20×20×32</td><td style="text-align:center">4×4</td><td style="text-align:center">2</td><td style="text-align:center">64</td><td style="text-align:center">ReLU</td><td style="text-align:center">9×9×64</td></tr><tr><td style="text-align:center">conv3</td><td style="text-align:center">9×9×64</td><td style="text-align:center">3×3</td><td style="text-align:center">1</td><td style="text-align:center">64</td><td style="text-align:center">ReLU</td><td style="text-align:center">7×7×64</td></tr><tr><td style="text-align:center">fc4</td><td style="text-align:center">7×7×64</td><td style="text-align:center"></td><td style="text-align:center"></td><td style="text-align:center">512</td><td style="text-align:center">ReLU</td><td style="text-align:center">512</td></tr><tr><td style="text-align:center">fc5</td><td style="text-align:center">512</td><td style="text-align:center"></td><td style="text-align:center"></td><td style="text-align:center">18</td><td style="text-align:center">Linear</td><td style="text-align:center">18</td></tr></table></div>

网络的输入是 4 幅 84×84 的画面，输出是可能的 18 个行动对应的 $Q$（选择其中最大值对应的行动），其中损失函数为：$$L=[r\_{t}+\gamma\cdot\max\limits\_{a}Q(s\_{t+1},a)-Q\_{t}(s\_{t},a\_{t})]^2$$由于 $Q$ 有可能是任何大小的值，因此上面的损失函数看重的是行动得到的 $r\_{t}+\gamma\cdot\max\limits\_{a}Q(s\_{t+1},a)$ 对原来预测 $Q\_{t}(s\_{t},a\_{t})$ 的偏离情况。

综上，本文的 deep Q-learning 用伪代码表示即：

```python
initialize D    // 初始化记忆空间
initialize Q    // 随机初始化Q
for episode in M:    // 每一局游戏为一个episode
    initialize S    // S为游戏开始的初始情形
    for t in T:    // 直到游戏结束
	    choose action a_t    // 以 ε-greedy 策略选择一个行动
            get r_t, x_t+1, s_t+1    // 执行 a_t 后，得到对应回报 r_t 和新的画面 x_t+1，情形更新为 s_t+1
	    update D    // 更新记忆空间D，添加经验(s_t, a_t, r_t, s_t+1)
	    sample random (s0, a0, r0, s0') from D   // 从D中随机抽取经验(s0, a0, r0, s0')
	    if game end at s':
	        y = r0    // 如果s0'是游戏终止
	    else:
                y = r0 + γmaxQ(s0',a)    // 如果s0'不是游戏终止,找到使Q最大的行动a
	    train the network using L = (y - Q(s0,a0))^2    // 按照损失函数L进行梯度下降
            S = s_t+1    // 情形更新，论文中为每C步更新一次
```


## 其他细节
### Experience replay
Experience replay 即从过往保存的记忆数据中随机抽取子样本用于训练，相比直接将上一次行动的经验用于训练，这种方法减少了训练数据的序列相关性，可以避免训练陷入局部最优。
### ε-greedy
为了解决“探索更优行动”和“直接采用现有经验”之间的矛盾（exploration-exploitation dilemma），本文采用了 ε-greedy 策略，以概率 $\varepsilon$ 选择随机行动（探索），概率 $1-\varepsilon$ 选择当前状态下效用值最大的行动（直接采用经验，即用 $\arg\max\limits\_{a}Q(s\_{t},a)$）。并且在训练开始时 $\varepsilon=1$，并逐渐随着训练降低至 $\varepsilon=0.1$，在这种策略下，训练开始时可以探索到尽可能多的情形，在后期侧重经验又可以保证游戏表现。


参考文献：
* [Replicating the Paper "Playing Atari with Deep Reinforcement Learning" (2014)](https://courses.cs.ut.ee/MTAT.03.291/2014_spring/uploads/Main/Replicating%20DeepMind.pdf)
* [Wikipedia: Q-learning](https://en.wikipedia.org/wiki/Q-learning)
* [用 Q-learning 玩 Flappy Bird](https://www.zhihu.com/question/26408259)
* [Tambet Matiisen's 关于本文的博客](http://www.nervanasys.com/author/tambet/)
* [Convergence of Q-learning: a Simple Proof](http://simplecore-dev.intel.com/nervana/wp-content/uploads/sites/55/2015/12/ProofQlearning.pdf)