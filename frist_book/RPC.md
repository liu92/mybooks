#### RPC（Remote Procedure Call）概念

远程方法调用，这只是一个通常，重点在于方法调用（不支持对象的概念），具体实现甚至可以用RMI RestFul等去实现，但一般不用，因为RMI不能跨语言，而RestFul效率太低。

多用于服务器集群间的通信，因此常使用更加高效，短小精悍的传输模式以提高效率。

从单机走向分布式，产生了很多分布式通信方式

```
1:最古老也是最有效，并且永不过时的，TCP/UDP的二进制传输。事实上所有的通信方法归根结低都是TCP/UDP
2:CORBA(Common Object Request Brober Architecture)。古老而复杂，支持面向对象的通信协议
3:Web Service(SOA SOAP ) 基于http + xml 的标准化web+api
4:RestFul(Representational State Transfer) 回归简单化本源的web Api的事实标准
5:RMI(Remote Method Invocation) java内部的分布式通信协议
6:JMS(Java Message Service) javaEE中的消息框架标准，为很多MQ所支持
7:RPC (Remote Procedure Call)
```

RPC序列化框架

```
1:java.io.Serializable
2:Hessian
3:google protobuf
4:facebook Thrift
5:kyro
6:fst
7:json序列化框架 （jackson, google Gson, Ali FastJson）
8:xmlrpc(xstream)
```

RPC通信协议

```
http、http2.0(gRPc)、Tcp、webservice
```





