---
layout: post
title:  "java关键字理解"
date:   2015-12-12 14:06:05
categories: Java基础
excerpt: java关键字分析。
---

* content
{:toc}

## 前言 
  闲来无事，突然想重新梳理一下整个java体系，索性从java的关键字开始入手整理，有不同见解的请及时指出，非常愿意与各位讨论。

## 1.访问控制
1)private 私有的
private关键字是访问控制修饰符，可以应用于类、方法或字段（在类中声明的变量）。只能在声明private（内部）类、方法或字段的类中引用这些类、方法或字段。在类的外部或者对于子类而言，它们是不可见的。所有类成员的默认访问范围都是package访问，也就是说，除非存在特定的访问控制修饰符，否则，可以从同一个包中的任何类访问类成员。
2)protected受保护的
protected关键字是可以应用于类、方法或字段（在类中声明的变量）的访问控制修饰符。可以在声明protected类、方法或字段的类、同一个包中的其他任何类以及任何子类（无论子类是在哪个包中声明的）中引用这些类、方法或字段。所有类成员的默认访问范围都是package访问，也就是说，除非存在特定的访问控制修饰符，否则，可以从同一个包中的任何类访问类成员。
3)public公共的
public关键字是可以应用于类、方法或字段（在类中声明的变量）的访问控制修饰符。可能只会在其他任何类或包中引用public类、方法或字段。所有类成员的默认访问范围都是package访问，也就是说，除非存在特定的访问控制修饰符，否则，可以从同一个包中的任何类访问类成员。

## 2.类、方法和变量修饰符
1)abstract声明抽象
abstract关键字可以修改类或方法。abstract类可以扩展（增加子类），但不能直接实例化。abstract方法不在声明它的类中实现，但必须在某个子类中重写。采用abstract方法的类本来就是抽象类，并且必须声明为abstract。
2)class类
class关键字用来声明新的Java类，该类是相关变量和/或方法的集合。类是面向对象的程序设计方法的基本构造单位。类通常代表某种实际实体，如几何形状或人。类是对象的模板。每个对象都是类的一个实例。要使用类，通常使用new操作符将类的对象实例化，然后调用类的方法来访问类的功能。
3)extends继承、扩展
extends关键字用在class或interface声明中，用于指示所声明的类或接口是其名称后跟有extends关键字的类或接口的子类。子类继承父类的所有public和protected变量和方法。子类可以重写父类的任何非final方法。一个类只能扩展一个其他类。
4)final最终、不可改变
final关键字可以应用于类，以指示不能扩展该类（不能有子类）。final关键字可以应用于方法，以指示在子类中不能重写此方法。一个类不能同时是abstract又是final。abstract意味着必须扩展类，final意味着不能扩展类。一个方法不能同时是abstract又是final。abstract意味着必须重写方法，final意味着不能重写方法。
5)implements实现
implements关键字在class声明中使用，以指示所声明的类提供了在implements关键字后面的名称所指定的接口中所声明的所有方法的实现。类必须提供在接口中所声明的所有方法的实现。一个类可以实现多个接口。
6)interface接口
interface关键字用来声明新的Java接口，接口是方法的集合。
接口是Java语言的一项强大功能。任何类都可声明它实现一个或多个接口，这意味着它实现了在这些接口中所定义的所有方法。
实现了接口的任何类都必须提供在该接口中的所有方法的实现。一个类可以实现多个接口。
7)native本地
native关键字可以应用于方法，以指示该方法是用Java以外的语言实现的。
8)new新,创建
new关键字用于创建类的新实例。
new关键字后面的参数必须是类名，并且类名的后面必须是一组构造方法参数（必须带括号）。
参数集合必须与类的构造方法的签名匹配。
=左侧的变量的类型必须与要实例化的类或接口具有赋值兼容关系。
9)static静态
static关键字可以应用于内部类（在另一个类中定义的类）、方法或字段（类的成员变量）。
通常，static关键字意味着应用它的实体在声明该实体的类的任何特定实例外部可用。
static（内部）类可以被其他类实例化和引用（即使它是顶级类）。在上面的示例中，另一个类中的代码可以实例化MyStaticClass类，方法是用包含它的类名来限定其名称，如MyClass.MyStaticClass。
static字段（类的成员变量）在类的所有实例中只存在一次。
可以从类的外部调用static方法，而不用首先实例化该类。这样的引用始终包括类名作为方法调用的限定符。
模式：publicfinal static <type> varName = <value>;通常用于声明可以在类的外部使用的类常量。在引用这样的类常量时需要用类名加以限定。在上面的示例中，另一个类可以用MyClass.MAX_OBJECTS形式来引用MAX_OBJECTS常量。
10)strictfp 严格,精准
strictfp的意思是FP-strict，也就是说精确浮点的意思。在Java虚拟机进行浮点运算时，如果没有指定strictfp关键字时，Java的编译器以及运行环境在对浮点运算的表达式是采取一种近似于我行我素的行为来完成这些操作，以致于得到的结果往往无法令人满意。而一旦使用了strictfp来声明一个类、接口或者方法时，那么所声明的范围内Java的编译器以及运行环境会完全依照浮点规范IEEE-754来执行。因此如果想让浮点运算更加精确，而且不会因为不同的硬件平台所执行的结果不一致的话，那就请用关键字strictfp。
可以将一个类、接口以及方法声明为strictfp，但是不允许对接口中的方法以及构造函数声明strictfp关键字
11)synchronized线程、同步
synchronized关键字可以应用于方法或语句块，并为一次只应由一个线程执行的关键代码段提供保护。
synchronized关键字可防止代码的关键代码段一次被多个线程执行。
如果应用于静态方法，那么，当该方法一次由一个线程执行时，整个类将被锁定。
如果应用于实例方法，那么，当该方法一次由一个线程访问时，该实例将被锁定。
如果应用于对象或数组，当关联的代码块一次由一个线程执行时，对象或数组将被锁定。
12)transient 短暂
transient关键字可以应用于类的成员变量，以便指出该成员变量不应在包含它的类实例已序列化时被序列化。
当一个对象被串行化的时候，transient型变量的值不包括在串行化的表示中，然而非transient型的变量是被包括进去的。
Java的serialization提供了一种持久化对象实例的机制。当持久化对象时，可能有一个特殊的对象数据成员，我们不想用serialization机制来保存它。为了在一个特定对象的一个域上关闭serialization，可以在这个域前加上关键字transient。
transient是Java语言的关键字，用来表示一个域不是该对象串行化的一部分。当一个对象被串行化的时候，transient型变量的值不包括在串行化的表示中，然而非transient型的变量是被包括进去的。
13)volatile 易失
volatile关键字用于表示可以被多个线程异步修改的成员变量。
注意：volatile关键字在许多Java虚拟机中都没有实现。volatile的目标用途是为了确保所有线程所看到的指定变量的值都是相同的。
Java语言中的volatile变量可以被看作是一种“程度较轻的synchronized”；与synchronized块相比，volatile变量所需的编码较少，并且运行时开销也较少，但是它所能实现的功能也仅是synchronized的一部分。

## 3.程序控制语句
1)break跳出，中断
break关键字用于提前退出for、while或do循环，或者在switch语句中用来结束case块。
break总是退出最深层的while、for、do或switch语句。
2)continue继续
continue关键字用来跳转到for、while或do循环的下一个迭代。
continue总是跳到最深层while、for或do语句的下一个迭代。
3)return返回
return关键字会导致方法返回到调用它的方法，从而传递与返回方法的返回类型匹配的值。
如果方法具有非void的返回类型，return语句必须具有相同或兼容类型的参数。
返回值两侧的括号是可选的。
4)do运行
do关键字用于指定一个在每次迭代结束时检查其条件的循环。
do循环体至少执行一次。
条件表达式后面必须有分号。
5)while循环
while关键字用于指定一个只要条件为真就会重复的循环。
6)if如果
if关键字指示有条件地执行代码块。条件的计算结果必须是布尔值。
if语句可以有可选的else子句，该子句包含条件为false时将执行的代码。
包含boolean操作数的表达式只能包含boolean操作数。
7)else否则
else关键字总是在if-else语句中与if关键字结合使用。else子句是可选的，如果if条件为false，则执行该子句。
8)for循环
for关键字用于指定一个在每次迭代结束前检查其条件的循环。
for语句的形式为for(initialize;condition; increment)
控件流进入for语句时，将执行一次initialize语句。
每次执行循环体之前将计算condition的结果。如果condition为true，则执行循环体。
每次执行循环体之后，在计算下一个迭代的condition之前，将执行increment语句。
9)instanceof实例
instanceof关键字用来确定对象所属的类。
10)switch 观察
switch语句用于基于某个表达式选择执行多个代码块中的某一个。
switch条件的计算结果必须等于byte、char、short或int。
case块没有隐式结束点。break语句通常在每个case块末尾使用，用于退出switch语句。
如果没有break语句，执行流将进入所有后面的case和/或default块。
11)case 返回观察里的结果
case用来标记switch语句中的每个分支。
case块没有隐式结束点。break语句通常在每个case块末尾使用，用于退出switch语句。
如果没有break语句，执行流将进入所有后面的case和/或default块。
12)default 默认
default关键字用来标记switch语句中的默认分支。
default块没有隐式结束点。break语句通常在每个case或default块的末尾使用，以便在完成块时退出switch语句。
如果没有default语句，其参数与任何case块都不匹配的switch语句将不执行任何操作。

## 4.错误处理
1)try捕获异常
try关键字用于包含可能引发异常的语句块。
每个try块都必须至少有一个catch或finally子句。
如果某个特定异常类未被任何catch子句处理，该异常将沿着调用栈递归地传播到下一个封闭try块。如果任何封闭try块都未捕获到异常，Java解释器将退出，并显示错误消息和堆栈跟踪信息。
2)catch处理异常
catch关键字用来在try-catch或try-catch-finally语句中定义异常处理块。
开始和结束标记{和}是catch子句语法的一部分，即使该子句只包含一个语句，也不能省略这两个标记。
每个try块都必须至少有一个catch或finally子句。
如果某个特定异常类未被任何catch子句处理，该异常将沿着调用栈递归地传播到下一个封闭try块。如果任何封闭try块都未捕获到异常，Java解释器将退出，并显示错误消息和堆栈跟踪信息。
3)throw抛出一个异常对象
throw关键字用于引发异常。
throw语句将java.lang.Throwable作为参数。Throwable在调用栈中向上传播，直到被适当的catch块捕获。
引发非RuntimeException异常的任何方法还必须在方法声明中使用throws修饰符来声明它引发的异常。
4)throws声明一个异常可能被抛出
throws关键字可以应用于方法，以便指出方法引发了特定类型的异常。
throws关键字将逗号分隔的java.lang.Throwables列表作为参数。
引发非RuntimeException异常的任何方法还必须在方法声明中使用throws修饰符来声明它引发的异常。
要在try-catch块中包含带throws子句的方法的调用，必须提供该方法的调用者。

## 5.包相关
1)import引入
import关键字使一个包中的一个或所有类在当前Java源文件中可见。可以不使用完全限定的类名来引用导入的类。
当多个包包含同名的类时，许多Java程序员只使用特定的import语句（没有“*”）来避免不确定性。
2)package包
package关键字指定在Java源文件中声明的类所驻留的Java包。
package语句（如果出现）必须是Java源文件中的第一个非注释性文本。
例:java.lang.Object。
如果Java源文件不包含package语句，在该文件中定义的类将位于“默认包”中。请注意，不能从非默认包中的类引用默认包中的类。

## 6.基本类型
1)boolean布尔型
boolean是Java原始类型。boolean变量的值可以是true或false。
boolean变量只能以true或false作为值。boolean不能与数字类型相互转换。
包含boolean操作数的表达式只能包含boolean操作数。
Boolean类是boolean原始类型的包装对象类。
2)byte字节型
byte是Java原始类型。byte可存储在[-128,127] 范围以内的整数值。
Byte类是byte原始类型的包装对象类。它定义代表此类型的值的范围的MIN_VALUE和MAX_VALUE常量。
Java中的所有整数值都是32位的int值，除非值后面有l或L（如235L），这表示该值应解释为long。
3)char字符型
char是Java原始类型。char变量可以存储一个Unicode字符。
可以使用下列char常量：\b- 空格,\f - 换页,\n - 换行,\r - 回车,\t - 水平制表符,\' - 单引号,\" - 双引号,\\ - 反斜杠,\xxx - 采用xxx编码的Latin-1字符。\x和\xx均为合法形式，但可能引起混淆。\uxxxx- 采用十六进制编码xxxx的Unicode字符。
Character类包含一些可用来处理char变量的static方法，这些方法包括isDigit()、isLetter()、isWhitespace()和toUpperCase()。
char值没有符号。
4)double双精度
double是Java原始类型。double变量可以存储双精度浮点值。
由于浮点数据类型是实际数值的近似值，因此，一般不要对浮点数值进行是否相等的比较。
Java浮点数值可代表无穷大和NaN（非数值）。Double包装对象类用来定义常量MIN_VALUE、MAX_VALUE、NEGATIVE_INFINITY、POSITIVE_INFINITY和NaN。
5)float浮点
float是Java原始类型。float变量可以存储单精度浮点值。
使用此关键字时应遵循下列规则：
Java中的浮点文字始终默认为双精度。要指定单精度文字值，应在数值后加上f或F，如0.01f。
由于浮点数据类型是实际数值的近似值，因此，一般不要对浮点数值进行是否相等的比较。
Java浮点数值可代表无穷大和NaN（非数值）。Float包装对象类用来定义常量MIN_VALUE、MAX_VALUE、NEGATIVE_INFINITY、POSITIVE_INFINITY和NaN。
6)int整型
int是Java原始类型。int变量可以存储32位的整数值。
Integer类是int原始类型的包装对象类。它定义代表此类型的值的范围的MIN_VALUE和MAX_VALUE常量。
Java中的所有整数值都是32位的int值，除非值后面有l或L（如235L），这表示该值应解释为long。
7)long长整型
long是Java原始类型。long变量可以存储64位的带符号整数。
Long类是long原始类型的包装对象类。它定义代表此类型的值的范围的MIN_VALUE和MAX_VALUE常量。
Java中的所有整数值都是32位的int值，除非值后面有l或L（如235L），这表示该值应解释为long。
8)short短整型
short是Java原始类型。short变量可以存储16位带符号的整数。
Short类是short原始类型的包装对象类。它定义代表此类型的值的范围的MIN_VALUE和MAX_VALUE常量。
Java中的所有整数值都是32位的int值，除非值后面有l或L（如235L），这表示该值应解释为long。
9)null空
null是Java的保留字，表示无值。
将null赋给非原始变量相当于释放该变量先前所引用的对象。
不能将null赋给原始类型（byte、short、int、long、char、float、double、boolean）变量。
10)true 真
true关键字表示boolean变量的两个合法值中的一个。
11)false 假
false关键字代表boolean变量的两个合法值之一。

## 7.变量引用
1)super父类,超类
super关键字用于引用使用该关键字的类的超类。
作为独立语句出现的super表示调用超类的构造方法。
super.<methodName>()表示调用超类的方法。只有在如下情况中才需要采用这种用法：要调用在该类中被重写的方法，以便指定应当调用在超类中的该方法。
2)this 本类
this关键字用于引用当前实例。
当引用可能不明确时，可以使用this关键字来引用当前的实例。
3)void无返回值
void关键字表示null类型。
void可以用作方法的返回类型，以指示该方法不返回值。

## 8.保留字
正确识别java语言的关键字（keyword）和保留字（reservedword）是十分重要的。Java的关键字对java的编译器有特殊的意义，他们用来表示一种数据类型，或者表示程序的结构等。保留字是为java预留的关键字，他们虽然现在没有作为关键字，但在以后的升级版本中有可能作为关键字。
识别java语言的关键字，不要和其他语言如c/c++的关键字混淆。
const和goto是java的保留字。所有的关键字都是小写
1)goto跳转
goto保留关键字，但无任何作用。结构化程序设计完全不需要goto语句即可完成各种流程，而goto语句的使用往往会使程序的可读性降低，所以Java不允许goto跳转。
2)const静态
const保留字，是一个类型修饰符，使用const声明的对象不能更新。与final某些类似。
3)native本地
　Java不是完美的，Java的不足除了体现在运行速度上要比传统的C++慢许多之外，Java无法直接访问到操作系统底层（如系统硬件等)，为此Java使用native方法来扩展Java程序的功能。
　　可以将native方法比作Java程序同Ｃ程序的接口，其实现步骤：
　　１、在Java中声明native()方法，然后编译；
　　２、用javah产生一个.h文件；
　　３、写一个.cpp文件实现native导出方法，其中需要包含第二步产生的.h文件（注意其中又包含了JDK带的jni.h文件）；
　　４、将第三步的.cpp文件编译成动态链接库文件；
　　５、在Java中用System.loadLibrary()方法加载第四步产生的动态链接库文件，这个native()方法就可以在Java中被访问了。
4)assert 断言

### 1.1. 语法表示
两种表达式，分别如下：
assertexpression1;
assertexpression1: expression2;

### 1.2. 语义含义
1. 在运行时，如果关闭了assertion功能，这些语句将不起任何作用。
2. 如果打开了assertion功能，那么expression1的值将被计算，如果它的值为false，该语句强抛出一个AssertionError对象。如果assertion语句包括expression2参数，程序将计算出expression2的结果，然后将这个结果作为AssertionError的构造函数的参数，来创建AssertionError对象，并抛出该对象；如果expression1值为true，expression2将不被计算。
3. 一种特殊情况是，如果在计算表达式时，表达式本身抛出Exception，那么assert将停止运行，而抛出这个Exception。
### 1.3. 运行
1. 这种程序必须在JDK1.4(或者更高版本)的JRE下运行。
2. 参数-esa和-dsa：
它们含义为开启(关闭)系统类的assertion功能。-esa和-dsa的全名为-enablesystemassertions和-disenablesystemassertions，全名和缩写名有同样的功能。
3. 参数-ea和-ea：
它们含义为开启(关闭)用户类的assertion功能。
-ea和-da的全名为-enableassertions和-disenableassertions，全名和缩写名有同样的功能。
下面表格表示了参数及其含义。
参数
例子
说明
-ea
java-ea
打开所有用户类的assertion
-da
java-da
关闭所有用户类的assertion
-ea:<classname>
java-ea:MyClass1
打开MyClass1的assertion
-da:<classname>
java-da: MyClass1
关闭MyClass1的assertion
-ea:<packagename>
java-ea:pkg1
打开pkg1包的assertion
-da:<packagename>
java-da:pkg1
关闭pkg1包的assertion
-ea:...
java-ea:...
打开缺省包(无名包)的assertion
-da:...
java-da:...
关闭缺省包(无名包)的assertion
-ea:<packagename>...
java-ea:pkg1...
打开pkg1包和其子包的assertion
-da:<packagename>...
java-da:pkg1...
关闭pkg1包和其子包的assertion
-esa
java-esa
打开系统类的assertion
-dsa
java-dsa
关闭系统类的assertion
综合使用
java-dsa:MyClass1:pkg1
关闭MyClass1和pkg1包的assertion
其中...代表，此包和其子包的含义。例如我们有两个包为pkg1和pkg1.subpkg。那么pkg1...就代表pkg1和pkg1.subpkg两个包。
### 1.4. Assert使用场合
#### 1.4.1. 适宜使用场合
1. 使用assert来检查private和protected函数中的参数预处理（preconditions）和后处理（postconditions）。
2. 使用assert来检查参数或者变量的特殊值，一旦参数或者变量为这些特殊值，则跟这些参数有变量有关的操作不应该出现。
eg.
Objectsource = event.getSoure();

Assert(source!= null);

…….

Inthc = source.hashCode();//一些使用source的代码

3. 使用assert来检查控制流（无效代码分支）。
eg.
If(source== yesBtn) {// do code
}
elseIf(source == noBtn) {// do code
}
elseIf(source == cancelBtn) {// do code
}
else{
assertfalse; "Invalid Source " +source.toString();
}
4. 使用assert来检查你的返回值（传递条件）。
Eg.
publicObject someMedth() {
Objectresult = null;
…… //dosome code that sets result
Asset(result!=null); // check post condition
returnresult;
}

#### 1.4.2. 不适宜使用场合
1. 不要在public函数中使用assert来验证参数。
这些函数应该抛出NullPointerException，IllegalArgumentException，或者其他相关的exception。
2. 不要使用assert来检查软件用户的错误。应该抛出IllegalArgumentException。
3. 不要使用assert来完成任何工作。如果你使用了assert来完成一些功能，那么assertions一旦被关闭了怎么办？
4. 不要无聊地将assert抛出的错误消息国际化。一旦assertions关闭，你不是白做工了？
trim()方法，对字符串
自己写手机号的正则表达式，首先第一位为“1”开头，第二位必须是“3、5、8”之间的任何一个，后面是9个数字，其正则表达式校验是“^1[358]\d{9}$”
