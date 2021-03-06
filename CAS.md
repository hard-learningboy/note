并发编程之缓存一致性

1、问题的根源不在于多个核，而是多个缓存，以及缓存的写操作

2、缓存一致性协议：MESI协议

通过定义一个状态机来保证缓存的一致性。在MESI协议中有四种状态，这些状态都是针对缓存行（缓存由多个缓存行组成，缓存行的大小单位与机器的位数相关）。

- （I）Invalid状态：缓存行无效状态。要么该缓存行数据已经过时，要么缓存行数据已经不在缓存中。对于无效状态，可直接认为缓存行未加载进缓存。
- （S）Shared状态：缓存行共享状态。缓存行数据与内存中对应数据保持一致，多个缓存中的相应缓存行都是共享状态。该状态下的缓存行只允许读取，不允许写。
- （E）Exclusive状态：缓存行独有状态。该缓存行中的数据与内存中对应数据保持一致，当某缓存行是独有状态，其他缓存对应的缓存行都必须为无效状态。
- （M）Modified状态：缓存行已修改状态。缓存行中的数据为脏数据，与内存中的对应数据不一致。如果一个缓存行为已修改状态，那么其他缓存中对应缓存行都必须为无效状态。另外，如果该状态下的缓存行状态被修改为无效，那么脏段必须先回写入内存中。

MESI协议的定律：所有M状态下的缓存行（脏数据）回写后，任意缓存级别中的缓存行的数据都与内存保持一致。另外，如果某个缓存行处于E状态，那么在其他的缓存中就不会存在该缓存行。

MESI协议保证了缓存的强一致性，在原理上提供了完整的顺序一致性。可以说在MESI协议实现的内存模型下，缓存是绝对一致的，但是这也会导致一些效率的问题，我们平时使用的机器往往都不会采用这种强内存模型，而是在这个基础上去使用较为弱一些的内存模型：如允许CPU读写指令的重排序等。这些弱内存模型可以带来一定的效率提升，但是也引入了一些语义上的问题。

~~~ java
public final boolean compareAndSet(int expect, int update) {
        return unsafe.compareAndSwapInt(this, valueOffset, expect, update);
    }
 
    public final int getAndIncrement() {
        for (;;) {
            int current = get();
            int next = current + 1;
            if (compareAndSet(current, next))
                return current;
        }
    }
~~~





### 相关问题及总结

#### 1、介绍下 CAS，存在什么问题

#### 2、介绍下悲观锁和乐观锁

#### 3、AQS 的底层原理

#### 4JDK 中的并发类知道哪些