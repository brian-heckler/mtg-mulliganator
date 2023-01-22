# mtg-mulliganator
R Shiny app that helps you build Magic the Gathering Commander decks by helping you optimize the probability of a 'playable' opening hand. Also includes a penalty function to account for optimizing solely for the opening hand (ie. more lands/ramp aren't always better!)

**What is Magic the Gathering and the Commander format?**

In the Commander format of the trading card game Magic the Gathering, each player builds a deck containing 100 unique cards, including one creature who acts as their 'commander' (The commander card exists outside of the deck, so the usual deck size is 99).

**What is a Mulligan?**

At the beginning of each game, players draw a 7-card starting hand. Then, they decide whether to keep that hand or perform a 'mulligan'. To mulligan, the hand is shuffled back into the deck and 7 new cards are drawn. Multiple mulligans can happen before a player decides to keep their starting hand and begin the game.

The decision on whether or not to mulligan is complex and depends on a myriad of factors such as turn order, opponent's decks, and the overall metagame. While these factors are better suited for game theory, there is an underlying baseline of what is considered a 'playable' starting hand. And it's in this space that we can apply probability theory and Monte Carlo simulations. 

In Magic, cards are played by paying for their mana cost, which is done by playing other cards that generate mana. Mana-generating cards include land cards (free to play one each turn) and other cards known as 'ramp'. Ramp cards have a mana cost, but help accelerate the amount of mana available on the following turn, allowing for more cards to be played faster.

**Example Game**

To demonstrate the importance of land and ramp cards, let's compare the mana available to three players each with different mulligan strategies. For the simplicity of this example, we'll look only at the first 3 turns of the game and disregard any other cards drawn.

Timmy, Power Gamer: Timmy loves big, expensive spells and chooses to keep a hand with 2 lands, 0 ramp, and 5 costly (but awesome) cards.

Johnny, Combo Player: Johnny is all about executing creative and intricate combos by playing specific cards in order just at the right time. They choose to keep a hand with 3 lands, 1 ramp, and 3 other cards to help get their game-winning combo going.

Spike, Tournament Grinder: Spike is extremely compeititve and has optimized their deck to have the most mana available after turn 3. Spike's hand contains 3 land cards, 3 ramp cards, and 2 other cards perfect for taking advantage of their available mana after turn 3.

With their hands drawn, let's start the game! Timmy rolled a natural 20 to decide turn order and goes first.

Turn Cycle 1:
- Timmy plays a land card (+1 mana).
- Johnny plays a land card (+1 mana).
- Spike plays both a land card _and_ can also play a ramp card (+2 mana).

Turn Cycle 2:
- Timmy plays a land card (+2 mana).
- Johnny plays a land card and can now pay for their ramp card (+3 mana).
- Spike plays both a land card and another ramp card (+4 mana).

Turn Cycle 3:
- Timmy is out of land cards and can't play anything else (+2 mana).
- Johnny plays their final land card but has no more ramp cards left (+4 mana).
- Spike plays their final land card and their final ramp card (+6 mana).

As we can see, Spike will have **three times** as much mana available to them after turn 3 compared to Timmy, and 50% more mana compared to Johnny. If the objective of the game was to have as much mana as possible on turn 4, Spike would be the clear winner. Of course, Magic doesn't work like that, and just because Spike has a ton of mana doesn't necessarily mean they are going to win the game (which could go all the way to turn 15!). 

However, it's generally accepted that in Commander, spending the first few turns accelerating your mana is the best way to improve your overall chances of winning. And the reason why is extremely simple - more mana means more resources to play cards that put you on the path to victory. If each of our three players had started the game with an additional card that read "Pay 10 mana, win the game", Spike would be the undisputed favorite going into turn 4.

**Where does the Mulliganator fit into all of this?**

The Mulliganator is a tool that can help in the deckbuilding process by telling you the probabilities associated with different deck setups. Every player will have a different definition of what they consider a 'playable' starting hand for a particular deck. 

The Mulligantor allows you to set your own parameters for the perfect starting hand by adjusting the amount of land and ramp cards inside the deck, as well as the desired amount of land and ramp cards in your opening hand.

**The optimization problem and the loss function solution**

Sadly, we can't just shove a bunch of land and ramp cards into a deck in order to completely optimize for the starting hand. We still have a game to win! And that requires non-ramp, non-land cards. The rule of thumb (metagame) within the Commander community is that a typical deck should contain between 35-40 land cards and 5-15 ramp cards. This provides a good balance between having a solid opening hand and having cards that will help win you the game (You don't want to be like Timmy stuck on 2 mana on Turn 4).

To address this, the Mulliganator is equipped with a loss function. A loss function helps avoid the optimization problem by applying a small penalty (or loss) to each hand for every additional land or ramp card added to the deck.

Try toggling the penalty function on and off to see how it affects the probabilities!

**Trying out the Mulligantor**

Below is an extreme example to demonstrate the power of the loss function:

Let's say we like starting hands that have any amount of land and ramp cards. So 7 lands + 0 ramp and 0 lands + 7 ramp are both gravy starting hands to us (as well as any combination in-between).

We build a deck containing 50 lands and 20 ramp cards. Let's look at our opening hand probabilities, disallowing mulligans:

![image](https://user-images.githubusercontent.com/123254289/213925470-196f450a-ef98-4214-935a-277984a636f8.png)
[Caption: quick note, because of R shiny RAM limitations we can only run up to 2,000 simulations. So probabilities can change between trials. Running more trials will make the probabilities much more stable (+/- 1%).

Hooray! Every single hand we draw is going to match our criteria! This deck must be perfect for us, right? 

Before we celebrate and start ordering a cart full of full-art, foil basic lands, let's apply the loss function and see what happens:

![image](https://user-images.githubusercontent.com/123254289/213925525-dd6ac30e-10f8-4d65-87a7-b69a8cb77eb6.png)

Now our perfect deck doesn't look so perfect. Since we weren't picky with our opening hand and filled our deck with land + ramp cards, our opening hands received nearly a **40% penalty**.

To drive home the importance of the loss function, let's consider another setup:

Desired hand and deck setup
- between 3 and 5 lands
- between 0 and 2 ramp
- 38 lands in deck
- 14 ramp in deck

This time let's add another condition: the total number of land + ramp cards in the starting hand. With the settings we have now, we could get hands with 3 lands + 0 ramp or 5 + 2 ramp, neither of which feel great. So we also add this condition:
- between 4 and 6 _total_ land + ramp cards

Great! Now we can guarantee we will have at least 4 sources of mana coming from lands/ramp but still leave room for a non-mana source card in the most extreme situation.

Let's take a look at these probabilities. First, without the loss function:

![image](https://user-images.githubusercontent.com/123254289/213926124-27feafc1-50af-4862-8f9d-615c6f311348.png)

About 40%. Clearly not as amazing as our 100% in the first example, but now our playable hands will actually allow us to start playing the non-mana cards that can lead us to victory. 

And now with the loss function:

![image](https://user-images.githubusercontent.com/123254289/213926257-44ed3210-451c-47ef-a1e8-d7aceb958f2b.png)

So about a 10% penalty. While not ideal, it's only a 25% relative decrease ((40-30)/20) compared to our first example of 40% ((100-40)/100). 

One thing to keep in mind is that there will _always_ be a penalty applied, no matter how perfect the setup is. What matters is how _substantial_ that penalty is. It also depends on your personal interpretation of how important getting a specific starting hand is vs. having more non-mana cards later in the game. If your commander was that 10-mana instant win card mentioned earlier you may love getting a starting hand of all land and ramp cards!

**Conclusion**

I encourage you to play around with the Mulligantor and to challenge what you currently consider 'playable' starting hands in Commander. You may be surprised that your favorite kind of starting hand requires a much different deck setup than you're currently using. Or you may not realize that by optimizing for good starting hands you've put yourself at a disadvantage for winning the game in the long run.

Finally, this tool is not meant to be used a hard rule for determining how to build a particular deck. Magic (especially the multiplayer Commander format) is incalculably complex, and no single piece of software can tell you the 'best' way to build a deck and craft your opening hands. That's what makes it such an enjoyable, challenging, and interesting game!
