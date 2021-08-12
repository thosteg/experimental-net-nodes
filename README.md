# experimental-net-nodes

An experiment dealing with nodes chatting with each other over the network whole solving sample problems.

## Intention

The intention is to experiment with nodes (processes) that communicate with each other to solve simple problems.

"To experiment" includes:

* TCP-socket connections (with and without encryption)
* different languages
* simple protocol for easy playing around
* using the above as extractable examples for "real" problems

## Overview

### Nodes

Each node is a simple process, that speaks to other processes via TCP-sockets.

    A ----- B ----- D
     \     /
      \   /
       \ /
        C
        
The process is started with:

* port it listens on (optional, CAN be defaulted)
* host + port it connects to  (CAN be defaulted)

Default-Port:

Arbitrarily chosen right now as 5769

### Protocol

The different nodes send tasks to each other.
If a node can process the task, it will return an answer.
If a node can not deal with a task, it will return an error.

The protocol is very simple for the initial implementation (may grow more complicated with time).

* A message is a single line of text ending with a newline ("\n")
* Messages do not have to be answered in the same order as they are received
* To be able to use a single connection for both sending requests as well as accepting requests, queries and responses are marked as such
* To enable a node to see which query is replied to, a self provided id will be echoed back with the response. It is entirely up to the sender which id he uses for each message

#### Connection Protocol

The format is as follows:

   <Q|R|Err>:\n
   Id:<id>\n
   T:text describing task or response\n
   \n
   
with:

   <id> = [0-9]+

Comment lines begin with the character "#":
       
    # this is a comment

Example 1 (no real task):

    # ->
    Q:
    Id:1
    T: calculate 1+1
    
    # <-
    R:
    Id:1  # is simply echoed
    T: 2

A node does not have to be able to perform a task. It can return an error, either by writing it as answer or responding with an "Err":
 
    # ->
    Q:
    Id:1
    T: calculate 1+1
    
    # <-
    Err:
    Id:1  # is simply echoed
    T: can not calculate operator "+"
