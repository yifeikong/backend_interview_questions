# 设计模式

## Q: 为什么全局和静态变量是邪恶的？你能给出一个代码例子吗？

A:

1. 对于 C 语言来说，占据了静态存储区，使程序变大
2. 破坏封装，函数不是幂等的
3. 代码可读性下降
4. 多线程程序会造成竞态条件

## Q: 给我讲讲 Inversion of Control，以及他是如何提升代码的设计水平的

A: 其实很简单，就是把一段代码中需要依赖的另一个类从这段代码中移出来，然后把生成的结果传递到这段代码中，这就是 Inversion of Control 了

https://stackoverflow.com/questions/3058/what-is-inversion-of-control

## Q: Demeter 定律（最小知识原则）说一个单元应该对其他单元了解的知识越少越好，而且应该只能和他的直接相关单元通信（或者说“不要和陌生人说话”）。你会写违反这个原则的代码吗？展示下为什么这个是不好的设计然后修复他。

A: 比如说一个类不应该访问另一个类的数据成员，而应该通过getter方法来访问。当然这样做可能会使运行效率低下。但是比如说另一个类的实现改变的时候，我们不需要改动代码，因为没有依赖。

## Q: Active Record 模式提倡对象中包含增删改查等方法，并且每个属性都对应到数据库的表中字段，介绍一下这种模式的限制和坑

但是缺少灵活性，实际中当你每次处理一张表时使用这个模式是可以的， 但是当你要处理很多关联表，类继承关系较为复杂的时候就会有很多问题，这种情况你可以使用Data Mapper模式

像是 Django，RoR 都是采用的 Active Record 模式

ref: http://www.jacopobeschi.com/post/active-record-design-pattern

## Q: Data Mapper 模式提倡使用一层mapper来在对象和数据库之间传递数据，但是保持了它们两者之间的独立。相反 Active Record 模式直接持久化到数据库中。关于两者你有什么看法？为什么使用其中一个而不是用其中另一个呢？

我们看到它在存数据的时候，没有用$user->save()去存，而是用了另一个类EntityManager去存。也就是说，它的模型$user没有继承一个数据库操作类，$user和数据库完全是分离开的。
它这样做有什么好处呢？$user没有继承一个庞大的基类，变得轻便多了。就好像蜗牛甩掉了重重的壳，变成了蛞蝓。
这是反Active Record人士最重要的攻击点，他们会嫌Active Record性能差。
另外一个就是在Data Mapper模式下，模型，数据操作层，数据库这三者是完全分开的，所以不必一一对应，这样在某些情况下是很灵活的。

ref: http://larabase.com/post/156

## Q: 为什么经常说 null 的引入是一个“价值百万美元的错误”？能不能谈一下避免Null值的一些设计模式，比如 Null Object 模式 和 Option Types？

A: null 是一个没有值的值，这就是他的错误。实际上，比如在Python中，我就经常遇到 None object has no method xxx 的问题。

Null Ojbect pattern 就是定义一个空对象，让他来当做当值为空时候的对象，而不要使用Null值来表示空。避免了检查返回是不是为null的问题。

在一些编程语言，尤其是函数式编程语言中，Option Type 是一个多态的类型，表示的是函数可能返回一个有效的值或者无效的值

ref:

1. https://www.lucidchart.com/techblog/2015/08/31/the-worst-mistake-of-computer-science/
2. http://www.cnblogs.com/gaochundong/p/design_pattern_null_object.html
3. https://en.wikipedia.org/wiki/Option_type

## Q: 好多人说在 OOP 中，组合比继承更好，你的观点呢？

A: 首先，类型系统看起来很好，太复杂了，问题就很多。其次，继承表达了 is-a 的关系，如果遇到要实现两个接口就很复杂。组合的话更加模块化一点。

ref: https://www.zhihu.com/question/49008835

## Q: 什么是 Anti-Corruption Layer？

通过在旧系统和新式系统之间放置防损层，将它们隔离。 此层转换两个系统之间的通信，在旧系统保持不变的情况下，新式应用程序可以避免破坏其设计和技术方法。

新式应用程序和防损层之间的通信始终使用应用程序的数据模型和体系结构。 从防损层到旧系统的调用符合该系统的数据模型或方法。 防损层包含在两个系统之间转换所必需的所有逻辑。 该层可作为应用程序内的组件或作为独立服务实现。

ref: https://docs.microsoft.com/zh-cn/azure/architecture/patterns/anti-corruption-layer

## Q: 写一个线程安全的单例模式

A: 这个也是经典问题了，但是实际上感觉并没有卵用，但是大家就是喜欢问

    import threading

    class SingletonMixin:

        _singleton_lock = threading.Lock()
        _singleton_instance = None

        @classmethod
        def get_instance(cls):
            if not cls._singleton_instance:
                # 其他语言中使用 lock 和 unlock，而 Python 的with语句就很方便了
                with cls._singleton_lock:
                    if not cls._singleton_instance:
                        cls._singleton_instance = cls()
            return cls._singleton_instance

## Q: 能够改变具体实现而不用更改使用方代码叫做数据抽象。给出一个违反这个原则的例子，然后修复他

A: 尽量使用 getter 和 setter，而不要直接访问数据成员。当然在 Python 中有更好的方法(properties)

## Q: 写一段违反 DRY 原则的代码，然后修复他

A: 

## 如何处理 Dependency Hell（依赖地狱）

1. 首先用包管理器，不要手工拷贝代码管理依赖
2. 多使用 virtual env 等虚拟环境工具
3. 使用docker 打包镜像

## Q: goto 语句是有害的吗？关于使用 goto 语句你有什么看法呢？

肯定最好别用。。

## Q: 保持软件健壮的一个核心原则是，对你的输出要严格，但是对你的输入要保持宽容，能不能讨论一下这个原则?

## Q: 责任分离原则用来把计算机程序分解到不同的领域，有不同的方法来实现这个原则（比如 对象、函数、模块、设计模式）等等，能不能讨论下这个话题?


# 代码设计问题

## 经常听人说，在OO设计中要满足高内聚和低耦合，这个是什么意思，怎么实现？

耦合性与内聚性是模块独立性的两个定性标准，将软件系统划分模块时，尽量做到高内聚低耦合，提高模块的独立性，为设计高质量的软件结构奠定基础。
有个例子很容易明白：一个程序有50个函数，这个程序执行得非常好；然而一旦你修改其中一个函数，其他49个函数都需要做修改，这就是高耦合的后果。

ref: https://www.cnblogs.com/robnetcn/archive/2012/04/15/2449008.html


## 为什么大多数语言的数组索引都是从0开始的

因为 C 是从0开始的，大多数语言都受C的影响，另外就是C接近硬件，0表示的是偏移量


## 测试与 TDD 如何影响代码设计

1. 有了测试用例以后重构更容易，而重构可以带来更好的代码
2. 测试更深入细节，可以更好的帮助思考

ref: http://www.jamesshore.com/Blog/How-Does-TDD-Affect-Design.html

## 内聚和耦合的区别在于？

内聚是应该在一起的，而耦合是不应该在一起的因为设计失误而在一起。

## 代码中的注释有用吗？有些人说应该尽量避免注释，你同意吗？

我同意，代码应该尽量是自解释的，而不是借助于注释才能明白

## 设计和架构之间的区别是？

## 为什么 TDD 中要在代码之前写测试

## C++ 支持多重继承，而在 Java 中允许一个类去实现多个
interface。使用这些方法对于代码的正交性有什么影响吗？使用多重继承和实现多个
interface有什么区别吗？使用委托和使用继承有什么区别吗？

## 把业务逻辑都放在 Stored Procedures 中的好处和坏处

貌似意思是把业务逻辑都写在数据库里，这得多奇葩呀。。

ref: https://softwareengineering.stackexchange.com/questions/158534/pros-and-cons-of-holding-all-the-business-logic-in-stored-procedures-in-web-appl

## 为什么 OO 设计垄断了业界这么多年？

## 你用什么方法来评价你的代码是否设计有问题？


# 语言相关问题

## 说出你喜欢的语言的三个缺点

Python：GIL、性能慢、2 和 3不兼容

## 为什么现在函数式编程的影响越来越大？

两个方面回答，一方面，为什么之前函数式编程不流行，函数式编程性能往往不高，这时候大家更愿意使用贴近硬件的 C 语言等
另一方面，函数式编程没有副作用，更好地适应并发高的情形。可以证明正确性。多核处理器的出现等等。方便实现高阶函数

## 什么是闭包，它和类之间有什么联系？

闭包和类都创建了一个独立的作用域，其中定义的函数都可以访问这个空间内的变量，闭包创建的作用域在函数执行结束之后依然是有效的

    function() {
        var a = 1;
        console.log(a); // works, -> 1
    }
    console.log(a); // fails, -> undefined

    function outer() {
        let a = 1
        function inner() {
            console.log(a)
        }
        return inner
    }

    let fn = outer()
    fn()  // -> 1

ref: https://stackoverflow.com/questions/36636/what-is-a-closure

## 泛型有什么用

## 高阶函数是什么？写一个

    def get_counter():
        count = 0
        def counter():
            count += 1
            return count
        return counter


## 写一个循环，然后把它变成一个递归，不要使用变量

    def sum(l):
        ret = 0
        for i in l:
            ret += i
        return ret

    def sum2(l):
        if len(l) == 0:
            return 0
        if len(l) == 1:
            return l[0]
        return l + sum2(l[1:])

## 当我们说一个语言中，函数是一等公民的时候，我们想表达什么

## 给我写一个匿名函数的例子

## 有很多不同的类型系统，我们来讨论一下静态和动态类型系统，以及强类型和弱类型。关于这个话题你肯定有你自己的观点，然后分享一下当开发一个企业级的系统的时候，你会如何选择

## 命名空间有什么用

## 聊聊 Java 和 C# 之间的互操作性，或者其他任意两种语言之间

## 命名空间有什么用？

## 为什么很多工程师不喜欢 Java

## 好语言好在哪里？坏语言坏在哪里？

## 写两个函数，一个是 Reference Transparency 的，一个不是

Referential transparency, referred to a function, indicates that you can determine the result of applying that function only by looking at the values of its arguments. You can write referentially transparent functions in any programming language, e.g. Python, Scheme, Pascal, C.

## 什么是堆，什么是栈，什么是 Stack Overflow

## 一些语言，尤其是支持函数式编程的语言，推崇一种称作 Pattern Matching 的技术，他和 switch 语句有什么区别吗？

Pattern Matching 相对于 switch 更加灵活，switch 只能使用常量匹配，pm 可以根据类型、值等多种方法匹配

## 为什么一些语言（比如 Go）没有异常呢？这样做的好处和坏处都是什么？

## 如果 `Cat` 是一个 `Animal`，那么 TakeCare<Cat> 是一个 `TakeCare<Animal>` 吗？

## 为什么在 Java 和 C# 中，构造函数不是接口的一部分呢?

# 数据库

## 你如何迁移应用的数据库，比如从 MySQL 迁移到 postgresql？如果你需要管理这样的项目，你预期会遇到什么问题？

首先，如果这个项目是可以停掉的，那直接停掉，迁移完毕之后验证数据就好了
如果这个项目不能停掉，可以按下面的步骤

1. 更改代码，数据开始双写新旧数据库
2. 更改读取代码，开始灰度读取新数据库数据
3. 把历史数据导入新数据库
4. 灰度下掉老数据库

预期会有数据不一致，性能问题等

## 为什么数据库把 null 当做一个特殊值来处理？比如说 `Select * From table Where Field = null` 不会匹配到含有 null 的行？

因为 null 表示的是没有值，它不和任何值相等

## ACID 是原子性（Atomicity）、一致性（Consistency）、隔离性（Isolation）、持久性（Durability）的缩写，几乎所有的数据库引擎都支持他们，关于这个话题，你了解多少？

一个支持事务（Transaction）的，必需要具有这四种特性，否则在事务过程（Transaction processing）当中无法保证数据的正确性，交易过程极可能达不到交易方的要求。

1. 原子性 整个事务中的所有操作，要么全部完成，要么全部不完成，不可能停滞在中间某个环节。事务在执行过程中发生错误，会被回滚（Rollback）到事务开始前的状态，就像这个事务从来没有执行过一样。

2. 一致性 在事务开始之前和事务结束以后，数据库的完整性约束没有被破坏。具体来说就是，比如表与表之间存在外键约束关系，那么你对数据库进行的修改操作就必需要满足约束条件，即如果你修改了一张表中的数据，那你还需要修改与之存在外键约束关系的其他表中对应的数据，以达到一致性。

3. 隔离性 隔离状态执行事务，使它们好像是系统在给定时间内执行的唯一操作。如果有两个事务，运行在相同的时间内，执行相同的功能，事务的隔离性将确保每一事务在系统中认为只有该事务在使用系统。这种属性有时称为串行化，为了防止事务操作间的混淆，必须串行化或序列化请求，使得在同一时间仅有一个请求用于同一数据。

4. 持久性 在事务完成以后，该事务所对数据库所作的更改便持久的保存在数据库之中，并不会被回滚。 由于一项操作通常会包含许多子操作，而这些子操作可能会因为硬件的损坏或其他因素产生问题，要正确实现ACID并不容易。ACID建议数据库将所有需要更新以及修改的资料一次操作完毕，但实际上并不可行。

目前主要有两种方式实现ACID：第一种是Write ahead logging，也就是日志式的方式。第二种是Shadow paging。

ref: http://blog.csdn.net/shenwansangz/article/details/47614759

## 如何管理数据库 schema 的变更，当程序一个一个版本迭代的时候，如何自动化地处理数据库 schema 的变更。

## 懒加载是如何实现的，什么时候有用？有什么坑呢？

```
if (_AllCustomers == null) {
    _AllCustomers = ExpensiveDataCall();
}

return _AllCustomers; // Was: _Customers, not sure if by design.
```

缺点是得到的数据可能是滞后的，以及更新策略的设计

https://softwareengineering.stackexchange.com/questions/278210/is-lazy-loading-always-required

## "N+1 问题" 如何解决？

举个例子，我们数据库中有两张表，一个是Customers，一个是Orders。Orders中含有一个外键customer_id，指向了Customers的主键id。

想要得到所有Customer以及其分别对应的Order，一种写法是 `SELECT * FROM Customers;`
对于每一个Customer: `SELECT * FROM Orders WHERE Orders.customer_id = #{customer.id}`

这样我们实际对数据库做了N+1次查询：选择所有Customer一次得到N个Customer，对于N个Customer分别选择其对应的Order一共N次。
所以，一共执行了N+1次查询，这就是N+1问题

N+1问题的一般解决方法

使用Left Join一次性取出所有数据：`SELECT * FROM Customers LEFT JOIN Orders on Customers.id = Orders.customer_id`。 这样虽然取出的数据相对多一些，但是只需要一次执行

PS：感觉数据库所谓的几个范式优点纸上谈兵，如果完全按照这些范式设计的话，性能就成渣了，互联网公司实际上极少用join。不过产品使用 hive 查询数据倒是经常用 join。

ref: http://kevinsun.logdown.com/posts/1069599

## 如何找出耗费资源最多的查询？

有一个查询 mysql 慢查询的语句，忘了记在哪儿了

## 从你来看，是否总要是的数据规范化，什么时候不规范更好？

数据规范化的主要目的是消除冗余信息，但是往往会对性能造成影响。如果性能更重要的话，应该放开规范化。

ref:

1. https://stackoverflow.com/questions/934577/should-i-normalize-my-db-or-not
2. https://stackoverflow.com/questions/1102590/what-exactly-does-database-normalization-do
3. http://www.25hoursaday.com/weblog/2007/08/03/WhenNotToNormalizeYourSQLDatabase.aspx

## 蓝绿部署涉及到数据库的时候会更加复杂，如果处理这种情况呢？

蓝绿部署：https://www.v2ex.com/t/344341


# 关于 NoSQL 的问题

## 什么是 Eventual Consistency？

因为大多数的 NoSQL 数据库都是分布式的，所以很难保证在任何时间都是完全一致的，但是可以保证在一定时间最终一致。

ref: https://stackoverflow.com/questions/10078540/eventual-consistency-in-plain-english

## 解释一下 CAP 理论

## 为什么 NoSQL 越来越流行了？

## NoSQL 如何解决 scalability 的问题？

## 在哪种情况下你会使用 MongoDB 这样的文档数据库来代替 MySQL 这种关系型数据库呢？

# 关于版本管理的问题

## 为什么 git 的分支比 SVN 更简单？

没用过 SVN，但是 SVN 好像是服务端分支，而 Git 在本地分支就可以了

## 像是 Git 这样的分布式版本管理系统相对于中心式的版本管理系统，比如 SVN，有什么优势和劣势？

没用过 SVN

## 能不能描述一下 GitHub Flow 或者 gitflow 的工作模式

## 什么是 rebase

## 为什么 git 中的 merge 比 SVN 方便很多？

# 关于并发的问题

## Q: 为什么我们需要并发呢？

## 为什么测试多线程的代码如此之难？

## 什么是竞态条件？举个例子

## 什么是死锁？举个例子

## 什么情况下会导致进程饥饿？

## 什么是 wait free 的算法

# 分布式系统的问题

## 如何测试一个分布式系统？
