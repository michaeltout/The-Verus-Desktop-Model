# The Verus Desktop Model (native mode)
To get started developing with Verus it helps to understand the APIs that Verus is based on. Because all of these are accessible and fully functional to a blockchain full node, communicating to a local node is the best way to get acquainted with the Verus interface (even if your applications will host users in "light" modes). 

This will show you how the Verus Desktop wallet (when running Verus in native mode) communicates with the chain APIs. There are three separate components to the Verus Desktop Wallet that run in parallel during operation:

 1. The Verus Daemon
 2. The NodeJS backend
 3. The React front-end

## The JS Electron Model

Numbers 2 and 3 are part of the electron/webpack JavaScript application. The NodeJS manages most operating system functions (window creation etc.) and creates a localhost endpoint that the React front-end can query for information. This is a common webpack/electron/react model.

## Starting the Verus Node

The Verus Daemon is the C++ full node that is started locally once a user chooses to add Verus (VRSC) in native mode within their wallet. When this happens, the React front end sends an API request to the NodeJS backend, which ensures the user has the correct local filestructure and prerequisites ([here](https://github.com/VerusCoin/Verus-Desktop/blob/060db2587a22e1de81c593309c1b5fa750f523c5/routes/api/daemonControl.js#L437)) and if so, starts the Verus daemon as a child process ([here](https://github.com/VerusCoin/Verus-Desktop/blob/060db2587a22e1de81c593309c1b5fa750f523c5/routes/api/daemonControl.js#L385)).

## Communicating to a Running Verus Node

Once the daemon is started as a child process, an API is opened. The full list of API commands can be found [here](https://wiki.verus.io/#!faq-cli/clifaq-02_verus_commands.md), where each command has an associated curl command that should show how to access it. Within the Verus Desktop NodeJS backend, axios is used to send commands over rpc to the daemon (through [this sendToCli](https://github.com/VerusCoin/Verus-Desktop/blob/060db2587a22e1de81c593309c1b5fa750f523c5/routes/api/rpc.js#L55) function, which is wrapped in [this callDaemon](https://github.com/VerusCoin/Verus-Desktop/blob/060db2587a22e1de81c593309c1b5fa750f523c5/routes/api/native/callDaemon.js#L13) function), using the username, password and port set in the user's Verus configuration file (which is fetched with Verus Desktop [here](https://github.com/VerusCoin/Verus-Desktop/blob/060db2587a22e1de81c593309c1b5fa750f523c5/routes/api/rpc.js#L7)). If the config file doesn't exist (because its the first time the daemon is being run on the machine) it can be created and filled with an rpcport (which must match the chain's rpcport, recorded in Verus Desktop [here](https://github.com/VerusCoin/Verus-Desktop/blob/master/routes/ports.js)), username and password.

It is worth noting that this rpc communication model is based off of [bitcoinrpc](https://developer.bitcoin.org/reference/rpc/), but there are a number of new commands and potentially differences in protocol so its best to use the verus API commands above as a reference.

## Getting an identity

Once the callDaemon function is defined, actually getting information from the daemon by using it is simple, as you can see in the get_identity function [here](https://github.com/VerusCoin/Verus-Desktop/blob/060db2587a22e1de81c593309c1b5fa750f523c5/routes/api/native/idInformation.js#L198).

## Running Verus Desktop in Dev Mode

To test all this in Verus Desktop, you will need to follow the readme at https://github.com/VerusCoin/Verus-Desktop.
