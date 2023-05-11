# LeapC Library V5.1 Gemini on Linux with a Socket Connection

## Introduction

This documentation explains how retrieve data from the Ultraleap3Di using the LeapC Library V5 with Python Client using a Polling server in C. The connection with the Python script is enabled with a Socket connection. The desired client language does not need to be Python, as long as it supports sockets.

## Prerequisites
This documentation was tested with the following system specifications:
- Ubuntu 22.04.2 LTS (64 bit)
- Ultraleap 3Di (Stereo Hand-tracking Camera)
- Python 3.10.6

First you need to install the Leap drivers and library using the instruction on their website for the fifth version of the LeapC library, called Gemini.

## Steps
After the successful installation, follow these steps:

1. **Create Polling-file in C**\
First browse to the directory of the LeapC library
```bash
# General library directory
cd /usr/share/doc/ultraleap-hand-tracking-service/
# Sample directory
cd sample/
# Copy the already existing PollingData sample file
cp PollingData.c leap_server.c
```
Now open the `leap_server.c`-file and add the functionality using the c-built-in "socket" library. An example is attached in the folder.

2. **Build library**\
Add the `leap_server.c`-file to the build-file `CMakeLists.txt` by adding the following command:
```bash
#add_sample(<Output script name> <C-file name>)
add_sample("LeapServer" "leap_server.c")
```
Now follow the instructions in the `Readme.md`-file in the General LeapC library directory, mainly this is the code you will need:
```bash
# Define
SRC_DIR=/usr/share/doc/ultraleap-hand-tracking-service/samples    
BUILD_TYPE='Release'    
REPOS_BUILD_ROOT=~/ultraleap-tracking-samples/build     
REPOS_INSTALL_ROOT=/usr/bin/ultraleap-tracking-samples    
# Source
cmake -S ${SRC_DIR} -B ${REPOS_BUILD_ROOT}/${BUILD_TYPE}/LeapSDK/leapc_example `       
        -DCMAKE_INSTALL_PREFIX="${REPOS_INSTALL_ROOT}/leapc_example" `    
        -DCMAKE_BUILD_TYPE="${BUILD_TYPE}"    
# Build
cmake --build ${REPOS_BUILD_ROOT}/${BUILD_TYPE}/LeapSDK/leapc_example -j --config ${BUILD_TYPE}
```
After build you can find the built file in the path `~/ultraleap-tracking-samples/build/Release/LeapSDK/leapc_example/LeapServer`. For execution run the following commands:
```bash
cd ~/ultraleap-tracking-samples/build/Release/LeapSDK/leapc_example/
./LeapServer
```
The scipt can be executed anywhere on the device !

3. **Create the Python client**\
Create a new Python-file anywhere on the local machine and use the example file as a template.

4. **Run the server and client**\
To run the system open two Terminals in parallel:
```bash
# Terminal 1
cd /path/to/serverfile/
# Run server c-script
./LeapServer
```
Wait for the server to output `Binding socket.`, otherwise the client will not be able to see an open connection.
```bash
# Terminal 2
cd /path/to/clientfile/
# Run python client file
python3 leapClient.py
```
The location of either of these files is not important, because this runs over a socket connection.

## Running the client in a docker container
It is also possible to run the client in a docker container, here you need to make sure that the Port is opened for the docker container. One easy way to enable this by using the `--network=host` argument:
```bash
sudo docker run -it --network=host hpe_ros
```

## Troubleshooting
Server:
- **"PORT already in use"**: run `sudo lsof -t -i:<PORTNR> | xargs kill -9` to kill all processes running on that Port

Client:
- **"Connection refused"**: maybe the server itself is not yet ready, try again by restarting the server and then the client.
