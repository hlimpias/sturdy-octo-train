# Cardcast Integration

Add your decks over at [Cardcast](http://www.cardcastgame.com/). It will give you a 5 character code for your deck. You'll need this to add your deck to your game.

## TL;DR

http://www.cardcastgame.com/

http://www.cardcastgame.com/browse

`5CODE` is the "Play Code" on the deck's page, or at the bottom right of its overview card.

`/addcardcast 5CODE`

`/removecardcast 5CODE`

`/listcardcast`

## Temporary hacky way of adding decks to the game.

Right now, the only way to add or remove Cardcast decks is via chat commands. The client UI needs a fairly significant overhaul to properly support these externally loaded decks.

To add a Cardcast deck to a game, the game host must type `/addcardcast 5CODE`, where `5CODE` is the 5 character code for the deck. A notice is broadcast to every player in the game.

To remove a Cardcast deck from a game, the game host must type `/removecardcast 5CODE`. A notice is broadcast to every player in the game.

To see which Cardcast decks are in use for a game, anyone may type `/listcardcast`.

The Cardcast decks currently in use for a game is displayed in the game's chat when a player joins the game.

There is currently no way to see which, if any, Cardcast decks a game is using from the game list.

## Potential errors

If Cardcast is down for some reason, and the decks are no longer cached by the PYX server, you may see some unusual errors. The most significant one would be "not enough players" when trying to start a game which has enough players. There is currently no easy way to change that error message. Another possibility is that listing the card sets in use in a game may completely fail if one of the card sets is deleted. The only way out of this is to manually figure out which one is broken, or make a new game.

### My deck isn't being found/is out of date.

I cache negative responses (i.e. "That isn't a valid deck") for 30 seconds, so if you just added it, wait a couple minutes and try again.

I cache positive responses (i.e. "Here's the deck you requested") for 15 minutes. If you (or anybody else) recently used a deck, any changes to that deck might not show up for about 15 minutes.

## I have to register?

I don't run Cardcast. They require registering to build decks. Whatever information you provide them stays with them, with the exception of the username you provide (which is listed on their site). I currently do not store these usernames anywhere. Of course the card text you provide is retrieved and distributed to players in the games that use your deck(s).