# GameChain/GameChain Lobby
Sometimes harder than winning a game itself is finding, rounding up, and synchronizing players. Doing it digitally in an open platform is even worse. Let's see if we can't make digital gaming better with a flow something like that.

### Hypothesis
A multiplayer game is a set of shared constraints on possible actions starting from an established set of conditions that a group of participants engages in. For video games, finding other parties who want to accept those constraints has traditionally been a developer-driven set of choices based on their software and servers. With Bitcoin Cash, there is a global, low-cost platform for sharing  transactions and messages that isn't bound to a particular server. Just as a variety of cryptocurrency wallets can find each other and transfer units of currency around the Internet, so can a multitude of game clients find other willing parties and transfer intents to play focusing on a shared protocol and not some matchmaking server somewhere.

These projects test the hypothesis that the Bitcoin Cash (BCH) blockchain and mempool with zero-confirmation transaction times can make a reliable, open, non-proprietary game lobby system for multi-player game participation.

Using the [GameChainLobby protocol](gamechain-lobby-protocol-specs.md), participants signal their intent to start or join a game (or one of its variants) with a public key-based player identity along with enough information to let participants connect/watch. Challengers can accept, and game moves/summaries/outcomes could published on the blockchain, too (see [GameChain protocol](gamechain-protocol-specs.md)).

## Matchmaking: GameChainLobby Protocol

The point of matchmaking is to make sure that all players interested in a game at a particular point in time can agree on the game itself, how the game will start, and protocols and semantics to use for making plays and taking turns. These common concerns are handled in this protocol; each individual game will have to have a protocol and software client for actual use.

This protocol focuses on initiating gameplay: selecting a game, finding other participants, and making sure everyone is clear on the rules/variant you are playing.

Once that's done, gameplay can start based on these parameters. For example, if a server is established as part of initiation, different client applications can connect using their already established (GameChainLobby protocol)(#gamechain-lobby-protocol) identities. Note that a companion protocol for on-chain game play is being developed as [GameChain](#gamechain-protocol). Ultimately, how a game ends being played is ultimately up to the participants.

[See technical details here.](gamechain-protocol-lobby-specs.md)


## Gameplay: GameChain Protocol

### What is a Game?
If you think of a game as a set of initial conditions and a series of validated transformations at the direction of participants (with some mechanism for randomness when appropriate), it becomes possible to abstract the back and forth as a series of messages. Games themselves can consist of two major components: client/user-interface and rules/validation engine. As long as you have the game as a complete specification of rules/protocols, gameplay can occur on-chain or off-chain (but still have moves be signed/authenticated by participants). Additionally, as long as clients can use these protocols, which clients people use become irrelevant to the game/blockchain itself as long as they validate according to the same sets of rules. Finally, games can resolve themselves the way they would off the blockchain - using observable behavior and conventions.

## Gameplay Basics
Typical games involve players taking turns making decisions. In theory, this back and forth can occur in messages encoded as blockchain transactions. With the proper abstraction, most classic games could be codified this way.

The use of this protocol is driven by the Game being played. These are some common interactions which should be common to most (if not all) games. The important things to publish on-chain so that they can be seen/recorded/agreed as fair are: game decisions during a turn, assessed outcomes of those game decisions, turn hand-off between players, resolving the game (win/loss/draw)and being able to disagree about the outcome (e.g. bug in rules, invalid play, flip the table, etc.). 

While games might be able to be encoded as Script, I think it's more important to enable games without requiring a centralized server (which means they could be Script-based... they just don't *have* to be). Admittedly this is hand-waving at this point. Coming up with a game initialization/play mechanism and implementing a range of games to do that in a range of use cases is the point of this project. 

[See technical details here.](gamechain-protocol-specs.md)


## There's a lot to do yet...
There are a lot of details to work out and conventions/protocols to support all types of games would be very important, but I am optimistic. One thing to note is that games that use randomness should be possible, but I'm starting as simply as possible. Many games have more than two players, which should also be possible, but I will start with only two players to keep communication simple while testing this hypothesis.
