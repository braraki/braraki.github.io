---
layout: page
title: Research
permalink: /research/
---

My [Google Scholar page](https://scholar.google.com/citations?user=j1OfCS8AAAAJ&hl=en&oi=ao) has an up-to-date list of my publications.

<div>
<a href="#publications">Publications</a>
{% if site.data.publications.categories %}
<ul>
  {% for category in site.data.publications.categories %}
    <li> <a href={{category[1] | prepend: '"#' | append: '"' }}>{{category[1]}}</a> </li>
  {% endfor %}
</ul>
{% endif %}
</div>

## My Research Path

My research interests have shifted a lot over the years. As an undergraduate studying Mechanical Engineering, I was interested in [Manufacturing](#Manufacturing) in areas such as soft robotics and 3D printing. The [Harvard REU program](https://www.seas.harvard.edu/office-education-outreach-community-programs/research-experience-undergraduates-reu), a summer research program funded by the [NSF](https://www.nsf.gov/crssprgm/reu/), which I participated in after my junior year of college, was a transformative experience for me. Under the mentorship of [Nils Napp](https://www.ece.cornell.edu/faculty-directory/nils-napp) I learned how fun and interesting design and manufacturing can be, as I got to design passive valves for soft robots. We even managed to publish a [paper](publications/icra14.pdf) from my summer work. After my summer at Harvard, I had the good fortune to be able to work at the [GRAB Lab](https://www.eng.yale.edu/grablab/) at Yale under the mentorship of [Prof. Aaron Dollar](https://seas.yale.edu/faculty-research/faculty-directory/aaron-m-dollar) and [Lael Odhner](https://www.linkedin.com/in/lael-odhner-52b12585) during my senior year. Our project involved using low melting temperature metal to make 3D electrical traces in 3D-printed parts, ultimately resulting in another [2](https://www.eng.yale.edu/grablab/pubs/Swensen_ICRA2015.pdf) [papers](https://pdfs.semanticscholar.org/a76e/749fd628c21773263a3f29e9cc3a7c6627fd.pdf). These amazing undergraduate experiences, which I was extremely fortunate to have, are the reason why I am doing research today.

After college, I entered the [Mechanical Engineering Master's program at MIT](http://meche.mit.edu/education/graduate). I found a home and place to work with [Prof. Daniela Rus](http://danielarus.csail.mit.edu/). But I first spent one semester working at Harvard again at the [Microrobotics Lab](https://www.micro.seas.harvard.edu/), where I worked on designing an adorable foldable robot that we called the [Flying Monkey](https://www.youtube.com/watch?v=m9Aa8BevJwg). During my Master's at MIT I worked on a project to [help blind people navigate](#Guiding the Blind). I learned a lot about embedded electronics as I helped design two belts for our experiments, a belt with LIDAR distance sensors and a belt with vibration motors, which communicated with each other via Bluetooth. Meanwhile, my work on the Flying Monkey led to an interest in [Robot Design](#Robot Design), and also caused Daniela to ask me, ["What if you have a swarm of such robots?"](https://www.technologyreview.com/2017/10/24/148390/building-tomorrows-robots/) This kicked off the main work of my Master's thesis, [designing small robots that can both fly and drive](https://www.youtube.com/watch?v=s-oFD5X-QtQ&ab_channel=MITCSAIL), and making path-planning algorithms to coordinate them and take advantage of their multi-modal locomotion abilities.

After I got my Master's, I decided to switch gears a little and transferred from MechE to [EECS](https://www.eecs.mit.edu/academics-admissions/graduate-program) (while staying in the same lab). I really enjoyed building robots, but now that I understood how to make them, I really wanted to learn [how to control them](#Multi-Robot Localization and Planning). That led to new projects in [localizing multiple cars using ultra-wideband radio distance measurements](publications/itsc19.pdf) and also a zany project on [using a quadcopter as a "mobile eye" for a car](fsr17.pdf). These projects were a baptism in the ways of [ROS](https://www.ros.org/), and I owe a lot of thanks to the [Duckietown](https://www.duckietown.org/) class for my imperfect but passable knowledge of this software that is at the core of so much open-source robot software. In addition to these projects, I was also fortunate enough to make small contributions to some [autonomous driving projects](#Autonomous Vehicles), although this was not my main focus.

After these projects, I felt like I had a good understanding of how to make and control robots. But I felt unsatisfied, because I felt like there was an essential element missing from all of these fields - the ability to do symbolic reasoning in a human-like manner. My conversion to this way of thinking occurred when I took a class called [Computational Cognitive Science](https://probmods.org/) with [Prof. Josh Tenenbaum](https://web.mit.edu/cocosci/josh.html). The realization that you can model virtually anything - even human cognition - using Bayesian models was a revelation to me, and since then I've focused my research on [applying this idea to robotic learning and planning](#Learning and Planning with Logic). My approach to this topic has been to take the traditional way of modeling an environment with a Markov Decision Process and add a high-level layer to it in the form of a Finite State Automaton that represents the rules/logical structure of the environment. Given this hierarchical model of the environment, the agent then has the structures in place to both learn the rules of the environment from demonstrations and use those rules to make plans. Although this is a far cry from human intelligence, I think that it is a fascinating area of study and hopefully a step in the right direction.

## Publications {#publications}

<div>
{% if site.data.publications.categories %}
{% if site.data.publications.papers %}
  {% for category in site.data.publications.categories %}
    <h1 id={{category[1] | prepend: '"' | append: '"' }}> {{category[1]}} </h1>
    {% for paper in site.data.publications.papers %}
      {% if paper.category == category[0] %}
        {% assign papers_of_type = site.data.publications.papers | where: "type", paper.type %}
        {% assign num_type = papers_of_type | size %}
        {% for type_paper in papers_of_type %}
          {% if type_paper.title == paper.title %}
            {% break %}
          {% else %}
            {% assign num_type = num_type | minus: 1%}
          {% endif %}
        {% endfor %}
        <p>
          <div class={{paper.type | prepend: '"header-' | append: '"'}}>[{{paper.type}}{{num_type}}]</div><!--
          --><div class="paper-title">{{paper.title}}.</div><!--
          --><span>
            <span class="tab">
              {% for author in paper.authors %}
                <span {% if author contains "Brandon Araki" %}class="first-author"{% endif %}>{{author}}</span><!--
                -->{% if forloop.last == false %}<!--
                  --><span>, </span>
                {% else %}<!--
                  --><span>.</span>
                {% endif %}
              {% endfor %}
              </span>
          </span> <br>
          <span class="tab">
            {{paper.conference | append: "." }}
            {{paper.date | append: "." }}
            {{paper.presentation | append: "." }}
          </span> <br>
          <span class="tab">
            {% for extra in paper.links %}
              <a href="{{extra[1]}}">[{{extra[0]}}]</a>
            {% endfor %}
          </span>
        </p>
      {% endif %}
    {% endfor %}
  {% endfor %}
{% endif %}
{% endif %}
</div>