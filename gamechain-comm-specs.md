## BCH-based Communication

### Assumptions
This system works by using mempools and 0-conf to notify clients about transaction relevant to protocols used for game discovery and play. We will start with some basic assumptions.
* A Player is denoted by a BCH address. This BCH address is that Player's PlayerId.
* A Player uses their private key to sign/authenticate messages they transmit.
* A Player who wishes to start a game is called the Initiator.
* A Game is defined as: a set of initial conditions, rules that define valid actions players can take, how the game state changes based on those actions, transitions between Player turns, when the game ends, and what the outcome of the game is.
* A Game has a GameLobby identifier BCH address.  
* A Match is an instance of a Game. A concluded Match consists of initial conditions, at least one Player, and a series of Player-induced actions.

### BCH Transaction addressing scheme
 The first occurring P2PKH UTXO in a GameChain/Lobby transaction is the address of the message sender. Addresses of intended recipients are the remainig P2PKH UTXO(s). The OP_RETURN code is the GameChain/Lobby message. If there are multiple OP_RETURN messages in a transaction, the OP_RETURN should be removed from each vout, the byte arrays concatenated, then the entire concatenated message should be processed as a single message.
