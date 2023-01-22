# mtg-mulliganator
R Shiny app that helps you build Magic the Gathering Commander decks by helping you optimize the probability of a 'playable' opening hand. Also includes a penalty function to account for optimizing solely for the opening hand (ie. more lands/ramp != always better)

**What is Magic the Gathering and the 'Commander' format?**

In the Commander format of the trading card game Magic the Gathering, each player builds a deck containing 100 unique cards including one creature who acts as their 'commander' (The commander card exists outside of the deck, so the usual deck size is 99).

**What is a Mulligan?**

At the beginning of each game, players draw a 7 card starting hand. They then decide whether to keep that hand or perform a 'mulligan'. To mulligan, your hand is shuffled back into your deck and 7 new cards are drawn. Multiple mulligans can happen before a player decides to keep their starting hand and begin the game.

The decision on whether or not to mulligan is complex and depends on a myriad of factors such as turn order, the decks opponents are playing, and what the overall metagame is. While these factors are better suited for game theory, there is an underlying baseline of what is considered a 'playable' starting hand. 

In Magic, cards are played by paying for their mana cost, which is done by playing other cards that generate mana. Mana-generating cards include land cards (free to play one each turn) and other cards known as 'ramp'. Ramp cards help accelerate the amount of mana available each turn, allowing for more cards to be played faster.

To demonstrate the importance of land and ramp cards, lets compare the mana available to 3 players each with different mulligan strategies. 

Timmy: 
