This project is my attempt at building a true **optimal strategy solver** for a custom poker variant I call **Ultimate Dealer Poker**. The whole thing runs on a Q-learning engine that teaches itself how to play purely from experience. There’s no built-in strategy, no shortcuts — it just plays millions of hands against a dealer and slowly figures out what works.

### Game Mechanism
Ultimate Dealer Poker is as simple as it gets: it’s just you versus the dealer. On every street — preflop, flop, turn, river — the player has only two choices:

- **Check (0)**  
- **Raise (4)**, where the raise amount gets **smaller the later you do it**  
  - Biggest raise preflop  
  - Smaller on the flop  
  - Smaller again on the turn  
  - Smallest on the river  

Because of this shrinking raise size, early aggression matters a lot. Good hands want to get money in right away, while weaker or borderline hands often perform better by checking and keeping the pot controlled.

### State & Hand Representation
Every state is based on your hole cards plus whatever community cards are out. I built a small labeling helper that turns messy states into clean, human-readable tags like `"AKs"`, `"KQo"`, `"T9s"`, `"JJ"`, or even `"72o"`. This makes comparing hands and reading charts way easier.

### Q-learning description
For each possible `(state, action)` pair, the solver stores a Q-value — basically an estimate of how profitable that decision is in the long run. After each hand finishes and the dealer’s hand is revealed, the model updates those values using:

- temporal-difference learning  
- reward back-propagation  
- a bit of randomness so it keeps exploring  
- and long-run averaging until it stabilizes  

After enough training, the system stops bouncing around and settles into something that looks a lot like an optimal strategy for this ruleset.

### Inference of strategy
Once the learning is done, the notebook includes a bunch of tools that turn raw Q-values into something humans can understand:

- EV of checking vs raising for every hand  
- Which option has the higher long-term return  
- “Raise advantage” rankings  
- Automatic classification into **Raise**, **Check/Call**, or **Neutral**

There are also visual outputs:

- a **13×13 heatmap** for the entire preflop range  
- a histogram of raise advantages  
- top 20 value-raise hands  
- the 20 most borderline hands  
- and a simple `explain_hand("AKs")` function that prints a readable analysis

### Description of results
Because raising shrinks each street, the model naturally gravitates toward:

- hammering strong hands **early**  
- slowing down on later streets where raising isn’t worth much  
- sorting hands into clear tiers (premium, playable, garbage)  
- and forming a surprisingly clean preflop strategy chart

It ends up looking a lot like a stripped-down GTO chart, but shaped around the specific economics of this game.

### Why I Built This
The goal was to see if a self-taught RL agent could find a solid, theoretically sound strategy for a simplified poker game with fixed rules and limited actions.  
The end result is a solver that produces interpretable charts, consistent hand rankings, and a complete strategic picture of how to beat the dealer in this format — all learned from scratch.

