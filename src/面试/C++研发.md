包含以下岗位网络上可以搜到的面经：

1. 淘天-业务技术-C/C++研发
2. 阿里巴巴-终端-C/C++研发

以及对业务相关的扩展问题

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