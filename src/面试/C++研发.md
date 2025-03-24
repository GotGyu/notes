包含以下岗位网络上可以搜到的面经：

1. 淘天-业务技术-C/C++研发
2. 阿里巴巴-终端-C/C++研发
3. 高德地图-C++开发

以及对业务相关的扩展问题

# C++八股

1. **三种智能指针**

   - `std::unique_ptr`

     - 特点
       - **独占所有权**：一个资源只能由一个 `std::unique_ptr` 拥有
       - **不可复制**：不能通过拷贝构造函数或赋值运算符复制 `std::unique_ptr`
       - **可移动**：可以通过 `std::move` 转移所有权
     - 使用场景：适用于需要独占资源所有权的场景，如动态分配的对象
     - 实现原理：

   - `std::shared_ptr`

     - 特点

       - **共享所有权**：多个 `std::shared_ptr` 可以共享同一个资源

       - **引用计数**：内部维护一个引用计数器，当引用计数为 0 时自动释放资源

       - **可复制**：可以通过拷贝构造函数或赋值运算符复制 `std::shared_ptr`

     - 使用场景：适用于需要共享资源所有权的场景，如多个对象共享同一个资源

     - 实现原理：

   - `std::weak_ptr`

     - **特点**
       - **弱引用**：不增加引用计数，不会影响资源的生命周期。
       - **解决循环引用**：用于打破 `std::shared_ptr` 的循环引用问题。
       - **需要转换为 `std::shared_ptr`**：通过 `lock()` 方法获取一个 `std::shared_ptr`
     - 使用场景：适用于需要观察资源但不拥有资源的场景，如缓存、观察者模式、

2. 智能指针的线程安全聊一下？

3. **`unique_ptr` 怎么赋值给另一个 `unique_ptr`？**

   - 只能通过**移动语义**将所有权转移来赋值

     ```c++
     std::unique_ptr<int> ptr1(new int(42));
     std::cout << "ptr1: " << *ptr1 << std::endl; // 输出 42
     
     // 将 ptr1 的所有权转移给 ptr2
     std::unique_ptr<int> ptr2 = std::move(ptr1);
     
     // ptr1 现在为空
     if (!ptr1) {
         std::cout << "ptr1 is now null" << std::endl;
     }
     
     // ptr2 现在拥有资源
     std::cout << "ptr2: " << *ptr2 << std::endl; // 输出 42
     ```

4. **`move` 的应用场景？**

   - 用于将对象的所有权从一个实例转移到另一个实例。它主要用于实现 **移动语义**，避免不必要的拷贝操作，从而提高性能

   - 应用场景：

     - 转移 `shard_ptr` 的所有权

     - 优化容器操作，避免拷贝构造，直接移动元素

       ```c++
       int main() {
           std::vector<std::string> vec;
           std::string str = "Hello";
       
           // 使用 std::move 将字符串移动到容器中
           vec.push_back(std::move(str));
       
           std::cout << "str after move: " << str << std::endl; // 输出空字符串
           std::cout << "vec[0]: " << vec[0] << std::endl; // 输出 Hello
       
           return 0;
       }
       ```

     - 实现移动构造函数和移动赋值运算符

     - 从函数返回局部对象

       ```c++
       std::string createString() {
           std::string str = "Hello, World!";
           return std::move(str); // 使用 std::move 返回
       }
       
       int main() {
           std::string result = createString();
           std::cout << result << std::endl; // 输出 Hello, World!
       
           return 0;
       }
       ```

     - 在需要传递或返回大型对象时，使用 `std::move` 避免深拷贝

   - **C++ 怎么保证线程安全**

     - 保证线程安全是确保多个线程并发访问共享资源时不会导致数据竞争或未定义行为的关键。标准库中的容器不是线程安全的
     - 
     - 使用互斥锁：线程同步，保护共享资源
     - 使用原子操作：简单的共享变量
     - 使用条件变量：用于线程间的通信，通常与互斥锁一起用
     - 使用无锁数据结构：通过原子操作实现线程安全，避免锁的开销

5. 右值&右值的作用？如果没有右值引用，怎么延长右值的生存期（常引用）

6. vector和list的区别？如果vector要加入的内容很多应该怎么做？push_back和emplace_back的区别？

7. 虚析构函数的必要性

8. C++内存泄漏如何判断

9. 内存数据存储位置

10. 模板类、模板函数

11. stl（vector/list...（应该按序列式和关联式分类说））

12. snprintf sprintf 

13. c++的多态底层怎么实现的（答虚函数，问底层，答虚指针）

14. `class A { void funv(){cout<<"hello"}}; A* a=nullptr;a->func()`有没有问题：

    没有。`func()` 在代码段静态编译，a没有实际对象只是没有this指针，dunc()里没有要用this访问成员变量，所以没问题

15. 析构函数virtual的意义？

16. new一个对象后调用free，有什么问题？

17. 为什么new和delete会导致资源消耗

18. unordered_map和map的区别？为什么一般都用map？

19. 禁用掉的构造函数或运算符去使用会报什么错？在哪个时期？

20. 几种类型转换的方式有什么区别

21. crtp特性

22. 模板元编程？比如让templae T这个T只能接收某一个基类如何去实现？

23. const用在哪？（常量，常成员函数，底层const）

24. const &amp; 传参为什么效率高？（不需要拷贝）

25. vector超出容量会怎样？（以1.5或者2倍扩容）扩容基数？

26. 虚函数表是类还是对象拥有的？（类）

27. 怎么获取虚函数表？（虚表指针）

28. map查询效率?unordered_map是哈希表，O(1)；map是红黑树，O(logn)

29. map的key是自定义的类，需要注意什么?类要实现<的重载，保证key可以比较（也可以是函数对象）

30. 遇到过内存泄露吗？这里以为问的是项目（傻），说没有，使用了智能指针（应该举小例子）

31. 到过内存越界吗？这里答错了，讲成栈溢出（举例数组下标越界就行）

32. 编程遇到错误怎么解决？用过GDB吗？用vscode断点调试，没用过

33. 红黑树？怎么插入和删除？只讲了下概念，插入只记得要左旋或者右旋

    

    

    

    

# 计算机网络基础

1. **计算机网络模型（OSI模型）**

   - 物理层：集线器、中继器、调制解调器
   - 数据链路层：网桥、交换机
   - 网络层：IP、ICMP、ARP、RARP、IPv6、路由器
   - 传输层：TCP、UDP、网关
   - 会话层
   - 表示层
   - 应用层：HTTP、HTTPS、FTP、SMTP、POP3、IMAP、DNS、SSH、SNMP、Telnet

2. **关于TCP协议和UDP协议**

   - 区别：
     - TCP：
       - 面向连接的、可靠的，确保数据包按顺序且无误地传送到接收方
       - 有流量控制、拥塞控制
     - UDP：
       - 无连接的、不可靠的，低延迟
       - 没有顺序保证或重传机制
       - 将数据分割成独立的数据报，带有一定信息，收到后再组装
       - 有简单的校验和机制
   - 应用场景：
     - TCP：适用于需要高可靠性和数据顺序保证的场景，如文件传输、电子邮件、远程登陆
     - UDP：适用于对传输速度要求较高、容忍一定数据丢失的场景，如视频流媒体、在线游戏、DNS查询
   - 在哪些协议中有用到：
     - TCP：[Telnet](https://zhida.zhihu.com/search?content_id=169112169&content_type=Article&match_order=1&q=Telnet&zd_token=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJ6aGlkYV9zZXJ2ZXIiLCJleHAiOjE3NDE5MzcwMDEsInEiOiJUZWxuZXQiLCJ6aGlkYV9zb3VyY2UiOiJlbnRpdHkiLCJjb250ZW50X2lkIjoxNjkxMTIxNjksImNvbnRlbnRfdHlwZSI6IkFydGljbGUiLCJtYXRjaF9vcmRlciI6MSwiemRfdG9rZW4iOm51bGx9.Gml07eGDbxa1jYaVJnEzfJtpRYWKdHsymc3SPDU94xM&zhida_source=entity)(远程登录)、[FTP](https://zhida.zhihu.com/search?content_id=169112169&content_type=Article&match_order=1&q=FTP&zd_token=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJ6aGlkYV9zZXJ2ZXIiLCJleHAiOjE3NDE5MzcwMDEsInEiOiJGVFAiLCJ6aGlkYV9zb3VyY2UiOiJlbnRpdHkiLCJjb250ZW50X2lkIjoxNjkxMTIxNjksImNvbnRlbnRfdHlwZSI6IkFydGljbGUiLCJtYXRjaF9vcmRlciI6MSwiemRfdG9rZW4iOm51bGx9.iHZkXQxdmu2OQ4OgzxCbFIqo7lwdSoapeMxibvKo0Xo&zhida_source=entity)(文件传输协议)、[SMTP](https://zhida.zhihu.com/search?content_id=169112169&content_type=Article&match_order=1&q=SMTP&zd_token=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJ6aGlkYV9zZXJ2ZXIiLCJleHAiOjE3NDE5MzcwMDEsInEiOiJTTVRQIiwiemhpZGFfc291cmNlIjoiZW50aXR5IiwiY29udGVudF9pZCI6MTY5MTEyMTY5LCJjb250ZW50X3R5cGUiOiJBcnRpY2xlIiwibWF0Y2hfb3JkZXIiOjEsInpkX3Rva2VuIjpudWxsfQ.il7Gezua-rDPO1Je3eM1wb_3lArMbaVGLPyPsi6jnEA&zhida_source=entity)(简单邮件传输协议)、HTTP、SSH
     - UDP：[NFS](https://zhida.zhihu.com/search?content_id=169112169&content_type=Article&match_order=1&q=NFS&zd_token=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJ6aGlkYV9zZXJ2ZXIiLCJleHAiOjE3NDE5MzcwMDEsInEiOiJORlMiLCJ6aGlkYV9zb3VyY2UiOiJlbnRpdHkiLCJjb250ZW50X2lkIjoxNjkxMTIxNjksImNvbnRlbnRfdHlwZSI6IkFydGljbGUiLCJtYXRjaF9vcmRlciI6MSwiemRfdG9rZW4iOm51bGx9._c_EheLfLeqLCL7fHxg7bxrYl7FuQnmFpdW2wwzJESw&zhida_source=entity)(网络文件系统)、[SNMP](https://zhida.zhihu.com/search?content_id=169112169&content_type=Article&match_order=1&q=SNMP&zd_token=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJ6aGlkYV9zZXJ2ZXIiLCJleHAiOjE3NDE5MzcwMDEsInEiOiJTTk1QIiwiemhpZGFfc291cmNlIjoiZW50aXR5IiwiY29udGVudF9pZCI6MTY5MTEyMTY5LCJjb250ZW50X3R5cGUiOiJBcnRpY2xlIiwibWF0Y2hfb3JkZXIiOjEsInpkX3Rva2VuIjpudWxsfQ.rLg3eyWT43bqE6XZSqj5MXMOtt9Xq0Dm6O4pP7GmWcI&zhida_source=entity)(简单网络管理系统)、[DNS](https://zhida.zhihu.com/search?content_id=169112169&content_type=Article&match_order=1&q=DNS&zd_token=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJ6aGlkYV9zZXJ2ZXIiLCJleHAiOjE3NDE5MzcwMDEsInEiOiJETlMiLCJ6aGlkYV9zb3VyY2UiOiJlbnRpdHkiLCJjb250ZW50X2lkIjoxNjkxMTIxNjksImNvbnRlbnRfdHlwZSI6IkFydGljbGUiLCJtYXRjaF9vcmRlciI6MSwiemRfdG9rZW4iOm51bGx9._FLGpV3NGLy7sTSLlD0qopQtufLfCx0RL-sm0rsgjrE&zhida_source=entity)(主域名称系统)、[TFTP](https://zhida.zhihu.com/search?content_id=169112169&content_type=Article&match_order=1&q=TFTP&zd_token=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJ6aGlkYV9zZXJ2ZXIiLCJleHAiOjE3NDE5MzcwMDEsInEiOiJURlRQIiwiemhpZGFfc291cmNlIjoiZW50aXR5IiwiY29udGVudF9pZCI6MTY5MTEyMTY5LCJjb250ZW50X3R5cGUiOiJBcnRpY2xlIiwibWF0Y2hfb3JkZXIiOjEsInpkX3Rva2VuIjpudWxsfQ.evs753NZcg7-JzU4J9zX8phKst5VHMRazegsuZKdJdY&zhida_source=entity)(通用文件传输协议)、NTP（网络时间协议）

3. **TCP三次握手详细描述，为什么要三次而不是两次**

   - 为了保证客户端和服务器端的可靠连接，TCP建立连接时**必须**要进行三次会话，也叫TCP三次握手，**目的是为了确认双方的接收能力和发送能力是否正常**
   - ![1741764800881](D:\WeChat\WeChat Files\wxid_4njvjhupxgbc22\FileStorage\Temp\1741764800881.png)
   - 最开始的时候客户端和服务器都是处于CLOSED关闭状态。主动打开连接的为客户端，被动打开连接的是服务器
   - TCP服务器进程先创建传输控制块TCB，时刻准备接受客户进程的连接请求，此时服务器就进入了 LISTEN 监听状态
   - **第一次握手**TCP客户进程也是先创建传输控制块TCB，然后向服务器发出连接请求报文，这是报文首部中的同部位SYN=1，同时选择一个初始序列号 seq=x ，此时，TCP客户端进程进入了 SYN-SENT 同步已发送状态
   - **第二次握手**TCP服务器收到请求报文后，如果同意连接，则会向客户端发出确认报文。确认报文中应该 ACK=1，SYN=1，确认号是ack=x+1，同时也要为自己初始化一个序列号 seq=y，此时，TCP服务器进程进入了 SYN-RCVD 同步收到状态
   - **第三次握手**TCP客户端收到确认后，还要向服务器给出确认。确认报文的ACK=1，ack=y+1，自己的序列号seq=x+1，此时，TCP连接建立，客户端进入ESTABLISHED已建立连接状态 触发三次握手
   - **为什么要有第三次？**主要原因是，防止已经失效的连接请求报文突然又传送到了服务器，从而产生错误。举例而言：客户端向服务器端发送的请求报文由于网络等原因滞留，未能发送到服务器端，此时连接请求报文失效，客户端会再次向服务器端发送请求报文，之后与服务器端建立连接，当连接释放后，由于网络通畅了，第一次客户端发送的请求报文又突然到达了服务器端，这条请求报文本该失效了，但此时服务器端误认为客户端又发送了一次连接请求，两次握手建立好连接，此时客户端忽略服务器端发来的确认，也不发送数据，造成不必要的错误和网络资源的浪费。如果采用三次握手的话，就算那条失效的报文发送到服务器端，服务器端确认并向客户端发送报文，但此时客户端不会发出确认，由于客户端没有确认，由于服务器端没有接收到确认，就会知道客户端没有请求连接。

4. **TCP四次挥手详细描述**

   - 断开TCP连接需要四次挥手
   - ![1741765297104](D:\WeChat\WeChat Files\wxid_4njvjhupxgbc22\FileStorage\Temp\1741765297104.png)
   - 数据传输完毕后，双方都可释放连接。最开始的时候，客户端和服务器都是处于ESTABLISHED状态，然后客户端主动关闭，服务器被动关闭
   - **第一次挥手** 客户端发出连接释放报文，并且停止发送数据。释放数据报文首部，FIN=1，其序列号为seq=u（等于前面已经传送过来的数据的最后一个字节的序号加1），此时，客户端进入FIN-WAIT-1（终止等待1）状态
   - **第二次挥手** 服务器端接收到连接释放报文后，发出确认报文，ACK=1，ack=u+1，并且带上自己的序列号seq=v，此时，服务端就进入了CLOSE-WAIT 关闭等待状态
   - **第三次挥手** 客户端接收到服务器端的确认请求后，客户端就会进入FIN-WAIT-2（终止等待2）状态，等待服务器发送连接释放报文，服务器将最后的数据发送完毕后，就向客户端发送连接释放报文，服务器就进入了LAST-ACK（最后确认）状态，等待客户端的确认
   - **第四次挥手** 客户端收到服务器的连接释放报文后，必须发出确认，ACK=1，ack=w+1，而自己的序列号是seq=u+1，此时，客户端就进入了TIME-WAIT（时间等待）状态，但此时TCP连接还未终止，必须要经过2MSL后（最长报文寿命），当客户端撤销相应的TCB后，客户端才会进入CLOSED关闭状态，服务器端接收到确认报文后，会立即进入CLOSED关闭状态，到这里TCP连接就断开了，四次挥手完成
   - **为什么客户端要等待2MSL？**主要原因是为了保证客户端发送的第一个ACK报文能到服务器，因为这个ACK报文可能丢失，并且2MSL是任何报文在网络上存在的最长时间，超过这个时间报文将被丢弃，这样新的连接中不会出现旧连接的请求报文

5. **IP 协议概述**

   - 所有的TCP、UDP、ICMP数据都以IP数据报格式传输
   - IP在传输数据包时，将数据报文分为若干分片进行传输，并在目标系统中进行重组
   - 不同链路类型规定有不同最大长度的链路层数据帧，称为链路层MTU，常见的MTU=1500，若IP报文长度大于转发接口的MTU，则会将数据报文分为若干分片进行传输
   - 对于不同的传输层协议，在IP层上需不需要进行分片也是不同的：
     - **对于TCP，尽量避免分片。**因为当在IP层进行了分片后，如果其中的某片数据丢失，则需对整个数据报进行重传。避免分层的方法是，在3次握手时协商一个MSS值，用来表示本段所能接收的最大长度的报文段
     - **对于UDP而言可以分片。**

6. **IP分片可能出现的问题**

   - **性能消耗**：分片和重组会消耗发送方、接收方一定的CPU等资源，且分片对接收方内存资源的消耗较多
   - **丢包导致重传**：如果某个分片报文在网络传输过程中丢失，那么接收方将无法完成重组，如果应用进程要求重传的话，发送方必须重传所有分片报文而不是仅重传被丢弃的那个分片报文，这种效率低下的重传行为会给端系统和网络资源带来额外的消耗
   - **分片攻击**：黑客构造的分片报文，但是不向接收方发送最后一个分片报文，导致接收方要为所有的分片报文分配内存空间，可由于最后一个分片报文永远不会达到，接收方的内存得不到及时的释放（接收方会启动一个分片重组的定时器，在一定时间内如果无法完成重组，将向发送方发送ICMP重组超时差错报文，，只要这种攻击的分片报文发送的足够多、足够快，很容易占满接收方内存，让接收方无内存资源处理正常的业务，从而达到DOS的攻击效果

7. **NAT网络地址转换**

   - NAT路由器内部会维护一个NAT表，进行 `本地ip：端口`到 `外部网络ip：端口` 的映射

8. **在浏览器地址栏输入一个URL后回车，背后会进行哪些技术步骤?**

   - 参考该链接：[经典面试题：在浏览器地址栏输入一个 URL 后回车，背后发生了什么-腾讯云开发者社区-腾讯云](https://cloud.tencent.com/developer/article/1793846)

   - DNS解析域名 -> IP地址
     - 搜索**浏览器的DNS缓存**，其中维护着一张域名与IP地址的对应表
     - 若没有命中，则继续搜索**操作系统的DNS缓存**
     - 若没有命中，则操作系统将域名发送至**本地域名服务器**，它查询自己的DNS缓存，成功则返回结果，此处是递归查询
     - 若没有命中，则本地域名服务器向上级域名服务器进行迭代查询，得到IP地址后返回给操作系统，同时将其放在缓存里：
       - 本地域名服务器向**根域名服务器**发起请求，根域名服务器是最高层次的，它并不会直接指明这个域名对应的 IP 地址，而是返回顶级域名服务器的地址，也就是说给本地域名服务器指明一条道路，让他去这里寻找答案
       - 本地域名服务器拿到这个**顶级域名服务器**的地址后，就向其发起请求，获取**权限域名服务器**的地址
       - 本地域名服务器根据权限域名服务器的地址向其发起请求，最终得到该域名对应的 IP 地址
     - 操作系统将IP地址返回给浏览器，同时也将其放在缓存里
     - DNS使用UDP协议，以上所有请求转发过程都是基于UDP
   - 建立TCP连接，发送封装好的HTTP请求报文
     - 先通过三次握手为浏览器和服务器之间建立可靠的连接
     - TCP 会将 HTTP 报文按序号分割成若干报文段并加上 TCP 首部，分别进行传输。接收方在收到这些报文段后，按照序号以原来的顺序重组 HTTP 报文
   - IP封装数据报
     - TCP在各阶段操作时，都是通过IP协议进行传输的，IP将数据添加IP首部封装成IP数据包再进行传输
     - IP数据报首部存有**源IP地址、目标IP地址**
   - ARP查询目的MAC地址
     - ARP协议将IP地址转化为MAC地址，每个主机都有一个ARP高速缓存，里面有本局域网上各主机和路由器的**IP地址到MAC地址的映射表**
   - 物理层在硬件之间传输
   - 服务器响应请求
     - 浏览器的 HTTP 请求报文通过 TCP 三次握手建立的连接通道被切分成若干报文段分别发送给服务器，服务器在收到这些报文段后，按照序号以原来的顺序重组 HTTP 请求报文。然后处理并返回一个 HTTP 响应。当然，HTTP 响应报文也要经过和 HTTP 请求报文一样的过程
   - 断开TCP连接、浏览器显示界面

9. 网络层的响应状态码

10. **http和https**

    - HTTP
      - 用于在web浏览器和服务器之间传递信息的协议
      - 默认工作在TCP80端口
      - 以明文方式发送内容，不提供加密
      - 页面响应更快、资源消耗更低
    - HTTPS
      - 由HTTP进行通信，利用SSL/TLS加密数据包
      - 主要用于身份认证、保护交换数据的隐私与完整性
      - 默认工作在TCP443端口
      - 需要到CA申请证书
    - **浏览器进行http请求的时候包含哪些头部**
      - 通用头部：适用于请求和响应，提供与消息本身相关的信息
      - 请求头部：用于传递客户端的详细信息、请求的上下文以及期望的响应格式
      - 实体头部：用于描述请求或响应的主体内容
      - GET请求包含1,2
      - POST请求包含1,2,3
      - PUT请求包含1,2,3
      - DELETE请求包含1,2

11. socket通信过程、客户端需要Bind吗

12. 讲讲为什么用select不用epoll？epoll比select好在哪？

    （答数据结构不同：红黑树加双向链表vs数组；答内核态用户态切换开销不一样）

13. websocket和socket的关系和区别？websocket是七层中的哪一层？（答应用层，不知道对不对），为什么不用socket而用websocket？

14. websocket和http的关系和区别？（答websocket能双向传输信息）又问http不能双向传输信息没？为什么你搭建的服务器不用http？

# OS

1. **进程间通信**
   - IPC 是指在不同进程之间传递数据或信号的机制
   - **管道**
     - 特点：
       - 半双工通信：数据只能单向流动。
       - 只能在具有亲缘关系的进程之间使用（如父子进程）。
       - 数据以字节流的形式传输。
     - 应用场景：
       - Linux命令（需查阅）
   - **命名管道**
     - 特点
       - 全双工通信：数据可以双向流动。
       - 可以在无关进程之间使用。
       - 通过文件系统中的命名管道文件进行通信。
     - 应用场景（需查阅）
   - **消息队列**
     - 特点
       - 通过消息队列传递结构化数据。
       - 消息可以按类型分类，支持优先级。
       - 消息队列独立于进程存在，进程终止后消息队列仍然存在。
   - **共享内存**
     - 特点
       - 多个进程共享同一块内存区域。
       - **通信速度最快**，但需要同步机制（如信号量）避免竞争。
       - 和mmap有什么区别（需查阅）
   - 信号量
     - 特点
       - 用于进程间的同步，控制对共享资源的访问。
       - 可以用于解决生产者-消费者问题。
   - 套接字socket
     - 特点
       - 支持网络通信，可以在不同主机上的进程之间通信。
       - 支持多种协议（如 TCP、UDP）
2. 进程、线程、协程的区别
3. 单线程+IO多路复用和多线程的区别
4. 操作系统进程切换过程
5. 用户态和内核态的区别？什么时候在用户态，什么时候在内核态，用户态什么时候切换到内核态，怎么返回用户态？
6. 多线程同步
7. Linux中时间片是按照进程去分的吗？

# 场景题

1. **主流电商用的是多进程还是多线程**
   - 通常采用多进程和多线程混合架构，前者用于任务隔离和横向扩展，后者用于高并发请求处理和 I/O 密集型任务
   - 多进程架构：
     - 应用场景：任务隔离、高可靠性、横向扩展
     - 优点：隔离性强、资源管理简单、适合CPU密集型任务
     - 缺点：IPC开销大、内存占用高
     - 典型应用：微服务架构、任务队列
   - 多线程架构：
     - 应用场景：高并发请求处理、共享内存频繁数据交换、I/O密集型任务
     - 优点：资源共享、开销低、适合I/O密集型任务
     - 缺点：线程安全问题、调试复杂、一个线程崩溃可能影响整个进程
     - 典型应用：web 服务器、数据库连接池、异步任务处理
2. 贪心和DP在实际应用中的选用