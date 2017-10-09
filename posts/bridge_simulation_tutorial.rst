.. title: A Simulation Tutorial for Better Decisionmaking at Bridge
.. slug: a-simulation-tutorial-for-better-decisionmaking-at-bridge
.. date: 2015/09/21 00:30
.. tags: bridge, simulation, tutorial
.. link:
.. description:


This article describes how computers can be used to help with the analysis of decisions made at the game of bridge. Probably many readers will be familiar with the fact that Monte Carlo simulation is a technique widely used by good quality bridge playing software, and even some top-class human players do something similar in their head at the table. What I would like to show is that such kind of analysis is not too hard to do, and should be accessible to "mere-mortals" who are neither bridge professionals nor PhDs in artificial intelligence. The concepts will be introduced by example, and are all related to a deal that I recently played and found interesting. We shall look at decisions that arise in bidding, cardplay and at the opening lead.

**The Bidding**

The story starts with a bidding problem. Holding South's hand in the diagram, what should we bid after partner's preempt of 3♣?

.. raw:: html

    
            <iframe
                width="160"
                height="250"
                src="http://www.bridgebase.com/tools/handviewer.html?s=saj874ha986daca53&d=n&a=3cp?"
                frameborder="0"
                allowfullscreen
            ></iframe>


The optimists will no doubt like the nice fit with four aces and bid 6♣. Others, more cautious, will note that partner's first-hand non-vulnerable preempt could be quite wild with just QJ to six in clubs and not much else; so they might be tempted to make do with a game and bid 5♣. Another conservative oldfashioned bidder might argue that partner promises six tricks for his preempt and we can count ten sure tricks, therefore 3NT is laydown and 5♣ might go down on occasion, so why not bid the safest game of 3NT?

I think that none of the above options are wrong, but some bids are certainly more likely to score better than others. Our approach to selecting the best bid is by simulation. We shall sample a large number of random deals such that South holds the cards shown and North's hand is consistent with his 3♣ opening. Then we'll do double dummy analysis on every sampled deal and compare the outcomes of the contracts we are considering.

In this particular case we are deciding between the following bids: PASS, 3NT, 5♣, 6♣ and 6NT, each of which sets the final contract. The definition of North's 3♣ will be the more loose (and modern) variant promising just QJxxxx, and we'll assume IMP scoring with none vulnerable.

The code below samples random deals in a loop (the so-called "in the long run"), accepts the ones satisfying the constraints (as defined in the accept function), computes the double dummy score of each competing contract, and saves the results in a payoff table.

.. code:: python

    from redeal import *
    from redeal import dds
    from redeal.global_defs import Seat, Suit, Card, Rank, Strain

    Deal.set_str_style('short')
    Hand.set_str_style('short')

    vuln = False
    predeal = {Seat['S']: H('AJ874 A986 A A53')}
    dealer = Deal.prepare(predeal)

    def accept(deal):
        if deal.north.hcp > 10:
            return False
        if len(deal.north.clubs) not in [6, 7]:
            return False
        if deal.north.clubs.hcp < 3:
            return False
        return True

    imps_payoff = Payoff(('pass', '3NT', '5C', '6C', '6NT'), imps)

    found = 0
    n = 1000
    for _ in range(1000 * n):
        if found > n:
            break
        deal = dealer()
        if not accept(deal):
            continue
        found += 1
        score_3c = deal.dd_score('3CN', vuln)
        score_3n = deal.dd_score('3NS', vuln)
        score_5c = deal.dd_score('5CN', vuln)
        score_6c = deal.dd_score('6CN', vuln)
        score_6n = deal.dd_score('6NS', vuln)
        data = {
            'pass': score_3c,
            '3NT': score_3n,
            '5C': score_5c,
            '6C': score_6c,
            '6NT': score_6n,
        }
        imps_payoff.add_data(data)

We can summarize the results of the simulation by a pairwise table comparing the expected number of imps scored in two competing contracts, on average in the long run. We can see for example that bidding 3NT is better than passing 3♣ because, on average, we'll win almost four more imps if we bid 3NT. Bidding 5♣ is even better as it improves over 3NT by at least one imp per board. Best is 6♣, which improves on the 5♣ contract by at least three imps. Although 6♣ may seem risky and fail on many occasions, the safer contract of 5♣ can be considered even more risky in that it loses an opportunity of more than 3 imps per board, on average.

.. code:: python

    imps_payoff.report()
    '''
         PASS    3NT     5C      6C      6NT            
    PASS         -3.62   -5.03   -5.85   +4.33
                 (0.17)  (0.10)  (0.28)  (0.23)
    3NT  +3.62           -1.29   -4.32   +6.01
         (0.17)          (0.14)  (0.27)  (0.25)
    5C   +5.03   +1.29           -3.46   +7.15
         (0.10)  (0.14)          (0.30)  (0.25)
    6C   +5.85   +4.32   +3.46           +7.46
         (0.28)  (0.27)  (0.30)          (0.20)
    6NT  -4.33   -6.01   -7.15   -7.46   
         (0.23)  (0.25)  (0.25)  (0.20)
    '''  

A few examples of possible North's hands which would allow 6♣ to make:

♠K6 ♡72 ♢Q86 ♣QJ9862

♠65 ♡4 ♢Q85 ♣KQT9874

♠3 ♡JT4 ♢J95 ♣KQJ962

and a few examples where the slam would fail:

♠932 ♡T5 ♢9 ♣KJT9872

♠K65 ♡72 ♢74 ♣KQ9764

♠5 ♡J52 ♢K62 ♣QJ9876

At the table I chose to bid 6♣, but I was not confident in my decision. Me selecting the winning bid was due to optimism, not sampling and double dummy analysis done in my head :)

Anyway, after East's lead of the ♥K, partner got some problems of his own to solve.

.. raw:: html

    
            <iframe
                width="350"
                height="350"
                src="http://www.bridgebase.com/tools/handviewer.html?n=st6hj4dj2ckqj9876&s=saj874ha986daca53&d=n&p=hkhah2h4c3ctckc2cqc4c5d3hjhqh6h3d4dad8d2h9h5&a=3cp6cppp"
                frameborder="0"
                allowfullscreen
            ></iframe>

**The Play**

Our prospects are not too good as the lead established a heart trick for the defense, and we seem to have to lose a spade trick as well. If we are to make this slam, then something has to happen in the hearts. The hope is that after losing the heart jack to the queen we can establish a heart, either by ruffing the third round and dropping the ten, or by doing a ruffing finesse against the ten in West. I invite you to step through the first few tricks by pressing the "Next " button at the bottom of the diagram. We take the lead (West follows small), we draw trumps in two runds with the K and Q (West discards a diamond on the second), and we lose the heart jack to East's queen (West following small). Having won the ♥Q, East exits with a diamond to dummy's ace (East follows suit).

Now the crucial moment. We play the ♥9 from dummy. East plays low, and we have to decide: do we ruff and hope to see East's 10, or do we discard a spade hoping that the 10 is in West?

It is probably realistic to expect declarer to figure out the correct answer in his head at the table, but we have a Monte Carlo simulator at our disposal, so we can afford to rest our brains and avoid overheating. The first thing in the code snippet below is the predeal object, which places every card whose location we already know at the moment of our decision. The main part is the for-loop which samples random hands for E-W and counts how many times the ruffing finesse line works and how many times the ruff-the-ten-out line works.

.. code:: python

    predeal = {
        Seat['S']: H('AJ874 A986 A A53'),
        Seat['N']: H('T6 J4 J2 KQJ9876'),
        Seat['E']: H('- KQ 4 42'),
        Seat['W']: H('- 532 83 T'),
    }
    dealer = Deal.prepare(predeal)

    winning_line = dict(finesse=0, ruffout=0)

    n = 1000
    for _ in range(n):
        deal = dealer()
        if Rank['T'] in deal.west.hearts:
            winning_line['finesse'] += 1
        if len(deal.east.hearts) == 3 and Rank['T'] in deal.east.hearts:
                winning_line['ruffout'] += 1

After looking at 1000 random layouts, we find that the ruffing finesse will work in 47% of the cases, and in 25% of the cases the ♥10 will fall from East. In the remaining 28% of the cases none of the lines will work because East will have a guarded ten. Good. The full deal was:

.. raw:: html

    
            <iframe
                width="350"
                height="350"
                src="http://www.bridgebase.com/tools/handviewer.html?n=st6hj4dj2ckqj9876&s=saj874ha986daca53&w=skq53h7532dqt83ct&d=n"
                frameborder="0"
                allowfullscreen
            ></iframe>

One down. Good bridge.

After the hand East might feel bad about his lead since it could have potentially helped declarer make his contract. (actually I'm making this up, nobody ever felt bad about their lead after defeating a slam, but for the sake of argument let's assume that East wants to know whether his lead was optimal)

**The Opening Lead**

Once again we can use simulation and double dummy analysis to find the best lead.

.. code:: python

    predeal = {Seat['E']: H('92 KQT K97654 42')}
    dealer = Deal.prepare(predeal)
    accept_north = accept

    contract = Contract.from_str('6CN')

    lead_payoff = Payoff(
        sorted(dds.valid_cards(dealer(), 'C', 'E'), reverse=True), 
        lambda ti, tj: imps(contract.score(ti), contract.score(tj))
    )

    found = 0
    n = 1000
    for _ in range(1000*n):
        if found > n:
            continue
        deal = dealer()
        if not accept_north(deal):
            continue
        if deal.south.hcp < 16:
            continue
        has_ace = lambda hand: Rank['A'] in hand
        n_aces_west = sum(map(int, map(has_ace, 
            [deal.west.spades, deal.west.hearts, deal.west.diamonds, deal.west.clubs]
        )))
        if n_aces_west > 1:
            continue
        found += 1
        lead_payoff.add_data(deal.dd_all_tricks('C', 'E'))

And yes, the lead of ♥K was the best possible.

.. code:: python

    lead_payoff.report()
    '''
        ♠9      ♠2      ♡K      ♡T      ♢K      ♢9      ♢7      ♣4      ♣2  
    ♠9          +0.00   -0.35   +0.31   +0.33   +0.01   +0.01   +0.17   +0.17
                (0.00)  (0.04)  (0.05)  (0.04)  (0.04)  (0.04)  (0.02)  (0.02)
    ♠2  -0.00           -0.35   +0.31   +0.33   +0.01   +0.00   +0.17   +0.17
        (0.00)          (0.04)  (0.05)  (0.04)  (0.04)  (0.04)  (0.02)  (0.02)
    ♡K  +0.35   +0.35           +0.67   +0.66   +0.35   +0.35   +0.52   +0.51
        (0.04)  (0.04)          (0.03)  (0.04)  (0.04)  (0.04)  (0.03)  (0.03)
    ♡T  -0.31   -0.31   -0.67           +0.01   -0.30   -0.30   -0.16   -0.16
        (0.05)  (0.05)  (0.03)          (0.05)  (0.05)  (0.05)  (0.04)  (0.04)
    ♢K  -0.33   -0.33   -0.66   -0.01           -0.33   -0.33   -0.17   -0.17
        (0.04)  (0.04)  (0.04)  (0.05)          (0.03)  (0.03)  (0.04)  (0.04)
    ♢9  -0.01   -0.01   -0.35   +0.30   +0.33           -0.00   +0.15   +0.15
        (0.04)  (0.04)  (0.04)  (0.05)  (0.03)          (0.00)  (0.03)  (0.03)
    ♢7  -0.01   -0.00   -0.35   +0.30   +0.33   +0.00           +0.16   +0.16
        (0.04)  (0.04)  (0.04)  (0.05)  (0.03)  (0.00)          (0.03)  (0.03)
    ♣4  -0.17   -0.17   -0.52   +0.16   +0.17   -0.15   -0.16           -0.00
        (0.02)  (0.02)  (0.03)  (0.04)  (0.04)  (0.03)  (0.03)          (0.00)
    ♣2  -0.17   -0.17   -0.51   +0.16   +0.17   -0.15   -0.16   +0.00   
        (0.02)  (0.02)  (0.03)  (0.04)  (0.04)  (0.03)  (0.03)  (0.00)
    '''  

