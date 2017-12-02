翻译LinkedTransferQueue源码中的Overview of Dual Queues with Slack这段注释。意译，可能有误。

《介绍松弛双队列》

双队列（Dual Queues），由Scherer和Scott设计的。其中节点可以表示数据（DATA）或请求（REQUEST）。当一个线程试图入列1个数据（DATA）节点时，如果遇到一个请求节点（REQUEST），它俩会进行匹配（match）并且被删除。反过来，对于入列请求（REQUEST）也一样。阻塞双队列（Blocking Dual Queues）让未匹配到节点的入列线程排队等待（阻塞），直到其他线程提供可匹配的节点为止。双同步队列（Dual Synchronous Queues）也会让未匹配到节点的线程阻塞。双传输队列（Dual Transfer Queues）支持所有这些模式。

有一种FIFO双队列（FIFO dual queue），实现了Michael & Scott(M&S)的lock-free队列算法。它维护两个指针属性head和tail。head，指向一个匹配的（matched）节点，这个节点又指向第一个实际的未匹配的（unmatched）队列节点，如果队列是空的它就是null。tail指向队列的最后一个节点，如果队列是空的，它就是null。比如下面这个四个节点的队列:
 
head               tail
|                   |
v                   v     
M -> U -> U -> U -> U

M&S队列算法通过CAS操作维护head和tail指针时，容易实现可伸缩性和进行开销限制。有一些算法基于这些特性实现，比如elimination arrays（出自论文Using elimination to implement scalable and lock-free FIFO queues）和optimistic back pointers（出自论文http://people.csail.mit.edu/edya/publications/OptimisticFIFOQueue-journal.pdf）。但是，双队列的性质使得在需要双性时，可以使用一种更简单的策略来改进m&s这类队列的实现。

在双队列中，每个节点必须通过原子操作维护它的状态。我们是这样实现的：对于DATA节点，匹配就是通过CAS操作，把它的item属性从一个非null的值改为null。对于REQUEST节点，匹配就是通过CAS操作把item从null改为非null值。双队列中的元素，通过link操作能变成可用的，通过match操作能变成不可用的。比普通的M&S队列，在入列/出列的时候多了原子操作。

一旦节点被匹配，它的匹配状态就不能更改了。我们可以这么安排链表里的节点：前面是0+个匹配过的节点，后面是0+个未匹配的节点。如果不考虑时间和空间性能的话，链表里的节点也可以是乱的（？猜的），遍历所有节点，匹配的时候匹配第一个遇到的未匹配节点，添加（append）的时候把最后一个节点的next指向新节点。但这不是个好方法，唯一的优点是改变head/tail节点的时候不用原子操作。
