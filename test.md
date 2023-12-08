## 缓存三种策略：

### 1、旁路缓存模式

写：先更新db，后删除缓存

读：从cache读，有返回，无从db，再放入cache

#### 先删除cache，后更新db	-> 数据不一致

1、requestA写数据先删除缓存，还没更新db

2、requestB此时读数据，先从缓存读，没有，从数据库读还没更新的旧数据，并缓存
3、requestA更新db

4、结果出现不一致

#### 先更新db，后删除缓存 -> 数据不一致

也可能出现数据库不一致，但是概率低

1. 缓存过期

2. A读数据，缓存没有，从数据库读到，还未放入缓存

3. B更新数据库、并删除缓存

4. A将旧数据放入缓存

   此时数据库的结果是resB，缓存的结果是resBefore

要同时满足：缓存过期，A读数据库+A写缓存要比B更新数据库+B删除缓存快

### 2、读写穿透

个人理解是：cache接管了读写操作，具体的读写操作都由缓存执行

写：先查询cache，若不存在直接更新db，若存在，先更新cache，再由cache服务更新db（同步更新cache和db）
读：先查询cache，有直接返回，无查询db，写入cache，由cache返回

### 3、异步缓存写入

和读写穿透类似，但不同步更新cache，由异步批量更新db

### Mysql字段类型

数值类型：整型tinyint、small，浮点型double、float、定点型decimal

字符串类型：char、varchar、text（tinytext、text、mediumtext、longtext），blob（blob，mediumblob，longblob）

日期时间类型：year、time、date，datetime，timestamp

### MyISAM与InnoDB

1. 是否支持外键
2. 是否支持事务
3. 是否支持崩溃恢复
4. MyISAM锁粒度为表锁，InnoDB支持行级锁
5. 索引实现不同

### 主从复制

原理通过binlog实现

1. 主库将数据修改写入binlog
2. 从库发起一个io线程向主库请求binlog
3. 主库发起一个binlog dump线程发送
4. 从库通过io线程接受，存入relay、 log
5. 从库通过sql线程读取relay log同步数据

### 分库分表

水平、垂直

分片算法（解决水平分片，数据存放位置）：哈希分片、范围分片、地理位置分片、组合分片

问题：

1. 无法使用join，手动查询一个数据后再去另一个数据库寻找对应数据
2. 事务问题，单个操作涉及多个不同数据库，分布式事务需要
3. 分布式id，数据库自增主键无法满足唯一性
4. 跨库聚合查询问题，group by，order by

数据迁移

1. 停机迁移
2. 双写访问，对老库的增删改，同时对新库操作，若无操作数据就插入新库

### 排序算法了解哪些？

1. 冒泡排序（Bubble Sort）：

   - 每次比较相邻的两个元素，如果顺序错误就交换它们，使较大（或较小）的元素逐渐浮动到列表的末尾。
   - 重复执行上述步骤，直到整个列表排序完成。
   - 冒泡排序的时间复杂度为O(n^2)，其中n是列表的长度。

2. 选择排序（Selection Sort）：

   - 每次从未排序的部分中选择最小（或最大）的元素，放置到已排序部分的末尾。
   - 重复执行上述步骤，直到整个列表排序完成。
   - 选择排序的时间复杂度为O(n^2)。

3. 插入排序（Insertion Sort）：

   - 将未排序的元素逐个插入到已排序的部分中的正确位置，以构建最终排序的列表。
   - 对于每个未排序元素，从后往前比较并移动已排序元素，直到找到合适的插入位置。
   - 插入排序的时间复杂度为O(n^2)，但在部分有序的情况下表现较好。

4. 快速排序（Quick Sort）：

   - 选择一个基准元素（通常是列表中的中间元素），将列表分割成两个子列表，其中一个子列表的所有元素小于基准，另一个子列表的所有元素大于基准。
   - 递归地对两个子列表进行快速排序，直到子列表的大小为1或0。
   - 快速排序的平均时间复杂度为O(nlogn)，但最坏情况下可能达到O(n^2)。

5. 归并排序（Merge Sort）：

   - 将列表递归地分成两个子列表，然后将两个子列表合并，合并时按照顺序排列元素。
   - 递归地将子列表排序，直到子列表的大小为1。
   - 归并排序的时间复杂度始终为O(nlogn)，但它需要额外的内存空间来存储中间结果。

6. 堆排序（Heap Sort）：

   - 将列表构建成最大堆（或最小堆），堆是一种特殊的完全二叉树结构，其中每个节点的值大于等于（或小于等于）其子节点的值。
   - 重复执行以下步骤，直到堆为空：
     - 取出堆顶元素（最大值或最小值）。
     - 重建堆，将剩余元素重新调整为最大堆（或最小堆）。
   - 堆排序的时间复杂度为O(nlogn)，且具有较好的性能。

7. 希尔排序（Shell Sort）：

   - 将列表按照一定的间隔分组，对每个分组进行插入排序，然后逐渐缩小间隔，重复进行分组和插入排序，直到间隔为1，完成最后的插入排序。
   - 希尔排序的时间复杂度取决于间隔序列的选择，但通常在最坏情况下为O(n^2)。

8. 计数排序（Counting Sort）：

   - 计数排序适用于元素值范围较小且已知的情况。
   - 统计每个元素在列表中出现的次数，并将统计结果存储在辅助数组中。
   - 根据统计结果，按照顺序重建排序后的列表。
   - 计数排序的时间复杂度为O(n+k)，其中n是列表的长度，k是元素值的范围。

9. 桶排序（Bucket Sort）：

   - 将元素分配到不同的桶中，每个桶可以使用不同的排序算法进行排序，或者递归地应用桶排序。
   - 桶排序的时间复杂度取决于桶的数量和每个桶内使用的排序算法，通常为O(n)或O(nlogn)。

10. 基数排序（Radix Sort）：

    - 基数排序按照元素的位数进行排序，通常从最低位到最高位依次进行排序操作。
    - 可以使用计数排序或桶排序作为中间步骤来对每个位进行排序。
    - 基数排序的时间复杂度为O(d*(n+k))，其中d是最大元素的位数，n是列表的长度，k是基数的大小。

    ### 反射的概念，具体应用场景

反射（Reflection）是一种计算机编程中的概念，指的是在运行时动态地获取、检查和修改程序的结构、属性和行为的能力。通常，反射使得程序能够在运行时获取关于类型、对象和方法的信息，并且可以通过获取的信息来进行操作和调用。

具体应用场景如下：

1. 动态加载类和实例化对象：通过反射可以在运行时动态地加载类，实例化对象，并调用其方法。这对于需要根据配置或条件来决定使用哪个类或对象的场景非常有用。

2. 探测和操作类的成员：通过反射可以获取类的属性、方法、构造函数等成员的信息，并且可以在运行时修改或调用它们。这使得一些框架和工具能够在不修改源代码的情况下对类进行扩展和定制。

3. 序列化和反序列化：反射可以用于将对象转换为字节流（序列化）或将字节流转换回对象（反序列化）。通过反射，可以检查对象的字段和属性，并将其存储为字节流，或者从字节流中还原为对象。

4. 动态代理：反射可以用于创建动态代理对象，即在运行时生成一个实现特定接口的代理类。通过代理类，可以在方法调用前后进行一些特定的处理，如日志记录、性能监控等。

5. 编写通用代码和工具：反射使得程序可以在更加通用的层面上操作和处理类型、对象和方法。一些通用的代码库和工具，如测试框架、ORM（对象关系映射）工具、依赖注入容器等，都使用了反射来实现其功能。

需要注意的是，反射对性能有一定的影响，因为它需要在运行时进行类型检查和方法调用，相比静态编译来说更加耗时。因此，在使用反射时需要权衡其灵活性和性能开销，并避免滥用反射导致代码可读性和可维护性的下降。

### Java序列化与反序列化

Java序列化（Serialization）是指将Java对象转换为字节序列的过程，可以将对象保存到文件、传输到网络或者在进程间进行通信。反序列化（Deserialization）则是将字节序列转换回Java对象的过程。

Java提供了`java.io.Serializable`接口，通过实现该接口的类可以被序列化和反序列化。要实现序列化和反序列化，可以按照以下步骤进行操作：

1. 实现`java.io.Serializable`接口：需要序列化的Java类必须实现`Serializable`接口。这是一个标记接口，没有需要实现的方法，它只是表示该类可以被序列化。

```java
import java.io.Serializable;

public class MyClass implements Serializable {
    // 类的成员和方法
}
```

2. 序列化对象：使用`ObjectOutputStream`类将Java对象序列化为字节流。

```java
MyClass obj = new MyClass();
try {
    FileOutputStream fileOut = new FileOutputStream("object.ser");
    ObjectOutputStream out = new ObjectOutputStream(fileOut);
    out.writeObject(obj);
    out.close();
    fileOut.close();
    System.out.println("对象已序列化并保存到文件");
} catch (IOException e) {
    e.printStackTrace();
}
```

3. 反序列化对象：使用`ObjectInputStream`类将字节流反序列化为Java对象。

```java
try {
    FileInputStream fileIn = new FileInputStream("object.ser");
    ObjectInputStream in = new ObjectInputStream(fileIn);
    MyClass obj = (MyClass) in.readObject();
    in.close();
    fileIn.close();
    System.out.println("对象已从文件反序列化");
} catch (IOException | ClassNotFoundException e) {
    e.printStackTrace();
}
```

需要注意以下几点：

- 被序列化的类需要实现`Serializable`接口，否则会抛出`NotSerializableException`异常。
- 静态和瞬态（transient）成员变量不会被序列化。
- 序列化和反序列化的对象的类必须是同一个版本，否则会引发`InvalidClassException`异常。
- 序列化和反序列化过程中可能会抛出`IOException`等异常，需要适当处理异常情况。

Java序列化和反序列化是一种方便的机制，但也需要注意安全性和性能方面的考虑。在分布式系统中，可以使用其他序列化框架（如JSON、Protocol Buffers等）来代替Java序列化，以提供更好的跨语言支持和性能优化。

### 创建线程有几种方法，实现Callable接口与其他创建方法有什么不同



### 了解的设计模式

当谈到设计模式时，以下是几个常见的设计模式：

1. 单例模式（Singleton Pattern）：确保一个类只有一个实例，并提供全局访问点。这对于需要共享资源的类或控制系统中的唯一实例非常有用。

2. 工厂模式（Factory Pattern）：通过工厂类创建对象，而不是直接实例化对象。这样可以将对象的创建与使用代码分离，提供更高的灵活性和可扩展性。

3. 观察者模式（Observer Pattern）：定义了对象之间的一对多依赖关系，当一个对象的状态发生变化时，其所有依赖对象都会收到通知并自动更新。

4. 适配器模式（Adapter Pattern）：将一个类的接口转换成客户端所期望的另一个接口。通过适配器，两个不兼容的接口可以协同工作。

5. 装饰器模式（Decorator Pattern）：动态地将责任附加到对象上，以扩展对象的功能。装饰器模式提供了比继承更灵活的替代方案。

6. 策略模式（Strategy Pattern）：定义了一系列算法，将每个算法封装到可互换的对象中。策略模式使得算法可以独立于使用它的客户端而变化。

7. 代理模式（Proxy Pattern）：为其他对象提供一个代理或占位符，以控制对这个对象的访问。代理模式可以提供额外的控制，例如延迟加载对象或在访问对象前后执行其他操作。

8. 建造者模式（Builder Pattern）：将一个复杂对象的构建过程与其表示分离，使得同样的构建过程可以创建不同的表示。

9. 模板方法模式（Template Method Pattern）：定义了一个算法的骨架，将一些步骤的具体实现延迟到子类中。模板方法允许子类在不改变算法结构的情况下重新定义算法的某些步骤。

这只是一小部分常见的设计模式，设计模式提供了在软件设计中常见问题的解决方案。每个设计模式都有其特定的用途和适用场景，可以根据具体需求选择合适的设计模式来实现代码的可维护性、可扩展性和复用性。

加载  验证 准备 解析 初始化  使用 卸载

自动装配：在springboot启动时，会扫描外部jar包中meta-inf下的spring.factories文件，将文件中的配置类型信息加载到spring容器

过程：@SpringbootApplication ->@Configuration、@EnableAutoConfiguration、@ComponentScan
@EnableAutoConfiguration中的@AutoConfigurationImportSelector——》实现了importSelector接口

-〉实现了这个接口中的 `selectImports`方法中的——》getAutoconfigurationEntry——〉



@SpringBootAppliction ->@EnableAutoConfiguration -> @ AutoConfigurationImportSelectors -> selectImports方法中首先判断了自动装配是否打开，然后调用getAutoconfiguration从Meta-inf/spring.factories获取