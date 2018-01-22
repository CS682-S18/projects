Project 1 - Distributed Chat Application
============================

### Demonstration Due - Tuesday, Feb 6 - start of class
### Code Due - Wednesday, Feb 7 - 5PM

For this project you will implement a chat application using the following technologies.

- Sockets
- ZooKeeper
- Protocol Buffers

You must follow the specifications below *exactly* as a large part of your grade will depend upon whether your solution is able to communicate with solutions implemented by other students. We will have an in-class demo session where everyone will execute their applications!

### Application Interface

Your application will provide an interface to support the following functionality:

1. List all other chat nodes in the network.
2. Send a message to a specific node.
3. Send a message to all other nodes.
4. Receive and display a message from another node.
5. The command `help` must list the commands available to the user.
6. Exit.

It is up to you to design an intuitive interface for supporting all functionality, but you must at least support the command `help`.

### Discovery

You will use ZooKeeper to discover other chat nodes. When your chat application launches it will connect to the ZooKeeper server running on `mc01` port `2181` to get a list of all other nodes in the network and to register itself. You will need to reconnect to get a list of nodes each time the user selects option 1 or 3 listed above.

The naming convention you *must* adhere to is as follows. The top-level znode will be named`/CS682_Chat`. To register a new node, you will create a new child using your USF username, for example `/CS682_Chat/srollins`. 

The data you store in your znode will note the IP address and port that may be used to connect to your host. You will use protocol buffers to encode this data and you *must* use exactly the following specification:

```
syntax = "proto3";

message ZKData {
    string ip = 1;
    string port = 2;
}
```

### Message Transmission

Each application will act as a *peer*---both a sender and a receiver. 

#### Sender

You will have a main thread of control that presents the UI and reads commands from the user. If the user selects option 2 or 3 listed above the node will perform the following operations: 

1. Use protobuf to create a Chat message as described below.
2. Connect to the host specified by the user in the case of option 2, or in case of option 3 perform steps 2-4 for each host in the system.
3. Send the message.
4. Read the reply from the other node.
5. If appropriate, report a message failure to the user.

Messages must be encoded using *exactly* the following specification:

```
message Chat {
    string from = 1;
    string message = 2;
    bool is_bcast = 3;
}
```

#### Receiver 

The receiver component will listen on a server socket and create a new thread to handle each incoming request. 

If the request is *not* a broadcast message the receiver will display the incoming message for the user and send a successful reply using the following format:

```
message Reply {
    int32 status = 1;
    string message = 2;
}
```

A status of 200 and a message of OK will be used to indicate success.

If the request is a broadcast message the receiver will perform the same steps as above, display for the user and reply, but must also save the message in a *thread-safe* data structure for use in Project 2.

### History

Your solution must maintain a thread-safe data structure to store the history of all broadcast messages received. You *may* use the `java.util` packages, but don't forget everything you learned in 601 about thread safety! For debugging purposes, you may also want to add an option to list the contents of the history data structure.

### Application Execution

- Your main method must be in a class `cs682.Chat`. 
- Your program will take two command line arguments:
  * `-user <username>` - The username that will be used for your client.
  * `-port <port>` - The port on which your server will listen.

Your program *must* run exactly as follows, where name and 9900 will be replaced with appropriate values:
```
java -jar project1.jar cs682.Chat -user name -port 9900
```

### Submission Requirements

There will be two deadlines for this assignment.

By **Tuesday, Feb 6** at the *beginning* of class you must be prepared to demonstrate your working solution. For full demonstration credit your solution must be completely interoperable with the correct solution and the solution of any other student who also has a correct solution.

By **Wednesday, Feb 7** at 5PM you must submit all code and a jar file to your github repository for this assignment. Your jar file *must* be in a `lib` directory under your top-level repo directory and it *must* be named `project1.jar`. 

Use the following link to create your private github repository for this assignment: [Project 1](https://classroom.github.com/a/ttmpXCg4)

For full credit, make sure to follow all [Style Guidelines](https://github.com/CS682-S18/notes/blob/master/style.md). Points will be deducted for each violation.


### Grading Rubric

| Points | Criterion |
| ------ | -------- |  
| 20 | Multithreaded Server | 
| 20 | Message History Data Structure |
| 15 | Node Registration and Discovery |
| 10 | UI | 
| 10 | Style | 
| 15 | Demonstration |
| 10 | Runs correctly |

Partial credit may be awarded for partial functionality and/or partially correct design or style elements.

### Academic Dishonesty

Any work you submit is expected to be your own original work. If you use any web resources in developing your code you are strongly advised to cite those resources. The only exception to this rule is code that is posted on the class website. The URL of the resource you used in a comment in your code is fine. If I google even a single line of uncited code and find it on the internet you may get a 0 on the assignment or an F in the class. You may also get a 0 on the assignment or an F in the class if your solution is at all similar to that of any other student.

