.. title: Parrondo's Paradox, or how to win by randomly losing
.. slug: parrondo-paradox-win-by-randomly-losing
.. date: 2014/08/21 00:30
.. tags: gambling, game theory, curiosities
.. link: 
.. description: 


Gambling enjoys a negative reputation, which is perhaps deserved,
because the games gamblers play are designed in such a way that the
chances of winning are just slightly smaller than the chances of losing.
This makes gambilng a losing activity in the long run. In what follows,
I am going to describe two betting games which are both losing, however
a strategy of randomly playing these losing games turns out to be very
interesting indeed.

.. TEASER_END: Click to read the entire article

**Losing Game A**

The first example of a losing game has the probability of winning
*p = 1/2 - e*, where *e* is a very
small number. For illustration, let's assume that we make 1000
successive bets getting $1 if we win, and losing $1 otherwise. The
simulation below runs our game 100 times, and shows that on average we
expect to lose about $15 (for *e = 0.005*).

.. code:: python

    import random
    from __future__ import division
    
    e = 0.005
    
    def game_A(m):
        return m + 1 if random.random() < (1/2 - e) else m - 1
    
    def simulate(n_runs, n_bets, game_fn):
        runs = np.zeros((n_runs, n_bets))
        for i in range(n_runs):
            money = []
            m = 0
            for _ in range(n_bets):
                m = game_fn(m)
                money.append(m)
            runs[i, :] = money
        return np.mean(runs, 0)
    
    avg_money = simulate(100, 1000, game_A)
            
    plot(avg_money);



.. image:: /img/parrondo/output_1_0.png


**Losing Game B**

The rules of the second game are a bit more complicated. If the current
amount of money is a multiple of 3, then the probability to win is
*1/10 - e*, otherwise the probability to win is
*3/4 - e*. The simulation below shows that game B
is also a losing game, as we are expected to lose around $12 after 1000
successive bets.

.. code:: python

    def game_B(m):
        if m % 3 == 0:
            return m + 1 if random.random() < 1/10 - e else m - 1
        else:
            return m + 1 if random.random() < 3/4 - e else m - 1
        
    avg_money = simulate(100, 1000, game_B)
    
    plot(avg_money);


.. image:: /img/parrondo/output_3_0.png


**Randomly switching between games A and B**

Now we have two losing games, A and B. What if we made 1000 bets
randomly switching back and forth between these two games, sure we would
also end up losing, right? Surprisingly no! It looks like if we just
randomly switch between the two losing bets, we actually win! (about $15
after 1000 bets)

.. code:: python

    def game_AB(m):
        if random.random() < 0.5:
            return game_A(m)
        else:
            return game_B(m)
    
    avg_money = simulate(100, 1000, game_AB)
    
    plot(avg_money);


.. image:: /img/parrondo/output_5_0.png


The surprising behavior described above was first discovered by `Juan
Parrondo <http://en.wikipedia.org/wiki/J._M._R._Parrondo>`_ in 1996. I
came accross it this year, while reading the book `Digital
Dice <http://www.amazon.com/Digital-Dice-Computational-Solutions-Probability/dp/0691126984>`_
which I would totally recommend as an interesting read.

An interesting observation is that Parrondo's paradox only works if
*e* is small enough. The figure below plots how much can be
won after 1000 bets for different values of *e*. We notice
that if *e = 0* we make the most money (+$30), while for
values greater than 0.01 this random switching game stops being
profitable. In the case of betting on a color in roulette
*e = 0.0263*, which is not small enough.

.. code:: python

    e_vals = np.linspace(0, 0.05, 100)
    avg_won = []
    for e in e_vals:
        avg_money = simulate(100, 1000, game_AB)
        avg_won.append(avg_money[-1])
.. code:: python

    fig, ax = plt.subplots(1,1)
    ax.plot(e_vals, avg_won);
    ax.set_xlim(0, 0.03);
    ax.set_ylim(-40, 40);
    ax.set_xlabel('values for epsilon');
    ax.set_ylabel('amount won after 1000 bets');
    ax.plot([0, 0.03], [0,0], 'r-');


.. image:: /img/parrondo/output_8_0.png

