## Blog

### 算法

### 第一章 数组part01 



####  今日任务 

- 数组理论基础
- 704 二分查找
- 27 移除元素 



#### 数组理论基础

文章链接：[https://programmercarl.com/%E6%95%B0%E7%BB%84%E7%90%86%E8%AE%BA%E5%9F%BA%E7%A1%80.html](https://programmercarl.com/数组理论基础.html)

**题目建议**： 了解一下数组基础，以及数组的内存空间地址，数组也没那么简单。



#### 704 二分查找

**题目建议**： 大家今天能把 704.二分查找 彻底掌握就可以，至于 35.搜索插入位置 和 34. 在排序数组中查找元素的第一个和最后一个位置 ，如果有时间就去看一下，没时间可以先不看，二刷的时候在看。

先把 704写熟练，要**熟悉 根据 左闭右开，左闭右闭 两种区间规则 写出来的二分法**。

题目链接：https://leetcode.cn/problems/binary-search/

文章讲解：[https://programmercarl.com/0704.%E4%BA%8C%E5%88%86%E6%9F%A5%E6%89%BE.html](https://programmercarl.com/0704.二分查找.html)

视频讲解：https://www.bilibili.com/video/BV1fA4y1o715



根据题目，在有序数组中查找目标值，这是标准的二分查找题

唯一的难点就是如何确定循环的边界，是`lefet < right`还是`left <= right`，我没有想到的好的方法，就是根据给定的示例$[-1, 0, 3, 5, 9, 12]$，但是查找的目标换成了边界值12，如果是`<`，那么在走到9时，`left = mid + 1`就已经是12了，那么循环就会退出，也就是没有找到目标值，这显然是错的

```cpp
class Solution {
public:
    int search(vector<int>& nums, int target) {
        int left = 0, right = nums.size() - 1;
        while (left <= right) {
            int mid = left + ((right - left) >> 1) ;
            if (target == nums[mid]) return mid;
            if (target > nums[mid])
                left = mid + 1;
            else right = mid - 1;
        }
        return -1;
    }
};
```

在看来Carl哥的讲解后，本题有2个关注点，一个就是循环不变量，也就是上面在自己做题时遇到的循环边界的问题，这里循环的边界与上面的取值边界有关，我这里选取的是左闭右闭的区间，因此循环不变量是`<=`；第二个关注点，就是变换后的left和right，是取$mid$还是$mid-1$，这个也取决于一开始选定的边界问题



#### 27 移除元素

**题目建议**： 暴力的解法，可以锻炼一下我们的代码实现能力，建议先把暴力写法写一遍。 **双指针法 是本题的精髓，今日需要掌握**，至于拓展题目可以先不看。 

题目链接：https://leetcode.cn/problems/remove-element/ 

文章讲解：[https://programmercarl.com/0027.%E7%A7%BB%E9%99%A4%E5%85%83%E7%B4%A0.html](https://programmercarl.com/0027.移除元素.html)

视频讲解：https://www.bilibili.com/video/BV12A4y1Z7LP 



需要原地移除元素，那么就不能申请额外的空间，因此，使用快慢指针的思想，一个用于遍历（快指针），另一个用于表示新数组。快指针不停向前遍历，如何不等于`val`就记录到慢指针中，否则跳过（即不记录）

```cpp
class Solution {
public:
    int removeElement(vector<int>& nums, int val) {
        int i = 0, j = 0;
        for (i = 0, j = 0; j < nums.size(); ++ j) {
           if (nums[j] != val)
               nums[i++] = nums[j];
        }
        return i;
    }
};
```

移除元素，在CPP中有提供库函数`erase()`，移除的本质是覆盖。



#### 977 有序数组的平方

**题目建议**： 本题关键在于理解双指针思想 

题目链接：https://leetcode.cn/problems/squares-of-a-sorted-array/

文章讲解：[https://programmercarl.com/0977.%E6%9C%89%E5%BA%8F%E6%95%B0%E7%BB%84%E7%9A%84%E5%B9%B3%E6%96%B9.html](https://programmercarl.com/0977.有序数组的平方.html)

视频讲解： https://www.bilibili.com/video/BV1QB4y1D7ep 



只想到了最基本的思路，即根据提示，先进行平方，再排序，那时间复杂度就是排序的时间复杂度，我用了比较熟悉的选择排序

```cpp
class Solution {
public:
    vector<int> sortedSquares(vector<int>& nums) {
        for (int i = 0; i < nums.size(); ++i)
            nums[i] = nums[i] * nums[i];

        for (int i = 0; i < nums.size() - 1; ++i)
            for (int j = i + 1; j < nums.size(); ++j)
                if (nums[i] > nums[j])
                    swap(nums[i], nums[j]);

        return nums;
    }
};
```

使用双指针的思路来解决问题。在经过平方后，最小值一定是在中间，因此可以用双指针的思路，由两边不断向中间趋近，比较两边的平方值，取大的，然后移动。这样得到的是递减的数据，而题目要求是非递减，因此可以由尾部开始赋值

```cpp
class Solution {
public:
    vector<int> sortedSquares(vector<int>& nums) {
        int i = 0, j = nums.size() - 1, k = nums.size() - 1;
        vector<int> result = nums;
        while (i <= j) {
            if (nums[i] * nums[i] >= nums[j] * nums[j]) {
                result[k--] = nums[i] * nums[i];
                i++;
            } else {
                result[k--] = nums[j] * nums[j];
                j --;
            }
        }
        return result;
    }
};
```



### 八股文

#### OSI 模型和 TCP/IP 模型 

#### OSI 模型 

掌握程度：

- 理解 OSI 模型共有 7 层，能够说明是哪 7 层。

- 能够粗浅描述每一层的基本功能和职责

- 对于某些层，熟悉一些常见的协议，比如网络层的 IP，传输层的 TCP 和 UDP。

#### TCP/IP 模型 

掌握程度：

- 理解 TCP/IP 模型通常被描述为 4 层，能够清晰地描述网络模型的层次结构。

- 能够解释每一层的作用，知道 TCP/IP 模型的层次是如何与 OSI 模型相对应的。
- 能够举例说明每层使用的协议，对某些层次的深入理解，比如 TCP 的三次握手、四次挥手，IP 数据报的路由选择。

- 理解不同层次间的交互：了解数据是如何在这些层次之间流动的，以及每一层如何为数据的传输添加或移除其所需的头部信息。

参考学习资料：[小林coding:  TCP/IP 网络模型有哪几层？](https://xiaolincoding.com/network/1_base/tcp_ip_model.html)



why：为什么要有这两种模型？

对于不同设备之间的通信，只能通过网络通信的方式，而设备有很多种，因此需要一个**<u>通用的网络协议</u>**来兼容这些设备

why：为什么要分层？

分层，简而言之就是解耦合，或者说是模块化，让各层都专注于特定的功能，独立发展和更新，这样当某层出现问题时，可以对特定的层进行排查

==**每个层级都构建在其下方的层级之上，并为上方的层级提供服务。**==

**OSI七层模型**：（开放系统互联模型： Open Systems Interconnection Model）

- 物理层：传输比特

  负责物理介质上传输原始的比特流（如0/1），通过电缆、光纤或无线信号等。它定义了硬件设备的功能规范，如电压、电缆类型、传输速率等。常见的协议有：IEEE802.3

- 数据链路层：组装帧，节点间传输

  负责将物理层的原始比特流组装成帧，并提供节点到节点之间的可靠传输。并能够进行错误检测和纠正，控制数据帧的流量，管理链路的访问权限。常见的协议有：以太网（Ethernet）、ARP协议（地址解析协议）[MAC地址]、PPP协议(点对点协议)

- 网络层：路由选择，传输数据包

  负责数据的路径选择和数据包的转发，将数据从源设备传输到目标设备。通过路由选择来决定数据的最佳传输路径，进行数据包的分段和重组，处理逻辑地址（如IP地址）。常见的协议有：IP协议、ICMP协议、OSPF协议、RIP协议（后两者都是路由协议）

- 传输层：确保端到端的可靠传输

  负责端到端的数据传输和数据完整性控制，确保数据在网络中可靠地传输。提供流量控制、错误检测和数据重传机制，保证数据传输的完整性与顺序。常见的协议有：TCP（传输控制协议）、UDP（用户数据报协议）

- 会话层：管理会话

  负责管理和维护应用程序之间的会话，确保会话的建立、管理和终止。控制会话的开始和结束，管理双向通信，提供检查点和恢复机制。常见的协议有：**PPTP（Point-to-Point Tunneling Protocol）**：用于创建虚拟专用网络（VPN）的协议；**RPC（Remote Procedure Call）**：允许程序在网络上的另一台计算机上执行过程调用。

- 表示层：数据格式化和转换

  负责数据的格式化和转换，以确保数据能被应用层正确解释。处理数据的加密、解密、压缩和数据格式转换（如字符编码、数据序列化）。常见的协议有：**SSL/TLS（Secure Sockets Layer / Transport Layer Security）**：用于数据加密和身份验证，常用于HTTPS；**JPEG/GIF**：图像压缩和表示格式协议。

- 应用层：提供网络应用服务

  负责与用户的直接交互，提供各种应用程序服务，如文件传输、电子邮件、远程登录等。定义网络应用程序的通信方式和协议，为用户提供网络服务接口。常见的协议有：

  - **HTTP/HTTPS（Hypertext Transfer Protocol / Secure）**：用于万维网上的数据传输；
  - **FTP（File Transfer Protocol）**：用于文件传输，提供简单的文件上传和下载功能；
  - **DNS（Domain Name System）**：将域名解析为IP地址的协议。

**TCP/IP四层模型**

- 网络接口层：本地网络连接和数据帧传输（协议如Ethernet、PPP）

  负责在主机和网络之间传输数据，在本地网络上传输帧。它包含OSI模型中的物理层和数据链路层的功能。能够控制数据帧的发送和接收、管理MAC地址和本地链路的物理连接。常见的协议有：

  - **Ethernet（以太网）**：广泛用于局域网，定义了帧结构和传输方式。
  - **Wi-Fi**：用于无线网络通信，符合IEEE 802.11标准。
  - **PPP（点对点协议）**：用于点对点连接，特别是在拨号连接和广域网中。
  - **ARP（地址解析协议）**：将IP地址解析为MAC地址，以便在本地网络中传输数据。

- 网络层：路由和寻址，跨网络传输数据包（协议如IP、ICMP、BGP）

  负责数据包在网络中的路由和传输，确保数据能跨越不同的网络到达目标地址。相当于OSI模型中的网络层。主要负责数据包的寻址和路由选择，将数据包从源端传递到目标网络。常见的协议有：

  - **IP（Internet Protocol）**：定义数据包结构和地址，主要版本有IPv4和IPv6。
  - **ICMP（Internet Control Message Protocol）**：用于发送错误和状态信息，比如Ping命令基于ICMP。
  - **IGMP（Internet Group Management Protocol）**：用于管理多播组通信。
  - **RIP/OSPF/BGP**：路由协议，分别用于小型局域网、大型网络和跨自治系统的路由。

- 传输层：端到端传输控制，可靠性保证（协议如TCP、UDP）。

  提供端到端的数据传输，保证数据能够在主机之间完整、可靠地传输。对应OSI模型中的传输层。提供可靠（或不可靠）的传输，控制数据流量，进行错误检测和恢复。常见的协议有：

  - **TCP（Transmission Control Protocol）**：提供可靠的、面向连接的数据传输，确保数据顺序无误。适用于需要数据准确传输的应用（如HTTP、SMTP）。
  - **UDP（User Datagram Protocol）**：提供不可靠的、无连接的数据传输，不进行数据重传和排序，适合对实时性要求高的应用（如视频流、DNS查询）。

- 应用层：用户应用程序通信接口（协议如HTTP、FTP、DNS）

  直接为用户提供各种网络服务，涵盖了OSI模型中的会话层、表示层和应用层。能够为网络应用程序提供通信接口，包括文件传输、邮件、远程登录等。常见的协议有：

  - **HTTP/HTTPS（Hypertext Transfer Protocol / Secure）**：用于万维网的数据传输，HTTPS提供加密。
  - **FTP（File Transfer Protocol）**：用于文件传输，支持文件的上传和下载。
  - **SMTP（Simple Mail Transfer Protocol）**：用于电子邮件的发送。
  - **DNS（Domain Name System）**：将域名转换为IP地址，便于用户访问网站。
  - **Telnet/SSH**：远程登录协议，SSH提供加密功能确保安全性。



#### 从输入URL到页面展示发生了什么

本道题目是一个综合题目，需要对 HTTP、DNS 查询、TCP 三次握手四次挥手、浏览器渲染等概念有更深入的认识之后才能回答得更加完善深入。

掌握程度：

- 基本要求：理解浏览器和服务器之间的交互过程，能够清晰地描述从输入URL到页面显示的整个流程。

    - DNS解析:描述如何将域名转换为IP地址。

    - TCP连接建立: 解释TCP三次握手的过程。

    - HTTP请求: 说明浏览器如何构建HTTP请求，并发送到服务器。

    - 服务器处理请求: 描述服务器接收请求、处理请求并生成响应的过程。

    - HTTP响应: 说明服务器如何将响应发送回浏览器。

    - TCP连接关闭: 描述TCP四次挥手的过程。

    - 浏览器渲染页面: 描述浏览器如何解析HTML、CSS和JavaScript，以及构建DOM树和渲染树。

    - 页面显示: 解释浏览器如何将渲染树绘制到屏幕上。

- 进阶要求：对每个步骤的关键点有所了解，如DNS解析、TCP/IP连接、HTTP请求和响应、浏览器渲染等，引导面试官向具体的问题深入。

- 进阶要求：对页面加载性能优化有所了解，如减少DNS查询时间、使用CDN、压缩资源、利用缓存等。

参考学习资料：

- [技术蛋老师：【在浏览器输入URL回车之后发生了什么？】](https://www.bilibili.com/video/BV1s44y117vK/?share_source=copy_web&vd_source=9bb0aa9c2c3cc1b12ca6f343a55b4e80)
- [小林coding:  从输入URL到页面渲染发生了什么](https://xiaolincoding.com/network/1_base/what_happen_url.html)
- [面试示范：【面试官：请说一下浏览器从输入URL 到页面展示这个过程中都经历了什么？你能答出来吗？】 ](https://www.bilibili.com/video/BV1QG4y1Y7vj/?share_source=copy_web&vd_source=9bb0aa9c2c3cc1b12ca6f343a55b4e80)
- [视频：【2分钟了解输入URL到页面呈现发生了什么？构建前端知识体系】 ](https://www.bilibili.com/video/BV1L8411g7Xp/?share_source=copy_web&vd_source=9bb0aa9c2c3cc1b12ca6f343a55b4e80)



1. URL补全

   URL —— 统一资源定位符，也叫网址，浏览器会判断输入的内容是否是合法的URL连接，如果合法，判断输入的URL是否完整，不完整则会对地址进行猜测补全前后缀；如果不合法，则将内容作为搜索条件，使用默认的搜索引擎进行搜索；大部分浏览器会从历史记录、书签等进行查找并给出内容提示

2. DNS解析：将域名转换为IP地址

   当浏览器接收到用户输入的URL（如`https://www.example.com`）时，首先需要将域名`example.com`转换为IP地址，以找到相应的服务器。

   - 浏览器先查找本地缓存中是否有该域名的IP地址，如果有，则使用缓存的IP地址。

   - 如果本地没有缓存，则会查询操作系统的DNS缓存。

   - 如果依旧没有找到，操作系统会向配置的DNS服务器发送请求，通过一系列的迭代查询，DNS服务器最终会找到该域名对应的IP地址并返回。

   - 这样，浏览器得到了服务器的IP地址（如`192.0.2.1`）。

     DNS解析时会按本地浏览器缓存->本地Host文件->路由器缓存->DNS服务器->根DNS服务器的顺序查询域名对应IP，直到找到为止

3. TCP连接建立：TCP的3次握手

   在获得服务器的IP地址后，浏览器和服务器之间通过TCP协议建立连接，这一过程称为“三次握手”：

   - **第一次握手**：客户端向服务器发送一个SYN（同步）报文，用于请求建立连接，同时指定一个初始序列号（如`seq=x`）。
   - **第二次握手**：服务器收到SYN后，返回一个SYN+ACK（确认）报文，确认收到客户端的请求，同时指定自己的序列号（如`seq=y`，`ack=x+1`）。
   - **第三次握手**：客户端收到服务器的SYN+ACK后，返回一个ACK（确认）报文（`ack=y+1`），表示连接建立完成。

   完成三次握手后，TCP连接建立，客户端和服务器可以开始数据传输。

4. HTTP请求：构建并发送请求

   在建立TCP连接后，浏览器会构建一个HTTP请求，将其发送到服务器。

   - 浏览器根据URL的协议（如HTTP或HTTPS）、主机名、请求路径和资源等信息构建HTTP请求报文。

   - 请求报文包含**请求行**（如GET或POST方法、请求的路径）、**请求头**（包含浏览器类型、语言、Cookie等）、**请求体**（在POST请求中用于包含数据）。

   - 请求报文构建完成后，通过TCP连接发送到服务器。

   - 如果是HTTPS的话，还要建立TLS连接，涉及到HTTPS的加解密流程。

5. 服务器处理响应：生成HTTP响应

   服务器接收到浏览器发来的HTTP请求后，开始处理请求并生成相应的响应。

   - 服务器根据请求报文中的URL路径来查找对应的资源文件（如HTML页面、图像、视频等），或将请求转发给后端应用程序。
   - 后端程序处理请求，可能会查询数据库、执行业务逻辑等操作，并生成响应数据。
   - 服务器将生成的响应数据封装为HTTP响应报文，并添加响应头（如内容类型、缓存指令、状态码等），准备返回给客户端。

6. HTTP响应：发送响应数据回浏览器

   服务器将响应报文发送回浏览器：

   - 响应报文包括**状态行**（如200 OK表示成功）、**响应头**（如Content-Type指定内容类型）、**响应体**（即网页内容）。
   - 响应体可能包含HTML、CSS、JavaScript等文件，浏览器会根据内容逐步解析页面。

7. TCP连接关闭：TCP的4次挥手

   在完成数据传输后，TCP连接需要关闭，采用四次挥手完成：

   - **第一次挥手**：客户端发送FIN（终止）报文，表示不再发送数据。
   - **第二次挥手**：服务器收到FIN后，返回ACK（确认）报文，确认客户端请求关闭连接。
   - **第三次挥手**：服务器发送FIN报文，表示数据发送完毕，并请求关闭连接。
   - **第四次挥手**：客户端收到FIN后，返回ACK报文，确认关闭连接。完成后，双方都进入关闭状态。

8. 浏览器渲染页面：解析HTML、CSS、JS

   浏览器会先解析响应头。若响应头状态码为301、302，会重定向到新地址；

   若响应数据类型是字节流类型，一般会将请求提交给下载管理器；

   若是HTML类型，则浏览器开始解析接收到的HTML、CSS和JavaScript，构建页面：

   - **HTML解析**：浏览器将HTML代码解析成**DOM树**（Document Object Model），每个HTML标签成为DOM树中的节点。
   - **CSS解析**：浏览器将CSS解析成**CSSOM树**（CSS Object Model），表示元素的样式信息。
   - **JavaScript执行**：在解析HTML和CSS的同时，JavaScript代码也会执行，JavaScript可以操作DOM和CSSOM树，进一步影响页面内容和样式。

9. 页面显示：构建渲染树并绘制到屏幕

   - 浏览器将DOM树和CSSOM树组合成**渲染树**，只包含页面可见的内容。
   - **布局**：浏览器根据渲染树计算元素的大小和位置。
   - **绘制**：浏览器将渲染树内容绘制到屏幕上，完成页面的显示。

详细：https://www.bilibili.com/video/BV1QG4y1Y7vj/?share_source=copy_web&vd_source=9bb0aa9c2c3cc1b12ca6f343a55b4e80

https://xiaolincoding.com/network/1_base/what_happen_url.html



### 总结

（补打卡），双指针的思路非常巧妙，长见识.jpg，在有序数组的搜索、寻找子序列以及去重等问题中，可以考虑使用双指针解法

