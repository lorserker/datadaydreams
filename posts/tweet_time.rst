.. title: Best Time to Tweet
.. slug: best-time-to-tweet
.. date: 2013/09/19 23:00
.. tags: social networks, optimization
.. link: 
.. description: 

Best Time to Tweet
==================

The **halflife of a tweet** is the amount of time it takes until the tweet will receive half of the clicks it is ever going to receive. In other words, how long do people pay attention to something you share. Recently, bitly published a `blogpost`_ in which they show that the typical halflife of a link is about 3 hours.

.. figure:: https://lh3.googleusercontent.com/z2g6vb18_hZvqswW0vQb-ncNpt3HseDXLIeD0Rb8PZfVRwpaaBgk4xGKavP7Pzb3X87OarapvM1rpJdOKgPzJLHXVMSu_T8zfsISBxvwxEgpgrVR3oE
   :width: 60%
   :align: center

   According to bitly, the halflife of a link published on twitter or facebook is about 3 hours, on youtube about 7 hours (the figure is from the bitly blogpost mentioned above)

Clearly, the attention span of  your followers is very short. What can you do to make the most of it?

.. TEASER_END: Click to read the entire article

You could certainly increase your impact on twitter by making your tweets more interesting or increasing the number of your followers. However, you could also do something much simpler than that. Knowing that the attention span for a tweet is very short, it is essential that most of your followers be online and watching when you post that tweet.

I have no idea when my followers are online, reading tweets. What I can easily find out, however, is when my followers tweet themselves. Assuming that the majority of people are likely to also read a few tweets when they post, it looks like I can increase the chances of my tweet being read by posting it when my followers are most likely to be active tweeting.

In order to find out when your followers are most likely to tweet, just open the twitter page of each of your followers in a separate tab (if you think that opening more than five tabs will cause your computer to explode, then take a look at `this`_) and paste this piece of code into the javascript console:

.. code:: javascript

   setInterval(function(){window.scrollBy(0, 500)}, 200)

This will automatically scroll the page, which will cause all tweets to be loaded. When the scrolling is done, and you have all tweets loaded, you just have to save the page and extract the timestamps of the tweets with some simple regular expressions. Like this you will know, for each follower, how many tweets he made, and at what time of day and day of week he tweets most. The data for my top followers looks like this:

.. figure:: /img/tweettime/hourly_tweets.png
   :width: 100%
   :align: center

   The tweeting activity for every follower (rows) in each hour (columns). The greener a cell, the more the user tweets in the corresponding hour. The red bar shows the total number of tweets

.. figure:: /img/tweettime/daily_tweets.png
   :width: 40%
   :align: center

   Tweeting activity for every follower (rows) in each day of the week starting Monday (columns). The greener a cell, the more the user tweets on the corresponding day of the week

In conclusion, most tweeting by my followers is happening in the morning around 10am, and in the evening around 8-9-10pm. Very little is happening in the weekends. The best days seem to be Wednesday and Thursday.

My optimal time to tweet is Wednesdays and Thursdays at 10am, when is yours?


.. Links

.. _blogpost: http://blog.bitly.com/post/9887686919/you-just-shared-a-link-how-long-will-people-pay
.. _this: https://fbcdn-sphotos-f-a.akamaihd.net/hphotos-ak-ash4/484180_304253776367386_1388838177_n.jpg