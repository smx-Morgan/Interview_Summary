**redis有哪些持久化机制 **

Redis的持久化机制主要有RDB（Redis DataBase，快照）和AOF（Append Only File，追加文件）两种方式。
1.RDB
RDB是Redis默认的持久化方式，它会在指定的时间间隔内将内存中的数据生成一个快照文件，存储到磁盘上。生成快照时，可以选择两种方式，一种是fork出一个子进程来进行快照，另外一种是启用RDB快照压缩功能，将快照进行压缩，然后再进行存储。

可以在Redis的配置文件中设置RDB持久化的周期和备份文件的名称。

2优点
RDB的快照文件非常紧凑，可以将数据保存在磁盘上，占用的空间比较小。

RDB文件中包含了所有的数据，可以很方便地备份和恢复Redis的数据，并且在Redis启动时，可以通过加载RDB文件来快速恢复数据，缩短Redis的启动时间。

3.缺点
RDB保存数据的时间间隔较长，如果Redis在保存快照之前发生崩溃，那么所有在快照之后修改的数据都将丢失。

生成RDB文件需要耗费CPU和内存资源，在数据量较大时，可能会对Redis服务器的性能造成一定的影响。

3.AOF
1.AOF
AOF是Redis提供的一种类似于日志的持久化方式，当Redis持久化数据时，会将写入Redis的每一次命令都追加到AOF文件末尾，这样即使Redis崩溃了，也可以通过重新执行AOF文件中的命令来恢复数据。

AOF文件的更新有两种模式：

每次写入
每次写入命令都会更新AOF文件，这种方式的优点是可靠性高，数据不容易丢失，但是有可能会降低Redis的性能。

定期更新
在一定时间间隔内，将Redis服务器的写入命令一次性保存到AOF文件中，这种方式的优点是在写入命令较多的情况下，可以减少AOF文件的体积，但是因为存储的不是实时数据，所以可靠性不如每次写入。

可以在Redis的配置文件中设置AOF持久化的更新模式、更新频率和文件名。

2优点
AOF可以实现精细的数据恢复，只需要从最近的一次持久化开始恢复即可。

AOF文件在持久化时，不会阻塞Redis的命令执行，可以在不影响用户使用的情况下实现持久化。

AOF提供了多种AOF文件更新的策略，可以根据业务需求进行优化。

3.缺点
每次写入命令都会更新AOF文件，可能会影响Redis的性能。

AOF文件通常比RDB文件大，占用的磁盘空间要多些，但是可以通过压缩AOF文件来减小文件大小。

如果更新方式为“每次写入”，则在数据量较大情况下，AOF文件会变得非常大，存储和备份将会变得复杂且耗时。   

### 衍生问题

**aof文件过大怎么办**

使用rewirte机制，rewrite机制现在达到一定的条件redis会自动触发
其具体的流程就是：
1，redis主进程fork一个子进程
2，子进程根据当前内存的数据，构建一个新的日志，写入一个新的AOF文件中
3，这段时间内，redis接收到的client的修改操作，都会在内存中新起一个日志文件去进入，并同步到旧的AOF文件中
4，当子进程完成了任务，redis就会把新的日志文件追加到新的AOF文件中
5，使用新的AOF文件替换旧的AOF文件

**Redis 4.0 混合持久化**

将 rdb 文件的内容和增量的 AOF 日志文件存在一起。这里的 AOF 日志不再是全量的日志，而是自持久化开始到持久化结束的这段时间发生的增量 AOF 日志，通常这部分 AOF 日志很小。相当于：

大量数据使用粗粒度（时间上）的rdb快照方式，性能高，恢复时间快。
增量数据使用细粒度（时间上）的AOF日志方式，尽量保证数据的不丢失。