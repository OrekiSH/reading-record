## 运行时数据区域
- 程序计数器(Program Counter Register): 在虚拟机概念模型中，字节码解释器通过改变程序计数器的值来选取下一条需要执行的字节码指令。每条线程都需要有一个独立的程序计数器，由于其独立存储互不影响，程序计数器的内存区域被称为"线程私有"的内存
- Java虚拟机栈(Virtual Machine Stacks): 每个方法在执行的同时都会创建一个栈帧(stack frame)用于存储局部变量表(内存空间在编译期间完成分配)，操作数栈，动态链接，方法出口等信息。局部变量表中存放了编译期可知的基本数据类型，对象引用(reference类型)和returnAddress类型。对象引用不等同于对象本身， 可能是一个指向对象起始地址的引用指针，也可能是指向一个代表对象的句柄; returnAddress类型指向了一条字节码指令的地址。`long`和`double`类型的数据会占用2个局部变量空间(Slot), 其他数据类型只占用1个。当线程请求的栈深度大于虚拟机允许的深度时将抛出`StackOverflowError`异常, 当虚拟机动态扩展时无法申请到足够的内存时将会抛出`OutOfMemoryError`异常
- 本地方法栈(Native Method Stack): 虚拟机栈为虚拟机执行Java方法(字节码)服务, 本地方法栈为虚拟机使用到的Native方法服务
- Java堆: Java堆是被所有线程共享的一块内存区域，在虚拟机启动时创建，存在的唯一目的便是存放对象实例.由于Java堆是垃圾收集器管理的主要区域，因此有时也被称为GC堆。如果在堆中没有足够的内存完成实例分配，且堆无法再拓展时将抛出`OutOfMemoryError`异常
- 方法区(Method Area): 同Java堆一样也是各线程共享的内存区域, 用于存储已被虚拟机加载的类信息，常量，静态变量，即时编译器编译后的代码等。Class文件中除了有类的版本，字段，方法，接口等描述信息外，还有编译期生成的各种字面量和符号引用，这部分内容会在类加载后被放入方法区的运行时常量池(Runtime Constant Pool)中存放

## Hotspot虚拟机对象
- 虚拟机遇到一条`new`指令时，首先去检查这个指令的参数是否能在常量池中定位到一个类的符号引用，并且检查这个符号引用代表的类是否已被加载，解析和初始化过，若没有则执行类加载。之后虚拟机将为新生对象分配内存, 内存分配完成后，虚拟机需要将分配到的内存空间都初始化为零值。接下来，虚拟机需要对对象进行必要的设置，例如这个对象是哪个类的实例，如何才能找到类的元数据信息，对象的哈希吗，对象的GC分代年龄等信息，最后执行<init>方法
- 若Java堆中的内存是规整的(已使用的内存与空闲内存之间放着一个指针作为分界点的指示器)，分配内存就是将指针向空闲空间方向挪动一段与对象大小相等的距离，这种内存分配方式被称为指针碰撞(bump the pointer)
- 若Java堆中的已使用的内存和空闲内存相互交错，虚拟机必须维护一个列表, 记录哪些内存块是可用的，在分配的时候从列表中找到一块足够大的空间划分给对象实例，并更新列表上的记录，这种内存分配方式被作为空闲列表(free list)
- 在HotSpot虚拟机中，对象在内存中存储的布局可分为: 对象头，实例数据和对齐填充(padding)
- 对象头: 包括存储对象自身的运行时数据(哈希码，GC分代年龄，锁状态标志，线程持有的锁，偏向线程ID, 偏向时间戳)与类型指针(即对象指向它的类元数据的指针)
- 实例数据: 程序代码中定义的各种类型的字段内容，内容的存储顺序受到虚拟机分配策略参数(FieldsAllocationStyle)和字段在Java源码中定义顺序影响。虚拟机默认分配策略为longs/doubles, ints, shorts/chars, bytes/booleans, ordinary object pointers
- 对齐填充: 虚拟机要求对象的起始地址必须是8字节的整数倍，而对象头部分正好是8字节的1倍或2倍，当对象实例数据部分未对齐时需要通过对齐填充来补全
- 对象的访问定位: 包括句柄访问和直接指针
- 句柄访问: Java堆会划分出一块内存来作为句柄池，reference中存储的便是对象的句柄地址，而句柄中包含了对象实例数据与类型数据(方法区)各自的具体地址信息
- 直接指针: reference中存储的便是对象地址, Java堆对象的布局中放置了访问类型数据的相关信息

## GC算法
- 对象存活判定算法: 引用计数(reference counting)和可达性分析(reachability analysis)
- 引用计数: 给对象添加一个引用计数器，有地方引用时加1，引用失效时减1，计数器为0时回收对象
- 可达性分析: 从GC Roots向下搜索，搜索路径称为引用链(reference chain)，当一个对象到GC Roots没有任何引用链相连时将被第一次标记。如果该对象覆盖了`finalize`方法或`finalize`没有被虚拟机调用过，该对象会被放置于F-Queue中，并由一个虚拟机自动建立的低优先级的Finalizer线程去执行`finalize`方法, 若`finalize`执行完该对象仍然没有与引用链上的任意对象建立关联则回收该对象
- GC Roots: 可作为GC Roots的对象有虚拟机栈中引用的对象，方法区中类静态属性/常量引用的对象，本地方法栈中JNI引用的对象
- 引用: JDK1.2后，Java将引用分为了强引用(例如`Object obj = new Object()`)，软引用(SoftReference)，弱引用(WeakReference)和虚引用(PhantomReference)
- 垃圾搜集算法: 标记-清除(mark-sweep), 复制， 标记-整理(mark-compact), 分代收集(generational collection)
- 标记-清除: 首先标记所有需要回收的对象，标记完成后统一回收被标记的对象。标记和清除的效率都不高，标记清除后会产生大量不连续的内存碎片, 空间碎片过多可能会导致后续的内存分配无法找到足够的连续内存而提前触发GC
- 复制: 将可用内存划分为两块，每次只使用其中的一块，当一块的内存用完了，便将依然存活的对象复制到另一块内存中并清理已使用过的内存空间
- 标记-整理: 标记完成后让所有存活的对象向一端移动，然后清理端边界以外的内存
- 分代收集: 根据对象存活周期将内存分为几块， 新生代使用复制算法，老生代使用标记-清除或者标记-整理算法
- HotSpot算法实现: 枚举根结点时必须停顿所有Java执行线程(Stop The World), 由于主流Java虚拟机使用的都是准确式GC，当程序执行停顿下来后并不需要检查所有执行上下文和全局的引用位置。在类加载完成时HotSpot会将对象内偏移量类型计算出来，在JIT编译过程中也会在特点的位置(安全点)记录下栈和寄存器中哪些位置是引用，并生成OopMap进行存储。虚拟机并不会为每条导致引用关系变化的指令生成对应的OopMap, 只有在安全点(safepoint)和安全区域(safe region)才会将程序执行停顿, 序列复用的指令(方法调用，循环跳转，异常跳转)才会产生安全点, 当GC需要中断线程时在安全点处设置一个标志，各个线程主动轮询这个标志，发现中断标志为真时自己中断挂起；当线程执行到安全区域中代码时将自己标识为进入安全区域，当JVM发起GC时不管标识为安全区域的线程

## 垃圾收集器
- Serial: 单线程收集器，进行GC时必须暂停所有用户线程, 新生代名为DefNew(Default New Generation)
- ParNew: 多线程收集器, 多条GC线程并行(parallel)工作, 用户线程仍处于等待状态
- Parallel Scavenge: 多线程收集器, 着重于达到一个可控的吞吐量(throughput), 即CPU用于运行用户代码的时间与CPU总消耗时间的比值
- Serial Old: Serial的老年代版本, 使用标记-整理算法
- Parallel Old: Parallel Scavenge的老年代版本, 使用标记-整理算法
- CMS(Concurrent Mark Sweep): 初始标记(标记GC Roots能够直接关联的对象) -> 并发标记 -> 重新标记(修正并发标记期间的标记变动)-> 并发清除。对CPU资源非常敏感, 无法处理浮动垃圾(并发清除期间产生的垃圾)，标记-清除算法容易产生大量空间碎片
- G1(Garbage-First): 使用复制算法与标记-整理算法。将Java堆划分为大小相等的独立区域，新生代和老生代不再物理隔离。通过跟踪各个独立区域中垃圾堆积的价值(回收可获得空间/回收时间)大小，优先回收价值最大的区域, 建立了可预测的停顿时间模型
- `[Times: user=0.01 sys=0.00 real=0.02 secs]`, 分别代表用户态消耗的CPU时间，内核态消耗的CPU时间，操作从开始到结束的墙钟时间(Wall Clock Time), Wall Clock Time包括非运算的等待耗时，如I/O,线程阻塞等

## 内存分配和回收策略
- 对象优先在Eden分配: 大多数情况下，对象在新生代Eden区分配，当Eden区没有足够空间分配时虚拟机将发起一次Minor GC(新生代GC)
- 大对象直接进入老年代: 大对象指的是需要大量连续内存空间的Java对象, 如`byte[] allocation = new byte[4 * 1024 * 1024]`
- 长期存活的对象将进入老年代: 虚拟机给每个对象定义了一个对象年龄计数器, 对象在Eden被分配且经过第一次Minor GC后依然存活，且能被Survivor容纳的话将被移动到Survivor中，且对象年龄计数器的值被设为1, 之后每经过一次Minor GC计数器加1，当计数器的值达到15(-XX:MaxTenuringThreshold)时该对象将被移动到老年代
- 动态对象年龄判定: 当Survivor空间中相同年龄的对象大小总和超过Survivor的一半是，年龄大于等于该年龄的对象直接进入老年代
- 空间分配担保: Minor GC之前，JVM会先检查老年代最大可用连续内存空间是否大于新生代所有对象之和，条件成立则认为Minor GC是安全的(复制算法)；若条件不成立JVM将会查看`HandlePromotionFailure`的设置值, 如果允许担保失败，将检查老年代最大可用连续内存空间是否大于每次晋升到老年代对象的评价大小，若条件成立则进行Minor GC, 否则进行Full GC

## 类文件结构
- Java虚拟机不和任何语言绑定，只于Class文件关联
- Class文件是一组以8位字节为基础单位的二进制流，各个数据项目之间没有分隔符，当遇到需要占用8位字节以上的数据项时则按照高位在前(大端, Big-Endian)的方式分割成若干个8位字节进行存储
- 每个Class文件的头4个字节(0xCAFEBABE)被称为魔数(Magic Number), 唯一作用便是身份识别(能否被虚拟机接受)。第5和6字节是次版本号，第7和8字节是主版本号
- 常量池: 常量池中常量的数量是不固定的，因此需要在常量池的入口放置一项u2类型的数据,代表常量池容量计数值(constant_pool_count),常量池容量计数值是从1开始的。常量池中存放了字面量(literal)和符号引用(symbolic references)，字面量包括字符串，常量值等；符号引用包括类和接口的全限定名，字段的名称和描述符，方法的名称和描述符。
- 访问标志(access_flags): 标识了用于识别类或接口的访问信息，例如Class是类还是接口，是否定义为public类型等
- 类索引(this_class)，父类索引(super_class), 接口索引集合(interfaces)
- 字段表(field_info): 用于描述接口或类中声明的变量，如字段的作用域(public/private/protected), 实例变量或类变量(static)等
- 方法表: 依次包括访问标志，名称索引，描述符索引，属性表集合(attributes)
- 属性表: `Code`(Java程序方法体中的代码经过编译后的字节码), `Exceptions`(方法描述时在throws关键字后列举的异常), `LineNumberTable`(Java源码行号与字节码行号之间的对应关系, 取消生成异常堆栈将不会显示出错行号也无法设置断点调试), `LocalVariableTable`(Java源码中定义变量与栈帧中局部变量表中变量的对应关系, 取消生成将无法在调试期间根据参数名称从上下文中获得参数值), `SourceFile`(Class文件的源码文件名), `ConstantValue`(通知JVM自动为静态变量赋值), `InnerClasses`(内部类和宿主类的关联)

## 字节码指令
- JVM的指令由1字节的数字(指令集为0-255)和0到多个参数构成，前者被称为操作码(opcode),后者被称为操作数(operands)。由于JVM采用面向操作数栈而不是寄存器，因此大多数指令只包含opcode
- 大多数指令都包含了操作对应的数据类型信息，如`iload`用于从局部变量表中加载`int`型的数据到操作栈中
- 加载和存储指令: 将一个局部变量加载到操作栈(`load`)，将一个数值从操作数栈存储到局部变量表(`store`), 将一个常量加载到操作数栈(`const`, `push`), 扩充局部变量表的访问索引(`wide`)
- 算术指令: 整型与浮点型，对于`byte`, `short`, `char`, `boolean`没有直接支持是算术指令，使用操作`int`类型的指令代替
- 类型转换指令: 宽化类型转换(widening numeric conversions)与窄化类型转换(narrowing numeric conversions), widening包括`int`->`long/float/double`, `long`->`float/double`, `float`->`double`; narrowing的转换规则为`NaN`->`0(int/long)`, IEEE754向零舍入模式取整
- 对象创建与访问指令: `new`, `newarray`, `getfield`, `arraylength`, `instanceof`
- 操作数栈管理指令: `pop`, `dup`, `swap`
- 控制转移指令: 有条件或无条件的修改PC寄存器的值
- 方法调用和返回指令: `invokevirtual`(调用对象的虚方法), `invokeinterface`(在运行时搜索实现该接口方法的对象), `invokespecial`(调用实例<init>方法，私有方法和父类方法), `invokestatic`, `invokedynamic`(在运行时解析并执行调用点限定符所引用的方法)
- 异常处理指令: `athrow`
- 同步指令: `monitorenter`, `monitorexit`

## 字节码执行引擎
- 运行时栈帧(stack frame): 运行时数据区中虚拟机栈的栈元素，存储了方法的局部变量表，操作数栈，动态连接和方法返回地址等信息
- 局部变量表: 容量以变量槽(variable slot)为最小单位, 第0位的Slot默认用于传递方法所属对象实例的引用(this)，同时为了节省栈帧空间Slot是可以重用的，当前字节码PC计数器的值已经超出某个变量作用域，那么这个变量的Slot就可以交给其他变量使用
- 操作数栈(operand stack): 概念模型中栈帧作为虚拟机栈的元素是相互独立的，在具体实现中下面的栈帧操作数栈与上方的栈帧局部变量表多存在部分重叠的共享区域
- 方法返回地址: 方法开始执行后有两种方式退出，一是执行引擎遇到任意一个方法返回的字节码指令,二是JVM内部异常/`athrow`字节码指令产生的异常未在方法的异常表中搜索到匹配的异常处理器.前者被称为正常完成出口(Normal Method Invocation Completion)，后者被称为异常完成出口(Abrupt Method Invocation Completion)。无论哪种退出方式，在方法退出后都需要返回到方法被调用的位置程序才能继续运行
- 方法调用: 方法调用阶段唯一的任务就是确定被调用方法的版本
- 解析: 方法调用的目标方法在Class文件中都是一个常量池中的符号引用, 在类加载解析阶段会将其中一部分符号引用转化为直接引用,这类方法的调用被称为解析(resolution)
- 静态分派(Method Overload Resolution): `Human man = new Man();`, `Human`被称为变量的静态类型或外观类型(apparent type), 最终的类型在编译阶段可知；`Man`被称为变量的实际类型(actual type), 最终类型在运行期才能确定。虚拟机在重载时通过参数的静态类型作为判定依据
- 动态分派: JVM如何根据实际类型分派方法执行版本?
- `invokevirtual`指令的运行时解析过程: 找到操作数栈顶的第一个元素指向对象的实际类型C, 若C中找到与常量中描述符和简单名称都相符的方法，则进行权限校验，通过则返回该方法的直接引用，否则返回`java.lang.IllegalAccessError`; 若没找到则按照继承关系依次对C的父类进行搜索和校验，若仍未找到则返回`java.lang.AbstractMethodError`
- 编译过程: 程序源码->词法分析->单词流->语法分析->AST->指令流->解释器->解释执行 / 程序源码->词法分析->单词流->语法分析->AST->优化器 -> 中间代码 -> 生成器 -> 目标代码
- Java编译器输出的指令流基本上是一种基于栈的指令集架构(Instruction Set Architecture, ISA)，指令流中的指令大部分都是零地址指令，依赖于操作数栈工作

```java
// Slot复用对GC的影响
public static voide main(String[] args) {
  { byte[] placeholder = new byte[64 * 1024 * 1024]; }
  // int a = 0;
  System.gc(); // placeholder占用的Slot未被其他变量复用, 局部变量表仍保持对它的关联
}
```

```java
// 静态分派
public class StaticDispatch {
  static abstract class Human {}
  static class Man extends Human {}

  public void sayHi(Human guy) { System.out.println("hi, guy"); }
  public void sayHi(Man guy) { System.out.println("hi, man"); }

  public static void main(String[] args) {
    Human human = new Man();
    Man man = new Man();
    StaticDispatch s = new StaticDispatch();
    s.sayHi(human);
    s.sayHi(man);
  }
}
// 动态分派
public class DynamicDispatch {
  static abstract class Human {
    protected abstract void sayHi();
  }

  static class Man extends Human {
    @Override
    protected void sayHi() {
      System.out.println("hi, man");
    }
  }

  static class IronMan extends Man {
    @Override
    protected void sayHi() {
      System.out.println("hi, hero");
    }
  }

  public static void main(String[] args) {
    Human man = new Man(); // new -> dup -> invokespecial -> astore_1
    man.sayHi(); // aload_1(将对象的引用压入栈顶，被称为接受者Receiver) -> invokevirtual
    man = new IronMan();
    man.sayHi();
  }
}
```

```java
public int calc() {
  int a = 100; // bipush 100(将单字节整型常量推入操作数栈顶) -> istore_1(操作数栈顶整型值出栈并存放到第一个局部变量Slot中)
  int b = 200; // sipush 200 -> istore_2
  int c = 300; // sipush 300 -> istore_3
  return (a + b) * c; // iload_1 -> iload_2 -> iadd -> iload_3 -> imul -> ireturn
}
```

## 类加载机制
- 生命周期: 加载(loading)，连接(linking)[验证(verification)，准备(preparation)，解析(resolution)]，初始化(initialization)，使用(using)，卸载(unloading)
- 类的立即初始化: 遇到`new`, `getstatic`, `pubstatic`, `invokestatic`这4条字节码指令时, 使用`java.lang.reflect`包的方法对类进行反射调用时，父类未初始化时，主类(包含main方法的类)未初始化时, `java.lang.invoke.MethodHandle`实例的解析结果为`REF_getStatic`/
`REF_putStatic`/`REF_invokeStatic`,且这个方法句柄对应的类未初始化时
- 加载: 通过类的全限定名获取定义此类的二进制字节流->将该字节流所代表的静态结构转化为方法区的运行时数据结构->在内存中生成一个代表该类的`java.lang.Class`对象，作为方法区该类各种数据的访问入口
- 验证: 文件格式验证，元数据验证(类是否存在父类，类的父类是否继承了不允许被继承的类)，字节码验证(类型转换是否合法，确保跳转指令不会跳转到方法体外的字节码指令上)，符号引用验证(符号引用中通过字符串描述的全限定名是否能找到对应的类)
- 准备: 为类变量(被`static`修饰的对象)分配内存并设置其初始值(数据类型的零值/类字段的字段属性表中的ConstantValue属性)，例如`public static int val = 123`初始值为0, `public static final int val = 123`的初始值为123
- 解析: 将常量池中的符号引用替换为直接引用的过程
- 初始化: 执行类构造器<clinit>方法的过程, <clinit>由编译器自动收集类中所有类变量的赋值动作和静态语句块(static {})中
的语句合并而成; <clinit>和<init>不同，不需要显示调用父类构造器(第一个执行的<clinit>的类肯定是`java.lang.Object`)
- 类加载器: 自定义类加载器 + 自定义类加载器 -> 应用程序类加载器(Application ClassLoader) -> 拓展类加载器(Extension ClassLoader) -> 启动类加载器(Bootstrap ClassLoader), 这种类加载器之间的层次关系被称为双亲委派模型(parents delegation model)。类加载器收到类加载的请求后将该请求委托给父类加载器完成，所有加载请求最终都应传递到顶层的启动类加载器。当父加载器无法完成该加载请求时(搜索范围中未找到所需的类),子加载器才会尝试自己加载
- 线程上下文加载器(Thread Context ClassLoader): 若创建线程时未调用`java.lang.Thread`类的`setContextClassLoader`设置类加载器，将从父线程继承一个。若应用程序全局范围都未设置，类加载器默认为应用程序类加载器
- OSGi: 将以`java.*`开头的类委托给父类加载器->将委派列表名单中的类委托给父类加载器->将Import列表中的类委托给Export类的Bundle类加载器->当前Bundle的ClassPath对应的类加载器->Fragement Bundle的类加载器->Dynamic Import列表的Bundle类加载器
