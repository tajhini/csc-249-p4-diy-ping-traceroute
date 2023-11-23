# CSC 249 - Project 4 - ICMP Ping and Traceroute

_Attribution: this assignment is based on ICMP Pinger Lab and ICMP Traceroute Lab from Computer Networking: a Top-Down Approach by Jim Kurose and Keith Ross. It was modified for use in CSC249: Networks at Smith College by R. Jordan Crouser in Fall 2022, and further modified by B. Cheikes for use in Fall 2023._

In this assignment, you will gain a better understanding of Internet Control Message Protocol (ICMP) by implementing your own **`ping`** and **`traceroute`** applications using ICMP request and reply messages. After building these tools you will use them to carry out some basic network performance analysis.

The network utility `ping` is a computer application used to test whether a particular host is reachable across an IP network. It is also used to self-test the network interface card of the computer or as a latency test. It works by sending ICMP `echo reply` packets to the target host and listening for ICMP `echo reply` packets in response (the return `echo reply` is sometimes called a _pong_). As written, `ping` measures the round-trip time (RTT), records packet loss, and prints a statistical summary of the echo reply packets received (min, max, and the mean of the round-trip times and in some versions the standard deviation of the mean).

Your task is to develop your own `ping` application in Python. Your application will use ICMP but, to keep it simple, will not exactly follow the official specification in [RFC 1739](https://datatracker.ietf.org/doc/html/rfc1739). 

_Note: you will only need to write the client side of these program, as the functionality needed on the server side is built into almost all operating systems._

## Part 0: Build a Spreadsheet for Testing and Analysis
Set up a spreadsheet as follows:

* Column headers should be: Domain, IP, Location, Distance, Ping Date, Avg RTT, Trace Date, Trace RTT, # Hops
* Select five domain names of sites located in various places around the US. Hint: Using university domain names, cultural institutions and names of state and local government sites is a good way to get a reasonable geographic distribution. Using a similar strategy, select five domain names of sites located outside the United States. Get creative! Load the 10 domain names as rows in your spreadsheet. Use nslookup to determine the IP address of each domain in your list, and add that to your spreadsheet.
* Use an IP-to-geolocation service (e.g., [https://tools.keycdn.com/geo](https://tools.keycdn.com/geo)) to find the (approximate) geographic coordinates of each domain in your list. Record the location in your spreadsheet.
* Use the same service to find the geographic coordinates of smith.edu, and use a geo-distance service (e.g., [https://www.nhc.noaa.gov/gccalc.shtml](https://www.nhc.noaa.gov/gccalc.shtml)) to compute the **pairwise geographic distance in miles** between Smith College and each domain in your list. Put the result in the appropriate column in your spreadsheet.

At this point your spreadsheet should have ten data rows, and each row should have values populated for Domain, IP, Location, and Distance. You're ready for the next part! 

## Part 1: `ping`
Your `ping` application should send a series of three `ping` requests to a specified host separated by approximately one second. Each message will contain a payload of data that includes a timestamp. After sending each packet, the application should wait up to one second to receive a reply. If one second goes by without a reply from the server, then the client should assume that either the ping packet or the pong packet was lost in the network (or that the server is down). The code should compute and display the RTT for each ping request that yields an associated pong. You'll use this information in later analysis.

### Support Code
The file `ICMPpinger.py` contains starter code for the client-side behavior of your `ping` program. Your task is to fill in code in the area marked with `#Fill in start` and `#Fill in end` in order to get your program to behave as described. You should also study the starter code carefully to make sure you fully understand it. Notice that the code is written to accept a target domain name or IP address from the command line. You can feel free to modify this part of the program to "hard code" the ten domains you plan to test, or write a shell script that repeated calls your pinger with each test argument.

### Details
1.	In the `receiveOnePing(...)` method, you need to receive the structure `ICMP_ECHO_REPLY` and fetch the information you need, such as checksum, sequence number, time to live (TTL), etc. Take a look at the `sendOnePing(...)` method and make sure you understand what it is doing before trying to complete the `receiveOnePing(...)` method.
2.	You do not need to be concerned about the `checksum(...)` method, as it is already given in the code.
3.	This exercise requires the use of raw sockets. In some operating systems, you may need **administrator/root privileges** to be able to run your pinger program (i.e., you'll run the program with the command `sudo python3 ICMPpinger.py target`).

### Testing the Pinger
First, test your client by sending packets to localhost, that is, 127.0.0.1. Then, see how your Pinger application communicates across the network by pinging the various servers listed in your spreadsheet. During this process you might discover that one or more of your selected servers appears to be unreachable; i.e., all ping requests are unanswered. This could be due to the server being temporarily offline, or it could be due to a common security configuration on the server which blocks replies to ping requests. Regardless, you should discard and replace any domain/IP selection which does not respond to ping requests.

If you are uncertain of whether your pinger is performing correctly, you can always compare its output to that of the standard ping application that is built into most operating systems.

### Ping Performance Data Collection
When you think your pinger is working properly, systematically run it against the ten domains in your spreadsheet. **Capture the output of this activity** as this will be one of your project deliverables. Note the date/time of your run in the "Ping Date" column of your spreadsheet, compute the average RTT for each target and load that in the "Avg RTT" column. That's all for now. Time to turn your attention to the next programming task.

## Part 2: `traceroute`
In this section, we'll expand on the pinger in order to implement a traceroute application using ICMP request and reply messages. For obvious reasons you are strongly encouraged to first do the `ping` section before attempting the `traceroute` section, as it is done with the same approach. The checksum and header making are not reiterated here; refer to the previous `ping` section for that purpose. The naming of most of the variables and socket is also the same.

`traceroute` is a computer networking diagnostic tool which allows a user to trace the route from a host running the `traceroute` program to any other host in the world. As with `ping`, `traceroute` is implemented with ICMP messages. It works by sending ICMP `echo` (ICMP type ‘8’) messages to the same destination with increasing value of the time-to-live (TTL) field. The routers along the traceroute path return ICMP `Time Exceeded` (ICMP type ‘11’ ) when the TTL field become zero. The final destination sends an ICMP `reply` (ICMP type ’0’ ) messages on receiving the ICMP `echo` request. The IP addresses of the routers which send replies can be extracted from the received packets. The round-trip time between the sending host and a router is determined by setting a timer at the sending host.

Your task is to develop your own `traceroute` application in python using ICMP. Your application will use ICMP but again, in order to keep it simple, will not exactly follow the official specification in RFC 1739. As you did with the ping program, you should also modify the code so that it takes an IP address or domain name from the command line.

### Support Code
The file `ICMPtraceroute.py` contains starter code for the client-side behavior of your `traceroute` program. Your task is to fill in code in the areas marked with `#Fill in start` and `#Fill in end` in order to get your program to behave as described.

### Details
1. As before, this exercise also requires the use of raw sockets. In some operating systems, you may need administrator/root privileges to be able to run your `traceroute` program.
2. This will not work for websites that block ICMP traffic. If you can't ping a target you also won't be able to correctly run a traceroute to it.
3. You may have to turn your firewall or antivirus software off to allow the messages to be sent and received.

### Optional Feature
Currently the application only prints out a list of ip addresses of all the routers along the path from source to the destination. Try using the `gethostbyname(...)` method to print out the names of each intermediate route along the route.

### Traceroute Performance Data Collection
When you think your traceroute program is working properly, systematically run it against the ten domains in your spreadsheet. **Capture the output of this activity** as this will be one of your project deliverables. If you are uncertain of whether your program is performing correctly, you can always compare its output to that of the standard traceroute application that is built into most operating systems. Augment your spreadsheet for each target by adding the date/time of your trace, the measured RTT of the final hop in the trace, and the total number of hops needed to reach the target.

## Part 3: Performance Analysis
At this point, you have built your own `ping` and `traceroute` utilities, tested them against the same set of ten target domains, and collected result data in a spreadsheet. Perform the following analyses:

* Using the pinger data, draw or create a scatterplot with the **Average RTT in milliseconds** on the Y-axis, and the **geographic distance in miles** on the X-axis. Save this scatterplot to be uploaded as one of your deliverables.

* Using the scatterplot you generated, answer the following questions:

1. Are RTT and geographic distance correlated positively, negatively, or not at all? If applicable, also comment on the strength of correlation (weak vs. strong).
2. Why do you think you observe this trend (or lack thereof)?

* Using the traceroute data, draw or create a scatterplot with the **# Hops to Target** on the Y-axis, and the **geographic distance in miles** on the X-axis. Save this scatterplot to be uploaded as one of your deliverables.

* Using the scatterplot you generated, answer the following questions:

1. Are # hops and geographic distance correlated positively, negatively, or not at all? If applicable, also comment on the strength of correlation (weak vs. strong).
2. Why do you think you observe this trend (or lack thereof)?

## Deliverables
Your work on this project must be submitted for grading on the last day of classes - **THURSDAY DECEMBER 14th at 11:59PM**. Because this is the last day of the class before finals, **no extensions will be available.** Work submitted after the due date will not be graded and will receive a score of zero. Seriously.

All work must be submitted via Gradescope. This project counts for 15 points towards your final course grade.

You must submit these work products:

1. Source code for your ping and traceroute utilities. Ideally, this will be a link to a public Git code repo you have created for this project.
2. Execution trace of ping output tested against ten (10) different IP addresses. 
3. Execution trace of traceroute output tested against the same ten (10) IP addresses.
4. Ping analysis: scatterplot plus answers to the two associated questions.
5. Traceroute analysis: scatterplot plus answers to the two associated questions.
6. Spreadsheet containing all ping and traceroute performance data.

You are strongly encouraged to combine deliverables 2 through 5 into a single Word / Google Doc / PDF document. Your Python code and your spreadsheet should be submitted as separate files. Ideally, all of these materials will be collected in a single Github repository, and you will supply the repository link to Gradescope when making your submission.

## Teamwork Policy
For this project, students are permitted to team up in pairs and submit joint work. Both partners should fully understand all work products that are submitted.

## Tips for Success

Want to ace this project? Then do these things:

* Read about ICMP in our Network+ text, pp. 189-190.
* Review the RFC (specification) for ICMP at this link: https://www.rfc-editor.org/rfc/rfc792.
* Study the Wireshark captures for ping and traceroute messages included in this Github repository.
* Don't wait until the last week of classes to start this project.

## Grading Rubric

Your work on this project will be graded on 15-point scale. Your programming work (deliverables 1-3) will account for ten points, and your performance analysis work (deliverables 4-6) will account for five points. Fractional points may be awarded.

Your `ping` and `traceroute` programs will each be assessed on a five point scale:

_0 pts:_ Deliverables not received by the due date or requested extension date.

_1 pt:_ Partial deliverables received by the due date or extension date.

_2 pts:_ Required deliverables were received but code doesn't run.

_3 pts:_ Code runs but does not produce correct output.

_4 pts:_ Code runs and does most but not all of what is required.

_5 pts:_ Nailed it. Code runs and does what is required.

Your performance analysis work will be graded on a 5-point scale.

_0 pts:_ Performance analyses not received by the due date or requested extension date.

_1 pt:_ Incomplete performance analyses received by the due date or requested extension date.

_2 pts:_ Complete performance analyses received but demonstrate large gaps in correctness and understanding.

_3 pts:_ Complete performance analyses received but demonstrate significant gaps in correctness and understanding.

_4 pts:_ Complete and correct performance analyses received but have minor gaps in understanding.

_5 pts:_ Nailed it. Complete, correct and thorough analyses. Answers to questions demonstrate breadth and depth of understanding of the underlying networking factors that might explain the observations.
