---
layout: post
title:  "Playing the OpenAI Gym Atari Games"
date:   2018-06-15 22:53:00 -0500
categories: research
---

OpenAI Gym has a ton of simulated environments that are great for testing reinforcement learning algorithms. Using them is extremely simple:

{% highlight ruby %}
import gym
env = gym.make("Pong-v4")
action = env.action_space.sample()
observation, reward, done, info = env.step(action)
{% endhighlight %}

But what if you want to play the games yourself? I searched online for places to play the Atari 2600 versions of Pong, Breakout, and other games, and it was actually very hard to find free online versions of specifically the Atari 2600 games. It might be possible to download and emulator and play using that, but fortunately OpenAI Gym has a built-in function that makes playing the games pretty easy.

The code for the function is [here](https://github.com/openai/gym/blob/master/gym/utils/play.py). You can use it very easily by running a script like this

{% highlight ruby %}
import gym
from gym.utils.play import play
env = gym.make("MontezumaRevengeNoFrameskip-v4")
play(env, zoom=4)
{% endhighlight %}

The linked code goes a little more in-depth on how to use the play function.

Just a few notes:

- I would recommend using the "NoFrameskip" versions of the games if you just want to play, because when there are frameskips it becomes very hard to play.
- So you can actually play on any gym environment, including after you have put wrappers on the environment. This is a good way to test what your wrapped environment will look like to the RL algorithm you're training.
- Pong is awful to play on OpenAI Gym... Most of the other games are fine, but Pong in particular is unplayable because the paddle moves a huge amount every time you press up or down.
- The process will crash whenever you press an unallowed combination of buttons (such as left and right at the same time). They didn't put a lot of effort into implementing this.
- The direction keys are WASD, and shooting/jumping is the Space Bar usually.