# 

# 计算机网络

# 计算机网络体系结构

![network-structure.png](https://github.com/Realee007/jobbook/blob/master/src/image/network-structure.png?raw=true) 

 **五层协议**

- **应用层** ：为特定应用程序提供数据传输服务，例如 HTTP、DNS 等。数据单位为报文。

- **运输层** ：提供的是进程间的通用数据传输服务。由于应用层协议很多，定义通用的运输层协议就可以支持不断增多的应用层协议。运输层包括两种协议：传输控制协议 TCP，提供面向连接、可靠的数据传输服务，数据单位为报文段；用户数据报协议 UDP，提供无连接、尽最大努力的数据传输服务，数据单位为用户数据报。TCP 主要提供完整性服务，UDP 主要提供及时性服务。

- **网络层** ：为主机间提供数据传输服务，而运输层协议是为主机中的进程提供服务。网络层把运输层传递下来的报文段或者用户数据报封装成分组。

- **数据链路层** ：网络层针对的还是主机之间的数据传输服务，而主机之间可以有很多链路，链路层协议就是为同一链路的节点提供服务。数据链路层把网络层传来的分组封装成帧。

- **物理层** ：考虑的是怎样在传输媒体上传输数据比特流，而不是指具体的传输媒体。物理层的作用是尽可能屏蔽传输媒体和通信手段的差异，使数据链路层感觉不到这些差异。

  注：五层协议的体系结构只是为介绍网络原理而设计，实际应用以TCP/IP四层体系结构为主。

## OSI七层模型

	数据从应用层发下来，会在每一层都加上头部信息，进行封装，然后再发生到数据接收端。这个基本流程就是数据都会经过数据的封装和解封装过程。 

![data transport from osi 7.png](https://github.com/Realee007/jobbook/blob/master/src/image/data%20transport%20from%20osi%207.png?raw=true) 

在OSI七层模型中，每一层的作用和对应的协议如下: 

![osi seven.png](https://github.com/Realee007/jobbook/blob/master/src/image/osi%20seven.png?raw=true) 

## TCP/IP

 它只有四层，对于五层协议中，将数据链路层和物理层合并为网络接口层。

TCP/IP协议族的特点是：上下两头大而中间小。这种很像**沙漏**计时器形状。即TCP/IP协议可以为各式各样的应用提供服务(所谓everything over IP)，同时TCP/IP协议也允许IP协议在各式各样的网络构成的互联网上运行(IP over everything)。

![TCP_IP_1.png](https://github.com/Realee007/jobbook/blob/master/src/image/TCP_IP_1.png?raw=true) 



注：随着技术的发展，现在的TCP/IP体系结构不严格遵循OSI分层概念，应用层可能会直接使用IP层或网络接口。



# 一. 物理层

- 物理层的主要任务就是确定与传输媒体的接口有关的一些特性。
- 传输媒体分为两大类：

1. 导引型传输媒体（双绞线、同轴电缆或光纤）
2. 非导引型传输媒体（无线或红外）

- 通信方式：

1. 单向通信（单工通信）
2. 双向交替通信（半双工通信）
3. 双向同时通信（全双工通信）

# 二. 数据链路层

- 链路是从一个结点到相邻结点的一段物理线路，数据链路则是在链路的基础上增加了一些必要的硬件（如网络适配器）和软件（如协议的实现）
- 数据链路层使用信道：1. 点对点信道；2. 广播信道
- 目前数据链路层广泛使用了循环冗余检验（CRC）来检查比特差错。

## 交换机

交换式集线器(switch hub)常称为以太网交换机，我们俗称的交换机是以太网交换机。

交换机具有自学习能力，学习的是交换表的内容，交换表中存储着 MAC 地址到接口的映射。正是由于这种自学习能力，因此交换机是一种即插即可即用设备，不需要网络管理员手动配置交换表内容。

下图中，交换机有 4 个接口，主机 A 向主机 B 发送数据帧时，交换机把主机 A 到接口 1 的映射写入交换表中。为了发送数据帧到 B，先查交换表，此时没有主机 B 的表项，那么主机 A 就发送广播帧，主机 C 和主机 D 会丢弃该帧。主机 B 收下之后，查找交换表得到主机 A 映射的接口为 1，就发送数据帧到接口 1，同时交换机添加主机 B 到接口 3 的映射。

![hub.png](https://github.com/Realee007/jobbook/blob/master/src/image/hub.png?raw=true) 



# 三. 网络层

网络层的设计初衷是尽可能简单。（因为网络层的端系统是有智能的计算机）

网络层向上只提供简单灵活的、无连接的、**尽最大努力交付的数据报服务**。网络层不提供服务质量的承诺，进程之间通信的可靠性由运输层负责。

下面讨论网络层如何传递IP数据报：

与 IP 协议配套使用的还有三个协议：

- 地址解析协议 ARP（Address Resolution Protocol）

- 网际控制报文协议 ICMP（Internet Control Message Protocol）

- 网际组管理协议 IGMP（Internet Group Management Protocol）

  ![IP_and other.png](https://github.com/Realee007/jobbook/blob/master/src/image/IP_and%20other.png?raw=true) 

使用 IP 协议，以把异构的物理网络连接起来，使得在网络层看起来好像是一个统一的网络。

如下图，源主机H1把一个IP数据报发送给目的主机H2，其中不经过任何路由器为直接交付。在R1到R4之间的三个网络可以是任意类型的网络。

![ip team_tansport.png](https://github.com/Realee007/jobbook/blob/master/src/image/ip%20team_tansport.png?raw=true) 

## IP 地址编址方式

IP 地址的编址方式经历了三个历史阶段：

- 分类
- 子网划分
- 无分类

### 1. 分类

由两部分组成，网络号和主机号，其中不同分类具有不同的网络号长度，并且是固定的。

IP 地址 ::= {< 网络号 >, < 主机号 >}

![ip-1-classfiy.png](https://github.com/Realee007/jobbook/blob/master/src/image/ip-1-classfiy.png?raw=true) 

### 2. 子网划分

通过在主机号字段中拿一部分作为子网号，把两级 IP 地址划分为三级 IP 地址。注意，外部网络看不到子网的存在。

IP 地址 ::= {< 网络号 >, < 子网号 >, < 主机号 >}

要使用子网，必须配置子网掩码。一个 B 类地址的默认子网掩码为 255.255.0.0，如果 B 类地址的子网占两个比特，那么子网掩码为 11111111 11111111 11000000 00000000，也就是 255.255.192.0。

### 3. 无分类

无分类编址 CIDR 消除了传统 A 类、B 类和 C 类地址以及划分子网的概念，使用网络前缀和主机号来对 IP 地址进行编码，网络前缀的长度可以根据需要变化。

IP 地址 ::= {< 网络前缀号 >, < 主机号 >}

CIDR 的记法上采用在 IP 地址后面加上网络前缀长度的方法，例如 128.14.35.7/20 表示前 20 位为网络前缀。

CIDR 的地址掩码可以继续称为子网掩码，子网掩码首 1 长度为网络前缀的长度。

一个 CIDR 地址块中有很多地址，一个 CIDR 表示的网络就可以表示原来的很多个网络，并且在路由表中只需要一个路由就可以代替原来的多个路由，减少了路由表项的数量。把这种通过使用网络前缀来减少路由表项的方式称为路由聚合，也称为  **构成超网** 。

在路由表中的项目由“网络前缀”和“下一跳地址”组成，在查找时可能会得到不止一个匹配结果，应当采用最长前缀匹配来确定应该匹配哪一个。

## 地址解析协议 ARP

网络层实现主机之间的通信，而链路层实现具体每段链路之间的通信。因此在通信过程中，IP 数据报的源地址和目的地址始终不变，而 MAC 地址随着链路的改变而改变。

![ip-a-networkInit1.jpg](https://github.com/Realee007/jobbook/blob/master/src/image/ip-a-networkInit1.jpg?raw=true) 

ARP 实现由 IP 地址得到 MAC 地址。

![ARP.jpg](https://github.com/Realee007/jobbook/blob/master/src/image/ARP.jpg?raw=true) 

每个主机都有一个 ARP 高速缓存，里面有本局域网上的各主机和路由器的 IP 地址到硬件地址的映射表。

如果主机 A 知道主机 B 的 IP 地址，但是 ARP 高速缓存中没有该 IP 地址到 MAC 地址的映射，此时主机 A 通过广播的方式发送 ARP 请求分组，主机 B 收到该请求后会发送 ARP 响应分组给主机 A 告知其 MAC 地址，随后主机 A 向其高速缓存中写入主机 B 的 IP 地址到 MAC 地址的映射。

![ARP0.png](https://github.com/Realee007/jobbook/blob/master/src/image/ARP0.png?raw=true) 

## 网际控制报文协议 ICMP

ICMP 是为了更有效地转发 IP 数据报和提高交付成功的机会。它封装在 IP 数据报中，但是不属于高层协议。

![icmp.jpg](https://github.com/Realee007/jobbook/blob/master/src/image/icmp.jpg?raw=true) ICMP 报文分为差错报告报文和询问报文。

### 1. Ping

Ping 是 ICMP 的一个重要应用，主要用来测试两台主机之间的连通性。

Ping 发送的 IP 数据报封装的是无法交付的 UDP 用户数据报。

### 2. Traceroute

Traceroute 是 ICMP 的另一个应用，用来跟踪一个分组从源点到终点的路径。

- 源主机向目的主机发送一连串的 IP 数据报。第一个数据报 P1 的生存时间 TTL 设置为 1，当 P1 到达路径上的第一个路由器 R1 时，R1 收下它并把 TTL 减 1，此时 TTL 等于 0，R1 就把 P1 丢弃，并向源主机发送一个 ICMP 时间超过差错报告报文；
- 源主机接着发送第二个数据报 P2，并把 TTL 设置为 2。P2 先到达 R1，R1 收下后把 TTL 减 1 再转发给 R2，R2 收下后也把 TTL 减 1，由于此时 TTL 等于 0，R2 就丢弃 P2，并向源主机发送一个 ICMP 时间超过差错报文。
- 不断执行这样的步骤，直到最后一个数据报刚刚到达目的主机，主机不转发数据报，也不把 TTL 值减 1。但是因为数据报封装的是无法交付的 UDP，因此目的主机要向源主机发送 ICMP 终点不可达差错报告报文。
- 之后源主机知道了到达目的主机所经过的路由器 IP 地址以及到达每个路由器的往返时间。

## IP 数据报格式



![ip data.png](https://github.com/Realee007/jobbook/blob/master/src/image/ip%20data.png?raw=true) 

- **版本**  : 有 4（IPv4）和 6（IPv6）两个值；
- **首部长度**  : 占 4 位，因此最大值为 15。值为 1 表示的是 1 个 32 位字的长度，也就是 4 字节。因为首部固定长度为 20 字节，因此该值最小为 5。如果可选字段的长度不是 4 字节的整数倍，就用尾部的填充部分来填充。
- **区分服务**  : 用来获得更好的服务，一般情况下不使用。
- **总长度**  : 包括首部长度和数据部分长度。
- **生存时间**  ：TTL（Time to Live），它的存在是为了防止无法交付的数据报在互联网中不断兜圈子。以路由器跳数为单位，当 TTL 为 0 时就丢弃数据报。
- **协议** ：指出携带的数据应该上交给哪个协议进行处理，例如 ICMP、TCP、UDP 等。
- **首部检验和** ：因为数据报每经过一个路由器，都要重新计算检验和，因此检验和不包含数据部分可以减少计算的工作量。
- **标识**  : 在数据报长度过长从而发生分片的情况下，相同数据报的不同分片具有相同的标识符。
- **片偏移**  : 和标识符一起，用于发生分片的情况。片偏移的单位为 8 字节。

# 四. 运输层

## TCP 与 UDP简要说明

*TCP* (Transmission Control Protocol)和*UDP*(User Datagram Protocol)协议

- 两者都是OSI模型中传输层的协议.
- TCP是全双工的可靠信道,UDP则是不可靠信道. 
- TCP的特性：属于面向连接的协议，需要三次握手与对方建立连接，即能为应用程序提供可靠的通信连接。断开连接需要四次挥手。 但是TCP也有一些缺点，如数据包和帧本身占用一些空间，并且面向连接的特性会有一些额外的通信量产生，速度较慢。比如文件传输就是使用的TCP协议. 
- UDP的特性：不属于连接型协议，因而具有资源消耗小，处理速度快的优点，所以通常音频、视频和普通数据在传送时使用UDP较多，因为它们即使偶尔丢失一两个数据包，也不会对接收结果产生太大影响。比如我们聊天用的QQ就是使用的UDP协议。 

## TCP首部格式

![TCP head.png](https://github.com/Realee007/jobbook/blob/master/src/image/TCP%20head.png?raw=true)

- **序号**  ：用于对字节流进行编号，例如序号为 301，表示第一个字节的编号为 301，如果携带的数据长度为 100 字节，那么下一个报文段的序号应为 401。
- **确认号**  ：期望收到的下一个报文段的序号。例如 B 正确收到 A 发送来的一个报文段，序号为 501，携带的数据长度为 200 字节，因此 B 期望下一个报文段的序号为 701，B 发送给 A 的确认报文段中确认号就为 701。
- **数据偏移**  ：指的是数据部分距离报文段起始处的偏移量，实际上指的是首部的长度。
- **确认 ACK**  ：当 ACK=1 时确认号字段有效，否则无效。TCP 规定，在连接建立后所有传送的报文段都必须把 ACK 置 1。
- **同步 SYN**  ：在连接建立时用来同步序号。当 SYN=1，ACK=0 时表示这是一个连接请求报文段。若对方同意建立连接，则响应报文中 SYN=1，ACK=1。
- **终止 FIN**  ：用来释放一个连接，当 FIN=1 时，表示此报文段的发送方的数据已发送完毕，并要求释放连接。
- **窗口**  ：窗口值作为接收方让发送方设置其发送窗口的依据。之所以要有这个限制，是因为接收方的数据缓存空间是有限的。 

## UDP首部格式

![UDP_head.png](https://github.com/Realee007/jobbook/blob/master/src/image/UDP_head.png?raw=true) 

首部字段只有 8 个字节，包括源端口、目的端口、长度、检验和。12 字节的伪首部是为了计算检验和临时添加的。



## TCP三次握手

客户端A，服务端B。 **A主动打开连接，而B被动打开连接**。

![TCP three.png](https://github.com/Realee007/jobbook/blob/master/src/image/TCP%20three.png?raw=true) 

1. 建立连接，A创建**传输控制模块TCB**，向B发出连接请求报文段，首部同步位SYN=1，同时选择一个初始序号seq =x。然后A进入SYN-SENT(同步已发送)状态。

2. B收到连接请求后，会向A发送确认报文段，(在报文段中)把SYN位和ACK位都置1，确认号ack = x+1,同时也为自己选择一个初始序号seq =y。这时B进入SYN-RCVD(同步收到)状态。

3. A收到B的确认后，还要向B给出确认。确认报文段的ACK置1，确认号ack = y+1，而自己的序号seq = x+1，发送完毕后，TCP连接已建立，A进入ESTABLISHED(已建立连接)状态。

   B收到A的确认后，也进入ESTABLISHED(已建立连接)状态。

   为什么A还要发送一次确认呢？ 主要是为了防止已失效的连接请求报文段突然又传送到 了B,因而出错。

   > 所谓“已失效的连接请求报文段”是这样产生的： 
   >
   > 	即A发出的第一个连接请求报文段并没有丢失，而是在某些网络节点长时间滞留了，以致延误到连接释放以后的某个时间才能到达B。 本来这是一个早已失效的报文段。   但B收到此失效的连接请求报文段后，就误认为A又发出了一次新的连接请求。    于是又向A发出确认报文段，同意建立连接。    假定不采用三次握手，那么只要B发出确认，新的连接就建立了。  由于现在A并没有发出建立连接的请求，因此不会理财B的确认，也不会向B发送数据。 但B却以为新的运输连接已经建立了，并一直等待A发来数据。 B的许多资源就这样浪费了。
   >
   > 　　采用三次握手的方法可以防止上述异常现象的发生。


## TCP四次挥手

客户端A，服务端B，数据传输结束后，通信的**双方都可释放连接**。现在A和B都处于ESTABLISHED状态。

![TCP four.png](https://github.com/Realee007/jobbook/blob/master/src/image/TCP%20four.png?raw=true) 

1. 假定A主动关闭TCP连接。A把连接释放报文段首部的终止控制位FIN置为1，其序号seq = u，它等于前面已传送过的数据的最后一个字节的序号加1。这时A进入FIN-WAIT-1(终止等待1)状态，等待B的确认。

2. B收到连接释放报文段后即发出确认，确认号ack= u+1，而这个报文段序号是v，等于前面已传送过的数据的最后一个字节的序号加1。然后B就进入CLOSE-WAIT(关闭等待)状态。A收到B的确认后就进入FIN-WAIT-2（终止等待2）状态， 此时A到B方向的连接就释放，但B到A方向的连接未释放（若B发数据，A仍要接收）。

3. B若没有要向A发送数据，则发出连接释放报文段使FIN=1。假定B现在的序号为w，B还必须重复上次已发送过的确认号ack = u+1。这是B就进入LAST-ACK(最后确认)状态，等待A的确认。

4. A收到B的释放报文段后，再对此发出确认。在确认报文段中把ACK置为1，确认号ack = w+1，而自己的序号是seq = u+1。然后进入到TIME-WAIT(时间等待)状态。 必须经过时间等待计时器设置的时间2MSL后，A进入CLOSED状态。而B收到A的确认后就关闭连接。

   > A在TIME_WAIT等待2MSL的时间原因：
   >
   > 1. 为保证主机1发送的最后一个ACK报文段能够到达B。这个ACK报文段可能丢失。
   > 2. 防止“已失效的连接请求报文段”出现在本连接中。（这个时间可以使本连接持续的时间内所产生的所有报文段都从网络中消失）



TIME_WAIT状态引起的问题：

	如果一台处于TIME_WAIT状态下的连接相关联的主机崩溃，然后再MSL内重新启动，并且使用与主机崩溃之前处于TIME_WAIT状态的连接相同的IP地址和端口号，那么将会怎样处理呢？

	在上述情况下，该连接在主机崩溃之前产生的延迟报文段会被认为属于主机重启之后的新连接，而不是之前的连接。 为了重启之前连接，解决办法：

	在bind设置`SO_REUSEADDR`套接字选项。	

```c++
const int on=1;
setsockopt(listenfd,SOL_SOCKET,SO_REUSEADDR,&on,sizeof(on));
```

- SO_REUSEADDR选项

  它允许启动一个监听服务器并绑定其端口，即使以前建立的该端口的本地连接仍存在。

  1. 启动一个监听服务器；
  2. 连接请求到达，派生一个子进程来处理这个client；
  3. 监听服务器终止，但子进程继续为现有连接上的client提供服务；
  4. 重启监听服务器

  默认情况下，当监听服务器在步骤(4)中通过调用socket、bind和listen重新启动时，由于它试图捆绑一个现有连接（即正由早先派生的那个子进程处理着的连接）上的端口，从而bind调用会失败。但如果该服务器在socket和bind中间调用设置了SO_REUSEADDR选项，那么bind将成功。

## TCP重传机制

重传分为两种：超时重传和快速重传。 

**超时重传（RTO）**  

当一个包被发送后，就开启一个定时器，如果定时时间到了，还未收到能确认该发送包的应答包，就重传一份数据。注意收到的应答包可能是该包也可能是后面包的，但是只要能确认该包被收到就行。另外如果，是因为网络延时造成重传，则接受端收到重复数据包后丢弃该包。  

**快速重传**  

当如果发送端收到一个包的三次应答包后，立即重传，比超时重传更高效。 

## TCP流量控制

### TCP窗口

TCP在每个传输方向都有两个窗口，发送端窗口和接收端窗口，又因为TCP是全双工通信，因此有四个窗口。 

### 窗口滑动

一图胜千言，看下面的图。

![slider windows TCP.jpg](https://github.com/Realee007/jobbook/blob/master/src/image/slider%20windows%20TCP.jpg?raw=true) 

	简单解释下，发送和接受方都会维护一个数据帧的序列，这个序列被称作窗口。发送方的窗口大小由接受方确定，目的在于控制发送速度，以免接受方的缓存不够大，而导致溢出，同时控制流量也可以避免网络拥塞。下面图中的4,5,6号数据帧已经被发送出去，但是未收到关联的ACK，7,8,9帧则是等待发送。可以看出发送端的窗口大小为6，这是由接受端告知的（事实上必须考虑拥塞窗口cwnd，这里暂且考虑cwnd>rwnd）。此时如果发送端收到4号ACK，则窗口的左边缘向右收缩，窗口的右边缘则向右扩展，此时窗口就向前“滑动了”，即数据帧10也可以被发送。 

## TCP 拥塞控制

如果网络出现拥塞，分组将会丢失，此时发送方会继续重传，从而导致网络拥塞程度更高。因此当出现拥塞时，应当控制发送方的速率。这一点和流量控制很像，但是出发点不同。流量控制是为了让接收方能来得及接收，而拥塞控制是为了降低整个网络的拥塞程度。

<div align="center"> ![tcp cwnd-1.jpg](https://github.com/Realee007/jobbook/blob/master/src/image/tcp%20cwnd-1.jpg?raw=true)</div><br>

TCP 主要通过四种算法来进行拥塞控制：慢开始、拥塞避免、快重传、快恢复。

发送方需要维护一个叫做拥塞窗口（cwnd）的状态变量，注意拥塞窗口与发送方窗口的区别：拥塞窗口只是一个状态变量，实际决定发送方能发送多少数据的是发送方窗口。

为了便于讨论，做如下假设：

- 接收方有足够大的接收缓存，因此不会发生流量控制；
- 虽然 TCP 的窗口基于字节，但是这里设窗口的大小单位为报文段。

<div align="center"> ![tcp cwnd-2.png](https://github.com/Realee007/jobbook/blob/master/src/image/tcp%20cwnd-2.png?raw=true)  </div><br>

### 1. 慢开始与拥塞避免

发送的最初执行慢开始，令 cwnd=1，发送方只能发送 1 个报文段；当收到确认后，将 cwnd 加倍，因此之后发送方能够发送的报文段数量为：2、4、8 ...

注意到慢开始每个轮次都将 cwnd 加倍，这样会让 cwnd 增长速度非常快，从而使得发送方发送的速度增长速度过快，网络拥塞的可能也就更高。设置一个慢开始门限 ssthresh，当 cwnd >= ssthresh 时，进入拥塞避免，每个轮次只将 cwnd 加 1。

如果出现了超时，则令 ssthresh = cwnd/2，然后重新执行慢开始。

### 2. 快重传与快恢复

在接收方，要求每次接收到报文段都应该对最后一个已收到的有序报文段进行确认。例如已经接收到 M<sub>1</sub> 和 M<sub>2</sub>，此时收到 M<sub>4</sub>，应当发送对 M<sub>2</sub> 的确认。

在发送方，如果收到三个重复确认，那么可以知道下一个报文段丢失，此时执行快重传，立即重传下一个报文段。例如收到三个 M<sub>2</sub>，则 M<sub>3</sub> 丢失，立即重传 M<sub>3</sub>。

在这种情况下，只是丢失个别报文段，而不是网络拥塞。因此执行快恢复，令 ssthresh = cwnd/2 ，cwnd = ssthresh，注意到此时直接进入拥塞避免。慢开始和快恢复的快慢指的是 cwnd 的设定值，而不是 cwnd 的增长速率。慢开始 cwnd 设定为 1，而快恢复 cwnd 设定为 ssthresh。

<div align="center"> ![tcp quick resend.png](https://github.com/Realee007/jobbook/blob/master/src/image/tcp%20quick%20resend.png?raw=true)  </div><br>

## 长连接和短连接

**长连接：**client向server发起连接，server接受client连接，双方建立连接。client与server完成一次读写之后，它们之间的连接并不会主动关闭，后续的读写操作会继续使用这个连接。 

这种方式下由于通讯连接一直存在。此种方式常用于P2P通信。

**短连接**：  

连接->传输数据->关闭连接

HTTP是无状态的，浏览器和服务器每进行一次HTTP操作，就建立一次连接，但任务结束就中断连接。也可以这样说：短连接是指SOCKET连接后发送后接收完数据后马上断开连接。  

### 怎样维护长连接

**方法1：应用层自己实现的心跳包**  

	由应用程序自己发送心跳包来检测连接是否正常，大致的方法是：服务器在一个 Timer事件中定时向客户端发送一个短小精悍的数据包，然后启动一个低级别的线程，在该线程中不断检测客户端的回应， 如果在一定时间内没有收到客户端的回应，即认为客户端已经掉线；同样，如果客户端在一定时间内没 有收到服务器的心跳包，则认为连接不可用。 

**方法2：TCP的KeepAlive保活机制** 



# 五. 应用层

域名系统DNS(基于UDP)

WWW的应用层协议HTTP（基于TCP）

文件传输协议FTP（基于TCP的FTP和基于UDP的TFTP）

远程登录协议 TELNET(基于TCP)

简单邮件传输协议 SMTP(基于TCP)

## http VS https

HTTPS和HTTP的区别主要如下：

　　1、https协议需要到ca申请证书，一般免费证书较少，因而需要一定费用。

　　2、http是超文本传输协议，信息是明文传输，https则是具有安全性的ssl加密传输协议。

　　3、http和https使用的是完全不同的连接方式，用的端口也不一样，前者是80，后者是443。

　　4、http的连接很简单，是无状态的；HTTPS协议是由SSL+HTTP协议构建的可进行加密传输、身份认证的网络协议，比http协议安全。



# socket 编程

![socket.png](https://github.com/Realee007/jobbook/blob/master/src/image/socket.png?raw=true) 

##  具体过程

对于服务端：

1. 创建套接字

    `int serv_sock = socket(domain,type,protocol);`

   domain: 使用何种的地址类型 ：ipv4或ipv6，AF_INET(ipv4 protocol)、AF_INET6(ipv6 protocol).

   type: 设置通信的协议类型 ：数据流或数据报，SOCK_STREAM(TCP), SOCK_DGRAM(UDP)等.

   protocol: IP协议值， IPPROTO_TCP、IPPROTO_UDP 、IPPROTO_ICMP等

2. 将套接字与IP、端口绑定

   `bind(int serv_sock, const struct sockaddr* addr, socklen_t addrlen);`

3. 进入监听状态，等待用户发起请求.`listen(int serv_sock, int backlog)`

   服务器处于被动模式，backlog是连接请求队列的最大长度（当系统还没调用`accept()`时，能够等待多个连接的最大数值）。

4. 接收客户端请求， `int cli_sock = accept(int serv_sock, struct sockaddr *addr,socklen_t *addrlen);`

   accept函数接收客服端的地址和地址长度，成功后返回与客户端建立连接的socket描述符，注意这跟监听socket不同。 

5. 向客户端收发送数据`read()` `write()`

6. 关闭套接字`close()`

对于客户端：

1. 创建套接字 `int cli_sock = socket(AF_INET,SOCK_STREAM,0)`;

2. 向服务器（特定的IP、端口）发起请求 `connect(cli_sock,serv_addr,sizeof(serv_addr));`

   第一个参数是客户端的socket描述符 ，后面为服务端的地址和长度。

3. 向服务端收发数据`write(), read()`

4. 关闭套接字`close()`

## windows版实现

```c++
server.cpp
#include <iostream>
#include <winsock.h>

/*
Windows下的socket程序和Linux思路相同，细节处区别如下：

（1）Windows下的socket程序依赖Winsock.dll或ws2_32.dll，必须提前加载。DLL有两种加载方式。

（2）Linux使用“文件描述符”的概念，而Windows使用“文件句柄”的概念；Linux不区分socket文件和普通文件，而Windows区分；Linux下socket()函数的返回值为int类型，而Windows下为SOCKET类型，也就是句柄。

（3）Linux下使用read()/write()函数读写，而Windows下使用recv()/send()函数发送和接收

（4）关闭socket时，Linux使用close()函数，而Windows使用closesocket()函数。

*/

#pragma comment(lib,"ws2_32.lib")		//Winsock Library file ws2_32.lib
int main(int argc,char** argv)
{
	//初始化WSA 
	//加载Winsock库：
	WORD sockVersion = MAKEWORD(2, 2);
	WSADATA wsaData;
	if (WSAStartup(sockVersion,&wsaData)!=0)
	{
		return 0;
	}
	//创建套接字
	SOCKET ser_sock = socket(AF_INET, SOCK_STREAM, IPPROTO_TCP);		//TCP连接
	//SOCKET ser_sock = socket(AF_INET, SOCK_DGRAM, IPPROTO_UDP);		//UDP连接

	if (ser_sock == INVALID_SOCKET)
	{
		std::cout << "socket error!";
		return 0;
	}
	//绑定IP和端口号
	sockaddr_in serv_addr;
	serv_addr.sin_family = AF_INET;
	serv_addr.sin_port = htons(8888);
	serv_addr.sin_addr.S_un.S_addr = INADDR_ANY; //INADDR_ANY就是指定地址为0.0.0.0的地址，这个地址事实上表示不确定地址，或“所有地址”、“任意地址”。 一般来说，在各个系统中均定义成为0值。

	if (bind(ser_sock,(LPSOCKADDR)&serv_addr,sizeof(serv_addr))	== SOCKET_ERROR)
	{
		std::cout << "bind error";
	}
	//开始监听
	if (listen(ser_sock,5) == SOCKET_ERROR)
	{
		std::cout << "listen 错误!";
		return 0;
	}
	//循环接收数据
	SOCKET cli_sock;
	sockaddr_in cli_addr;
	int cli_addrlen = sizeof(cli_addr);
	char revData[255];
	while (true)
	{
		std::cout << "等待连接...\n";		//服务端：等待客户端接入
		cli_sock = accept(ser_sock, (SOCKADDR*)&cli_addr, &cli_addrlen);
		if (cli_sock == INVALID_SOCKET)
		{
			std::cout << "accept 错误!";
			continue;
		}
		std::cout << "接收到一个连接" << inet_ntoa(cli_addr.sin_addr)<<"\n";

		//接收数据
		int ret = recv(cli_sock, revData, 255, 0);
		if (ret>0)
		{
			revData[ret] = 0x00;
			std::cout << "接收到数据：\n";
			std::cout << revData<<"\n";
		}
		//发送数据
		const char* sendData = "你好哦，TCP客户端！\n";
		send(cli_sock, sendData, strlen(sendData), 0);
		closesocket(cli_sock);
	}
	closesocket(ser_sock);
	WSACleanup();		//释放winsock库
	return 0;
}
```



```c++
client.cpp
#include <WinSock2.h>
#include <iostream>
#include <string>

#pragma comment(lib,"ws2_32.lib")
int main()
{
	WORD sockVersion = MAKEWORD(2, 2);
	WSADATA data;
	if (WSAStartup(sockVersion, &data) != 0)
	{
		return 0;
	}
	while (true)
	{
		SOCKET cli_socket = socket(AF_INET, SOCK_STREAM, IPPROTO_TCP);		//TCP连接
		if (cli_socket == INVALID_SOCKET)
		{
			std::cout << "错误 socket!";
			return 0;
		}
		sockaddr_in serv_Addr;
		serv_Addr.sin_family = AF_INET;
		serv_Addr.sin_port = htons(8888);
		serv_Addr.sin_addr.S_un.S_addr = inet_addr("127.0.0.1");
		//客户端：请求与服务端连接
		if (connect(cli_socket,(sockaddr*)&serv_Addr,sizeof(serv_Addr)) == SOCKET_ERROR)
		{
			//连接失败
			std::cout << "连接失败";
			closesocket(cli_socket);
			return 0;
		}
		std::string data;
		std::cin >> data;
		const char* sendData;
		sendData = data.c_str();

		send(cli_socket, sendData, strlen(sendData), 0);

		char recData[255];
		int ret = recv(cli_socket, recData, 255, 0);
		if (ret > 0)
		{
			recData[ret] = 0x00;
			std::cout << recData;
		}
		closesocket(cli_socket);
	}
	WSACleanup();
	return  0;
}
```

## 可能出现的问题

-  Time_wait状态情况下产生地址和端口占用，怎么解决？

		socket 中的SO_REUSEADDR



# 从输入URL到页面加载发生了什么

- DNS 解析

  - DNS存在着多级缓存，从离浏览器的距离排序开始：浏览器缓存，系统缓存，路由器缓存，IPS服务器缓存，根域名服务器缓存，顶级域名服务器缓存，主域名服务器缓存。

- TCP连接

- 发送http请求

  - http请求报文由三部分组成：请求行，请求报头和请求正文。

- 服务器处理请求并返回http报文

  - http响应报文也由有三部分组成：状态码，响应报头和响应报文。

- 浏览器解析渲染页面

- 连接结束


# Cookie和Session

- cookie机制采用的是在客户端保持状态的方案，而session机制采用的是在服务器端保持状态的方案。
- Cookie
  - cookie的内容主要包括：名字、值、过期时间、路径和域。
  - 若不设置过期时间，则表示这个cookie的生命期为浏览器会话期间，关闭浏览器窗口，cookie就消失。
- Session
  - Session id借助Cookie发送回客户端
  - 若禁止了客户端Cookie
    - 把session id直接附加在URL路径后面。（URL重写）
    - 表单隐藏字段，服务器会自动修改表单，添加一个隐藏字段。

1. cookie数据存放在客户的浏览器上，session数据放在服务器上。
2. cookie不是很安全，别人可以分析存放在本地的cookie并进行cookie欺骗，考虑到安全应使用session。
3. session会在一定时间内保存在服务器上。当访问增多，会比较占用你服务器的性能，考虑到减轻服务器性能方面，应使用cookie。
4. 单个cookie保存等待数据不能超过4k，很多浏览器都限制一个站点最多保存20个cookie。
5. 建议： 将登陆信息等重要信息存放在session，其他信息如果需要保留，可以放在cookie。

# UNIX I/O 模型

## 1. 简述

UNIX系统将所有的外部设备都看作一个文件来看待。文件描述符(file descriptor)是内核为了高效管理已被打开的文件所创建的引用。文件描述符是一个非负整数，它指向内核中的一个结构体。当打开一个现有的文件或创建一个新文件时，内核向进程返回一个文件描述符。而对于一个socket的读写也会有相应的文件描述符，称为socketfd.

根据《UNIX网络编程卷1：套接字联网API》第6章给出的5种IO模型：

1. 阻塞IO - blocking IO
2. 非阻塞IO - nonblocking IO
3. IO多路复用 - IO-multiplexing
4. 信号驱动IO - signal driven IO
5. 异步IO - asynchronous IO

在UNIX中，一个输入操作通常包括两个不同的阶段：

1. 等待数据准备好；

2. 从内核向进程复制数据。

   对于一个套接字上的输入操作：

   第一步通常涉及等待数据从网络中到达。当所等待分组到达时，它被复制到内核中的某个缓冲区。

   第二步就是把数据从内核缓冲区复制到应用进程缓冲区。

## 2. I/O模型

### 1.阻塞式I/O模型

在默认情况下，所以套接字都是阻塞的。由于UDP数据准备好读取的概念相对于TCP简单，即要么整个数据报已经收到，要么还没有。所以以数据报套接字作为例子，如下图：

![io-blocking io.png](https://github.com/Realee007/jobbook/blob/master/src/image/io-blocking%20io.png?raw=true)

图中recvfrom函数视为系统调用，它会从在应用进程空间中运行切换到内核空间中运行，直到数据报到达且被复制到应用进程缓冲区中才返回。

### 2. 非阻塞式I/O模型

进程把一个套接字设置成非阻塞是指，在等I/O数据时，进程并不阻塞，如果数据还没准备好，则直接返回一个错误。

![io-noblocking io.png](https://github.com/Realee007/jobbook/blob/master/src/image/io-noblocking%20io.png?raw=true)



前三次调用都是立刻返回一个EWOULDBLOCK错误。	

当一个应用进程像这样对一个非阻塞描述符循环调用recvfrom时，我们称之为**轮询(polling)**.应用进程持续轮询内核，以查看某个操作是否就绪。这么做往往耗费大量CPU时间，通常这种模型比较少见。

### 3. I/O复用模型

I/O复用我们通常调用`select`或`poll`函数，阻塞在`select`或`poll`系统调用中的某一个之上，而不是阻塞在真正的I/O系统调用上。

![io-multiplexingio.png](https://github.com/Realee007/jobbook/blob/master/src/image/io-multiplexingio.png?raw=true)

我们阻塞于`select`调用，等待数据报套接字变为可读。当`select `返回套接字可读时，我们调用recvfrom把所读数据报复制到应用进程缓冲区。

比较3和1，I/O复用并不显得有什么优势，事实上由于使用`select`，我们额外需要一个系统调用不是之前的单个。 不过I/O复用模型的优势在于可以同时等待多个套接字描述符就绪。



### 4 信号驱动式I/O模型

我们也可以用信号，让内核在描述符就绪时发送SIGIO信号通知我们。我们称这种模型为信号驱动式I/O。

![io-signalio.png](https://github.com/Realee007/jobbook/blob/master/src/image/io-signalio.png?raw=true)

具体是用过sigaction系统调用安装一个信号处理函数。sigaction函数立即返回，我们的进程继续工作，即进程没有被阻塞。当数据报准备好时，内核会为该进程产生一个SIGIO信号，这样我们可以在信号处理函数中调用recvfrom读取数据报，并通知主循环数据已准备好待处理。

无论 如何处理SIGIO信号，这种模型的优势在于等待数据报到达期间不被阻塞。主循环可以继续执行，只要等待来自信号处理函数的通知。

### 5 异步I/O模型

异步I/O(asynchronous I/O)由POSIX规范定义。

异步I/O中，我们调用`aio_read`函数(POSIX异步I/O函数以`aio_`或`lio`开头)，告诉内核，当整个I/O操作完成后通知我们。该系统调用立即返回，而在等待I/O完成期间，应用进程不会被阻塞。当I/O完成（包括数据从内核复制到用户进程）后，内核会产生一个信号通知应用进程，应用进程对数据报进行处理。

![io-asyn io.png](https://github.com/Realee007/jobbook/blob/master/src/image/io-asyn%20io.png?raw=true)

异步I/O模型与信号驱动式I/O的区别在于：信号驱动IO是由内核通知我们何时可以启动一个I/O操作，而异步I/O模型是由内核通知我们I/O操作何时完成。



## 3. 5种I/O模型对比

![io-5.png](https://github.com/Realee007/jobbook/blob/master/src/image/io-5.png?raw=true)

从图可以看到，前四种I/O模型的主要区别在于第一个阶段，它们的第二个阶段是一样的：在数据从内核复制到应用进程的缓冲区期间，进程会被阻塞于recvfrom系统调用。  而异步I/O模型则是整个操作完成内核才通知应用进程。

## 4. 同步I/O和异步I/O

POSIX标准将同步I/O和异步I/O定义为：

- 同步I/O操作：导致请求进程阻塞，直到I/O操作完成。
- 异步I/O操作：不导致请求进程阻塞。

根据上述两个定义，本文介绍的前面四种模型，包括阻塞式I/O，非阻塞式I/O，I/O复用和信号驱动式I/O模型都是同步I/O模型，因为其中真正的I/O操作（recvfrom）将阻塞进程。只有异步I/O模型才符合POSIX标准的异步I/O定义。

## 5. 生活中的类比例子

以生活中钓鱼为例子（[参考](https://blog.csdn.net/historyasamirror/article/details/5778378)），来说明各种I/O模型的不同，例子中的等待鱼上钩对应于上文中的等待数据，拉竿操则作对应于上文的将数据从内核复制到用户空间。

有A，B，C，D，E五个人在钓鱼。 
A使用了最古老的鱼竿，所以开始钓鱼后，就一直守着，直接鱼上钩了再拉竿； 
B由于着急想知道有没鱼上钩，所以隔一会就看一次鱼竿看有没鱼上钩，直到看到鱼上钩后，再拉竿； 
C同时使用了N支鱼竿来钩鱼，然后等着，只要有其中一支鱼竿有鱼上钩，就将对应的鱼竿拉起来； 
D的鱼竿比较高级，当有鱼上钩后，会发出警报提示，所以D开始钓鱼后不用一直守着，一旦鱼竿发出警报，D再回来拉竿即可； 
E为了更省事，直接雇个佣人给他钓鱼，当佣人钓起鱼后，再通知E去取鱼即可。

# I/O 复用

select/poll/epoll 都是 I/O 多路复用的具体实现，select 出现的最早，之后是 poll，再是 epoll。