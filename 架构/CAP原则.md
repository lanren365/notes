# 1. 定义
CAP原则又称 CAP 定理，1998年，加州大学的计算机科学家 Eric Brewer 提出的，指的是在一个分布式系统中，Consistency（一致性）、 Availability（可用性）、Partition tolerance（分区容错性），三者不可兼得（我们常说的鱼和熊掌不可兼得）。CAP 原则也是 NoSQL 数据库的基石。
# 2. 指标说明
1. 一致性（Consistency，C）：在分布式系统中的所有数据备份，在同一时刻是否同样的值。（等同于所有节点访问同一份最新的数据副本）。

2. 可用性（Availability，A）：在一个分布式系统的集群中一部分节点故障后，该集群是否还能够正常响应客户端的读写请求。（对数据更新具备高可用性）。

3. 分区容错性（Partition tolerance，P）：大多数的分布式系统都分布在多个子网络中，而每个子网络就叫做一个区（partition）。分区容错的意思是，区间通信可能失败。比如阿里巴巴的服务器（不知道各位有没有发现，不管你到那个城市去，你访问的服务器总是该城市的，其中使用 了算法，由于篇幅有限就不再这儿一一讲解了），一台服务器放在上海，另一台服务器放在北京，这就是两个区，它们之间可能存在无法通信的情况。在一个分布式系统中一般分区容错是无法避免的，因此可以认为 CAP 中的 P 总是成立的。CAP 理论告诉我们，在 C 和 A 之间是无法同时做到。
