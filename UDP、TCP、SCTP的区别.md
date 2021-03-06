# UDP、TCP、SCTP的区别
+ **UDP**(User Datagram Protocol,用户数据报协议)：简单的、不可靠的、无连接的、提示消息边界的数据报协议
+ **TCP**(Transmission Control Protocol,传输控制协议)：复杂的、可靠的、面向连接的、无消息边界的字节流协议
+ **SCTP**(Stream Control Transmission Protocol,流控制传输协议)：可靠的、面向连接的，流控制传输协议，提供消息边界、传输级别多宿支持以及线头阻塞减少到最少的一种协议。

## 用户数据报(UDP)
1. **不可靠**：不保证UDP数据包会到达最终目的地，不保证各个数据包的先后顺序跨网络后保持不变，也不保证每个数据包自到达一次；如果一个数据包到达最终目的地，但校验和检测发现有错误，或者数据包在网络传输中被丢弃了，也无法投递给UDP套接字，也不会被源端重传。
2. **提供消息边界**：每个数据包都有一个长度。如果一个数据包正确到达目的地，那么该数据包的长度将随数据一道传递给接收端应用进程。
3. **提供无连接服务**：比如，一个UDP客户可以创建一个套接字并发送一个数据报给一个给定服务器，然后立即用同一个套接字发送另一个数据报给另一个服务器。同样的，一个UDP服务器可以用同一个UDP套接字从若干个不同的客户端接收数据报，没个客户一个数据报。
## 传输控制协议(TCP)
1. **面向连接**：TCP客户先与某个给定的服务器建立一个连接，在跨该连接与那个服务器交换数据。
2. **可靠的**：当TCP向另一端发送数据时，它要求对端返回一个确认。如果没有收到确认，TCP就自动重传数据并等待更长的时间。在数次重传失败后，TCP才放弃，如此尝试发送数据上所花的总时间一般为4-10分钟(依赖具体实现)。
3. 含有用于动态估算客户和服务器之间的往返时间的算法。
4. 通过给其中每个分节关联一个序号对其所发送的数据进行排序。
5. 提供流量控制，总是告知对端在任何时刻它一次能够从对端接收多少字节数据，这称为**通过窗口**.
6. 连接是全双工的，建立连接后也可以把它转换称为一个单工连接。
==**注意**：TCP并不保证数据一定会被对方端点接收，可能会因为一些物理原因等，**TCP提供的是数据的可靠递送或故障的可靠通知。**==
## 流控制传输协议(SCTP)
1. 在客户和服务器之间提供关联，并像TCP那样给应用提供可靠性、排序、流量控制以及全双工的数据发送。
2. SCTP中使用“关联”取代“连接”，一个连接只涉及两个IP地址之间的通信，一个关联指代两个系统之间的一次通信，它可能因为SCTP支持多宿而设计不止两个地址。
3. 与TCP不同的是，SCTP是面向消息的。他提供各个记录的按序递送服务。
4. 与UDP一样，有发送端写入的每条记录的长度随数据一道传递给接收端应用。
5. 能够在所连接的端点之间提供多个流，每个流各自可靠的按序递送消息。
6. 提供多宿性，使得单个SCTP端点能够支持多个IP地址。
