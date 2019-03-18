- ``make docker_run``: run postgres in container
- ``make docker_stop``: stop container


#SQL，本应该很简单
------
本教程是面向非计算机专业，对立志于从事data science, data analysis, statistician, machine learning, data mining的人的一部数据库简明教程。
这个project的目的既是教学，也是自己的学习总结。希望能让没有编程经验的人能感觉到，原来编程与科学，可以如此优雅的结合。^_^

对于来自统计，金融财经，数学等专业而又缺乏计算机科学的基本常识的人来说，学习一门新的编程语言总是痛苦不已的。更何况本教程的内容同时涵盖了SQL和Python两门编程语言，数据库一门计算机课程。而在统计，数据分析领域已经有R，Matlab这样强大的语言和工具，那么我们为什么还要学习SQL语言和数据库呢？
## 数据库在Data Science中有什么用？
在分析数据时，数据无非是以以下几种形式存在

 - 本地文件： txt，csv，xlsx，json，tab，mat，arff等数据格式。在需时候用工具把数据读入，进行分析。
 - 数据库：在需要的时候选择部分我们需要用到的数据，导出成其他格式的文件，再在其他工具中进行处理。

好了，我们来想象一下下面的两个例子

- 案例1
> 比如我们有365个文件，每个文件是一年中一天的商品销售数据。而我们进行分析的时候一定会需要经过汇总，选择，分析这样的过程。而我们每进行一次分析，这个汇总、选择、分析所使用到的数据，预处理的方式，分析的算法都可能不同。**当我们的数据总量很大的时候，我们在每次数据的预处理上，很可能就要花费大量的时间**。

- 案例2
> 我们把原始数据预处理成了我们需要的数据，要怎么储存呢？假设我们将其储存在一个大文件中。很多时候我们的分析只需要用到其中的一小部分，此时我们**不得不将整个大文件读入内存**，再取得我们所要用到的部分。
而如果我们将其存成许多格式一致的小文件。一旦我们需要对一个相对较大的数据子集进行分析，那么我们就**不得不浪费很多代码在汇总，选择数据上**。而且我们需要很小心的设计储存数据的形式，才能保证不给之后的操作带来麻烦。

可能很多人并不清楚内存和硬盘在数据处理中的关系。在这里进行一个简单的科普

> - 硬盘是指本地磁盘，用磁盘的物理状态储存数据，断电后数据不会丢失
- 内存是指高速读写记忆体，用通电的01状态储存数据，断电后数据丢失
内存的读写速度要比磁盘块很多很多倍
无论是什么程序和语言，对数据的处理都是需要在内存中进行处理的。无论是数据本身，算法推导的中间过程以及最后的数据结果，直到我们把结果保存成本地文件之前，一切操作都是在内存中进行的。所以数据分析的第一步，永远是“把数据读入内存”。我们知道，内存相比硬盘空间要小很多很多。那比如说我们要从一年的数据中选择周六周日的数据；假设数据总量是7G，那么我们其实只用到了2/7也就是2G的内存。但是为了取得这2G内存，我们需要耗费7G内存之多。在数据量大的时候，本地文件存储方案的劣势可见一斑。

由于在每一个项目中，我们对数据的存储，读取，格式，分析的需求都会产生变化。为了适应这些变化我们不得不浪费许多时间和经历在数据分析之外的地方。那么有没有一个解决方案能简化这一切呢，让我们能专注于分析数据呢？答案就是：**数据库**。

## 数据库的优势

 - 在数据库中查找数据的内存消耗很小。例如在一个10G的数据库中查找1G的数据量，查找本身的内存消耗不过几十M，而我们可以通过每次读取100M的数据量将我们所需要的1G数据读出来。这样我们一共仅用到了110M

 - 在数据库中查找数据很快，而且不需要把整个数据读入内存。例如在一个10G的数据库中查找1G数据。如果打开一个10G的excel文件，起码需要几十分钟，而且需要10G内存。而在数据库中，整个查找过程不超过2秒，读取1G数据的时间也不会超过一分钟。

 - 从数据库中获取数据的SQL语句的代码非常简洁，远远少于对多个本地文件进行读取，筛选的代码量。这点我们将会在学习了SQL语句之后看到。

 - 数据库有一整套安全机制确保数据的读写不会损坏整个数据库。例如我们已有一个1G的excel文件，我们在内存中将其扩充到了1.5G，然后要写入覆盖原文件，但是如果在写入到一半的时候断电了或者出错程序退出，那么我们不仅连写入的一半文件失去了，而且连原有的1G文件也失去了。为了避免这样，我们通常得将文件写入一个新的文件中，成功后再删去原文件，但是这样对磁盘是很大的浪费。而数据库可以在对自己进行写覆盖的同时，保证中途出错的情况下也能保证自动恢复到写覆盖之前的状态。
