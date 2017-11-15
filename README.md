# JavaDesignPattern
设计模式在java中的应用示例代码

建议阅读源码时配合文章介绍

http://blog.anxpp.com/index.php/archives/489/

或者

http://blog.csdn.net/anxpp/article/details/51224293


以上为原作者内容，以下为结合上述博客进行整理内容。

java设计模式
============
模式可以分成3类：创建型、行为型和结构型。

#  创建型模式

创建型模式涉及对象的实例化，特点是不让用户代码依赖于对象的创建或排列方式，避免用户直接使用new创建对象。

创建型模式有以下5个:

工厂方法模式、抽象工厂方法模式、生成器模式、原型模式和单例模式。

# 行为型模式

行为型模式涉及怎样合理的设计对象之间的交互通信，以及怎样合理为对象分配职责，让设计富有弹性，易维护，易复用。

行为型模式有以下11个：

责任链模式、命令模式、解释器模式、迭代器模式、中介者模式、备忘录模式、观察者模式、状态模式、策略模式、模板方法模式和访问者模式。

# 结构型模式

结构型模式涉及如何组合类和对象以形成更大的结构，和类有关的结构型模式涉及如何合理使用继承机制；和对象有关的结构型模式涉及如何合理的使用对象组合机制。

结构型模式有以下7个：

适配器模式、组合模式、代理模式、享元模式、外观模式、桥接模式和装饰模式。

创建型模式
--------------------
### 1.单例模式（Singleton Pattern）

Ensure a class only has one instance,and provide a global point of access to it.<br> 保证一个类仅有一个实例，并提供一个访问它的全局访问点。
 
【何时使用】：

* 当系统需要某个类只有一个实例的时候。

【优点】：

* 单例模式的类唯一实例由其本身控制，可以很好的控制用户何时访问它。

单例模式概念很简单，而且也比较常用。<br>
如果直接将例子应用到多线程中，可以直接把getInstance()设置为同步的(synchronized)，但是并不高效，任一之后，只能有一个线程可以调用这个方法，其余的会排队等待。<br>
所以整个方法做同步不是优解，那就只同步代码块就好了。这就引出了双重检验锁，即在同步块外检查一次null，然后再在同步块内检查一次。但是最终这种方式也是会有问题的，使用静态内部类是一种比较好的方式。


### 2.工厂方法模式（Factory Method Pattern）

别名：虚拟构造（Another Name：Virtual Constructor）。<br>
Define an interface for creating an object,but let subclasses decide which class to instantiate.Factory Method lets a class defer instantiation to subclassess.<br>
定义一个用于创建对象的接口，让子类决定实例化哪一个类。工厂方法使一个类的实例化延迟到其子类。

【何时使用】：

* 用户需要一个类的子类的实例，但不希望与该类的子类形成耦合。
* 用户需要一个类的子类的实例，但用户不知道该类有哪些子类可用。

【优点】：

* 使用工厂方法可以让用户的代码和某个特定类的子类的代码解耦。
* 工厂方法使用户不必知道它所使用的对象是怎样被创建的，只需知道该对象有哪些方法即可。

简单工厂模式<br>
简单工厂模式又称静态工厂方法模式。从命名上就可以看出这个模式一定很简单。它存在的目的很简单：定义一个用于创建对象的接口。<br>
如果一个一些对象（产品），已经确定了并不易改变和添加新的产品，那么久可以使用简单工厂模式。下面就是简单工厂的例子：
```java
//演示简单工厂
public class SimpleFactory {
    public static void main(String args[]) throws Exception{
        Factory factory = new Factory();
        factory.produce("PRO5").run();
        factory.produce("PRO6").run();
    }
}
//抽象产品
interface MeizuPhone{
    void run();
}
//具体产品X2
class PRO5 implements MeizuPhone{
    @Override
    public void run() {
        System.out.println("我是一台PRO5");
    }
}
class PRO6 implements MeizuPhone{
    @Override
    public void run() {
        System.out.println("我是一台PRO6");
    }
}
//工厂
class Factory{
    MeizuPhone produce(String product) throws Exception{
        if(product.equals("PRO5"))
            return new PRO5();
        else if(product.equals("PRO6"))
            return new PRO6();
        throw new Exception("No Such Class");
    }
}
```
很容易看出，简单工厂模式是不易维护的，如果需要添加新的产品，则整个系统都需要修改。如果我们需要添加诸如PRO7、PRO8等产品，直接在工程类中添加即可。但是如果这时候根部不知道还有什么产品，只有到子类实现时才知道，这时候就需要工厂方法模式。


工厂方法模式<br>
工厂方法模式去掉了简单工厂模式中工厂方法的静态属性，使得它可以被子类继承。这样在简单工厂模式里集中在工厂方法上的压力可以由工厂方法模式里不同的工厂子类来分担。<br>
Java中的Collection接口的实现都能通过iterator()方法返回一个迭代器，而不同的实现的迭代器都在该实现中以内部类的方式对Iterator接口实现的，然后通过iterator()方法返回。那么，这个iterator()方法就是一种工厂方法。<br>
可以看到，在这里抽象产品是Iterator接口，具体产品就是Collection接口的实现中对Iterator接口的实现，构造者是Collection接口，其提供的工厂方法就是Iterator iterator();，具体构造者就是Collection的实现。而工厂方法模式的结构，也就是由前面加粗的4部分组成。<br>
一抽象产品类派生出多个具体产品类；一抽象工厂类派生出多个具体工厂类；每个具体工厂类只能创建一个具体产品类的实例。 即定义一个创建对象的接口（即抽象工厂类），让其子类（具体工厂类）决定实例化哪一个类（具体产品类）。“一对一”的关系。<br>
与简单工厂间的取舍：工厂方法模式和简单工厂模式在定义上的不同是很明显的。工厂方法模式的核心是一个抽象工厂类,而不像简单工厂模式, 把核心放在一个实类上。工厂方法模式可以允许很多实的工厂类从抽象工厂类继承下来, 从而可以在实际上成为多个简单工厂模式的综合,从而推广了简单工厂模式。 反过来讲,简单工厂模式是由工厂方法模式退化而来。设想如果我们非常确定一个系统只需要一个实的工厂类, 那么就不妨把抽象工厂类合并到实的工厂类中去。而这样一来,我们就退化到简单工厂模式了。<br>
工厂方法的加入，使得对象的数量成倍增长。当产品种类非常多时，会出现大量的与之对应的工厂对象，这不是我们所希望的。<br>
如果再分得详细一点，一个工厂可能不只是生产手机（如小米除了手机，连电饭锅都有），但有得工厂智能生成低端的产品，而大一点的工厂可能通常是生成更高端的产品。所以一个工厂是不够用了，这时，就应该使用抽象工厂来解决这个问题。

### 3.抽象工厂方法模式（Abstract Factory Pattern）  
别名：配套（Another Name：Kit）<br>
Provide an interface for creating families of related or dependent objects without specifying their concrete classess.<br>
提供一个创建一系列或相互依赖对象的接口，而无须指定他们的具体的类。

* 简单工厂模式是由一个具体的类去创建其他类的实例，父类是相同的，父类是具体的。
* 工厂方法模式是有一个抽象的父类定义公共接口，子类负责生成具体的对象，这样做的目的是将类的实例化操作延迟到子类中完成。
* 抽象工厂模式提供一个创建一系列相关或相互依赖对象的接口，而无须指定他们具体的类。它针对的是有多个产品的等级结构。而工厂方法模式针对的是一个产品的等级结构。

### 4.生成器模式（Builder Pattern）
Separate the construction of a complex object from its representation so that the same construction process can create different representations.<br>
将一个复杂对象的构建与它的表示分离，使同样的构建过程可以创建不同的表示。

【何时使用】：  

* 当系统准备为用户提供一个内部结构复杂的对象，而且在构造方法中编写创建该对象的代码无法满足用户需求时，就可以使用生成器模式老构造这样的对象。
* 当某些系统要求对象的构造过程必须独立于创建该对象的类时。

【优点】：

*  生成器模式将对象的构造过程封装在具体的生成器中，用户使用不同的具体生成器就可以得到该对象的不同表示。
*  生成器模式将对象的构造过程从创建该对象的类中分离出来，使用户无须了解该对象的具体组件。
*  可以更加精细有效的控制对象的构造过程。生成器将对象的构造过程分解成若干步骤，这就是程序可以更加精细，有效的控制整个对象的构造。
*  生成器模式将对象的构造过程与创建该对象类解耦，是对象的创建更加灵活有弹性。
*  当增加新的具体的生成器是，不必修改指挥者的代码，即该模式满足开-闭原则。

模式的重心在于分离构建算法和具体的构造实现，从而使构建算法可以重用。

### 5.原型模式（Prototype Pattern）  

Specify the kinds of objects to create using a prototypical instance,and create new objects by copying this prototype.<br>
用原型实例指定创建对象的种类，并且通过复制这些原型创建新的对象。

【何时使用】：

*  程序需要从一个对象出发，得到若干个和其状态相同，并可独立变化其状态的对象时。
*  当对象的创建需要独立于它的构造过程和表示时。
*  一个类创建实例状态不是很多，那么就可以将这个类的一个实例定义为原型，那么通过该实例复制该原型得到新的实例可能比重新使用类的构造方法创建新实例更方便。

【优点】:

* 当创建类的新实例的代价更大时，使用原型模式复制一个已有的实例可以提高创建新实例的效率。
* 可以动态的保存当前对象的状态。在运行时，可以随时使用对象流保存当前对象的一个复制品。
* 可以在运行时创建新的对象，而无须创建一系列类和集成结构。
* 可以动态的添加、删除原型的复制品。

原型模式要求对象实现一个可以“克隆”自身的接口，这样就可以通过复制一个实例对象本身来创建一个新的实例。这样一来，通过原型实例创建新的对象，就不再需要关心这个实例本身的类型，只要实现了克隆自身的方法，就可以通过这个方法来获取新的对象，而无须再去通过new来创建。

Java中所有类都直接或间接继承自Object类，Object类中已有clone()方法：”protected native Object clone() throws CloneNotSupportedException;“，可以看到权限是protected的，所以仅有子类可以访问这个方法，但我们可以在子类中重写这个方法，将访问权限上调到public，然后方法体里面return super.clone()。

我们能看到这个Object方法是可能会抛出异常的，我们必须实现Cloneable接口，才可以使用这个方法，否则会抛出“java.lang.CloneNotSupportedException”的异常。这个Cloneable接口其实是空的，实现它的目的在于让JVM知道这个对象是可以可复制的，否则clone()时就会发生异常。

除此之外，java中还有一种序列化，只需要对象实现Serializable接口。

----------------

行为型模式
----------------
### 6.责任链模式（Chain of Responsibility Pattern）
使很多个对象都有机会处理请求，从而避免请求的发送者和接收者之间的耦合关系。将这些对象连成一条链，并沿着这条链传递该请求，直到有一个对象处理它为止。

【何时使用】：

* 有许多对象可以处理用户请求，希望程序在运行期间自动确定处理用户的那个对象。
* 希望用户不必明确指定接收者的情况下，想多个接受者的一个提交请求。
* 程序希望动态的指定可处理用户请求的对象集合。

【优点】

*  低耦合。
*  可以动态的添加删除处理者或重新指派处理者的职责。
*  可以动态改变处理者之间的先后顺序。

通常来说，一个纯粹的责任链是先传给第一个处理，如果处理过了，这个请求处理就此结束，如果没有处理，再传给下一个处理者。

我们写java web程序的时候，通常会编写一些过滤器（Filter），然后配置到web.xml中，这其实就是责任链模式的一种实践。而使用Log4j记录日志，配置级别的时候，也同样用到了责任链模式。

我们使用责任链模式的时候，不一定非得某一处理者处理后就得终止请求的传递，如果有其他需求，我们依然可以继续传递这个请求到下一个具体的处理者。

java web中的过滤器，log4j的日志就是使用行为型模式。

### 7.命令模式
别名：动作，事物（Another Name：Action，Transaction）  <br>
Encapsulate a request as an object,thereby letting you parameterize clients with different reauests,queue or log requests,and support undoable operations.<br>
将一个请求封装为一个对象，从而使用户可用不同的请求对客户进行参数化；对请求排队或记录请求日志，以及支持可撤销的操作。
【何时使用】：

* 程序需要在不同的时刻指定、排列和执行请求。
* 程序需要提供撤销操作。
* 程序需要支持宏操作。

【优点】：

* 在命令模式中，请求者（Invoker）不直接与接受者（Receiver）交互，及请求者（Invoker）不包含接受者（Receiver）的引用，因此彻底消除了彼此间的耦合。
* 命令模式满足“开-闭原则”。如果增加新的具体命令和该命令的接受者，不必修改调用者的代码，调用者就可以使用新的命令对象；反之，如果增加新的调用者，不必修改现有具体命令和接收者，新增加的调用者就可以使用已有的具体命令。
* 由于请求者的请求被封装到具体的命令中，那么就可以将具体命令保存到持久化的媒介中，在需要的时候，重新执行这个具体命令。因此，使用命令模式可以记录日志。
* 使用命令模式可以对请求者的“请求”进行排队。每个请求都各自对应一个具体命令，因此可以按一定顺序执行这些具体命令。

一个对象有多种操作，但是我们不希望调用者（请求者）直接使用，我们就额外添加一个对象，然后让调用者通过这个对象来使用那些操作。

比如，我们有一个类可以在磁盘上新建或是删除文件（接收者），但是我们不希望直接提供给别人（请求者）使用，所以我们就为它的各种操作创建对应的命令，下面我们用代码来实现这个需求：

