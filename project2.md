Project 2 - Reliable Data Transfer
==================================

### Demonstration Due - Thursday, Feb 22 - start of class
### Code Due - Friday, Feb 23 - 5PM

For this project you will extend your Project 1 Chat application to include a UDP client and server to support reliable bulk download of the `History` data structure of another chat peer in the network. You will use the following technologies:

- UDP Sockets

You must follow the specifications below *exactly* as a large part of your grade will depend upon whether your solution is able to communicate with solutions implemented by other students. We will have an in-class demo session where everyone will execute their applications!

### Application Interface

You will extend your application interface to support the following functionality:

**Download history** - The user may request to download the entire broadcast chat history from another peer. If requested, you will send a UDP request to another peer in the network and implement a reliable download protocol (as described below) to receive the history and store it locally.

It is up to you to determine how to select the server from which the data will be requested.

After the new history is retrieved you will replace any previous history data structure with the new data.

You may optionally add the functionality to fetch the existing history during your peer's startup process.

**Show history** - The user must be able to display the contents of its current history data structure.

### Discovery

Each peer will need to identify the UDP port on which the history download *service* is running.

You will extend your use ZooKeeper for this purpose. You will modify the `ZKData` message to also specify the UDP port as follows:

```
syntax = "proto3";

message ZKData {
    string ip = 1;
    string port = 2;
    string udpport = 3;
}
```


### Messaging

Recall that UDP is *connectionless*. This means that you will use **one** UDP socket on each host, and this socket will be used for *both sending and receiving messages*.

A host may concurrently be acting as a receiver, for a download request it initiated, or a sender, for one or more requests it has received. Each time a new packet is received you will perform a demultiplexing operation to determine how to handle the incoming packet.

A packet is specified as follows:

```
message Data {
    packetType type = 1;
    int32 seq_no = 2;
    bytes data = 3;
    bool is_last = 4;

    enum packetType {
        REQUEST = 0;
        ACK = 1;
        DATA = 2;
    }
}
```

**REQUEST** - A message with type REQUEST is the initial message that will begin a new download operation. You will need to get the IP and port of the requester from the `DatagramPacket` and set up internal state to keep track of the in-progress download. The reply to this message will be the first data packet. No fields other than `type` will be relevant for this message.

**ACK** - A message with type ACK will acknowledge a correctly received data packet using the `seq_no` field. You will need to get the IP and port of the requester from the packet; update the sending window for that download as appropriate; and possibly send additional data packets. 

**DATA** - A message with type DATA is a new data packet with sequence number `seq_no`. If this is the last data packet `is_last` will be `true`. As described below, if the `seq_no` is the expected sequence number the `data` will be saved and an ACK will be sent in reply. If the data is out of order it will be discarded.

### Data Format and Framing

A UDP sender will send the history data in the following format:

```
message History {
    repeated Chat history = 1;
}
```

The data, however, will be sent in frames of 10 bytes. You will create a byte array of the entire structure and send 10 bytes in each `Packet` as defined above.

The last packet may have fewer than 10 bytes if the entire length of the array is not a multiple of 10. It should be flagged with `is_last = true`.

### Reliability

You will implement a Go-Back-N algorithm for ensuring reliable delivery of all data. 

You will use a window size of four packets.

The general algorithm for the receiver is as follows:

```
initiate download with REQUEST packet
until all DATA packets correctly received
	read next packet
	if seq_no is the expected sequence number
		save data
		send ACK for seq_no 
		increment expected sequence number
```

The general algorithm for the sender is as follows:

```
on REQUEST received
	initialize state for download
	send window of data
	start timer

on timer expiration
	resend outstanding window of data
	start timer

on ACK received
	if seq_no >= base outstanding seq_no
		stop timer
		slide window
		send new packet(s)
		start timer
```

### Application Execution

- Your main method must be in a class `cs682.Chat`. 
- Your program will take three command line arguments:
  * `-user <username>` - The username that will be used for your client.
  * `-port <port>` - The port on which your server will listen.
  * `-udpport <port>` - The UDP port on which your download service will listen.

Your program *must* run exactly as follows, where name, 9900, and 9901 will be replaced with appropriate values:
```
java -cp project2.jar cs682.Chat -user name -port 9900 -udpport 9901
```

### Submission Requirements

There will be two deadlines for this assignment.

By **Thursday, Feb 22** at the *beginning* of class you must be prepared to demonstrate your working solution on the CS microcloud. When testing or running during demonstration please use only your [assigned microcloud node(s) and port(s)](https://github.com/CS682-S18/notes/blob/master/microcloud.md). For full demonstration credit your solution must be completely interoperable with the correct solution and the solution of any other student who also has a correct solution.

By **Friday, Feb 23** at 5PM you must submit all code and a jar file to your github repository for this assignment. Your jar file *must* be in a `lib` directory under your top-level repo directory and it *must* be named `project2.jar`. 

Use the following link to create your private github repository for this assignment: [Project 2](https://classroom.github.com/a/-PlYL18N)

For full credit, make sure to follow all [Style Guidelines](https://github.com/CS682-S18/notes/blob/master/style.md). Points will be deducted for each violation.


### Grading Rubric

| Points | Criterion |
| ------ | -------- |  
| 20 | Receiver Implementation | 
| 5 | Sender Implementation - Framing |
| 5 | Sender Implementation - Sockets |
| 10 | Sender Implementation - Timers |
| 10 | Sender Implementation - Retransmissions |
| 5 | Node Registration and Discovery |
| 10 | UI | 
| 10 | Style | 
| 10 | Runs correctly |
| 15 | Demonstration  |
|  | [5] - Your solution running on two nodes without failure.  |
|  | [5] - Your solution running on two nodes with the following two failure scenarios: (1) Lost DATA packet; (2) Lost ACK packet.  |
|  | [5] - Your solution with failure scenarios described above downloading from another student's solution running on a different node.  |
|  | [5] - Your solution running on two nodes with the failure scenario Lost REQUEST packet **and** doing concurrent download and upload.  |

You may earn up to 5 points extra demonstrate credit for this assignment.

You must sign up for a demonstration appointment using [this link](https://calendar.google.com/calendar/selfsched?sstoken=UU1MWWxGQlFmamJYfGRlZmF1bHR8NGRmNmZlODI3ZTdkYzU4YmQ4ZDdiYWM5Yzc0ZWJkZWE).

Partial credit may be awarded for partial functionality and/or partially correct design or style elements.

### Academic Dishonesty

Any work you submit is expected to be your own original work. If you use any web resources in developing your code you are strongly advised to cite those resources. The only exception to this rule is code that is posted on the class website. The URL of the resource you used in a comment in your code is fine. If I google even a single line of uncited code and find it on the internet you may get a 0 on the assignment or an F in the class. You may also get a 0 on the assignment or an F in the class if your solution is at all similar to that of any other student.
