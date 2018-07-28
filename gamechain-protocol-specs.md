# GameChain Protocol

### Message Format
For basic assumptions and messaging layer details, see [the GC Comm spec](gamechain-comm-specs.md).

### Message Format

Gamechain messages are preceded by a version byte and a message code byte. During development, the version byte will be set to 1. Message codes are as follows.

| Message Code<br/>Hex Value | Name    | Description |
|:--------------------------:| --------| ----------- | 
| 0x01                       | **STT** | Setting the Table. Initiator broadcasts the game's starting conditions. This includes the player addresses, public keys, inital player order, and game-specific information. |
| 0x02                       | **STC** | Setting the Table - Continuation. Initiator continues broadcasting the game's starting conditions from a previous STT or STC message. |
| 0x03                       | **TMT** | Taking My Turn. Player indicates their move using the game-specific protocol. |
| 0x04                       | **WIN** | I Win. Player claims they win with the move executed using the game-specific protocol. |
| 0x05                       | **LUZ** | I Lose. Player loses. There may be a move they take that is the last move they will execute. |
| 0x06                       | **DRW** | We Draw. Player broadcasts that the game is ending in a tie using the move executed using the game-specific protocol. |
| 0x07                       | **CCD** | I Concede. Player accepts losing and indicates they will be taking no more turns. |
| 0x08                       | **ACC** | I Accept Outcome. Player indicates they accept the proffered outcome. They may express a sentiment. |
| 0x09                       | **DIS** | I Dispute Outcome. Player indicates they do accept the proffered outcome. They may express a sentiment and indicate the basis for their dispute. |
| 0x0A                       | **PTD** | Propose to Draw. Player broadcasts that they would like to end the game in a tie. |
| 0x0B                       | **ATD** | Agree to Draw. Player responds that they are willing to end the game in a tie. |
| 0x0C                       | **DTD** | Disagree to Draw. Player responds that they are not willing to end the game in a tie. |


### Message/Action: Setting the Table
 The transaction (which may consist of multiple OP_RETURN scripts) contain enough information for a game client to display the game and enable play options as appropriate. The following information is in the OP_RETURN:
 
| Byte Count | Name    | Format/Values | Description |
|:----------:| --------| ----------- | ---- | 
| 1          | Protocol version | Version byte | See protocol description. |
| 1          | **STT** | 0x01 | _Setting the Table_ message code. See above. |
| 33         | Initiator's public key | <public_key> | Key used by the initiator to sign messages. |
| 32         | Initiator's game ID | <game_id_str> | String created by the Initiator to uniquely identify the game. Pad to 32 bytes as necessary. This might be the transaction ID containing the initiator's original game ID using GCL/LFG.|
| 1          | Signed Tx ID data size | Size byte | Number of bytes to read for signed transaction ID data |
| 2          | Message data size | Size bytes | Number of bytes to read for message data. Includes subsequent OP_RETURN messages in the same transaction excluding the OP_RETURN byte. |
| <signed_tx_id_size> | Signed Tx ID | Byte array used to authenticate message from transmitters | Signed version of the transaction ID signaling the game will be played. Prevents spoofing of this message.| 
| <message_data_size> | Game start conditions | Complete information about how game will start and be played | Depends on game. Information that should be included regardless of game is: player order, player public keys, player addresses | 


 ### Message/Action: Taking My Turn
 Taking My Turn messages contain enough information for a game client to display the actions of the player and determine which player's turn is next. The following information is in the OP_RETURN:
 
| Byte Count | Name    | Format/Values | Description |
|:----------:| --------| ----------- | ---- | 
| 1          | Protocol version | Version byte | See protocol description. |
| 1          | **TMT** | 0x03 | _Taking My Turn_ message code. See above. |
| 32         | Previous Tx ID | <transaction_id> | Transaction ID of the preceding TMT message (or STT/STC message if it's the first turn). |
| 2          | Message data size | Size byte| Number of bytes to read for message data  |
| 1          | Signed Previous Tx ID data size | Size byte | Number of bytes to read for signed transaction ID data |
| <message_data_size> | Game turn operations | Complete information about the player's turn| Depends on game. | 
| <signed_tx_id> | Signed Previous Tx ID | Byte array used to authenticate message from transmitters | Signed version of the previous TMT/STT/STC transaction ID. Prevents spoofing of this message.| 


### Message/Action: I Win
I Win messages contain enough information for a game client to convince other players the sending player has won using a game-specific protocol. The following information is in the OP_RETURN:
 
| Byte Count | Name    | Format/Values | Description |
|:----------:| --------| ----------- | ---- | 
| 1          | Protocol version | Version byte | See protocol description. |
| 1          | **WIN** | 0x04 | _I Win_ message code. See above. |
| 32         | Previous Tx ID | <transaction_id> | Transaction ID of the preceding TMT message. |
| 2          | Message data size | Size byte| Number of bytes to read for message data  |
| 1          | Signed Previous Tx ID data size | Size byte | Number of bytes to read for signed transaction ID data |
| <message_data_size> | Winning move | Complete information about the player's turn that results in a win| Depends on game. | 
| <signed_tx_id> | Signed Previous Tx ID | Byte array used to authenticate message from transmitters | Signed version of the previous TMT transaction ID. Prevents spoofing of this message.| 


### Message/Action: I Lose
I Lose messages can contain last game-turn information using a game-specific protocol. The following information is in the OP_RETURN:
 
| Byte Count | Name    | Format/Values | Description |
|:----------:| --------| ----------- | ---- | 
| 1          | Protocol version | Version byte | See protocol description. |
| 1          | **LUZ** | 0x05 | _I Lose_ message code. See above. |
| 32         | Previous Tx ID | <transaction_id> | Transaction ID of the preceding TMT message. |
| 1          | Message data size | Size byte| Number of bytes to read for message data  |
| 1          | Signed Previous Tx ID data size | Size byte | Number of bytes to read for signed transaction ID data |
| <message_data_size> | Losing move | Optional. Complete information about the player's turn that results in a loss | Depends on game. | 
| <signed_tx_id> | Signed Previous Tx ID | Byte array used to authenticate message from transmitters | Signed version of the previous TMT transaction ID. Prevents spoofing of this message.| 


### Message/Action: We Draw
We Draw messages contain last game-turn information using a game-specific protocol and indicate the game ends in a tie. The following information is in the OP_RETURN:
 
| Byte Count | Name    | Format/Values | Description |
|:----------:| --------| ----------- | ---- | 
| 1          | Protocol version | Version byte | See protocol description. |
| 1          | **DRW** | 0x06 | _We Draw_ message code. See above. |
| 32         | Previous Tx ID | <transaction_id> | Transaction ID of the preceding TMT message. |
| 2          | Message data size | Size byte| Number of bytes to read for message data  |
| 1          | Signed Previous Tx ID data size | Size byte | Number of bytes to read for signed transaction ID data |
| <message_data_size> | Ending move | Complete information about the player's turn that results in a draw | Depends on game. | 
| <signed_tx_id> | Signed Previous Tx ID | Byte array used to authenticate message from transmitters | Signed version of the previous TMT transaction ID. Prevents spoofing of this message.| 


### Message/Action: I Concede
I Concede messages indicate sender accepts losing and will be taking no more turns. The following information is in the OP_RETURN:
 
| Byte Count | Name    | Format/Values | Description |
|:----------:| --------| ----------- | ---- | 
| 1          | Protocol version | Version byte | See protocol description. |
| 1          | **CCD** | 0x07 | _I Concede_ message code. See above. |
| 32         | Prevous Tx ID | <transaction_id> | Transaction ID of the preceding TMT message. |
| 1          | Message data size | Size byte| Number of bytes to read for message data  |
| 1          | Signed Tx ID data size | Size byte | Number of bytes to read for signed transaction ID data |
| <message_data_size> | Game concession details| Final message | Any color sender wishes to provide on the concession | 
| <signed_tx_id> | Signed Tx ID | Byte array used to authenticate message from transmitters | Signed version of the previous TMT transaction ID. Prevents spoofing of this message.| 


### Message/Action: I Accept Outcome
I Accept Outcome messages indicate sender accepts proferred outcome. They may express a sentiment. The following information is in the OP_RETURN:
 
| Byte Count | Name    | Format/Values | Description |
|:----------:| --------| ----------- | ---- | 
| 1          | Protocol version | Version byte | See protocol description. |
| 1          | **ACC** | 0x08 | _I Accept_ message code. See above. |
| 32         | Prevous Tx ID | <transaction_id> | Transaction ID of the preceding TMT message. |
| 1          | Message data size | Size byte| Number of bytes to read for message data  |
| 1          | Signed Tx ID data size | Size byte | Number of bytes to read for signed transaction ID data |
| <message_data_size> | Game outcome details| Final message | Any color sender wishes to provide on the outcome | 
| <signed_tx_id> | Signed Tx ID | Byte array used to authenticate message from transmitters | Signed version of the previous WIN/LUZ/DRW/CCD transaction ID. Prevents spoofing of this message.| 


### Message/Action: I Dispute Outcome
I Dispute Outcome messages indicate sender disagrees with proferred outcome. They may express a sentiment. The following information is in the OP_RETURN:
 
| Byte Count | Name    | Format/Values | Description |
|:----------:| --------| ----------- | ---- | 
| 1          | Protocol version | Version byte | See protocol description. |
| 1          | **DIS** | 0x09 | _I Accept_ message code. See above. |
| 32         | Prevous Tx ID | <transaction_id> | Transaction ID of the preceding TMT message. |
| 1          | Message data size | Size byte| Number of bytes to read for message data  |
| 1          | Signed Tx ID data size | Size byte | Number of bytes to read for signed transaction ID data |
| <message_data_size> | Game dispute details| Final message | Any color sender wishes to provide on the dispute | 
| <signed_tx_id> | Signed Tx ID | Byte array used to authenticate message from transmitters | Signed version of the previous WIN/LUZ/DRW/CCD transaction ID. Prevents spoofing of this message.| 

 
 ### Message/Action: Propose to Draw
Propose to Draw messages indicate sender would like to end the game in a draw. They may express a sentiment. The following information is in the OP_RETURN:
 
| Byte Count | Name    | Format/Values | Description |
|:----------:| --------| ----------- | ---- | 
| 1          | Protocol version | Version byte | See protocol description. |
| 1          | **PTD** | 0x0A | _Propose to Draw_ message code. See above. |
| 32         | Prevous Tx ID | <transaction_id> | Transaction ID of the preceding TMT message. |
| 1          | Message data size | Size byte| Number of bytes to read for message data  |
| 1          | Signed Tx ID data size | Size byte | Number of bytes to read for signed transaction ID data |
| <message_data_size> | Propose to Draw message | Freeform | Any color sender wishes to provide on the draw proposal| 
| <signed_tx_id> | Signed Tx ID | Byte array used to authenticate message from transmitters | Signed version of the previous TMT transaction ID. Prevents spoofing of this message.| 
 
 ### Message/Action: Agree to Draw
Agree to Draw messages indicate sender accepts ending game in a draw. They may express a sentiment. The following information is in the OP_RETURN:
 
| Byte Count | Name    | Format/Values | Description |
|:----------:| --------| ----------- | ---- | 
| 1          | Protocol version | Version byte | See protocol description. |
| 1          | **ATD** | 0x0B | _Agree to Draw_ message code. See above. |
| 32         | Prevous Tx ID | <transaction_id> | Transaction ID of the preceding PTD message. |
| 1          | Message data size | Size byte| Number of bytes to read for message data  |
| 1          | Signed Tx ID data size | Size byte | Number of bytes to read for signed transaction ID data |
| <message_data_size> | Accept to Draw message | Freeform | Any color sender wishes to provide on the draw acceptance | 
| <signed_tx_id> | Signed Tx ID | Byte array used to authenticate message from transmitters | Signed version of the previous PTD transaction ID. Prevents spoofing of this message.| 
 
 ### Message/Action: Disagree to Draw
 Propose to Draw messages indicate sender would like to end the game in a draw. They may express a sentiment. The following information is in the OP_RETURN:
 
| Byte Count | Name    | Format/Values | Description |
|:----------:| --------| ----------- | ---- | 
| 1          | Protocol version | Version byte | See protocol description. |
| 1          | **DTD** | 0x0C | _Disagree to Draw_ message code. See above. |
| 32         | Prevous Tx ID | <transaction_id> | Transaction ID of the preceding PTD message. |
| 1          | Message data size | Size byte| Number of bytes to read for message data  |
| 1          | Signed Tx ID data size | Size byte | Number of bytes to read for signed transaction ID data |
| <message_data_size> | Disagree to Draw message | Freeform | Any color sender wishes to provide on the draw rejection | 
| <signed_tx_id> | Signed Tx ID | Byte array used to authenticate message from transmitters | Signed version of the previous PTD transaction ID. Prevents spoofing of this message.| 
