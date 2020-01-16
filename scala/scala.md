# 1. scala
## 1.1. class定义
在scala中，**类名**可以和**对象**名为同一个名字，该对象称为**该类的伴生对象**，类和伴生对象可以相互访问他们的私有属性，但是**他们必须在同一个源文件内**。类只会被编译，不能直接被执行，类的申明和主构造器在一起被申明，在一个类中，主构造器只有一个所有必须在内部申明主构造器或者是其他申明主构造器的辅构造器，主构造器会执行类定义中的所有语句。scala对每个字段都会提供getter和setter方法，同时也可以显示的申明，**但是针对val类型，只提供getter方法，默认情况下**，字段为公有类型，可以在setter方法中增加限制条件来限定变量的变化范围，在scala中方法可以访问改类所有对象的私有字段
## 1.2. object定义
在scala中没有静态方法和静态字段，所以在scala中可以用`object`来实现这些功能，直接用对象名调用的方法都是采用这种实现方式，例如`Array.toString`。对象的构造器在第一次使用的时候会被调用，如果一个对象从未被使用，那么他的构造器也不会被执行；对象本质上拥有类（scala中）的所有特性，除此之外，object还可以一扩展类以及一个或者多个特质：例如，
```scala  
abstract class ClassName（val parameter）{}
object Test extends ClassName(val parameter){}
```
*注意：object不能提供构造器参数，也就是说object必须是无参的*
## 1.3. trait定义
在java中可以通过interface实现多重继承，在Scala中可以通过特征（trait）实现多重继承，不过与java不同的是，它可以定义自己的属性和实现方法体，在没有自己的实现方法体时可以认为它时java interface是等价的，在Scala中也是一般只能继承一个父类，可以通过多个with进行多重继承。
```scala
trait TraitA{}
trait TraitB{}
trait TraitC{}
object Test1 extends TraitA with TraitB with TraitC{}
```
## 1.4. calss和object的区别
1 class
scala的类和C#中的类有点不一样，诸如： 声明一个未用priavate修饰的字段 var age，scala编译器会字段帮我们生产一个私有字段和2个公有方法get和set ，这和C#的简易属性类似；若使用了private修饰，则它的方法也将会是私有的。这就是所谓的统一访问原则。
```scala
//类默认是public级别的  
class Person{  
  var age=18  //字段必须得初始化()  
  def Age=age //这个是方法，没有参数可以省略()  
  def incremen(){this.age+=1}  
}  
  
  
class Student{  
  var age=20     //底层编译器会自动为私有的age添加get和set的公有方法,可以理解为伪public类型  
  private[this] var gender="male" //private[this] 只有该类的this可以使用  
  private var name="clow" //声明了private,底层编译器会自动为私有的name添加get和set的私有方法  
  //但是可以自己定义属性方法  
  def getName=this.name  
  def setName(value:String){this.name=value}  
}  
  
//构造器的使用  
class Teacher {  
  var age: Int = _  
  var name: String = _  //可以预留  
  
  //重载的构造器和C#里面的public Teacher(){}类似  
  def this(age: Int, name: String){  
    this() //必须得调用一次主构造器  
    this.age=age  
    this.name=name  
  }  
}  
```
2、scala没有静态的修饰符，但object下的成员都是静态的 ,若有同名的class,这其作为它的伴生类。在object中一般可以为伴生类做一些初始化等操作,如我们常常使用的val array=Array(1,2,3)  (ps:其使用了apply方法)
```scala
object Dog{  
  private var age=0  
  def Age={  
    age+=1  
    age  
  }  
}  
  
class Dog{  
  var age1=Dog.age //Dog.age是object Dog的私有字段。这不禁让我回想起了C++的友元类  
}  
```
3、Apply的使用，怎么解释呢？ 还是来看看怎么用它来实现单例模式
```scala
class ApplyTest private{  //添加private隐藏构造器  
  def sayHello(){  
    println("hello jop")  
  }  
}  
  
object ApplyTest{  
  var instant:ApplyTest=null  
  def apply() ={  
    if(instant==null) instant=new ApplyTest  
    instant  
  
  }  
}  
  
object ApplyDemo {  
  def main(args:Array[String]){  
      val t=ApplyTest()  
      t.sayHello()  
  }  
}  
```
## 1.5. "_"的使用
1. 作为“通配符”，类似Java中的`*`。`如import scala.math._`
2. `_*`作为一个整体，告诉编译器你希望将某个参数当作参数序列处理！例如val s = sum(1 to 5:_*)就是将1 to 5当作参数序列处理。
3. 指代一个集合中的每个元素。例如我们要在一个Array a中筛出偶数，并乘以2，可以用以下办法：`a.filter(_%2==0).map(2*_)`
4. 在元组中，可以用方法`_1`, `_2`, `_3`访问组员。如`a._2`。其中句点可以用空格替代。
5. 使用模式匹配可以用来获取元组的组员，例如: `val (first, second, third) = t`但如果不是所有的部件都需要，那么可以在不需要的部件位置上使用_。比如上一例中`val (first, second, _) = t`
6. 还有一点，下划线_代表的是某一类型的默认值: 对于Int来说，它是0;对于Double来说，它是0.0;对于引用类型，它是null
## 1.6. Val 和Var 的区别
1. 最直观的就是：val定义的变量不能被再次赋值，而var定义的可以. 
> scala> val s=10
>s: Int = 10
> 
>scala> s=11
><console>:27: error: reassignment to val\n
         s=11
>          ^
> 
>scala> var a=10
>a: Int = 10
> 
>scala> a=11

2. 事实上，var 修饰的对象引用可以改变，val 修饰的则不可改变，但对象的状态却是可以改变的。
```scala
class A(n: Int) { 
    var value = n //利用可变修饰符定义一个值
} 
class B(n: Int) { 
    val value = new A(n) //利用不变修饰符定义一个值
} 
 
class C(n: Int) { 
    var value = new A(n) //利用可变修饰符定义一个值
}
 
object Test { 
    def main(args: Array[String]) { 
        val x = new B(5) //首先定义一个不可变变量x,所以下一行会报错
        x = new B(6) // 错误，因为 x 为 val 修饰的，引用不可改变 
        x.value = new A(6) // 错误，因为 x.value 为 val 修饰的，引用不可改变 
        
        val y = new C(5) //定义一个不可变变量y,但是y.value是 var修饰的，所以下一行正确
        y.value = new A(7) //因为y.value 是var修饰的。当然y = new C(7)依然报错，因为y是val修饰的
        x.value.value = 6 // 正确，x.value.value 为var 修饰的，可以重新赋值 
    } 
}
```

### 1.6.1. 使用val的好处
1. 如果一个对象不想改变其内部的状态，那么由于不变性，我们不用担心程序的其他部分会改变对象的状态；
2. 当用 var 修饰的时候，你可能在多个地方重用 var 修饰的变量，这样会产生下面的问题：
    1. 对于阅读代码的人来说，在代码的确定部分中知道变量的值是比较困难的.
    2. 你可能会在使用代码前初始化代码，这样会导致错误；

### 1.6.2. 使用var的好处
1. 使用可变性可以大幅度提高程序的执行效率。
2. 避免了由于其他用途而对变量进行重用。

## 1.7. def需要注意的地方

```scala
abstract class Person {
    def id: Int  
}
class Student extends Person{
    override var id = 9527  //Error: method id_= overrides nothing

```
上述继承时，发生错误，因为用def定义的成员变量，不具备setter和getter方法。

那么如果父类中只定义了一个方法def id: Int，用来生成用户的id，而子类用var变量重写这个方法的话override var id = 9527，编译会报错method id_=overrides nothing，从报错中可以看出来，由于scala会为var变量自动生成了一个setter方法（id_），但是父类并没有这个方法，所以是无法重写的。

如果改下，必须为以下代码，此时重新定义了父类的setter方法
```scala
abstract class Person {
    def id: Int  
    def id_=(value: Int) //父类必须有set方法
}
class Student extends Person{
    override var id = 9527 //为var变量自动生成get和set方法
}
```
 也可以利用var定义函数的成员变量
```scala
abstract class Person1 {
    var id: Int  
}
class Student extends Person1{
     var id = 9527 //为var变量自动生成get和set方法
}
```