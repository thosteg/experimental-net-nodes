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

The format is be as follows:

   <Q|A|E><id>: text describing task or response 
   
with:

   <id> = [0-9]+
   
Examples (no real tasks):

    -> Q1: calculate 1+1
    -> Q2: write "hi" on the console
    -> Q3: calculate 2*(2+3)
    <- A1: 2
    <- Q1: play "For Elise" on the speakers
    <- Q2: write "ho" on the console
    -> Q1: calculate 7*7
    <- A3: 10
    -> A2: wrote "ho"
    -> A1: played "For Elise"
    <- A1: 49
    <- A2: wrote "hi"

Note:

* Use T|R (ticket|response) instead of Q|A?
     
A node does not have to be able to perform a task. It can return an error, either by writing it as answer or responding with an "E":
     
    -> Q1: calculate 1/0
    <- A1: Err
    -> Q1: print "hello" on the console
    <- E1: I can not print to the console

