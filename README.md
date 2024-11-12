
参考：


* 官方链接：[Gym documentation \| Make your own custom environment](https://github.com)
* [腾讯云 \| OpenAI Gym 中级教程——环境定制与创建](https://github.com)
* [知乎 \| 如何在 Gym 中注册自定义环境？](https://github.com)
* g，写完了才发现自己曾经写过一篇：[RL 基础 \| 如何搭建自定义 gym 环境](https://github.com):[豆荚加速器](https://baitenghuo.com)


（这篇博客适用于 gym 的接口，gymnasium 接口也差不多，只需详细看看接口定义 魔改一下即可）




---


安装 openai gym：



```


|  | # pip install gym |
| --- | --- |
|  | import gym |
|  | from gym import spaces |


```

需实现两个主要功能：


* `env.__init__()` 和 `obs = env.reset()` 函数；
* `obs, reward, done, info = env.step(action)` 函数。


## 01 env 的初始化与 reset


`env.__init__()` 函数：


* 输入是 env 的一些初始化条件，比如环境的地图多大、环境里有多少个金币以及每个金币的位置。如果只训练一个特定的任务，比如在 3×3 地图中吃右上角的一个金币，则这些设置都可以写死，不需要在 env 初始化时传递参数。
* 在 `env.__init__()` 函数中，需要定义 `self.observation_space` 和 `self.action_space` 。
	+ 如果状态空间 / 动作空间是离散的，则使用 `spaces.Discrete([space dim])` ；
	+ 如果是连续的，则使用 `spaces.Box(low=np.array([0,1]), high=np.array([100,50]), dtype=np.float32)` ，其中 low high 都要符合状态空间 / 动作空间的维度，分别代表每一维的最大最小值。
	+ 连续空间也可以使用 `spaces.Box(low=0, high=255, shape=(84, 84), dtype=np.uint8)` 这种形式，其中空间的每个维度具有相同的最大最小值，shape 表示空间的维度。
	+ `gym.spaces` 的具体使用：官方文档 [Gym documentation \| Spaces](https://github.com) ， [知乎 \| Gym 中 Spaces 浅入浅出的理解](https://github.com) 。
* 可以在 `env.__init__()` 函数的最后调用一下 `env.reset()` 函数。


`obs = env.reset()` 函数：


* 作用是初始化环境，比如把 agent 放到地图左下角，金币放在地图右上角，内置的计步器 reset 到 0 之类。
* 它的返回值 obs，应该是一个与状态空间维度相同的 np.ndarray。


## 02 与环境交互的 step 函数


`obs, reward, done, info = env.step(action)` 函数：


* 输入 action，应该是一个与动作空间维度相同的 np.ndarray。（一般的环境应该不支持批量输入 action，即 `action.shape = (batch_size, action_dim)` ）
* step 函数被用在 agent 与 env 的交互；env 接收到输入的动作 action 后，内部进行一些状态转移，输出：
	+ 新的状态 obs：与状态空间维度相同的 np.ndarray；
	+ reward：奖励值，实数；
	+ done：bool 值，True 代表这个 episode 已经跑完（比如 agent 吃到了金币，或 agent 已经走完了 1000 步）（此时该 reset 了），False 则代表这个 episode 还没跑完。
	+ info：python 的字典（dict），可以传递一些信息，没有信息传递则可以设成 `{}` 。


## 03 高级用法


* `env.__init__(render_mode="human" or "rgb_array")` 以及 `rgb_frame = env.render()` 。render mode \= human 好像可以使用 pygame，rgb frame 则是直接输出（比如说）shape \= (256, 256, 3\) 的 frame，可以用 imageio 保存成视频。
* 如何注册 gym 环境：[RL 基础 \| 如何注册自定义 gym 环境](https://github.com)


