# Source Code Read Note --- etcd

## 代码结构

etcd这个名字本身就很容易令人混淆，etcd既可以指这个项目的名字，本意是像Linux /etc这样配置功能的一个distribution实现，也可以指可执行程序etcd这样一个后台服务进程。etcd这个项目为了方便用户使用，还可以集成在客户的程序中。如果客户仅仅是希望Raft的实现，etcd也提供了这么Raft的library给客户使用。所以，理清楚这些关系，才方便浏览代码。

- ./raft/ 这里面是最核心的Raft算法的实现。这里实现的功能不仅仅包括Leader election, Log replication, Membership changes这三个最基本的功能，还包括许多工程化中需要考虑的功能。[./raft/RAEADME.md](https://github.com/coreos/etcd/blob/master/raft/README.md#features)中有详细描述。一个好的工程化实现，才是连接算法和应用的桥梁。./raft/中并不包含任何和platform相关的实现，而是定义一些interface给客户的程序去实现。当然对于服务etcd，已经给予了一种实现，客户仅仅使用./raft/作为library来用，那么客户需要自己实现。这里平台相关的实现包括，如何收发消息，如何永久存储log，timer等常见的问题。
- ./mvcc/ key-value存储的实现，可以认为是Raft中的状态机。
- ./lease/ lease的一个实现。在工程中，为了解决Raft一致性读的问题，./raft/依赖lease来提供高效的读方法。
- ./wal/ Write Ahead Log的实现
- ./pkg/ 是服务进程etcd所需要的各种小工具的集合。
- ./etcdserver/ 这是对./raft第一层次的集成，把相关的平台实现以及etcd这个配置管理服务的功能整合起来。但是，这个时候还不能使用。
- ./embed/ 这里封装了./etcdserver/的接口，保证etcd可以被集成到客户程序中，而不是作为一个独立服务。当然也可以被./etcdmain/集成起来作为etcd这个独立的配置管理服务。
- ./proxy/ 这里实现了etcd的proxy服务，将外围的各种形式的request，转成etcd所能处理的格式。这个功能和etcd的配置管理功能是并行的。
- ./etcdmain ./main.go，这里封装了etcd这个可执行文件，这里会判断工作在服务模式还是proxy模式，前者会通过./embed/(偶尔也会直接使用./etcdserver/)来提供配置管理服务。后者会通过./proxy/来提供gateway的功能。
- ./client 提供了Go client library for etcd
- ./etcdctl 是client的命令行形式
## 源代码中名词
- Raft: Etcd所采用的保证分布式系统强一致性的算法。
- Node: Raft算法中server的一个实例，仅指代参与Raft算法部分。
- Member: 服务etcd的一个实例，包含一个Node，并且负责和client交互，也提供member之间的通信。
- Proxy: etcd的一个工作模式，为etcd cluster提供反向代理

其它的名词要么是Raft中所使用的，要么是字面意思比较清晰的。

## library


## etcd

- etcd/mvcc 应该是用来etcd Key-Value键值存储的存储引擎。 注意： 这个存储引擎，不等于Raft library中需要persistent存储，Raft需要的存储是用来存储log，configuration，term等信息的。
- 

## 参考:

-
