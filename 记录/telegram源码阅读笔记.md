# telegram源码阅读笔记

## 关键点

* 使用MTProto作为网络协议
* MTProto会合并一些发送的消息
* 消息id是通过服务器时间戳进行计算出来的。如果服务器意识到和客户端时间相差较大，会忽略掉客户端的消息。
* 消息打包：MTRequest会携带一个它要发送的消息数据，而RPC服务会存储多个Request一起发送出去。这样减少了网络传输次数并且提高了相应的及时性。
* 依赖处理：因为消息是打包发送的，所以就引出了一个时序问题，某些消息需要在另外一些消息之前进行处理。这样对并发处理的服务端有很好的提示，但是增加了客户端实现的复杂度。
* 超时管理：因为消息是延后发送的，所以这些消息会在真正发送的时候做超时检测，在收到相应之后也会再做检测。
* 错误处理：因为消息id是根据服务器的时间戳进行计算出来的，如果收到的消息小于上一次收到的消息，那么会重置当前session，并且抛弃掉本次的所有消息。
* 客户端与服务器之间只有一个连接，而且为长连接。若连接断开则会有个定时器重连。
* 消息会使用gzip重新再压缩一次，减少数据包的大小。
* 使用TLLanguage作为消息描述
* TLLanguage是一个类似于脚本语言的文本，可以执行函数、多态、条件判断、多态等。MTProto通过将TLLanguage进行序列化

## 一些uml

* MTProtoKit的核心原型类图 ![核心原型图](http://blog.makeex.com/images/2015/06/13/01.png)
* MTProtoKit消息处理序列图 ![消息处理序列图](http://blog.makeex.com/images/2015/06/13/02.png)
* 同步消息类图（因为消息id是由服务器时间戳算出来的 所以需要服务器时间做校准） ![同步消息类图](http://blog.makeex.com/images/2015/06/13/03.png)

## 参考资料

* 关于MTProto的一些介绍博客：[Telegram 之 MTProtoKit 架构分析](http://blog.makeex.com/2015/06/13/the-architecture-of-telegram-mtprotokit/)
* 关于TLLanguage的一些介绍博客：[Telegram 之 TL Language](http://blog.makeex.com/2015/06/14/the-tl-language-of-telegram/)
* 这个博客感觉不错 可以保存了解一下