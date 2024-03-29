## 设计模式七大原则

面向对象的设计目标在于支持 **可维护性和复用性 **。可维护性指软件被理解、修改、适应和扩展的难易程度；复用性指软件能够被重复使用的难易程度。

### 1.开闭原则

> 软件实体应当对扩展开放，对修改关闭。

强调使用功能使用框架构建框架，实现扩展细节，提高系统可复用性和维护性。

如公司实行弹性作息时间，只规定每天工作8小时。意思就是说，对于每天工作8小时这个规定是关闭的，但是你什么时候来、什么时候走是开放的。早来早走，晚来晚走。



###  2. 依赖倒置

> 高层模块不应该依赖底层模块，都应该依赖抽象。抽象不应该依赖细节，细节应该依赖抽象。

针对抽象层编程，将具体类的对象通过依赖注入的方式注入到其他对象中。依赖注入是值当一个对象与其他对象发生依赖关系时采用抽象的形式来注入所依赖的对象。

常用的方式有3种：构造器注入、Setter 注入和接口注入。



### 3. 里氏代换

> 所有引用基类的地方都必须透明地使用其子类对象。

里氏替换原则表明，在软件中一个基类对象替换成他的子类对象，程序不会产生任何错误或异常，反过来则不成立。

例如：我喜欢动物，拿我就一定喜欢狗，因为狗是动物的子类；但我喜欢狗，不能以此断定我喜欢所有动物。

###  4. 单一职责

> 一个对象中应该包含单一职责，并且该职责被完整地封装在一个类中。

单一职责原则的另一种定义是：就一个类而言，应该只有一个引起它变化的原因。它是实现高内聚、低耦合的指导方针。



### 5. 接口隔离

> 客户端不应该依赖哪些它不需要的接口。



### 6. 合成复用

> 优先使用对象组合，而不是通过继承来达到复用的目的。



### 7. 迪米特法则

> 每个软件单位对其他软件只有最少的知识，而且局限于那些与本单位密切相关的软件单位。

迪米特法则还有几种定义方式，包括不要和陌生人说话，只与你的朋友直接通信等。

在迪米特法则中，其朋友包括以下几类：

> （1）当前对象本身（this）
>
> （2）以参数形式传入到当前对象中的对象
>
> （3）当前对象的成员对象
>
> （4）当前对象所创建的对象 