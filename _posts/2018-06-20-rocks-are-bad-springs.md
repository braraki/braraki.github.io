---
layout: post
title:  "Rocks Are Bad Springs"
date:   2018-06-20 22:25:00 -0500
categories: science
---

Something that really bothered me in high school and college was potential energy (PE). You know, they tell you in physics class that "all energy is conserved" and that "energy can neither be created nor destroyed". And true, PE certainly helps for accounting purposes. But still, what **IS** PE? When you lift a 1kg ball up by 1m, ~10 Joules of energy just disappears into thin air! Sure, it's stored as '10 J of PE', but as far as we know it has disappeared into the ether, only to mysteriously come flooding back into the physical realm when you drop the ball.

Thinking about PE inevitably leads one to think about jumping off a cliff. For scientific purposes, of course. For better or worse, I thought about this a lot - what mysterious thing happens as you walk up a cliff that causes your chemical and kinetic energy to transform into PE, and what happens when you jump off the cliff that causes the kinetic energy to come rushing back into your body? Well, obviously Earth's gravitational force imbues you with KE as you fall from the cliff. But why does that same force not do anything when you're standing on top of the cliff? We're taught that it's because the normal force from the ground cancels out the gravitational force, so there is net zero force on your body. That felt like a cop out though. Because isn't technically everything a spring?... isn't even the cliff a spring?!?! Maybe that's where all of that so-called 'potential energy' is going!

So I looked into it, and it turns out that, at human scales, the ground is a sucky spring. I still haven't solved the mystery of potential energy.

$$ \sigma = - \mathbb{E} * e $$

We can reformulate this as

$$ F = - A * \mathbb{E} * \delta_{l}/L $$

For simplicity's sake let's assume you somehome manage to occupy $$A = 1 m^2$$ of surface area, you weigh 100 kg, standard gravity is $$10 m/s^2$$, and the cliff is a steel cliff $$ L = 100 m $$ tall. The Young's Modulus of steel is 200 GPa. Therefore

$$ \delta_{l} = - \frac{F * L }{A * \mathbb{E}} = - \frac{(100 kg) * (10 m/s^2) * (100 m)}{(1 m^2) * (200 * 10^9 Pa)} = 0.5 \textrm{microns} $$

For scale, a human hair is ~75 microns in diameter, a red blood cell is ~5 microns across, and many bacteria are ~0.5 microns across. Buttttt, how much spring energy is stored in the cliff?

$$ PE = \frac{1}{2} (A * \mathbb{E} / L) \delta_{l}^2 = \frac{1}{2} ((1 m^2 * (200 * 10^9 Pa)) / (100 m))) * (0.5 * 10^-6 m)^2 = 0.00025 J$$

Sooooooooo... if you weigh 100 kg and scaled a 100 m tall cliff, then that means there are ~100,000 J of PE to account for. The springiness of the cliff accounts for 0.00025 J of those 100,000 J.