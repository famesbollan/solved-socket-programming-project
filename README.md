Download Link: https://assignmentchef.com/product/solved-socket-programming-project
<br>
<h1>1  Introduction</h1>

Bitcoin was unleashed on the world in 2009 as the first decentralized digital currency. The technology behind it is the <em>blockchain</em>, which has since been found to be a useful idea for much more than just currency.

A <em>blockchain </em>is a single universally accessible digital ledger. It’s called a chain because changes can be made only by adding new information to the end. Each new addition, or <em>block</em>, contains a set of new transactions that reference previous transactions in the chain. So if Abby pays Barbara a bitcoin, that transaction appears at the end of the chain, and it points to the transaction in which Barbara was previously paid that coin by Carol, which in turn points to the time before that when Carol was paid by coin by Damla, and so on.

Bitcoin’s blockchain, unlike the ledgers maintained by traditional banks, is represented on networked computers around the world and is accessible by anyone. A class of participants on this network, called <em>miners </em>is responsible for detecting transaction requests from users, aggregating them, validating them, and adding them to the blockchain as new blocks.

Validation entails both verifying that Abby actually owns the bitcoins in her transaction and that she has not spent them elsewhere. Bitcoin is a <em>cryptocurrency </em>because it relies on the use of private and public keys to validate transactions, and ensure they’re irreversible. Because Bitcoin has no central authority to enforce the rules it uses a mechanism called <em>proof-of-work </em>to hold the anonymous miners operating around the world accountable.

This project description is organized as follows. First we discuss time in distributed systems in §2 and distributed agreement in §3. The objective of this project is to develop a simplified variant of Bitcoin using a peer-to-peer (P2P) approach, with a centralized server to maintain the active miners; the requirements of the project are described in §4. Finally, submission instructions are provided in §5.

<h1>2           Time in Distributed Systems</h1>

In real life, clocks are used to represent <em>time</em>, and a <em>timer </em>is used to describe occurrences of events in three different ways: When an event occurs, how long it takes, and which event occurs first.

For computer applications we also need the notions of time and timer. As long as the clock representing time is increased uniformly and monotonically, there is no ambiguity in answering these questions for events that occur within the same process. However, interacting processes on separate machines may have a different perception of the time. Without a global time consensus, it is difficult to coordinate distributed activities. Here we describe the notion of a <em>logical clock </em>that preserves only the ordering of events.

<h2>2.1         Logical Clocks</h2>

For many applications, events need not be synchronized with respect to the real-time clock. It is only the ordering of event execution that is of concern. In such cases, logical clocks can be used to indicate the ordering information for events, particularly in distributed systems where it is difficult to maintain a common physical clock among all coordinating processes. <em>Lamport’s logical clock </em>is a fundamental concept for ordering processes and events in distributed systems.

Each process <em>P<sub>i </sub></em>in the system maintains a logical clock <em>C<sub>i</sub></em>. Lamport defines → as the <em>happens-before </em>relation to synchronize logical clocks between two events. <em>a </em>→ <em>b </em>means that event <em>a </em>precedes event <em>b</em>. Within a process, if event <em>a </em>occurs before <em>b</em>, logical clocks <em>C</em>(<em>a</em>) and <em>C</em>(<em>b</em>) are assigned such that <em>C</em>(<em>a</em>) <em>&lt; C</em>(<em>b</em>). The logical clock in a process is always incremented when events in the process progress (i.e., time never goes back and is only a relative measure for logical clock). Processes interact with each other through a pair of <em>send </em>and <em>receive </em>operations. Sends and receives are considered to be events. A corresponding <em>send </em>and <em>receive </em>pair from process <em>P<sub>i </sub></em>to process <em>P<sub>j </sub></em>must have the property that <em>C<sub>i</sub></em>(<em>send</em>) <em>&lt; C<sub>j</sub></em>(<em>receive</em>) since a receive event cannot be completed until a corresponding send is done. This logical clock based on the <em>happens-before </em>relation is summarized in the following two rules:

<ol>

 <li>If <em>a </em>→ <em>b </em>within the same process then <em>C</em>(<em>a</em>) <em>&lt; C</em>(<em>b</em>).</li>

 <li>If <em>a </em>is the sending event of <em>P<sub>i </sub></em>and <em>b </em>is the corresponding receiving event of <em>P<sub>j</sub></em>, then <em>C<sub>i</sub></em>(<em>a</em>) <em>&lt; C<sub>j</sub></em>(<em>b</em>).</li>

</ol>

Rule 1 can be easily implemented since events occur in the same process. Rule 2 can be enforced if the sending process timestamps its logical clock time in the message and the receiving process updates its logical clock by using the large of its own clock time and the incremented timestamp. That is, <em>C</em>(<em>b</em>) = <em>C</em>(<em>a</em>) + 1 and <em>C<sub>j</sub></em>(<em>b</em>) = max(<em>TS<sub>a </sub></em>+ 1<em>,C<sub>j</sub></em>(<em>b</em>)), where <em>TS<sub>a </sub></em>is the timestamp of the sending event. The happens-before relation describes the causality between two events. It is transitive, i.e., if <em>a </em>→ <em>b </em>and <em>b </em>→ <em>c</em>, then <em>a </em>→ <em>c</em>. Two events, <em>a </em>and <em>b </em>are said to be <em>disjoint </em>and can run concurrently if neither <em>a </em>→ <em>b </em>nor <em>b </em>→ <em>a </em>is true. The time-space diagram on the left in Figure 1 shows examples of causally related events (e.g., <em>a </em>and <em>d</em>, <em>b </em>and <em>f</em>) and concurrent events (e.g., <em>b </em>and <em>e</em>, <em>d </em>and <em>e</em>). Logical clocks for concurrent events do not relate to each other.

1         2                                                                                          (1,0,0) (2,0,0)

e                                                                f                                        e                                                                f

Figure 1: The happens-before relation and assignments of logical clock time using Lamport’s logical clocks (left), and vector logical clocks for the same events (right).

Using the happens-before relation and the two rules, all causally related events are ordered by the logical clocks. This results in a <em>partially ordered </em>event graph. For two disjoint events <em>a </em>and <em>b</em>, <em>C<sub>i</sub></em>(<em>a</em>) <em>&lt; C<sub>j</sub></em>(<em>b</em>) does not imply <em>a </em>→ <em>b</em>. Furthermore, it is possible that <em>C<sub>i</sub></em>(<em>a</em>) = <em>C<sub>j</sub></em>(<em>b</em>). <em>Total ordering </em>of events may be achieved by assuming the following additional rule for disjoint events:

<ol start="3">

 <li>For all events <em>a </em>and <em>b</em>, <em>C</em>(<em>a</em>) 6= <em>C</em>(<em>b</em>).</li>

</ol>

<h3>2.1.1         Vector Logical Clocks</h3>

Even with the total ordering of events using logical clocks, it is not possible to tell whether event <em>a </em>actually happened before event <em>b </em>if <em>C<sub>i</sub></em>(<em>a</em>) <em>&lt; C<sub>j</sub></em>(<em>b</em>), i.e., they might be concurrent. This is where a vector logical clock is used.

In the vector logical clock method, each process <em>P<sub>i </sub></em>maintains a vector of logical clocks for each event. We denote the vector logical clock for event <em>a </em>at processor <em>i </em>as <em>V C<sub>i</sub></em>(<em>a</em>) = [<em>TS</em><sub>1</sub><em>,TS</em><sub>2</sub><em>,…,C<sub>i</sub></em>(<em>a</em>)<em>,…,TS<sub>n</sub></em>], where <em>n </em>is the number of cooperating processes. <em>C<sub>i</sub></em>(<em>a</em>) (also represented as <em>TS<sub>i</sub></em>) is the logical clock time of event <em>a </em>in <em>P<sub>i</sub></em>, and <em>TS<sub>k</sub></em>, for <em>k </em>= 1<em>,…,n </em>except <em>i</em>, is the logical clock time for process <em>P<sub>k </sub></em>obtained through the timestamp information carried by messages in the system.

The vector clocks are initialized to zero at the beginning of process execution. The logical clock within a process is incremented as in Rule 1 for the basic logical clock approach. Rule 2 is modified as follows: When sending a message <em>m </em>from <em>P<sub>i </sub></em>(event <em>a</em>) to <em>P<sub>j</sub></em>, the logical timestamp of <em>m</em>, <em>V C<sub>i</sub></em>(<em>m</em>) is sent along with <em>m </em>to <em>P<sub>j</sub></em>. Let <em>b </em>be the event of receiving <em>m </em>at <em>P<sub>j</sub></em>. That is, <em>P<sub>j </sub></em>takes the <em>pairwise maximum </em>of the entries for every <em>k </em>= 1<em>,…,n </em>and also increments is logical clock. Thus the most recent logical clock information is propagated to every process by sending timestamps <em>TS<sub>k </sub></em>in the messages.

It is obvious that if event <em>a </em>in <em>P<sub>i </sub></em>happened before event <em>b </em>in <em>P<sub>j</sub></em>, then <em>V C<sub>i</sub></em>(<em>a</em>) <em>&lt; V C<sub>j</sub></em>(<em>b</em>), meaning that <em>TS<sub>k</sub></em>(<em>a</em>) ≤ <em>TS<sub>k</sub></em>(<em>b</em>) for every <em>k </em>and also <em>TS<sub>j</sub></em>(<em>a</em>) <em>&lt; TS<sub>j</sub></em>(<em>b</em>). This is because there is a causal path from event <em>a </em>to event <em>b</em>, and event <em>b </em>should have a more up-to-date logical time information than event <em>a </em>since the timestamps are passed along the path and the update rule is based on the larger of the two timestamps. Furthermore, the logical clock of the successor event <em>b </em>has been incremented since event <em>a</em>; therefore <em>TS<sub>j</sub></em>(<em>b</em>) must be greater than <em>TS<sub>j</sub></em>(<em>a</em>).

For disjoint events, it is not possible to have <em>V C<sub>i</sub></em>(<em>a</em>) <em>&lt; V C<sub>j</sub></em>(<em>b</em>) unless <em>a </em>→ <em>b</em>, since process <em>P<sub>i</sub></em>, which contains event <em>a</em>, should have a better update of its own time than any other process’ estimate of the current clock time of <em>P<sub>i</sub></em>; that is, <em>C<sub>i</sub></em>(<em>a</em>) is greater or equal to the <em>TS<sub>i </sub></em>in other vectors. Similarly, <em>V C<sub>j</sub></em>(<em>b</em>) <em>&lt; V C<sub>i</sub></em>(<em>a</em>) is true only if <em>b </em>→ <em>a</em>. We can conclude that it is possible to tell whether two events are causally related or not by comparing the vector logical clocks of the two events. If <em>V C<sub>i</sub></em>(<em>a</em>) <em>&lt; V C<sub>j</sub></em>(<em>b</em>) then <em>a </em>→ <em>b</em>; otherwise <em>a </em>and <em>b </em>are concurrent. The time-space diagram on the right in Figure 1 gives the vector logical clocks for the same events on the left.

<h1>3           Distributed Consensus</h1>

A fundamental problem in distributed computing is the <em>distributed agreement </em>or the <em>distributed consensus </em>problem – how to get a set of processors to agree on a value.

The formal setting for a distributed consensus protocol is the following: There are <em>M </em>processors <em>P </em>= <em>p</em><sub>1</sub><em>,…,p<sub>M </sub></em>that are trying to reach agreement. A subset <em>F </em>of the processors are faulty, and the remaining are non-faulty. Each processor <em>p<sub>i </sub></em>∈ <em>P </em>stores a value <em>V<sub>i</sub></em>. During the consensus protocol, the processors calculate an agreement value <em>A<sub>i</sub></em>. After the protocol ends, the following two conditions should hold:

<ol>

 <li>For every pair <em>p<sub>i </sub></em>and <em>p<sub>j </sub></em>of non-faulty processors <em>A<sub>i </sub></em>= <em>A<sub>j</sub></em>. This value is the <em>agreement value</em>.</li>

 <li>The agreement value is a function of the initial values {<em>V<sub>i</sub></em>} of the non-faulty processors (<em>P </em>− <em>F</em>).</li>

</ol>

The distributed consensus problem has been studied under a number of failure models, to understand when the problem can be solved. Most of the results use adversary arguments.

Byzantine agreement generally refers to distributed consensus algorithms in which a faulty processor can send arbitrary messages into the network. In particular, a faulty processor can try to prevent the non-faulty processors from reaching an agreement. Indeed, in this setting, if there are <em>t </em>traitors (faulty processors), and <em>M &gt; </em>3<em>t</em>, we can solve the Byzantine generals problem. But if there are too many traitors, then it can be shown that agreement cannot be reached.

To simplify this project, we’ll assume all processors are non-faulty. In addition, we assume the communication is reliable, i.e., uses TCP. Then, distributed consensus can be solved trivially through a simple broadcast, here implemented as repeated unicast.

<h1>4           Socket Programming Project</h1>

The architecture of the Bitcoin system consists of a centralized server to manage miners. It also consists of <em>n </em>peers where each peer, is identified by name, IP address, port number, and an initial number of coins. Each peer combines the functionality of a peer-to-peer (P2P) client and a transient P2P server. As a P2P client, a peer interacts with the server to join or leave the set of active miners, query the active miner set.

As a transient P2P server, a peer participates in processing Bitcoin transactions. You are to write two programs for this socket programming project:

<ol>

 <li>A miner server that takes (optionally) a command line parameter that is the name of a text file containing miner information; see §4.1 for the commands the server must be able to process.</li>

 <li>A P2P miner client and transient miner server; see §4.2 for specifications.</li>

</ol>

<h2>4.1         Miner Management Server</h2>

A <em>miner server </em>maintains a “database” of the active miners, i.e., the participants in the network that detect transaction requests, validate them, and add them to the blockchain as new blocks. The miner server must process messages containing the following commands:

<ol>

 <li>query, returns <em>n</em>, the number of miners, and for each miner four attributes are stored: user name, IP address, port number, and the initial number of coins owned.</li>

 <li>register username IP-address port coins, when a new miner client process is started, the first thing it does is register itself with the miner server. The server adds the miner attributes to the database or verifies an exact match to an existing records. The server returns an (integer) identifier for the user, and a return code of SUCCESS indicates the information is successfully added to, or verified in, the database. Otherwise, it returns FAILURE.</li>

 <li>deregister user-name, removes the named miner and its associated information from the database. A return code of SUCCESS is returned if the miner is successfully removed from the database. Otherwise, it returns FAILURE.</li>

 <li>save file-name, saves in the text file named file-name.txt:

  <ul>

   <li>A line containing the number <em>n </em>of miners.</li>

   <li>For each of the <em>n </em>miners, a line containing the username followed by its IP address and port number, the initial number of coins owned, and its integer identifier.</li>

  </ul></li>

</ol>

A return code of SUCCESS indicates the miners were saved successfully to the file, or FAILURE otherwise.

When the miner server is started, it may be provided a file in this format to initialize the active miners to speed testing of your application. This file may also be generated manually. Of course, the miner processes started should correspond to the attributes in the file.

<h2>4.2         Peer-to-Peer Miner Operation</h2>

Recall that the miners are the participants in the network that detect transaction requests, aggregate them, validate them, and add them to the blockchain as new blocks. In this project, a miner corresponds to user with a user name (given by a string) running on a host. For simplicity, we will not aggregate transactions, only handle them one at a time.

When a new miner process is started on a host it registers with the miner server. It then queries the miner server in order to know the size, <em>n</em>, and details of the active miner set, which are then output. The ledger (blockchain) at the miner is initialized with a block containing the initial number of coins <em>c<sub>i </sub></em>for each active miner, <em>i </em>= 1<em>,…,n</em>, and a null purchase request. The new miner then broadcasts that it has joined the active set of miners, providing its attributes. When an active miner receives the attributes of a new miner joining the active set, it responds with its blockchain. The new miner should replace its blockchain with the longest blockchain received with the latest timestamp.

In this project, a transaction corresponds to a purchase request. A miner <em>M<sub>i </sub></em>forms a transaction by indicating the miner <em>M<sub>j </sub></em>from which it intends to make a purchase, along with the amount in coins <em>c<sub>ij</sub></em>. In a valid transaction, the amount <em>c<sub>ij </sub></em>is less than or equal to the number of coins <em>M<sub>i </sub></em>owns, i.e., <em>c<sub>ij </sub></em>≤ <em>c<sub>i</sub></em>. If the amount <em>c<sub>ij </sub>&gt; c<sub>i </sub></em>then the transaction is considered invalid and is rejected because <em>M<sub>i </sub></em>can’t afford to make the purchase. The resulting effect of processing a valid transaction is to decrease the coins owned by <em>M<sub>i </sub></em>by the purchase amount, <em>c<sub>i </sub></em>= <em>c<sub>i</sub></em>−<em>c<sub>ij</sub></em>, and to increase the coins owned by <em>M<sub>j </sub></em>by the same amount, <em>c<sub>j </sub></em>= <em>c<sub>j </sub></em>+<em>c<sub>ij</sub></em>.

All events at a miner must be timestamped using vector logical clocks. A message that is broadcast is to be implemented using repeated unicast; each unicast in the broadcast has the same timestamp.

The steps to follow in processing transactions in our Bitcoin network are:

<ol>

 <li><em>M<sub>i </sub></em>broadcasts a new transaction to all active miners.</li>

 <li>Each miner receives a new transaction and stores it in a block; for simplicity we do not aggregate transactions.</li>

 <li>Each miner works on finding a difficult proof-of-work for the block received.</li>

 <li>When a miner finds a proof-of-work, it broadcasts the block to all active miners.</li>

 <li>Miners accept the block only if the transaction in it is valid.</li>

 <li>Miners express their acceptance of the block by appending it to the blockchain if it is unique.</li>

</ol>

In a real Bitcoin network, proof-of-work involves a time-consuming computation based on hash functions. In our version of Bitcoin, we start with the first block in blockchain and verify that each successive block is both valid and the timestamps must be either concurrent, or follow the happens-before relation. That is, for each successive pair of blocks <em>b<sub>i </sub></em>and <em>b<sub>i</sub></em><sub>+1</sub>, <em>i </em>= 1<em>,…,`</em>−1, where <em>` </em>is the length (i.e., number of blocks in) of the blockchain, then the transaction in <em>b<sub>i </sub></em>must happen-before or be concurrent with the transaction in block <em>b<sub>i</sub></em><sub>+1</sub>. In order to make the proof-of-work computation more time consuming, we fake the hash computation by taking a very large integer at random and testing if it is a prime number; repeat a small random number of times (≤ 50 times).

You will receive multiple copies of the same transaction that are equivalent (i.e., identical except for the vector timestamp). Only one copy of each transaction should be appended to the blockchain.

Transactions are generated repeatedly until the miner decides to exit the system. It then sends a deregister command to the miner server, and broadcasts its departure. Before a miner terminates, it prints its blockchain clearly labelling each block with its timestamp and associated transaction.

Design your Bitcoin application to read commands from standard input (or from a file redirected to stdin). The sequence of commands has the form:

<ul>

 <li>register attributes with the miner server,</li>

 <li>query the miner server, and then broadcast its attributes to active miners in orderinitialize its blockchain based on responses,</li>

 <li>repeatedly generate transactions to buy from other miners, process transactions of other miners, and finally,</li>

 <li>deregister and exit the system.</li>

</ul>

<h2>4.3         Message Format</h2>

You must define the format of all the messages exchanged between the active miner server and its clients, and between peers. This is achieved by defining a structure with all the fields you need. For example, for the communication with the miner server, you could define a command field as an integer, e.g., if command is equal to one, then this indicates the query command. It is also useful to define meaningful return codes to indicate success and differentiate failures.

For communication between peers think about what a transaction should contain. It is likely to contain the miner <em>M<sub>i </sub></em>initiating the transaction, the intended recipient <em>M<sub>j</sub></em>, and the current balances of all active miners. To be able to distinguish copies of the same transaction, you may want to include a transaction identifier.

The set of active miners grows and shrinks over time; you may define an upper bound on their number (say, 10). This can bound the size of your vector for implementing vector clocks.

<h2>4.4         Port Numbers</h2>

Both TCP and UDP use 16-bit integer port numbers to differentiate between processes. Both TCP and UDP define a group of well-known ports to identify well-known services. For example, every TCP/IP implementation that supports FTP assigns well-known port of 21 (decimal) to the FTP server.

Clients on the other hand, use ephemeral, or short-lived, ports. These port numbers are normally assigned to the client. Clients normally do not care about the value of the ephemeral port; the client just needs to be certain that the ephemeral port is unique on the client host.

RFC 1700 contains the list of port number assignments from the Internet Assigned Numbers Authority (IANA). The port numbers are divided into three ranges:

<ul>

 <li><em>Well-known ports: </em>0 through 1023. These port numbers are controlled and assigned by IANA. When possible the same port is assigned to a given server for both TCP and UDP. For example, port 80 is assigned for a Web server for both protocols, though all implementations currently use only TCP.</li>

 <li><em>Registered ports: </em>1024 through 49151. The upper limit of 49151 for these ports is new; RFC 1700 lists the upper range as 65535. These port numbers are not controlled by the IANA. As with well-known ports, the same port is assigned to a given service for both TCP and UDP.</li>

 <li><em>Dynamic </em>or <em>private ports: </em>49152 through 65535. The IANA dictates nothing about these ports. These are the ephemeral ports.</li>

</ul>

In this project, each group <em>G </em>is assigned a set of 1000 unique port numbers to use in the following range:

[1000 + (<em>G </em>× 1000)<em>,</em>1000 + (<em>G </em>× 1000) + 999]

<h1>5           Submission Requirements and Guideline for Deliverables</h1>

Using the submission link on Blackboard, submit a zip file<a href="#_ftn1" name="_ftnref1"><sup>[1]</sup></a> named Groupx.zip, where x is your group number, before 11:59pm of Sunday, 02/25/2018. This zip file should contain the following:

<ol>

 <li><strong>Design document (30%). </strong>Describe the design of your miner server and P2P Bitcoin application. Include a description your message format and all protocols you have implemented in your system. Include a pseudocode description of the miner server, P2P client, and P2P server. In addition, describe your data structures, design choices, and algorithms used. User documentation describing how to compile and run your program must be provided.</li>

 <li><strong>Code, demo, and documentation (70%). </strong>Submit all source code implementing your miner server and P2P Bitcoin application. This will be downloaded, compiled, and run during the demo of your project. If you demo on the racks in BYENG 217 a bonus of 10% may be obtained.</li>

</ol>

<h2>5.1         Suggested Timeline</h2>

Here is a timeline you may consider following to complete this project on time:

<table width="549">

 <tbody>

  <tr>

   <td width="82">Date</td>

   <td width="467">Milestone</td>

  </tr>

  <tr>

   <td width="82">02/04/2018</td>

   <td width="467">Design message format between miner server and P2P client; test</td>

  </tr>

  <tr>

   <td width="82">02/11/2018</td>

   <td width="467">Design message format between P2P Bitcoin peers</td>

  </tr>

  <tr>

   <td width="82">02/18/2018</td>

   <td width="467">Implementation of vector clocks and transaction processing among P2P peers</td>

  </tr>

  <tr>

   <td width="82">02/25/2018</td>

   <td width="467">Continue to test and debug application</td>

  </tr>

 </tbody>

</table>

<h1>6           For Honours Project Credit</h1>

If you are interested in extending this project for honours credit I have some ideas. One is implementing the Bitcoin proof-of-work based on public and private keys. Another could involve using UDP instead of TCP for message transmission and coping with lost messages (using what you can deduce from vector clocks to request retransmission). I will also consider your own ideas. Please think about it and come talk to me.

<a href="#_ftnref1" name="_ftn1">[1]</a> <strong>Do not </strong>use any other archiving program except zip.