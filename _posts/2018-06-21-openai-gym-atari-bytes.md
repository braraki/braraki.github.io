---
layout: post
title:  "OpenAI Gym Atari Bytes"
date:   2018-06-21 22:20:00 -0500
categories: research
---

The OpenAI Gym Atari environments can output two types of states - an image and/or 128 bytes of RAM. The RAM apparently describes the state of the game as it was represented on the Atari 2600. You can extract a lot of information from this RAM, but unfortunately the byte meanings for many games are not available online. For a project I'm working on, I need to be able to extract certain pieces of information from the RAM of Atari games, and I'll post some of our results here. I want to emphasize, however, that this is **not** my work but the work of an undergraduate working with me. 



**Space Invaders**

| Byte [0, 127] | Description |
|:-------:|--------|
| 17 | Number of remaining aliens |
| 73 | Number of remaining lives |
| 75 | 6 when player is blowing up, 0 or 1 otherwise |
| 76 | 4 when pink saucer is on screen, 3 for a period <br> after shooting, 0 otherwise |
| 78 | Y-coord of shot laser (increases as projectile <br> approaches top of screen), 0 when no laser <br> has been shot by the player |
<br>

**Montezuma's Revenge**

| Byte [0, 127] | Description |
|:-------:|--------|
| 58 | Number of remaining lives |
| 65 | Inventory (represented as a bitstring) |
|  3 | Number of current room |
| 66 | Bits 3 and 2 represent whether the left and right <br> doors are locked, respectively (there are only up to 2 doors per room) |
| 30 | Character sprite state (idle, in air, on ladder, <br> on rope, dying, etc) |


(More information [available here](https://repositori.upf.edu/bitstream/handle/10230/30867/Garriga_2016.pdf?sequence=1&isAllowed=y))

**Venture**

| Byte [0, 127] | Description |
|:-------:|--------|
| 70 | Lives Remaining |
| 26 | Y-coord of player (top = 1, bottom = 76) |
| 85 | X-coord of player (left = 1) |
| 188 | 251 in outer map, 247 in rooms? |
| 19 | Room player is in (outer = 8, bottom left = 1, <br> bottom right = 0, top left = 64, top right = 2) |
| 73 | 0 when arrow is in air, 128 when holding arrow, <br> also 0 when in outer map |
| 92 | Possibly a countdown for hall monster |
| 102 | Seems to be 2 when an item is picked up and 0 otherwise |
| 17 | Maybe inventory (1 - room0, 2 - room1, 4 - room64, 8 - room4)
<br>
**Private Eye**

| Byte [0, 127] | Description |
|:-------:|--------|
| 38 | Constant for screens with nothing moving, counts <br> down for screens with flowerpots/bricks |
| 92 | Room no. [0, 32] |
| 67 | Minute counter |
| 72 | Stage of game you are on? <br> 0 - game start <br> 1 - return gun <br> 2 - get the money after returning gun <br> 3 - return the money before getting gun <br> 4 - return the money after returning the gun <br> 4 - get the gun after returning the money <br> 7 - when you have Le Fiend after doing gun then money <br> 6 - get Le Fiend after doing money then gun <br> 9 - finish game by returning Le Fiend |
| Either 60 or 5 | Inventory 24 - got money <br> 20 - got gun <br> 0 - nothing <br> 28 - got Le Fiend <br> (5 has flashing item while being dropped while 60 <br> is whether the item is held or not) |
<br>
**Pitfall!**

| Byte [0, 127] | Description |
|:-------:|--------|
| 89 | Seconds countdown |
|  1 | Room number? But no order? |
| 100| Player state <br> 5 - stand <br> 43210 - walk <br> 0 - jump <br> 78 - ladder climbing <br> 6 - vine |
|  4 | 0 when jump or on vine, 128 otherwise |
| 113| Treasures left? Starts at 31, game probably ends <br> when it is equal to 0 and player picks up the last treasure |
| 109| Is 0, becomes 2 when the treasure of screen 7 is <br> collected. Stays 0 when a different treasure is <br> collected. Maybe a bitmask for which of the <br> 8 gold bars have been collected? |


[Pitfall! map](http://pitfallharry.tripod.com/MapRoom/PitfallMap.html 
)