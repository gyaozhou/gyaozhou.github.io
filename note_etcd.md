# Source Code Read Note --- etcd

## library


## etcd

etcd/mvcc 应该是用来etcd Key-Value键值存储的存储引擎。 注意： 这个存储引擎，不等于Raft library中需要persistent存储，Raft需要的存储是用来存储log，configuration，term等信息的。

## 参考:

-
