Redis Rehash
---

1. 正常情况下master到达满容后根据驱逐策略淘汰Key并同步给Slave，因此Slave在这种情况下也不会因满容触发驱逐
   1. 集中排查思路聚焦Slave内存陡增的问题上
   2. 包括客户端连接、输出/输出缓冲区、业务数据存取访问、网路抖动等导致Redis内存陡增的所有外部因素。
   3. 而这容易导致线上集群发生大量淘汰，而导致主从不一致的问题，同时在业务层面也会发生大量超时，影响业务的可用性。
2. 案例描述：Redis满容状态下由于Rehash导致大量的Key被驱逐
3. <a href = "https://tech.meituan.com/2018/07/27/redis-rehash-practice-optimization.html">美团针对Redis Rehash机制的探索和实践</a>