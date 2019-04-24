# Map_Reduce_Using_Thrift

## 1. Overview
The goal of this project is to implement a simple MapReduce-like Compute Framework using Thrift and Java.
The stack used for analysis:
1. Thrift ​- To build distributed services.
2. Java​ - For coding

## 2. Components Implemented
In this section, we present a brief overview of the various components used.
### 2.1 Client (Client.java)
The client reads all the files given in the input directory and pass it to the server for map reduce tasks. It reads the fileNames as Strings into an arrayList which is then sent to Server.
### 2.2 Server (Server.java)
The server receives all the filenames from the Client as an ArrayList and reads it. It then sends it to the Compute Nodes for Map-Reduce tasks.
### 2.3 Worker Node (WorkerNode.java)
Worker Nodes does the Map and Sort tasks as specified. Each Worker node runs on a different machine.
### 2.4 Calculate Sentiment (CalculateSentiment.java)
Calculate Sentiment service is the code that is generated using Apache Thrift to build RPC between the client and server. This file is generated from the CalculateSentiment.thrift file.
### 2.5 Client Server Handler (ClientServerHandler.java)
The client server handler receives all the strings(FileNames) sent by the Client. From there, based on the hostname and ports provided in the config file, the handler establishes the connection between the server and the Worker Nodes. It uses all the strings to generate individual threads and divide them among worker nodes. It is done on the basis of scheduling policy as mentioned in the config file. The Handler also takes care to run Sort task after the map task is completed for all the threads or Strings(Filenames).
### 2.6 Sentiment Service (SentimentService.java)
Sentiment service is the code that is generated using Apache Thrift to build RPC between the Server and the Worker Node. This file is generated from the SentimentService.thrift file.
### 2.7 Sentiment Handler (SentimentHandler.java)
Sentiment Handler takes in the thread and based on scheduling policy mentioned in the config file is handled by either Random or Load Balancing policy. The Sentiment Handler calls MapTest and SortTest which does the Map task and sort task respectively.
### 2.8 MapTest (MapTest.java)
The MapTest class takes in the fileName as input and computes the sentiment score using the positives and negatives as mentioned. It uses Regular expression to segregate the words from the file and classify them according to the negatives and the positives. The resulting score along with the file name is written into the intermediate directory which is in the same directory as input and output directory.
### 2.9 SortTest (SortTest.java)
The SortTest class uses all the files in the intermediate directory and created a single output file containing the file name and the sentiment score of each file name.

## 3. Workflow
We created two thrift files and generated two services for communication between client and server and server and worker nodes respectively. We then created the handlers for each server and worker node.
Detailed Workflow:
1. The client extracts all the filenames from the input directory and passes it to the Server.
2. The Server runs the main thread. Using ClientServerhandler we generate the threads corresponding to each string and randomly choose a worker node from the given worker
nodes in config file and assign the thread to it for the map task.
3. Each thread calls the services method of SentimentHandler.java and based on the
scheduling policy defined in the config file, the thread is serviced.
4. The services method calls the Map function which in turn calls the map method in
MapTest class which calculates the sentiment score based on the positives and negatives.
5. Once the map is computed for each file, the sort function is called.
6. The Sort function does the sorting based on Sentiment Score using Priority Queue and
writes the final output to the output directory.

## 4. Running the Code
We created a config.file which specifies the following:
First line mentions the scheduling policy. (0-Random 1-Load Balancing)
Second line mentions the port numbers. (9106-Client-Server, 9107-Server-WorkerNode)
which is separated by a single space
From third line onwards, each line has a description of the hostnames and load value of each
WorkerNode, separated by a single space. (​e.g​ ​csel-kh4250-06.cselabs.umn.edu 0.2​)
Note: All the values are separated by a single space.

To run the code, we need to follow the following steps:
(Everything needs to be run from the directory in which the files are present)
1. We first compile the project using the following syntax :
javac -cp ".:/usr/local/Thrift/*" *.java -d .
2. Then we ssh into the WorkerNodes mention in the config file by ssh-ing into the CSE lab machines. For example, we ssh on 4 machines for running 4 individual worker nodes.
3. We then run the WorkerNodes individually using the following command
java -cp ".:/usr/local/Thrift/*" WorkerNode
4. We then run the Server on the local node using:
java -cp ".:/usr/local/Thrift/*" Server
5. Next, we then run the Client on the local node to run the entire Map-Reduce functionality:
java -cp ".:/usr/local/Thrift/*" Client
