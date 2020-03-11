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
## 1.7. classof[T]与getClass的区别
`getClass`方法得到的是`Class[A]`的某个子类，而`classOf[A]`得到是正确的`Class[A]`，但是去比较的话，这两个类型是equals为true的。这里有细微的差别，体现在类型赋值时，因为java里的 Class[T]是不支持协变的，所以无法把一个 Class[_ < : A] 赋值给一个 Class[A]。
## 1.8. 关于协变和逆变
scala 逆变和协变的概念网上有很多解释， 总结一句话就是:**参数是逆变的或者不变的，返回值是协变的或者不变的。**  
<svg xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" contentScriptType="application/ecmascript" contentStyleType="text/css" height="312px" preserveAspectRatio="none" style="width:321px;height:312px;" version="1.1" viewBox="0 0 321 312" width="321px" zoomAndPan="magnify"><defs/><g><!--cluster ext--><rect fill="#FFFFFF" height="241" style="stroke: #424242; stroke-width: 1.0; stroke-dasharray: 7.0,7.0;" width="107" x="30" y="60"/><text fill="#424242" font-family="'Input Mono','Microsoft YaHei Mono',Menlo, Monaco, 'Courier New', monospace" font-size="16" font-weight="bold" lengthAdjust="spacingAndGlyphs" textLength="68" x="49.5" y="80.3359">继承关系</text><text fill="#424242" font-family="'Input Mono','Microsoft YaHei Mono',Menlo, Monaco, 'Courier New', monospace" font-size="12" lengthAdjust="spacingAndGlyphs" textLength="60" x="53.5" y="97.6035">[Container]</text><!--cluster ext1--><rect fill="#FFFFFF" height="241" style="stroke: #424242; stroke-width: 1.0; stroke-dasharray: 7.0,7.0;" width="133" x="177" y="60"/><text fill="#424242" font-family="'Input Mono','Microsoft YaHei Mono',Menlo, Monaco, 'Courier New', monospace" font-size="16" font-weight="bold" lengthAdjust="spacingAndGlyphs" textLength="68" x="209.5" y="80.3359">继承关系</text><text fill="#424242" font-family="'Input Mono','Microsoft YaHei Mono',Menlo, Monaco, 'Courier New', monospace" font-size="12" lengthAdjust="spacingAndGlyphs" textLength="60" x="213.5" y="97.6035">[Container]</text><!--class A--><rect fill="#E3F2FD" height="46" id="A" style="stroke: #64B5F6; stroke-width: 1.0;" width="39" x="73.5" y="125"/><ellipse cx="87.5" cy="140" fill="#ADD1B2" rx="10" ry="10" style="stroke: #64B5F6; stroke-width: 1.0;"/><path d="M87.2813,144.3906 Q85.75,144.3906 84.8438,143.2422 Q83.9375,142.0938 83.9375,139.625 Q83.9375,137.5469 84.8984,136.1797 Q85.8594,134.8125 87.5,134.8125 Q87.875,134.8125 88.2656,134.9219 Q88.6406,135.0938 88.9219,135.0938 Q89.1406,135.0938 89.2734,135.0391 Q89.4063,134.9844 89.5156,134.8125 L90.0156,137.2188 L89.6875,137.3281 Q89.3594,136.2969 88.8125,135.75 Q88.2656,135.2031 87.3906,135.2031 Q86.2344,135.2031 85.6094,136.4609 Q84.9844,137.7188 84.9844,139.5781 Q84.9844,141.5938 85.5313,142.7422 Q86.0781,143.8906 87.3281,143.8906 Q88.2031,143.8906 88.8359,143.2656 Q89.4688,142.6406 89.6875,141.5938 L90.0156,141.7656 Q89.7344,142.9063 89,143.6484 Q88.2656,144.3906 87.2813,144.3906 Z "/><text fill="#222222" font-family="'Input Mono','Microsoft YaHei Mono',Menlo, Monaco, 'Courier New', monospace" font-size="14" lengthAdjust="spacingAndGlyphs" textLength="9" x="100.5" y="146.4839">A</text><line style="stroke: #64B5F6; stroke-width: 1.0;" x1="74.5" x2="111.5" y1="155" y2="155"/><line style="stroke: #64B5F6; stroke-width: 1.0;" x1="74.5" x2="111.5" y1="163" y2="163"/><!--class B--><rect fill="#E3F2FD" height="46" id="B" style="stroke: #64B5F6; stroke-width: 1.0;" width="39" x="73.5" y="231"/><ellipse cx="87.5" cy="246" fill="#ADD1B2" rx="10" ry="10" style="stroke: #64B5F6; stroke-width: 1.0;"/><path d="M87.2813,250.3906 Q85.75,250.3906 84.8438,249.2422 Q83.9375,248.0938 83.9375,245.625 Q83.9375,243.5469 84.8984,242.1797 Q85.8594,240.8125 87.5,240.8125 Q87.875,240.8125 88.2656,240.9219 Q88.6406,241.0938 88.9219,241.0938 Q89.1406,241.0938 89.2734,241.0391 Q89.4063,240.9844 89.5156,240.8125 L90.0156,243.2188 L89.6875,243.3281 Q89.3594,242.2969 88.8125,241.75 Q88.2656,241.2031 87.3906,241.2031 Q86.2344,241.2031 85.6094,242.4609 Q84.9844,243.7188 84.9844,245.5781 Q84.9844,247.5938 85.5313,248.7422 Q86.0781,249.8906 87.3281,249.8906 Q88.2031,249.8906 88.8359,249.2656 Q89.4688,248.6406 89.6875,247.5938 L90.0156,247.7656 Q89.7344,248.9063 89,249.6484 Q88.2656,250.3906 87.2813,250.3906 Z "/><text fill="#222222" font-family="'Input Mono','Microsoft YaHei Mono',Menlo, Monaco, 'Courier New', monospace" font-size="14" lengthAdjust="spacingAndGlyphs" textLength="9" x="100.5" y="252.4839">B</text><line style="stroke: #64B5F6; stroke-width: 1.0;" x1="74.5" x2="111.5" y1="261" y2="261"/><line style="stroke: #64B5F6; stroke-width: 1.0;" x1="74.5" x2="111.5" y1="269" y2="269"/><!--class la--><rect fill="#E3F2FD" height="46" id="la" style="stroke: #64B5F6; stroke-width: 1.0;" width="65" x="220.5" y="125"/><ellipse cx="234.5" cy="140" fill="#ADD1B2" rx="10" ry="10" style="stroke: #64B5F6; stroke-width: 1.0;"/><path d="M234.2813,144.3906 Q232.75,144.3906 231.8438,143.2422 Q230.9375,142.0938 230.9375,139.625 Q230.9375,137.5469 231.8984,136.1797 Q232.8594,134.8125 234.5,134.8125 Q234.875,134.8125 235.2656,134.9219 Q235.6406,135.0938 235.9219,135.0938 Q236.1406,135.0938 236.2734,135.0391 Q236.4063,134.9844 236.5156,134.8125 L237.0156,137.2188 L236.6875,137.3281 Q236.3594,136.2969 235.8125,135.75 Q235.2656,135.2031 234.3906,135.2031 Q233.2344,135.2031 232.6094,136.4609 Q231.9844,137.7188 231.9844,139.5781 Q231.9844,141.5938 232.5313,142.7422 Q233.0781,143.8906 234.3281,143.8906 Q235.2031,143.8906 235.8359,143.2656 Q236.4688,142.6406 236.6875,141.5938 L237.0156,141.7656 Q236.7344,142.9063 236,143.6484 Q235.2656,144.3906 234.2813,144.3906 Z "/><text fill="#222222" font-family="'Input Mono','Microsoft YaHei Mono',Menlo, Monaco, 'Courier New', monospace" font-size="14" lengthAdjust="spacingAndGlyphs" textLength="22" x="247.5" y="146.4839">List</text><rect fill="#FFFFFF" height="21.1201" style="stroke: #589DF6; stroke-width: 1.0; stroke-dasharray: 2.0,2.0;" width="11" x="277.5" y="122"/><text fill="#589DF6" font-family="'Input Mono','Microsoft YaHei Mono',Menlo, Monaco, 'Courier New', monospace" font-size="14" lengthAdjust="spacingAndGlyphs" textLength="9" x="278.5" y="139.0439">A</text><line style="stroke: #64B5F6; stroke-width: 1.0;" x1="221.5" x2="284.5" y1="155" y2="155"/><line style="stroke: #64B5F6; stroke-width: 1.0;" x1="221.5" x2="284.5" y1="163" y2="163"/><!--class lb--><rect fill="#E3F2FD" height="46" id="lb" style="stroke: #64B5F6; stroke-width: 1.0;" width="65" x="220.5" y="231"/><ellipse cx="234.5" cy="246" fill="#ADD1B2" rx="10" ry="10" style="stroke: #64B5F6; stroke-width: 1.0;"/><path d="M234.2813,250.3906 Q232.75,250.3906 231.8438,249.2422 Q230.9375,248.0938 230.9375,245.625 Q230.9375,243.5469 231.8984,242.1797 Q232.8594,240.8125 234.5,240.8125 Q234.875,240.8125 235.2656,240.9219 Q235.6406,241.0938 235.9219,241.0938 Q236.1406,241.0938 236.2734,241.0391 Q236.4063,240.9844 236.5156,240.8125 L237.0156,243.2188 L236.6875,243.3281 Q236.3594,242.2969 235.8125,241.75 Q235.2656,241.2031 234.3906,241.2031 Q233.2344,241.2031 232.6094,242.4609 Q231.9844,243.7188 231.9844,245.5781 Q231.9844,247.5938 232.5313,248.7422 Q233.0781,249.8906 234.3281,249.8906 Q235.2031,249.8906 235.8359,249.2656 Q236.4688,248.6406 236.6875,247.5938 L237.0156,247.7656 Q236.7344,248.9063 236,249.6484 Q235.2656,250.3906 234.2813,250.3906 Z "/><text fill="#222222" font-family="'Input Mono','Microsoft YaHei Mono',Menlo, Monaco, 'Courier New', monospace" font-size="14" lengthAdjust="spacingAndGlyphs" textLength="22" x="247.5" y="252.4839">List</text><rect fill="#FFFFFF" height="21.1201" style="stroke: #589DF6; stroke-width: 1.0; stroke-dasharray: 2.0,2.0;" width="11" x="277.5" y="228"/><text fill="#589DF6" font-family="'Input Mono','Microsoft YaHei Mono',Menlo, Monaco, 'Courier New', monospace" font-size="14" lengthAdjust="spacingAndGlyphs" textLength="9" x="278.5" y="245.0439">B</text><line style="stroke: #64B5F6; stroke-width: 1.0;" x1="221.5" x2="284.5" y1="261" y2="261"/><line style="stroke: #64B5F6; stroke-width: 1.0;" x1="221.5" x2="284.5" y1="269" y2="269"/><!--reverse link A to B--><path d="M93,191.164 C93,204.812 93,219.399 93,230.9761 " fill="none" id="A&lt;-B" style="stroke: #616161; stroke-width: 1.0;"/><polygon fill="none" points="86.0001,191.056,93,171.056,100.0001,191.056,86.0001,191.056" style="stroke: #616161; stroke-width: 1.0;"/><!--reverse link la to lb--><path d="M253,191.164 C253,204.812 253,219.399 253,230.9761 " fill="none" id="la&lt;-lb" style="stroke: #616161; stroke-width: 1.0;"/><polygon fill="none" points="246,191.056,253,171.056,260,191.056,246,191.056" style="stroke: #616161; stroke-width: 1.0;"/><!--link ext to ext1--><path d="M42.1048,59.4036 C42.1705,59.1838 42.2368,58.9643 42.3037,58.745 C42.5712,57.8677 42.8483,56.9943 43.1352,56.1253 C44.283,52.6497 45.5885,49.2455 47.0704,45.95 C52.9978,32.7682 61.7461,21.3255 74.5,14 C106.923,-4.6225 133.8618,26.2345 153.022,63.0069 C162.6021,81.3931 170.2376,101.2581 175.6417,117.1565 C175.9795,118.1501 176.3085,119.1283 176.6288,120.0896 C176.7088,120.3299 176.7883,120.5692 176.8673,120.8074 " fill="none" id="ext-&gt;ext1" style="stroke: #616161; stroke-width: 1.0;"/><polygon fill="#616161" points="176.8673,120.8074,177.8323,111.0059,175.294,116.0614,170.2386,113.5232,176.8673,120.8074" style="stroke: #616161; stroke-width: 1.0;"/></g></svg>  
当s 是A的子类， 那么func()\:s 是func():A的子类。 也就是被参数化类型的泛化方向与参数类型的方向是一致的，所以称为协变。 
同协变定义，但是是反过来，即当S是A的子类时，func（S）是func（A）的父类。
## 1.9. 多维数组的定义
```scalar
val  myMatrix = Array.ofDim[Int](3,4)  //类型实际就是Array[Array[Int]]
val  myCube = Array.ofDim[String](3,2,4)  //类型实际是Array[Array[Array[Int]]]
```
可以使用多个圆括号来访问多维数组的元素，例如myMatrix(0)(1)返回第一行第二列的元素
## 1.10. 避免使用 Null
大多数语言都有一个特殊的关键字或者对象来表示一个对象引用的是"无"，在 Java，它是`null`。在 Java 里，`null` 是一个关键字，不是一个对象，所以对它调用任何方法都是非法的。但是这对语言设计者来说是一件令人疑惑的选择。为什么要在程序员希望返回一个对象的时候返回一个关键字呢？
## 1.11. Option类型
为了让所有东西都是对象的目标更加一致，也为了遵循函数式编程的习惯，Scala 鼓励你在变量和函数返回值可能不会引用任何值的时候使用 `Option` 类型。在没有值的时候，使用 `None`，这是 `Option` 的一个子类。如果有值可以引用，就使用 `Some` 来包含这个值。`Some` 也是 `Option` 的子类。 `None` 被声明为一个对象，而不是一个类，因为我们只需要它的一个实例。这样，它多少有点像 `null` 关键字，但它却是一个实实在在的，有方法的对象。`Option` 类型的值通常作为 Scala 集合类型（`List`, `Map` 等）操作的返回类型。
`Option` 有两个子类别，`Some` 和 `None`。当程序回传 `Some` 的时候，代表这个函式成功地给了你一个 `String`，而你可以透过 `get()` 函数拿到那个 `String`，如果程序返回的是 `None`，则代表没有字符串可以给你。Scala 程序使用 `Option` 非常频繁，在 Java 中使用 `null` 来表示空值，代码中很多地方都要添加 null 关键字检测，不然很容易出现 `NullPointException`。因此 Java 程序需要关心那些变量可能是 `null`,而这些变量出现 null 的可能性很低，但一但出现，很难查出为什么出现 `NullPointerException`。 Scala 的 `Option` 类型可以避免这种情况，因此 Scala 应用推荐使用 `Option` 类型来代表一些可选值。使用 `Option` 类型，读者一眼就可以看出这种类型的值可能为 `None`。实际上，多亏 Scala 的静态类型，你并不能错误地尝试在一个可能为 `null` 的值上调用方法。虽然在 Java 中这是个很容易犯的错误，它在 Scala 却通不过编译，这是因为 Java 中没有检查变量是否为 `null` 的编程作为变成 Scala 中的类型错误（不能将 `Option[String]` 当做 `String` 来使用）。所以，`Option` 的使用极强地鼓励了更加弹性的编程习惯。