.. title: What was your name again?
.. slug: what-was-your-name-again
.. date: 2013/09/17 13:00
.. tags: statistics, simulation
.. link: 
.. description: the article estimates the probability of a name collision in Slovenia using simulation

What was your name again?
============================

One of the thoughts which sometimes crosses my mind when I join a group of people is *"What if there is someone with my name already? How likely is that?"* In Slovenia, the name Lorand is very rare. Nevertheless it is interesting to think about how likely name collisions are for different names. 

This article aims to answer two questions:

1. How likely is it that there is a name collision in a group of people in Slovenia?

2. Given a specific name, how likely is it that it will not be unique in a group of given size?

.. TEASER_END: Click to read the entire article

First of all, let me list a few simplifying assumptions that I make:

- the group of people I am looking at is a random sample of the population. Groups of people working together at a job or studying together at the universitity are far from random, I hope :)

- there cannot be a name collision with a person of the opposite gender, i.e. the set of girl and boy names are disjoint (with a few exceptions this is true; only Saša occurs as both a girl's and a boy's name in the 200 most frequent names in Slovenia)

The problem of computing the probability of a name collision is related to the `birthday problem`_, where birthday collisions are computed. What makes it different is the distributions. Birthdays are uniformly distributed, while the frequency distribution of names is exponential.   

The figure below shows the frequency of each of the `most popular 200 Slovenian names`_, on the right, and the percentage of people accounted for by the names up to a certain popularity (on the left). Indeed, an exponential decrease of popularity can be observed, also, half of the population is covered by the most popular 50 names.

.. figure:: /img/namecollision/freqdist_cumulative_pair.png
   :width: 100%
   :align: center

   Frequency of the 200 most popular names (left) and cummulative distribution (right)

The top 200 names only cover about 80% of the population (819966 boys and 824442 girls). Using this data only would leave around 120000 boys and 115000 girls nameless.

Knowing that the popularity of names decreases exponentially, we can estimate the frequency of the boy names which didn't make it into the top ten by tuning the parameters *n* and *b* in the equation below:

.. figure:: http://latex.codecogs.com/gif.latex?730\int_{0}^{n}{e^{-bx}~dx}%20=%20120000
   :align: center

Doing some quick trial and error in `WolframAlpha`_ I came up with the values of *n* = 1800 and *b* = 0.006, which look reasonable. The tail of the boy name frequency distribution therefore looks like this:

.. figure:: /img/namecollision/boy_tail.png
	:width: 60%
	:align: center

	Frequency distribution of boy names. The blue line corresponds to real data (up to rank 200), the green line is my best guess of what happens from 200 onwards

For girls, the popularity of rare names was found in a similar way.

------

Knowing the frequency distributions of names, it is time to determine the probability of collisions. I am sure that there is a mathematical solution to this problem, which I would very much like to hear, however I decided to estimate the probabilities by **simulation**. 

The simulation algorithm is very simple. For example, if we want to determine the probability of at least two girls having the same name in a group of 20 girls, we can sample 20 names from the distribution derscibed above, and see if there is a collision or not. If we do this *one million times* we get a pretty good estimate of the probability of collision by dividing the number of groups where there was a collision by one million. For our example, the probability of at least two of 20 girls having the same name turned out to be 77.53% (compare this with the birthday problem, where the probability of collision in a group of 20 is under 50%)

.. figure:: /img/namecollision/p_same_name.png
   :width: 60%
   :align: center

   The probability (y-axis) of at least [two, three, ..., six] girls (dotted) and boys (dashed) having the same name in a group of given size (x-axis)

The above figure shows the probability of name collisions in groups of sizes 2 up to 200. Groups always have people all of the same gender. Dotted lines show probabilities for girls and dashed lines for boys. It looks like in a group of 14, the chances of at least two people having the same name are about 50%, both for girls and for boys. If a group is larger than 50, then it is practically certain that two people will have the same name, moreover ot is very likely that there will be three girls (80%) or three boys (70%) with the same name. As many as six people having the same name is almost certain if the group is large enough (200 people or more). In general, groups of girls are more likely to have collisions (especially more than two with the same name), because the most frequent girl's name, Marija, is more than twice as popular than the most popular boy's name.

Finally, let's look at the probability of collision for a specific name; how likely is a collision of Matjaz compared to a collision of Luka for instance. 

.. figure:: /img/namecollision/pcoll_2_10_50_200.png
   :width: 80%
   :align: center

   Collision probability of a specific name. The x-axis represents the top 100 names ordered by popularity. The y-axis shows the probability of collision for each of these names. Each of the 4 images represents a different group size: 2 (top left), 10 (top right), 50 (bottom left), 200 (bottom right). Blue line for boys, red line for girls.

Two random people having the same name is very unlikely, even for the most popular names (well under 1%). For groups of 50, any of the top 10 names has approx. 10% or more chance of not being unique, and for groups of 200 each of the top 100 names has at least a 10% chance of not being unique, while the top names are almost certainly not unique in the group.

In conclusion, name collisions are surprisingly frequent. Perhaps one could make some money by standing in front of a supermarket and taking bets about the names of people inside.

.. Links

.. _birthday problem: http://en.wikipedia.org/wiki/Birthday_problem
.. _most popular 200 Slovenian names: http://www.stat.si/imena_top_imena_spol.asp?r=True
.. _WolframAlpha: http://www.wolframalpha.com/input/?i=integrate+730*+exp%28-0.006x%29+dx+from+0+to+1800
