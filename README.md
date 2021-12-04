# 50.012 Project Group 6

## Introduction
In this project, we explore the Quick UDP Internet Connection (QUIC) protocol developed by Google. 

### Problem Statement
Examine how network latency is reduced when QUIC is used in place of TCP, and to measure the improvement in network performance of QUIC over TCP. 

### Motivations
Limitations of HTTP (TLS/TCP)
* Difficult to upgrade TCP due to middleboxes such as firewalls and Network Address Translators
* Different versions of user OS limits the speed at which the TCP stack is changed
* TCP + TLS incur additional RTTs during handshake
* Head-of-line blocking delay in event of retransmissions

Limitations of UDP
* No encryption
* No built-in error recovery for lost/corrupted packets
* No congestion control

### QUIC Design for Improved Performance
The main features of QUIC over existing TCP+TLS+HTTP2 that we are focusing on are:
* Reduced connection establishment time
* Improved loss recovery
* Multiplexing without head of line blocking

## Our Experiment

### The Emulating Environment
#### Experimental Platform 
Our experiments were performed on a virtual machine using VirtualBox.

* Software

  | Software |      Parameters         |
  | -------- | ----------------------- |
  | OS       | Ubuntu16.04             |
  | OS-type  | 64 bit                  |
  | Kernel   | Linux 4.4.0-210-generic |
  | GCC      | GCC 5.4                 |
  | Python   | Python 2.7.12           |
  
#### Test Variables

  |   Variables   |      Definition                                                       |
  | --------------| --------------------------------------------------------------------- |
  | Protocol      | Choosing between TCP or QUIC                                          |
  | Bandwidth     | Limiting the maximum link bitrate                                     |
  | Network Delay | One-way delay to packets that are going from a server to client       |
  | Packet Loss   | Drop packets that are going from a server to client                   |
  | No. of tests  | Adjust the number of rounds of tests to run.                          |

  
### Starting the Experiment

#### Setting up VM
1. Clone the [source of chromium](https://www.chromium.org/developers/how-tos/get-the-code) and follow the instructions to build Chromium on Linux. 
2. Install dependencies when building for first time
```
./src/build/install-build-deps.sh
```
3. Build the QUIC client, server, and tests:
```
cd src
gn gen out/Default && ninja -C out/Default quic_client quic_server net_unittests
```
4. Set-up [Apache2 Server](https://ubuntu.com/tutorials/install-and-configure-apache#1-overview) and configure it to use SSL.

#### Running the Experiment
1. Prepare test data from www.example.org 
```
mkdir /tmp/quic-data
cd /tmp/quic-data
wget -p --save-headers https://www.example.org
```
2. Run the chromium server and leave the terminal running
```
cd path-to-repo
./scripts/server.sh
```
Note to update the file path in `server.sh` accordingly.

3. Run the client in another terminal
```
./scripts/env_setup.sh
./scripts/run.sh
./scripts/analyse.sh
```
Note to update the file paths in `env_setup.sh`, `run.sh` and `analyse.sh` accordingly. 

## Results

### Loss Recovery 
#### QUIC vs TCP Performance under High Packet Loss
<img src="https://user-images.githubusercontent.com/62118373/144176097-2e079619-0b6d-4a1f-b1e4-dd066e2a6c48.png" width="600">

QUIC yields much higher throughput under high loss environments than TCP and hence has better performance in this aspect. 
#### QUIC vs TCP Performance under Packet Delay
<img src="https://user-images.githubusercontent.com/62118373/144176023-67f90b7f-a4b7-46a5-bbd1-2b43d6a6f6a9.png" width="600">
QUIC yields higher throughput than TCP when there is packet delay and thus has better performance in this aspect. 
