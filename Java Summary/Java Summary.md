[TOC]

# Java相关知识总结

## 第一章 Java基础

### 1.1 基本概念

#### 1.1.1 `main`方法

Java中`main`方法是Java应用程序的入口。它的定义通常如下：

```java
public static void main(String[] args) {

}
```

`main`方法必须是`static`的，返回类型为`void`，且被`public`修饰。

`main`方法也可以被`synchronized`和`final`修饰。

**`main`方法为什么必须是`public static void`的？**

- 声明为`public`的任何方法或变量都可以从该类的外部访问。 而JVM访问`main`方法显然不是在类的内部访问，因此`main`方法需要定义为`public`的方法。
- `static`关键字修饰的方法，可以不用通过创建一个类的实例，而是直接通过类名直接去访问。并且, 从内存角度来说`static`修饰的方法以及变量存储在虚拟机当中的方法区当中，而非堆内存当中。那么，对于虚拟机来说也是一样的道理。`main`方法定义为`static`的，则对于虚拟机来说，在程序启动之后并不需要创建一个实例，也就可以去调用这个`main`方法。
- `void`是`main`方法的返回值，理解返回值应该从方法调用者的角度去理解。Java的`main`方法的调用者是JVM，而JVM在启动Java程序时，只需要调用`main`方法，而不需要此方法返回任何的值，因此，`main`方法被设计为`void`的。

**同一个.java文件中是否允许定义多个`main`方法？**

是可以的，因为每个类都可以的定义`main`方法，但只有与文件名相同的用`public`修饰的类中的定义良好的`main`方法才会作为程序的入口。例如：

```java
// 文件名为Test.java
class T {
    public static void main(String[] args) {
        // ...
    }
}

public class Test {
    // 程序入口
    public static void main(String[] args) {
        // ...
    }
}
```

**如何在`main`方法执行前执行操作？**

这里可以使用静态代码块，由于其在类加载时就会被调用，因此可以在`main`方法前被执行。例如：

```java
public class Test {
    static {
        System.out.print("Hello ");
    }
    public static void main(String[] args) {
        System.out.print("world");
    }
}
// 输出结果为：Hello world
```

#### 1.1.2 Java程序初始化顺序

Java程序的初始化一般遵循三个原则（优先级依次递减）：

- 静态对象（变量）优先于非静态对象（变量）初始化。其中静态对象（变量）初始化一次，而非静态对象（变量）可能会初始化多次。
- 父类优先于子类进行初始化。
- 按照成员变量的定义顺序进行初始化。即使变量定义散布于方法定义之中，它们依然在任何方法（包括构造函数）被调用前先初始化。

初始化工作可以由许多不同的代码块来完成，总的顺序如下：

1. 父类静态变量
2. 父类静态代码块
3. 子类静态变量
4. 子类静态代码块
5. 父类非静态变量
6. 父类非静态代码块
7. 父类构造函数
8. 子类非静态变量
9. 子类非静态代码块
10. 子类构造函数

#### 1.1.3 Java作用域

在Java语言中，作用域是由“{}”来决定的，它决定了其定义的变量名的可见性和生命周期。

变量的类型主要分为三种：

- 成员变量：类的成员变量的作用范围与类的实例化对象的作用范围相同，当类被实例化时，成员变量就会在内存中分配空间并初始化，直到这个被实例化对象的生命周期结束时，成员变量的声明周期才结束。
- 静态变量：`static`修饰的成员变量被成为静态变量，静态变量不依赖于特定的实例，而是被所有实例共享，也就是说，只要一个类被加载，JVM就会给类的静态变量分配存储空间。因此可以通过`类名.变量名`的方式进行访问。
- 局部变量：局部变量的作用域与可见性与它所在的“{}”有关。

成员变量的四种作用域：

作用域与可见性 | 当前类 | 同一包 | 子类 | 其他包
----- | ----- | ----- | ----- | ----- 
`public` | √ | √ | √ | √  
`protected` | √ | √ | √ | ×
`default` | √ | √ | × | ×
`private` | √ | × | × | ×

#### 1.1.4 构造函数

构造函数具有以下特点：

- 构造函数与类名相同，且没有返回值。
- 一个类可以有多个构造函数。当没有显式指定构造函数时，编译器在编译时会自动生成一个无参构造函数，但该构造函数不会执行任何代码。如果显式指定了构造函数，则编译器不会在编译时生成默认的构造函数。
- 构造函数不能被继承，因此不能被覆盖，但是可以被重载。
- 子类可以通过`super`关键字来显示调用父类的构造函数，当父类没有提供无参构造函数时，子类的构造函数中必须显式地调用父类的构造函数。当有父类时，在实例化对象时会先执行父类的构造函数，然后执行子类的构造函数。
- 普通方法可以与构造函数同名。

#### 1.1.5 接口

接口是抽象方法定义的集合，是一种特殊的抽象类。接口中只包含方法的定义，没有方法的实现。自Java 8开始，接口中的方法可以有默认实现，并可以定义静态方法。接口中成员的作用域修饰符都是`public`，接口中的常量值默认均为`public static final`。有于一个类可以实现多个接口，因此通常可以采用实现多个接口的方式来间接达到多重继承的目的。

**为什么有些接口中没有声明任何方法？**

这些没有任何方法声明的接口被称为标识接口，标识接口对实现它的类没有任何语义上的要求，它仅仅起到一个标识的作用，用来表明实现它的类属于一个特定的类型。例如`Cloneable`和`Serializable`接口。在使用时可以使用`instanceof`来判断实例是否实现了一个给定的标识接口。

#### 1.1.6 `clone`方法

在实际编程中，经常会遇到从某个已有的对象A创建出另一个与A具有相同状态的对象B，并且对B的修改不会影响A的状态，例如Prototype模式中，就需要clone一个对象实例。这里可以使用`clone()`方法来满足这个需求。

Java中的所有类都默认继承自`Object`类，而`Object`类中提供了一个`clone()`方法。该方法会返回一个`Object`类对象的复制。这个复制函数返回的时一个新的对象而不是一个引用。

步骤如下：

1. 实现`Cloneable`接口，该接口为标记接口，无任何方法。
2. 在类中重写`Object`类中的`clone()`方法。
3. 在`clone()`方法中调用`super.clone()`。
4. 把浅复制的引用指向原型对象的新克隆体。

示例：

```java
class Obj implements Cloneable {

    private int i = 0;

    public int getI() {
        return i;
    }

    public void setI(int i) {
        this.i = i;
    }

    public void changeI() {
        this.i++;
    }

    @Override
    protected Object clone() {
        Object o = null;
        try {
            o = super.clone();
        } catch (CloneNotSupportedException e) {
            e.printStackTrace();
        }
        return o;
    }
}

public class TestClass {
    @Test
    public void testCase() {

        Obj a = new Obj();
        Obj b = (Obj) a.clone();
        b.changeI();
        Assert.assertEquals(1, b.getI()); // true
        Assert.assertEquals(0, a.getI()); // true
    }
}
```

**浅复制与深复制**

当类中只有一些基本的数据类型时，采用上述方法就可以完成复制，但是当类中包含一些对象时，就需要使用深复制。深复制的实现方法是在对对象调用`clone()`方法完成复制后，接着对对象中的非基本类型的属性也调用`clone()`方法来完成深复制。

```java
    @Override
    protected Object clone() {
        Object o = null;
        try {
            o = super.clone();
        } catch (CloneNotSupportedException e) {
            e.printStackTrace();
        }
        o.attr = this.getAttr().clone();
        // ...
        return o;
    }
```

**深复制和浅复制有什么区别？**

- 浅复制：被复制对象的所有变量都含有与原来对象相同的值，而所有其他对象的引用仍然指向原来的对象。浅复制仅仅复制所考虑的对象，而不复制它引用的对象。
- 深复制：被复制对象的所有变量都含有与原来对象相同的值，除去那些引用其他对象的变量。那些引用其他对象的变量将指向被复制的新对象，而不再是原有的那些被引用的对象。即深复制把复制的对象所引用的对象都复制了一遍。

### 1.2 面向对象

#### 1.2.1 面向对象的特征

面向对象的主要特征如下：

- 抽象：抽象指忽略一个主题中与当前目标无关的那些方面，以便更充分地注意与当前目标有关的方面。抽象并不打算了解全部问题，而只是选择其中的一部分。抽象包含过程抽象和数据抽象两个部分。
- 继承：继承是一种层次模型，它允许并鼓励类的重用。它提供了一种明确表述共性的方法。
- 封装：封装指将客观事物抽象成类，每个类对自身的数据和方法实行保护。
- 多态：多态允许不同类的对象对同一消息做出相应。多态包括参数化多态和包含多态。多态性语言具有灵活、抽象、行为共享、代码共享等优势，很好地解决了应用程序函数同名的问题。

#### 1.2.2 组合和继承的区别

组合和继承是面向对象中两种代码复用的方式。组合是指在新类中创建原有类的对象，重复利用已有类的功能。

组合和继承都可以实现代码的重用，一般情况下遵循以下两点原则：

- 除非两个类之间是“is-a”关系，否则不要轻易使用继承。
- 不要仅仅为了实现多态而使用继承。

在Java中，能使用组合就尽量不要使用继承。

#### 1.2.3 多态是如何实现的

多态是面向对象程序设计中代码重用的一个重要机制，它表示当同一个操作作用在不同对象时，会有不同的语义，从而产生不同的结果。在Java语言中多态主要有两种表现方式：

- 方法的重载（overload）：重载指同一个类中有多个同名方法，但这些方法有着不同的参数列表，因此在编译时就可以确定到底调用的哪个方法，它是一种编译时多态。重载可以被看作一个类中的方法多态性。
- 方法的覆盖（override）：子类可以覆盖父类的方法，因此同样的方法会在父类与子类中有着不同的表现形式。这种多态被称为运行时多态。

Java语言中，基类的引用变量不仅可以指向基类的实例对象，也可以指向其子类的实例对象。而程序调用的方法在运行期才动态绑定引用变量指向具体实例对象的方法，也就是内存中正在运行的对象的方法，而不是引用变量的类型中定义的方法。

运行时多态示例：

```java
class Base {

    public Base() {
        g();
    }

    public void f() {
        System.out.println("Base f()");
    }

    public void g() {
        System.out.println("Base g()");
    }

}

class Sub extends Base {

    public void f() {
        System.out.println("Sub f()");
    }

    public void g() {
        System.out.println("Sub g()");
    }
}

public class TestClass {

    @Test
    public void testCase() {
        Base b = new Sub(); // Sub g()
        b.f(); // Sub f()
        b.g(); // Sub g()
    }
}
```

需要注意的是只有方法有多态的概念，而成员变量没有多态的概念。

```java
class Base {
    public int i = 0;
}

class Sub extends Base {
    public int i = 1;
}

public class TestClass {

    @Test
    public void testCase() {
        Base b = new Sub();
        System.out.println(b.i); // 0
    }
}
```

成员变量的值取父类还是子类并不取决于创建对象的类型，而是取决于所定义变量的类型，这是在编译期间确定的。

**重载和覆盖的区别：**

使用重载时需要注意以下几点：

- 重载是通过不同的参数列表来区分的，例如不同的参数个数、不同的参数类型或不同的参数顺序。
- 不能通过方法的访问权限、返回值类型和抛出的异常类型来进行重载。
- 对于继承来说，如果基类方法的访问权限为`private`，那么就不能在派生类对其重载。如果派生类也定义了一个同名函数，这只是一个新的方法，不会达到重载的效果。

使用覆盖时需要注意以下几点：

- 派生类中的覆盖方法必须要和基类中被覆盖的方法有相同的函数名参数列表。
- 派生类中的覆盖方法的返回值必须和基类中被覆盖的方法的返回值相同。
- 派生类中的覆盖方法所抛出的异常必须和基类中被覆盖方法所抛出的异常一致。
- 基类中被覆盖的方法不能为`private`，否则子类只是定义了一个新方法，并没有发生覆盖。

二者的主要区别如下：

- 覆盖是子类和父类之间的关系，是垂直关系；重载是同一个类中方法之间的关系，是水平关系。
- 覆盖只能由一个方法或只能由一对方法产生关系；重载是多个方法之间的关系。
- 覆盖要求参数列表相同；重载要求参数列表不同。
- 覆盖关系中，调用方法体是根据对象的类型来决定的；而重载关系是根据调用时的实参表和形参表来选择方法的。

#### 1.2.4 抽象类与接口的异同

相同点：

- 都不能实例化。
- 接口的实现类或抽象类的子类都只有实现了接口或抽象类中的方法后才能被实例化。

不同点： 

- Java8之前，接口中只有方法定义，只有实现接口的类才能实现接口中定义的方法，而抽象类可以由定义与实现，即其方法可以在抽象类中被实现。Java8中接口也可以定义方法的默认实现了。
- 接口需要实现（implements），而抽象类只能被继承（extends）。由于Java只支持单继承，所以接口可以用于间接地实现多继承。
- 接口中定义的成员变量默认为`public static final`，并且必须赋初值。抽象类则可以自由地定义自己的成员变量。
- 接口被运用于实现比较常用的功能，便于日后维护或者添加删除方法；而抽象类更倾向于充当公共类的角色，不适用于日后重新对里面的代码进行修改。

总而言之，接口是一种特殊形式的抽象类，使用接口完全有可能实现与抽象类相同的操作，但一般而言，抽象类多用于在同类事物中有无法具体描述的方法的场景，所以当子类和父类之间存在有逻辑上的层次结构时推荐使用抽象类；而接口多用于不同类之间，定义不同类之间的通信规则，所以当希望支持差别较大的两个或者更多对象之间的特定交互行为时，应该使用接口。

#### 1.2.5 内部类

内部类主要有以下4种：

- 静态内部类（static inner class）
- 成员内部类（member inner class）
- 局部内部类（local inner class）
- 匿名内部类（anonymous inner class）

静态内部类指被声明为`static`的内部类，它可以不依赖于外部类实例而被实例化。静态内部类不能访问外部类的普通成员变量，只能访问外部类中的静态成员和静态方法。

成员内部类可以自由地引用外部类的属性和方法，无论这些属性和方法是静态的还是非静态的。但不能定义静态的属性和方法。它与一个实例绑定在一起，只有在外部类被实例化后，这个内部类才能被实例化。

局部内部类指在一个代码块中定义的类，它的作用范围为其所在的代码块。局部内部类与局部变量一样，不能被`public`、`protected`、`private`、`static`修饰。

匿名内部类是一种没有类名的内部类，不使用关键字`class`、`extends`、`implements`，没有构造函数，它必须继承其他类或者实现其他接口。因为匿名内部类为局部内部类，所以局部内部类的所有限制都对其生效。

#### 1.2.6 获取父类类名

Java语言提供了获取类名的方法`getClass.getName()`，我们可以使用这个方法来获取类名。例如：

```java
class Base {

}

class Sub extends Base {
    public void getClassName() {
        System.out.println(this.getClass().getName());
    }
}

public class TestClass {
    @Test
    public void testCase() {
        new Sub().getClassName();
    }
}

// pers.tavish.test.Sub
```

那么我们是否可以通过将`this`修改为`super`来获取其父类的类名呢？示例如下：

```java
class Base {

}

class Sub extends Base {
    public void getClassName() {
        System.out.println(super.getClass().getName());
    }
}

public class TestClass {
    @Test
    public void testCase() {
        new Sub().getClassName();
    }
}

// pers.tavish.test.Sub
```

因为所有的类均继承自`Object`类，而`getClass()`方法在`Object`类中是定义为`final native`的，所以子类不能覆盖该方法，因此无论是`this.getClass()`还是`super.getClass()`方法最终调用的都是`Object`类的`getClass()`方法。而`Object`类对该方法的解释为返回此对象的运行时类。由于这里运行时类为`Sub`类，所以返回结果为`Sub`的全限定类名。

这里我们要返回父类类名需要调用`getSuperclass()`，即`this.getClass().getSuperclass().getName()`。

### 1.3 关键字

#### 1.3.1 变量命名规则

在Java语言中，变量名、函数名、数组名称统称为标识符，Java语言规定标识符只能由字母、数字、下划线、和$符号组成，并且标识符的第一个字符不能是数字，且标识符不能包含空白字符。

#### 1.3.2 `break`、`continue`和`return`的区别

- `break`：用于直接强行跳出当前循环，不再执行剩余代码，程序控制在循环体后面的语句重新开始执行。当有多层嵌套且`break`存在于内存循环时，它将仅仅终止内层循环的执行，而不影响外层循环。
- `continue`：用于停止当此循环，回到循环起始处并进入下一次循环。
- `return`：用来表示从一个方法的返回。

#### 1.3.3 `final`、`finally`和`finalize`的区别

- `final`：用于声明属性、方法和类，分别表示属性不可变、方法不可覆盖和类不可被继承。注意属性不可变指的是引用的不可变性，即它只能指向初始时指向的那个对象，而不关心指向对象内容的变化。
- `finally`：作为异常处理的一部分，它只能用在`try/catch`语句中，并且附带一个语句块，表示这段语句最终一定会执行。
- `finalize`：`finalize()`是`Object`类的一个方法，在垃圾回收器执行时会调用被回收对象的`finalize()`方法，可以覆盖此方法来实现对其他资源的回收。注意，调用该方法不意味着垃圾回收器会立即进行垃圾回收动作。

#### 1.3.4 `static`的作用

`static`关键字的作用主要有两个：

1. 为某特定数据类型或对象分配单一的存储空间，而与创建对象的个数无关。
2. 实现某个方法或某个变量与类而不是对象关联在一起，也就是说，在不创建对象的情况下就可以通过类来直接调用方法或使用类的属性。

具体而言，`static`主要有以下几种使用情况：

- 静态成员变量
- 静态成员方法
- 静态代码块
- 静态内部类
- 静态导入

#### 1.3.5 `volatile`的作用

在Java程序中，有时为了提高程序的运行效率，编译器会自动对其进行优化，将经常访问的变量缓存起来，此时程序在读取这个变量时有可能会直接从缓存中读取而不是取内存中读取，这样可以提高程序的运行效率。但当遇到多线程时，变量的值可能因为别的线程而改变，而此时缓存中的值不会相应改变，从而造成应用程序读取到的值与实际值不一致的问题。

被类型修饰符`volatile`修饰的变量，系统每次用到它时都会直接从对应的内存中提取，而不会利用缓存，这就确保所有线程在任何时候看到变量的值都是相同的。

**需要注意的是该运算符并不能保证操作的原子性。**因此，一般情况下`volatile`并不能代替`sychronized`。

#### 1.3.6 `instanceof`的作用

该运算符的作用是判断一个引用类型的变量所指向的对象是否是一个类（父类、接口、抽象类）的实例。

示例如下：

```java
interface Interface {

}

class Base implements Interface {

}

class Sub extends Base {

}

public class TestClass {
    @Test
    public void testCase() {
        Sub sub = new Sub();
        String s = "123";
        int[] a = {1, 2, 3};
        System.out.println(sub instanceof Sub); // true
        System.out.println(sub instanceof Base); // true
        System.out.println(sub instanceof Interface); // true
        System.out.println(s instanceof String); // true
        System.out.println(a instanceof int[]); // true
    }
}
```

#### 1.3.7 `strictfp`的作用

`strictfp`即strict float point (精确浮点)。`strictfp`关键字可应用于类、接口或方法。使用`strictfp`关键字声明一个方法时，该方法中所有的`float`和`double`表达式都严格遵守FP-strict的限制,符合IEEE-754规范。当对一个类或接口使用`strictfp`关键字时，该类中的所有代码，包括嵌套类型中的初始设定值和代码，都将严格地进行计算。严格约束意味着所有表达式的结果都必须是IEEE 754 算法对操作数预期的结果，以单精度和双精度格式表示。如果我们想要浮点运算更加精确，而且不会因为不同的硬件平台所执行的结果不一致的话，可以用关键字`strictfp`。

### 1.4 基本数据类型与运算

#### 1.4.1 基本数据类型

- 字符型：`char`
- 布尔型：`boolean`
- 数值型：
    + 整形：`byte`、`short`、`int`、`long`
    + 浮点型：`float`、`double`

*`String`不是基本数据类型，是引用数据类型。*

**基本数据类型的范围**

数据类型 | 字节数 | 二进制位数 | 范围 | 默认值
----- | ----- | ----- | ----- | -----
`byte` | 1 | 8 | [-128, 127] | 0
`short` | 2 | 16 | [-32768, 32767] | 0
`int` | 4 | 32 | [-2147483648, 2147483647] | 0
`long` | 8 | 64 | [-9223372036854775808, 9223372036854775807] | 0L/0l
`float` | 4 | 32 | [1.4E-45, 3.4028235E38] | 0.0F/0.0f
`double` | 8 | 64 | [4.9E-324, 1.7976931348623157E308] | 0.0
`char` | 2 | 16 | [0, 65535] | \u0000
`boolean` | 1 | 8 | `true` or `false` | `false`

**赋值相关问题**

`byte a = 130;`，赋值错误，超出范围。
`long a = 9999999999;`，赋值错误，右边常量超过了`int`的范围
`float a = 3.4;`，赋值错误，右边常量默认为`double`类型
`byte a = 3; byte b = a + 4;`，变量`b`赋值错误，因为右边此时是`int`类型
`byte a = 3 + 4;`，该语句正确。
`int a = 100; char c = a;`，赋值错误，不兼容的类型，`int`转为`char`会损失精度。
`char c = 100;`，该语句正确。
`short s1 = 1; s1 = s1 + 1;`，赋值错误，不兼容的类型。
`short s2 = 1; s2 += 1;`，该语句正确

**类型转换问题**

Java中整数类型默认为`int`类型，而小数默认为`double`类型。
`char`可以当作特殊的整数类型使用，以字符形式表现的数字在转换为数字时可以减去字符`'0'`。
小数类型在转换为整数类型时需要强转，因为小数会被舍去，发生精度损失。
当将占位数少的类型赋值给占位数多的类型时，java自动使用隐式类型转换（如`int`型转为`long`型）。
当把在级别高的变量的值赋给级别低变量时，必须使用显式类型转换运算（如`double`型转为`float`型）。

**四则运算**

如果两个操作数中有一个是`double`类型，另一个就会转换为`double`类型。否则，如果有一个操作数是`float`类型，另一个就会转化为`float`。否则如果一个操作数是`long`类型，另一个就会转换为`long`。否则，两个操作数都将转为`int`类型。

#### 1.4.2 不可变类

不可变类是指当创建了这个类的实例后，就不允许修改它的值了。也就是说，一个对象一旦被拆功能键出来，在其整个声明周期中，它的成员变量就不能被修改了。它有点类似于常量，即只允许读而不允许修改。

在Java中，所有基本类型的包装类都是不可变类，例如`Integer`、`Float`等。此外`String`也是不可变类。

**如何创建一个不可变类？**

- 类中所有成员变量被`private`所修饰。
- 不提供修改成员变量的方法，例如setter方法，只提供构造函数。
- 确保类中所有方法不会被子类覆盖
- 如果一个类成员都不是不可变量，那么成员初始化或者使用getter方法获取该成员变量时，需要通过`clone()`来确保类的不可变性。

示例如下：

```java
class ImmutableClass {
    private Date date;

    public ImmutableClass(Date date) {
        this.date = (Date) date.clone();
    }

    public Date getDate() {
        return (Date) date.clone();
    }

    public final void printState() {
        System.out.println(date);
    }
}
```

#### 1.4.3 值传递和引用传递

Java提供了两种参数传递的方式：

- 值传递：在方法调用中，实参会把它的值传递给形参，形参只是用实参的值初始化一个临时的存储单元，因此形参与实参虽然有着相同的值，但是却有着不同的存储单元，因此对形参的改变不会影响实参的值。
- 引用传递：在方法调用中，传递的时对象，这时形参与实参所代表的对象为同一块存储单元，因此对形参的修改会影响实参的值。引用传递也可看作为值传递，只不过此时传递的是对象的地址值。

#### 1.4.4 `Math`类中的`round`、`ceil`和`floor`方法

- `round`方法：该方法表示四舍五入。其规则为当前数字增加0.5，然后向下取整。
- `ceil`方法：该方法表示向上取整。其规则为若当前数为正数，则小数“入”，若当前数为负数，则小数“舍”。
- `floor`方法：该方法表示向下取整。其规则为若当前数是正数，则小数“舍”，若当前数为负数，则小数“入”。

#### 1.4.5 中文字符串的长度和字节数

根据编码方式的不同，中文字符所占的字节数是不同的。

示例:

```java
public class TestClass {

    public void getLength(String s) {
        System.out.println("utf-8编码：" + "\"" + s + "\"的长度：" + s.length() + " 所占字节数：" + s.getBytes(Charset.forName("utf-8")).length);
        System.out.println("gbk编码：" + "\"" + s + "\"的长度：" + s.length() + " 所占字节数：" + s.getBytes(Charset.forName("gbk")).length);
    }

    @Test
    public void testCase() {
        String s1 = "测试abc";
        String s2 = "测试";
        String s3 = "abc";
        getLength(s1);
        getLength(s2);
        getLength(s3);
    }
}

/*
    utf-8编码："测试abc"的长度：5 所占字节数：9
    gbk编码："测试abc"的长度：5 所占字节数：7
    utf-8编码："测试"的长度：2 所占字节数：6
    gbk编码："测试"的长度：2 所占字节数：4
    utf-8编码："abc"的长度：3 所占字节数：3
    gbk编码："abc"的长度：3 所占字节数：3
*/
```

由上我们可以得出：

- utf-8编码下，一个中文占3个字节，一个英文一个字节。
- gbk编码下，一个中文占2个字节，一个英文一个字节。

并且我们可以看出，纯英文的字符串长度和字节数相同，所以我们可以依据此来判断一个字符串是否包含中文：

```java
public class TestClass {

    @Test
    public void testCase() {
        String s1 = "测试abc哈哈";
        String s2 = "测试";
        String s3 = "abc";

        judgeChineseCharacter(s1);
        judgeChineseCharacter(s2);
        judgeChineseCharacter(s3);
    }

    public void judgeChineseCharacter(String str) {
        if (str.length() == str.getBytes().length) {
            System.out.println("无汉字");
        } else {
            Matcher m = Pattern.compile("[\u4e00-\u9fa5]").matcher(str);
            while (m.find()) {
                System.out.print(m.group(0));
            }
            System.out.println();
        }
    }
}

/*
    测试哈哈
    测试
    无汉字
*/
```

### 1.5 字符串

#### 1.5.1 字符串创建与存储机制

字符串的声明与初始化主要由以下两种方式：

- 通过`new`，对于`String s1 = new String("abc")`与`String s2 = new String("abc")`语句，这里创建两个对象，分别为`s1`和`s2`，它们在内存中的地址是不同的。
- 直接使用字符串字面量，对于`String s1 = "abc"`与`String s2 = "abc"`语句，在JVM中存在一个字符串常量池，其中保存着很多字符串对象，并且可以被共享使用。这里`s1`和`s2`引用的是同一个常量池中的对象。由于`String`类实现了享元模式，所以当创建一个字符串常量时会首先在字符串常量池中查找是否已经有相同的字符串被定义，这里的判断依据为`equals`方法的返回值。若已经定义，则直接获取对其的引用，此时不再创建新的对象；若没有定义，则首先创建这个对象，然后将其加入到字符串常量池中，最后返回其引用。由于`String`是不可变类，一旦创建就不会被修改，所以`String`对象可以被共享而不会产生程序的混乱。

`new String("abc")`创建了几个对象？

可能是一个或两个。如果常量池中原来就存在“abc”，那么只需要在堆内存中创建一个对象；如果常量池中原来没有字符串“abc”，则会创建两个。

#### 1.5.2 `==`、`equals`和`hashCode`的区别

`==`运算符用来比较两个变量的值是否相等。也就是说该运算符用于比较变量对应的内存中所存储的数值是否相同，要比较两个基本类型的数据或两个引用类型的地址是否相同只能使用该运算符。

`equals()`是`Object`类提供的方法之一。由于任何一个Java类都继承自`Object`类，所以每一个对象都具有该方法。`Object`类中定义的`equals()`方法使用的是`==`运算符。所以在没有覆盖该方法的前提下，调用该方法进行比较和使用`==`进行比较是相同的，此时比较的都是地址。我们可以通过覆盖该方法来实现自定义的比较规则。

`hashCode()`方法也是从`Object`类中继承而来的，也可以用来鉴定两个对象是否相等。`Object`类中的`hashCode()`方法返回对象在内存中地址转换成的一个int值，所以如果没有重写该方法，那么任何不同对象的`hashCode()`方法都是不相等的。

`equals()`方法和`hashCode()`方法的区别：

通常来讲，`equals()`方法是给用户调用的，如果需要判断两个对象是否相等，那么可以重写该方法，然后在代码中调用。而对于`hashCode()`方法，用户一般不会去调用它，但在`HashMap`中，由于key是不可重复的，所以在判断key是否重复时就使用了`hashCode()`方法，同时也用到了`equals()`方法。这里的“不可重复”指的是`equals()`和`hashCode()`只要有一个不等就可以了。

一般在覆盖`equals()`方法时同时也要覆盖`hashCode()`方法，否则就会违反Object.hashCode的通用约定，从而导致该类无法与所有基于散列值的集合类结合在一起正常运行。

`hashCode()`方法的返回值和`equals()`方法的关系如下：

- 如果`x.equals(y)`返回`true`，那么调用这两个对象中任意一个对象的`hashCode()`方法都必须产生同样的整数结果。
- 如果`x.equals(y)`返回`false`，那么两个对象的`hashCode()`方法的返回值有可能相等，也有可能不相等。
- `hashCode()`的返回值不相等，那么`equals()`方法一定返回`false`
- `hashCode()`返回值相等，那么`equals()`方法有可能是返回`true`，也可能返回`false`。

### 1.6 异常处理

#### 1.6.1 `finally`块的执行

`finally`块的作用就是为了保证无论出现什么情况，该块的代码一定会被执行。由于`return`执行就意味着结束对当前函数的调用并跳出这个函数体，因此任何语句要执行都只能在`return`前执行（exit()除外），因此`finally`块里的代码也是在`return`前执行的。**此外，如果`try-finally`或者`catch-finally`块中也都有`return`，那么`finally`中的`return`会覆盖别处的`return`，最终返回给调用者的是`finally`中的返回值。**

示例：

```java
public class TestClass {

    public int testFinally1() {
        try {
            return 1;
        } catch (Exception e) {
            return 0;
        } finally {
            System.out.println("execute finally1");
        }
    }

    public int testFinally2() {
        try {
            return 1;
        } catch (Exception e) {
            return 0;
        } finally {
            System.out.println("execute finally2");
            return 2;
        }
    }

    @Test
    public void testCase() {
        System.out.println(testFinally1());
        System.out.println(testFinally2());
    }
}

/*
execute finally1
1
execute finally2
2
*/
```

此外，由于在一个方法内部定义的变量都存储在栈中，当这个函数结束后，其对应的栈就会被回收，此时在其方法体中定义的变量就不存在了，因此`return`在返回时不是直接返回变量的值，而是复制一份，然后返回。因此，对于基本类型的数据，在`finally`块中改变要返回的值对返回值没有任何影响（不在`finally`块中进行`return`），而对引用类型的数据会有影响。

```java
public class TestClass {

    public int testFinally1() {
        int result = 1;
        try {
            result = 2;
            return result;
        } catch (Exception e) {
            return 0;
        } finally {
            result = 3;
            System.out.println("execute finally1");
        }
    }

    public int testFinally2() {
        int result = 1;
        try {
            result = 2;
            return result;
        } catch (Exception e) {
            return 0;
        } finally {
            result = 3;
            System.out.println("execute finally2");
            return result;
        }
    }

    public StringBuilder testFinally3() {
        StringBuilder s = new StringBuilder("Hello");
        try {
            return s;
        } catch (Exception e) {
            return null;
        } finally {
            s.append(" world");
            System.out.println("execute finally3");
        }
    }

    @Test
    public void testCase() {
        System.out.println(testFinally1());
        System.out.println(testFinally2());
        System.out.println(testFinally3());
    }
}
/*
execute finally1
2
execute finally2
3
execute finally3
Hello world
*/
```

`finally`块是否一定会被执行？不一定，有两种情况`finally`代码块不会被执行。

情况一，在进入`try`块前就发生异常：

```java
public class TestClass {

    public void testFinally() {
        int result = 1 / 0;
        try {
            System.out.println("try block");
        } catch (Exception e) {
            System.out.println("catch block");
        } finally {
            System.out.println("finally block");
        }
    }

    @Test
    public void testCase() {
        testFinally();
    }
}
```

情况二：在`try`块中退出虚拟机：

```java
public class TestClass {

    public void testFinally() {
        try {
            System.out.println("try block");
            System.exit(0);
        } catch (Exception e) {
            System.out.println("catch block");
        } finally {
            System.out.println("finally block");
        }
    }

    @Test
    public void testCase() {
        testFinally();
    }
}
```

### 1.7 输入输出流

#### 1.7.1 Java IO流的实现机制

与Java流操作有关的类或接口：

![与IO操作有关的类或接口](images\与IO操作有关的类或接口.png)

Java中，输入和输出都被称为抽象的流，流可以被看做一组有序的字节集合，即数据在两设备之间的传输。流的本质是数据传输，根据处理数据类型的不同，流主要分为字节流和字符流两大类。

字节流以字节（8bit）为单位，而字符流以字符（16bit）为单位，根据码表映射字符，一次可以读取多个字节。字节流与字符流最主要的区别为：字节流在处理输入输出时不会用到缓存，而字节流用到了缓存。

IO流类图结构：

![IO流类图结构](images\IO流类图结构.png)

Java IO类在设计时采用了装饰者模式，使用这种设计模式的好处是可以在运行时动态地给对象添加一些额外的职责，与使用继承的设计方法相比，该方法具有更好的灵活性。

#### 1.7.2 文件和目录的管理

Java提供了`File`类对文件夹和文件进行管理，通过该类可以查看文件或目录的属性，并且可以实现文件或目录的创建。

例如，在控制台打印一个目录下的所有文件：

```java
public void printFiles(String path) {
    File file = new File(path);
    File[] files = file.listFiles();
    for (int i = 0; i < files.length; i++) {
        if(files[i].isDirectory()) {
            printFiles(files[i].getAbsolutePath());
        } else {
            System.out.println(files[i].getName());
        }
    }
}
```

#### 1.7.3 Java Socket

网络上的两个程序通过一个双向通信链接实现数据的交换，这个双向链路的一端称为一个Socket，也被称为套接字。

Java中Socket分为两种类型：

- 面向连接的Socket通信协议，TCP（Transmission Control Protocol），传输控制协议
- 面向无连接的Socket通信协议，UDP（User Datagram Protocol），用户数据报协议

Socket的声明周期可以分为3个阶段：打开Socket、使用Socket收发数据和关闭Socket

用Socket实现客户端和服务端的简单通信：

服务端：

```java
public class Server {
    public static void main(String[] args) {
        BufferedReader br = null;
        PrintWriter pw = null;
        try {
            ServerSocket server = new ServerSocket(29999);
            Socket socket = server.accept();
            br = new BufferedReader(new InputStreamReader(socket.getInputStream()));
            pw = new PrintWriter(socket.getOutputStream(), true);
            while (true) {
                String s = br.readLine();
                if (s == null || s.equals("OVER")) {
                    pw.println("OVER");
                    break;
                }
                System.out.println("From Client: " + s);
                pw.println("Connecting...");
            }
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            try {
                br.close();
                pw.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }
}
```

客户端：

```java
public class Client {
    public static void main(String[] args) {
        BufferedReader br = null;
        BufferedReader br2 = null;
        PrintWriter pw = null;
        try {
            Socket socket = new Socket("127.0.0.1", 29999);
            br = new BufferedReader(new InputStreamReader(System.in));
            br2 = new BufferedReader(new InputStreamReader(socket.getInputStream()));
            pw = new PrintWriter(socket.getOutputStream(),true);
            while (true) {
                String s = br.readLine();
                pw.println(s);
                if ((br2.readLine()).equals("OVER")) {
                    break;
                }
            }
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            try {
                br.close();
                pw.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }
}
```

#### 1.7.4 Java序列化

Java提供了两种对象持久化的方式：序列化和外部序列化。

**序列化**

在分布式环境下，当进行远程通信时，无论是何种类型的数据，都会以二进制序列的形式在网络上传送。序列化是一种将对象以一连串字节描述的过程，用于解决在对对象流进行读写操作时所引发的问题。序列化可以将对象的状态卸载流里进行网络传输，或者保存到文件、数据库等系统里，并在需要时把该流读取出来重新构造一个相同的对象。

所有要序列化的类都必须实现`Serializable`接口，该接口是一个标识接口，其中没有定义任何方法。接下来使用一个输出流来构造一个`ObjectOutputStream`对象，然后使用该对象的`writeObject()`方法就可以将对象写出，而要恢复时可以使用其对应的输入流。

序列化有如下两个特点：

- 如果一个类可以被序列化，那么它的子类也能够被序列化
- 由于`static`代表类的成员，`transient`代表对象的临时数据，因此被声明为这两种类型的数据成员不能够被序列化。

序列化示例：

```java
class People implements Serializable {
    private static final long serialVersionUID = 1271116621626210818L;
    private String name;
    private int age;

    public People(String name, int age) {
        this.name = name;
        this.age = age;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public int getAge() {
        return age;
    }

    public void setAge(int age) {
        this.age = age;
    }

    @Override
    public boolean equals(Object o) {
        if (this == o) {
            return true;
        }
        if (o == null || getClass() != o.getClass()) {
            return false;
        }
        People people = (People) o;
        return age == people.age &&
                Objects.equals(name, people.name);
    }

    @Override
    public int hashCode() {
        return Objects.hash(name, age);
    }
}

public class TestClass {

    @Test
    public void testCase() {
        People p1 = new People("Tavish", 25);
        // 写出
        ObjectOutputStream oos = null;
        try {
            oos = new ObjectOutputStream(new FileOutputStream("people.out"));
            oos.writeObject(p1);
        } catch (IOException e) {
            e.printStackTrace();
        }

        // 读取
        ObjectInputStream ois = null;
        try {
            ois = new ObjectInputStream(new FileInputStream("people.out"));
            People p2 = (People) ois.readObject();
            Assert.assertEquals(true, p1.equals(p2)); // true
        } catch (IOException | ClassNotFoundException e) {
            e.printStackTrace();
        }
    }
}
```

与序列化相对比的是反序列化，它将流转换为对象。在序列化与反序列化的过程中，`serialVersionUID`起着非常重要的作用，每个类都有一个特定的`serialVersionUID`，在反序列化的过程中，通过`serialVersionUID`来判定类的兼容性，如果反序列化时读入对象的`serialVersionUID`与目标类的`serialVersionUID`不同，那么在反序列化时就会抛出`InvalidClassException`异常。

自定义`serialVersionUID`有如下3个优点：

1. 提高程序的运行效率。如果没有显式声明`serialVersionUID`，那么在序列化时会通过计算得到一个`serialVersionUID`值。那么显式地声明也就省去了这一过程。
2. 提高程序在不同平台上的兼容性，由于各个平台的编译器在计算`serialVersionUID`时完全有可能采用不同的计算方法，这就会导致在一个平台上序列化的对象在另外一个平台上将无法实现反序列化操作。而显式地声明可以避免该问题的发生。
3. 增强程序各个版本的可兼容性。在默认情况下，每个类都有唯一的`serialVersionUID`，因此当后其对类进行修改时，类的`serialVersionUID`值将会发生变化，这将会导致类再修改前对象序列化文件在修改后无法完成反序列化操作。而显式地声明可以避免该问题的发生。

**外部序列化**

Java还提供了另外一种方式来实现对象序列化，即外部序列化，其接口定义如下：

```java
public interface Externalizable extends java.io.Serializable {

    void writeExternal(ObjectOutput out) throws IOException;

    void readExternal(ObjectInput in) throws IOException, ClassNotFoundException;
}
```

与序列化的区别在于外部序列化中的读写方法需要我们自己实现。因此与实现`Serializable`接口相比，实现`Externalizable`接口的编写难度更大，但是由于把控制权交给了开发人员，因此具有更高的灵活性。

### 1.8 容器

#### 1.8.1 Java Collection

Java Collection框架包含了大量集合接口以及这些接口的实现类和操作它们的算法。具体而言，主要提供了`List`、`Queue`、`Set`、`Stack`和`Map`。

Collection框架类图如下：

![Collection类图](images\Collection.png)

具体参照[Java Collection框架概述](https://blog.csdn.net/holmofy/article/details/71215548){:target="_blank"}

#### 1.8.2 `ArrayList`、`Vector`和`LinkedList`有什么区别

`ArrayList`和`Vector`都是基于`Object[]`实现的，它们会在内存中开辟一块连续的空间来存储，由于数据存储是连续的，因此它们支持用序号对元素进行访问。由于这个特性，所以二者查询元素的速度很快，而插入元素比较慢，由于此时可能会涉及到对数组中元素的移动。
二者均有一个默认的初始化容量大小，当里面存储的元素超过这个大小时就会动态地扩充。`Vector`默认扩充为原来的2倍，并提供了设置扩充大小的方法，而`ArrayList`则是1.5倍，并且没有提供设置扩充大小的方法。二者的最大区别在于`Vector`是线程安全的，由于其绝大多数方法都是直接或间接同步的，而`ArrayList`则不是。

`LinkedList`则采用双向链表实现，对数据的查找需要从头开始遍历，因此其对数据的查找速度较慢，但由于其插入元素时不需要对数据进行移动，因此插入效率较高。`LinkedList`是非线程安全的容器。

#### 1.8.3 `HashMap`、`Hashtable`、`TreeMap`和`WeakHashMap`有哪些区别

`HashMap`与`Hashtable`的主要区别如下：

1. `HashMap`是`Hashtable`的轻量级实现，主要区别在于`HashMap`允许空键值，而`Hashtable`不允许，且`HashMap`是非线程安全的，而`Hashtable`是线程安全的。
2. `HashMap`使用`Iterator`，而`Hashtable`使用`Enumeration`。
3. `HashMap`的底层数组默认大小为16，增长为2的指数，而`Hashtable`的底层数组默认大小为11，增长方式为old*2+1。
4. `Hashtable`直接使用key的hashCode，而`HashMap`则在取得hashCode后做了位运算，`h ^ (h >>> 16)`。

`TreeMap`实现了`SortMap`接口，能够把它保存的记录根据键进行排序，因此，取出时是排序后的键值对。如果想要按照输入的顺序进行输出，那么可以使用`LinkedHashMap`。`WeakHashMap`与`HashMap`类似，二者的不同之处在于`WeakHashMap`中的key采用的弱引用的方式，只要`WeakHashMap`中的key不再被外部引用，它就可以被垃圾回收器回收。`HashMap`中的key则是强引用方式，即使其中的key没有被外部引用，那么也只有这个key从`HashMap`中删除时才能被垃圾回收器回收。

如果使用自定义类型作为`HashMap`的键，那么需要重写`equals()`方法和`hashCode()`方法。

## 第二章 Java高级

### 2.1 JVM

#### 2.1.1 JVM内存模型

JVM内存布局规定了Java在运行过程中内存申请、分配、管理的策略，保证了JVM的高效稳定运行
不同的JVM对于内存的划分方式和管理机制存在着部分差异。

![JVM_1](images\JVM_1.png)

![JVM_2](images\JVM_2.png)

![JVM_3](images\JVM_3.png)

##### 2.1.1.1 Program Counter Register

**定义**

Register的命名源于CPU的寄存器，CPU只有把数据装载到寄存器才能够运行
寄存器存储指令相关的现场信息，由于CPU时间片轮限制，众多线程在并发执行过程中，任何一个确定的时刻，一个处理器或者多核处理器中的一个内核，只会执行某个线程中的一条指令。这样必然导致经常中断或恢复，如何保证分毫无差呢?
每个线程在创建后，都会产生自己的程序计数器和栈帧，程序计数器用来存放执行指令的偏移量和行号指示器等，线程执行或恢复都要依赖程序计数器。程序计数器在各个线程之间互不影响，此区域也不会发生内存溢出异常。

Program Counter Register（程序计数器），是一块较小的内存空间，可以看作是当前线程正在执行的字节码的行号指示器。根据当前线程执行方法的不同，其值也不同：

- Java方法：计数器此时记录的是当前线程正在执行的字节码指令的地址
- 本地方法：计数器此时的值位undefined

**作用**

程序计数器有两个作用：

- 字节码解释器通过改变计数器来依次读取指令，从而实现代码的流程控制，如：顺序执行、选择、循环及异常处理。
- 在多线程的情况下，程序计数器用于记录当前线程执行的位置。

**特点**

- 占用较小的内存空间。
- 线程私有，即每个线程都拥有一个独立的计数器。
- 是唯一一个不会出现OOM的内存区域。
- 生命周期随着线程的创建而创建，随着线程的结束而死亡。

##### 2.1.1.2 JVM Stack

**JVM Stack定义**

JVM Stack是描述Java方法执行的内存区域，它是线程私有的。

栈中的元素用于支持VM进行方法调用，每个方法从开始调用到执行完成的过程，就是栈帧从入栈到出栈的过程。在一个活动线程中，只有位于栈顶的帧才是有效的，被称为当前栈帧，正在执行的方法称为当前方法。其中栈帧是方法运行的基本结构。

在执行引擎运行时，所有指令都只能对当前栈帧进行操作。

![JVM_Stack](images\JVM_Stack.png)

虚拟机栈通过压/出栈的方式，对每个方法对应的活动栈帧进行运算处理，方法正常执行结束，肯定会跳转到另一个栈帧上。

栈帧包括局部变量表、操作栈、动态链接和方法返回地址：

**局部变量表：**

用于存放方法参数和局部变量。

代码在编译时就可以确定该栈帧需要多大的局部变量表，局部变量表的容量以Variable Slot为最小单位，每个变量槽都可以存储32位长度的内存空间。

在方法执行时，VM使用局部变量表完成参数值到参数变量列表的传递过程。如果执行的是实例方法，那么局部变量表中的0号索引的Slot默认是用于传递方法所属对象实例的引用。其余参数则按照参数表顺序排列，占用从1开始的局部变量Slot。基本类型数据以及引用和返回地址占用一个Slot，`long`和`double`类型需要两个。

**操作数栈**

JVM的执行引擎是基于栈的执行引擎，其中的栈就是指的操作栈。

操作栈的大小（深度）同样可以在编译期确定。

操作栈是一个初始状态位空的桶式结构栈。在方法的执行过程中，会有各种指令向栈中写入或提取信息。

**动态链接**

每个栈帧中都包含一个在常量池中对当前方法的引用，目的是为了支持方法调用过程的动态链接。

在类加载阶段中的解析阶段会将符号引用转为直接引用，这种转化称为静态解析。另外的一部分将在运行时转化位直接引用，这部分称为动态链接。

**方法返回地址**

方法执行时有两种退出情况：

- 正常退出，正常执行到任何方法的返回字节码指令。
- 异常退出。

JVM Stack也是线程私有的，每个线程都有各自的JVM Stack，且随着线程的创建而创建，随着线程的死亡而死亡。

JVM Stack可能会出现两种错误：

- `StackOverFlowError`：若JVM Stack的内存大小不允许动态扩展，那么当线程请求的栈深度大于虚拟机允许的最大深度时(但内存空间可能还有很多)，就抛出此异常。
- `OutOfMemoryError`：若JVM Stack的内存大小允许动态扩展，且当线程请求栈时内存用完了，无法再动态扩展了，此时抛出此异常。

##### 2.1.1.3 Native Method Stack

本地方法栈和JVM Stack实现的功能与抛出异常几乎相同，只不过JVM Stack是为虚拟机执行Java方法的服务，而本地方法区则为虚拟机使用到Native方法服务。

本地方法栈也是线程对象私有的。本地方法可以通过JNI（Java Native Interface）来访问虚拟机运行时的数据区，甚至可以调用寄存器，其具有和JVM相同的能力和权限。

如果有大量的本地方法存在，势必会削弱JVM对系统的控制力，且如果大量使用其他语言来实现JNI，就会一定程度上丧失跨平台特性。这里可以使用中间标准框架进行解耦，这样即使本地方法甭哭哦也不至于影响到JVM的稳定。但如果要求极高的执行效率、偏向底层的跨进程操作等，可以考虑设计使用JNI调用方式。

##### 2.1.1.4 Java Heap

Java Heap是OOM故障最主要的发源地，它存储着几乎所有的实例对象，堆由垃圾收集器自动回收，堆区由各子线程共享使用。通常情况下，它占用的空间是所有内存区域中最大的，但如果无节制地创建对象，也容易消耗完所有空间。

堆的内存空间可以固定大小也可以运行时动态调整。例如如下参数分别设置了最小堆容量和最大堆容量：`-Xms256M`，`-Xmx1024M`。

但是在通常情况下，服务器在运行过程中，堆空间不断地扩容与回缩，势必形成不必要的系统压力，所以在线上生产环境中，JVM的Xms和Xmx设置成一样大小，避免在GC后调整堆大小时带来的额外压力。

堆分为两个部分：新生代和老年代。对象产生之初在新生代，步入暮年时进入老年代，但是老年代也接纳在新生代无法容纳的超大对象。

其中新生代包括一个Eden区和2个Survivor区。

绝大部分对象在Eden区生成，当Eden区装填满的时候，会触发Young GC。垃圾回收的时候，在Eden区实现清除策略，没有被引用的对象则直接回收。依然存活的对象会被移送到Survivor区。Survivor区分为S0和S1两块内存空间，送到哪块空间呢?每次Young GC的时候，将存活的对象复制到未使用的那块空间，然后将当前正在使用的空间完全清除，交换两块空间的使用状态。如果YGC要移送的对象大于Survivor区容量上限，则直接移交给老年代。

这里每个对象都有一个计数器，每次YGC时会加1，`-XX:MaxTenuringThreshold`参数可以配置当计数器达到某个阈值时，对象从新生代晋升到老年代。

![Java_Heap](images\Java_Heap.png)

若Survivor区无法放下，或者超大对象的阈值超过上限，则尝试在老年代中进行分配。如果老年代也无法放下，则会触发Full Garbage Collection(Full GC)。如果依然无法放下，则抛OOM。

##### 2.1.1.5 方法区

Java虚拟机规范中定义方法区是堆的一个逻辑部分，但是别名为Non-Heap，以与Java堆区分。方法区中存放已经被虚拟机加载的类信息、常量、静态变量、即时编译器编译后的代码等数据。

方法区是线程共享的，整个虚拟机中只有一个方法区。且和堆一样，允许固定大小、也允许可扩展大小，而且还允许不实现垃圾回收。

运行时常量池（Runtime Constant Pool）是方法区的一部分（JDK8）。方法区中存储的常量被存储在运行时常量池中。

##### 2.1.1.6 Metaspace

JDK8，Metaspace（元空间）的前身Perm区被淘汰。

在JDK8中，Perm区存放的所有内容中：

- 字符串常量移至堆内存
- 其他内容例如类元信息、字段、静态属性、方法、常量等移动至元空间。

#### 2.1.2 JVM类加载机制

JVM类加载机制主要分为五个部分：加载、校验、准备、解析、初始化。

![类的生命周期](images\类的生命周期.png)

##### 2.1.2.1 加载

加载指的是将类的class文件读入到内存，并为之创建一个`java.lang.Class`对象，也就是说，当程序中使用任何类时，系统都会为之建立一个`java.lang.Class`对象。

类的加载由类加载器完成，类加载器通常由JVM提供，这些类加载器也是前面所有程序运行的基础，JVM提供的这些类加载器通常被称为系统类加载器。除此之外，开发者可以通过继承ClassLoader基类来创建自己的类加载器。

通过使用不同的类加载器，可以从不同来源加载类的二进制数据，通常有如下几种来源。

- 从本地文件系统加载class文件。
- 从JAR包加载class文件。
- 通过网络加载class文件。
- 把一个Java源文件动态编译，并执行加载。
    类加载器通常无须等到“首次使用”该类时才加载该类，Java虚拟机规范允许系统预先加载某些类。

##### 2.1.2.2 链接

当类被加载之后，系统会为之生成一个对应的`Class`对象，接着会进入链接阶段。该阶段负责将类的二进制数据合并到JRE中。链接阶段又可分为如下3个阶段：

**1.验证**

验证阶段用于检测被加载的类是否有正确的内部结构，并和其他类协调一致。

验证主要包含4个步骤：

- 文件格式验证：主要验证字节流是否符合`Class`文件格式规范。并且能被当前的虚拟机加载处理。
- 元数据验证对字节码描述的信息进行语义分析，分析是否符合Java的语言规范。
- 字节码验证分析数据流和控制，确定语义是合法的、符合逻辑的。保证类方法在运行时不会有危害出现。
- 符号引用验证：确定访问类型等涉及到引用的情况，主要是保证引用一定会被访问，不会出现类无法访问的问题。

**2.准备**

类准备阶段负责为类的静态变量分配内存，并设置默认初始值。

**3.解析**

将类的二进制数据中的符号引用替换成直接引用。

##### 2.1.2.3 初始化

初始化是为类的静态变量赋予正确的初始值。这个正确的初始值会覆盖准备阶段的默认初始值。

在Java中，对于初始化阶段，有且只有以下5种情况才会要求对类立刻进行初始化：

1. 使用`new`关键字实例化对象、访问或者设置一个类的静态字段、调用类方法。
2. 初始化一个类时如果其父类没有被初始化过，那么要先对父类进行初始化。
3. 使用反射加载类时，如果该类没有被初始化，则要先初始化。
4. 虚拟机启动时，用户会先初始化要执行的主类。
5. JDK7后，如果`java.lang.invoke.MethodHandle`的实例最后对应的解析结果是REF_getStatic、REF_putStatic、REF_invokeStatic方法句柄，并且这个方法所在类没有初始化，则先初始化。

##### 2.1.2.4 类加载器

把类加载阶段的“通过一个类的全限定名来获取描述此类的二进制字节流”这个动作交给虚拟机之外的类加载器来完成。这样的好处在于，我们可以自行实现类加载器来加载其他格式的类，只要是二进制字节流就行，这就大大增强了加载器灵活性。

系统自带的类加载器有三个：

1. 启动类加载器，`BootstrapClassLoader`：它用来加载Java 的核心类，是用C++来实现的，并不继承自`java.lang.ClassLoader`。开发者无法直接获取到启动类加载器的引用，所以不允许直接通过引用进行操作。
2. 扩展类加载器，`ExtClassLoader`：它负责加载JRE的扩展目录，lib/ext或者由`java.ext.dirs`系统属性指定的目录中的JAR包的类。由Java语言实现，父类加载器为`BootstrapClassLoader`。
3. 应用程序类加载器，`AppClassLoader`：它负责在JVM启动时加载来自Java命令的-classpath选项、`java.class.path`系统属性，或者CLASSPATH换将变量所指定的JAR包和类路径。程序可以通过`ClassLoader`的静态方法`getSystemClassLoader()`来获取 应用程序类加载器。如果没有特别指定，则用户自定义的类加载器都以此类加载器作为父加载器。由Java语言实现，父类加载器为`ExtClassLoader`。

示例如下：

```java
public class TestClass {

    @Test
    public void testCase() {
        ClassLoader appClassLoader = TestClass.class.getClassLoader();
        ClassLoader extClassLoader = appClassLoader.getParent();
        ClassLoader bootstrapClassLoader = extClassLoader.getParent();
        System.out.println(appClassLoader);
        System.out.println(extClassLoader);
        System.out.println(bootstrapClassLoader);
    }
}
/*
sun.misc.Launcher$AppClassLoader@18b4aac2
sun.misc.Launcher$ExtClassLoader@452b3a41
null
*/
```

![类加载器的层级关系](images\类加载器的层级关系.png)

JVM的类加载机制主要有如下3种：

1. 全盘负责：所谓全盘负责，就是当一个类加载器负责加载某个Class时，该Class所依赖和引用其他Class也将由该类加载器负责载入，除非显示使用另外一个类加载器来载入。
2. 双亲委派：所谓的双亲委派，则是先让父类加载器试图加载该Class，只有在父类加载器无法加载该类时才尝试从自己的类路径中加载该类。通俗的讲，就是某个特定的类加载器在接到加载类的请求时，首先将加载任务委托给父加载器，依次递归，如果父加载器可以完成类加载任务，就成功返回；只有父加载器无法完成此加载任务时，才自己去加载。
3. 缓存机制。缓存机制将会保证所有加载过的Class都会被缓存，当程序中需要使用某个Class时，类加载器先从缓存区中搜寻该Class，只有当缓存区中不存在该Class对象时，系统才会读取该类对应的二进制数据，并将其转换成Class对象，存入缓冲区中。这就是为很么修改了Class后，必须重新启动JVM，程序所做的修改才会生效的原因。

具体而言，类加载器加载一个类大致要经过如下步骤：

1. 检测此Class是否载入过，即在缓冲区中是否有此Class，如果有直接进入第8步，否则进入第2步。
2. 如果没有父类加载器，则要么Parent是根类加载器，要么本身就是根类加载器，则跳到第4步，如果父类加载器存在，则进入第3步。
3. 请求使用父类加载器去载入目标类，如果载入成功则跳至第8步，否则接着执行第5步。
4. 请求使用根类加载器去载入目标类，如果载入成功则跳至第8步，否则跳至第7步。
5. 当前类加载器尝试寻找Class文件，如果找到则执行第6步，如果找不到则执行第7步。
6. 从文件中载入Class，成功后跳至第8步。
7. 抛出`ClassNotFountException`异常。
8. 返回对应的`java.lang.Class`对象。

#### 2.1.3 JVM垃圾回收

为了减轻开发人员的工作，同时增加系统的安全性与稳定性，Java提供了垃圾回收器来自动检测对象的作用域，可自动地把不再被使用的存储空间释放。

具体而言，垃圾回收器主要完成3项工作：

- 分配内存
- 确保被引用对象的内存不被错误地回收
- 回收不再被引用的对象的内存空间

垃圾回收主要发生在堆（Java Heap）和元空间（Metaspace）上。

##### 2.1.3.1 垃圾对象判定标准

垃圾对象的判别标准主要有两种：

- 引用计数法
- 根搜索法

**引用计数法**

引用计数法作为一种简单的方法，其主要原理如下：在堆的每个对象都有一个引用计数器，当对象被引用时，计数器加1；当引用被置空或离开作用域时，引用计数减1。由于这种方法无法解决相互引用的问题，因此JVM没有采用这个算法。

**根搜索法**

对于一个对象而言，如果没有任何变量去引用它，那么该对象将不可能被程序访问，因此可以认为它是垃圾信息，可以被回收。

对于垃圾回收器而言，它使用有向图来记录和管理堆内存中的所有对象，通过这个有向图就可以识别哪些对象是“可达的”，哪些对象是“不可达”的，所有“不可达”的对象都是可以被回收的。示例如下：

```java
public class TestClass {
    @Test
    public void testCase() {
        Integer i1 = new Integer(1);
        Integer i2 = new Integer(2);
        i2 = i1;
        // ...
    }
}
```

当上述代码执行到`i2 = i1;`后，由于此时引用`i2`指向的是对象`new Integer(1)`的地址，那么对象`new Integer(2)`就成为不可达的对象了。所以此时垃圾回收器就会认为这块内存已经不会再被使用了，就会在适当的时候对内存这块内存进行回收。

具体而言，垃圾回收器通过一系列称为“GC Roots”的对象作为起始点，从这些节点开始向下搜索，搜索所走过的路径称为引用链，当一个对象到GC Roots没有任何引用链相连时，则证明此对象是不可用的。

可以作为GC Roots的对象包括下面几种：

- 虚拟机栈（JVM Stack）中局部变量表中引用的对象。
- 方法区中类静态属性引用的对象。
- 方法区中常量引用的对象。
- 本地方法栈中JNI引用的对象。

**四种引用**

- 强引用：形如`Object obj = new Object()`这类的为强引用。
- 软引用：一些有用但并非必须，用软引用关联的对象，当系统将要发生OOM前，这些对象就会被回收。
- 弱引用：一些有用（程度比软引用更低）但是并非必需，用弱引用关联的对象，只能生存到下一次垃圾回收之前，GC发生时，不管内存够不够，都会被回收。
- 虚引用：任何时候可能被GC回收，就像没有引用一样。

##### 2.1.3.2 垃圾回收算法

**标记清除算法**

JVM会扫描所有的对象实例，通过根搜索算法，将活跃对象进行标记。之后JVM再一次扫描所有对象，将未标记的对象进行清除，只有清除动作，不作任何的处理，这样导致的结果会存在很多的内存碎片。

![标记清除算法](images\标记清除算法.png)

**复制算法**

将堆分成两个大小相同的区域，在任何时刻，只有其中的一个区域被使用，直到这个区域被消耗完为止，此时垃圾回收器会中断程序的执行，通过遍历的方式把所有活动的对象复制到另一个区域，在复制的过程中它们是紧挨着分布的，从而消除内存碎片。当复制过程结束后，垃圾对象被回收，程序继续运行，直到当前使用的区域再次使用完毕。

![复制算法](images\复制算法.png)

**标记整理算法**

标记整理实际上是在标记清除算法上的优化，执行完标记清除全过程之后，再一次对内存进行整理，将所有存活对象统一向一端移动，这样解决了内存碎片问题。

![标记整理算法](images\标记整理算法.png)

**分代回收算法**

目前JVM常用回收算法就是分代回收，年轻代以复制算法为主，老年代以标记整理算法为主。原因是年轻代对象比较多，每次垃圾回收都有很多的垃圾对象回收，而且要尽可能快的减少生命周期短的对象，存活的对象较少，这时候复制算法比较适合，只要将有标记的对象复制到另一个内存区域，其余全部清除，并且复制的数量较少，效率较高；而老年代是年轻代筛选出来的对象，被标记比较高，需要删除的对象比较少，显然采用标记整理效率较高。

![分代回收算法](images\分代回收算法.png)

##### 2.1.3.3 内存泄漏问题

内存泄漏是指一个不再被程序使用的对象或变量还在内存中占有存储空间。Java语言引入了垃圾回收机制，由垃圾回收器负责回收不再使用的对象，但即使这样，是否可以确保不会发生内存泄漏呢？

内存泄漏主要有两种情况：

- 在堆中申请的空间没有被释放。
- 对象已不再被使用，但还仍然在内存中保留着。

垃圾回收机制的引入可以有效地解决第一种情况，但对于第二种情况，垃圾回收机制则无法保证不再使用的对象会被释放。因此Java中的内存泄漏主要指的是第二种情况。

例如：

```java
List<Object> list = new ArrayList<>();
for (int i = 0; i < 10; i++) {
    list.add(new Object());
}
```

Java中的内存泄漏范围要比C++小，因为Java保证任何对象都是可达的，所有不可达的对象都由GC进行

![内存泄漏](images\内存泄漏.png)

当循环结束后，由于`list`在使用添加到其中的对象，所以垃圾回收器无法将他们回收，只有当这些对象从`list`中删除才能释放内存空间。

Java中容易引起内存泄漏的原因很多，主要有如下几个方面：

1. 静态集合类，例如`HashMap`和`List`。当这类容器被修饰为静态时，它们的生命周期和应用程序一致，他们所引用的所有的对象也就不能被释放，从而造成内存泄漏。
2. 各种连接，如数据库连接、网络连接和IO连接等。在对数据库进行操作的过程中，首先需要建立与数据库的连接，当不再使用时，需要调用`close()`方法来释放与数据库的连接。只有连接被关闭后，垃圾回收器才会回收对应的对象。否则，如果在访问数据库的过程中，对`Connection`、`Statement`或`ResultSet`不显式地关闭，将会造成大量的对象无法被回收，从而引起内存泄漏。
3. 监听器，通常一个应用会用到多个监听器，但在释放对象时如果没有相应地删除监听器，那么也会造成内存泄漏。
4. 变量不合理的作用域。一般而言，如果一个变量定义的作用范围大于其使用范围，就很有可能造成内存泄漏；另一方面如果没有及时地把对象设置为`null`，很有可能会导致内存泄漏。
5. 单例模式可能会造成内存泄漏。当`Singleton`持有一个外部引用时，这个外部引用的对象就不能被回收了。因为单例对象以静态变量方式存储，因此在JVM整个生命周期中都存在。
示例：
```java
class BigClass {
    // ...
}

class Singleton {
    private BigClass bc;
    private static Singleton instance;
    private Singleton(BigClass bc) {
        this.bc = bc;
    }
    public static Singleton getInstance(BigClass bc) {
        if (instance == null) {
            instance = new Singleton(bc);
        }
        return instance;
    }
}
```
上述实现的单例模式中，返回的`instace`对象持有一个`BigClass`的引用，这将导致其不能被回收。

### 2.2 反射机制

反射机制是Java语言中一个非常重要的特性，它允许程序在运行时进行自我检查，同时也允许对其内部的成员进行操作。由于反射机制能够实现在运行时对类进行装载，因此能够增加程序的灵活性，但是不恰当地使用反射机制，也会严重影响系统的性能。

与反射相关的类如下：

- `Class`类：代表类的实体，在运行的应用程序中标识类和接口
- `Field`类：代表类的成员变量
- `Method`类：代表类的方法
- `Constructor`类：代表类的构造方法

具体而言，反射机制提供的功能主要有：

- 得到一个对象所属的类。
- 获取一个类的所有成员变量和方法。
- 在运行时创建对象。
- 在运行时调用对象的方法。

#### 2.2.1 `Class`类

在Java中，类也是一个对象，是`java.lang.Class`类的实例对象。

在反射机制中，`Class`类是一个非常重要的类，由于其构造器为`private`的，所以我们不能通过`new`的方式获取一个`Class`对象。

我们可以通过三种途径来获得：

- `Class.forName();`
- `ClassName.Class;`
- `Instance.getClass();`

得到`Class`对象后，我们就可以获取该类的相关信息，例如：

```java
public void getClassInfo(Object o) {
    Class clazz = o.getClass();
    System.out.println("类的全限定类名：" + clazz.getName());
    System.out.println("类的名字：" + clazz.getSimpleName());
    System.out.println("===================================");

    // 获取该类声明的方法，包括public、protected、default和private修饰的方法
    Method[] thisMethods = clazz.getDeclaredMethods();

    // 遍历方法
    for (Method method : thisMethods) {

        System.out.println("方法名称：" + method.getName());

        // 获取参数列表
        int i = 1;
        for (Parameter parameter : method.getParameters()) {
            System.out.println("参数" + i++ + "类型：" + parameter.getType().getSimpleName());
        }

        System.out.println("返回类型：" + method.getReturnType().getName());
        System.out.println("===================================");
    }

    // 获取该类声明的成员变量，包括public、protected、default和private修饰的成员变量
    Field[] fields = clazz.getDeclaredFields();
    for (Field field : fields) {
        System.out.println("成员变量类型：" + field.getType());
        System.out.println("成员变量名称：" + field.getName());
    }
}
```

反射机制非常重要的一个作用就是可以在运行时动态地创建类的对象，示例如下：

```java
class Base {
    public String method() {
        return "Base";
    }
}

class Sub extends Base {
    @Override
    public String method() {
        return "Sub";
    }
}

public class TestClass {
    @Test
    public void testCase() {
        try {
            Class clazz = Class.forName("pers.tavish.test.Sub");
            // 调用无参构造器
            Base b = (Base) clazz.newInstance();
            Assert.assertEquals("Sub", b.method()); // true
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```

由此Java共有4种方式可以创建一个对象：

- 通过`new`实例化一个对象。
- 通过反射机制创建对象。
- 通过`clone()`方法创建一个对象。
- 通过反序列化的方式创建对象。

#### 2.2.2 `Method`类

`Method`类用于对方法进行反射操作，我们使用方法名和方法的参数列表来确定一个方法。

示例：

```java
class MethodDemo {
    public static void staticMethod(String s) {
        System.out.println("Static " + s);
    }

    public void method1(String s) {
        System.out.println("Non-static " + s);
    }

    public void method2(int i, int j) {
        System.out.println(i + "" + j);
    }
}

public class TestClass {

    @Test
    public void testCase() {

        MethodDemo md = new MethodDemo();

        Class clazz = md.getClass();
        try {

            // 根据方法名和参数列表来获取方法
            Method staticMethod = clazz.getDeclaredMethod("staticMethod", new Class[]{String.class});
            Method method1 = clazz.getDeclaredMethod("method1", new Class[]{String.class});
            Method method2 = clazz.getDeclaredMethod("method2", new Class[]{int.class, int.class});

            // 对方法进行调用
            // rv为方法返回值，如果方法的返回类型为void，那么rv为null
            Object rv1 = staticMethod.invoke(null, "Static");
            Object rv2 = method1.invoke(md, "Non-static")
            Object rv3 = method2.invoke(md, 1, 2);

        } catch (NoSuchMethodException | IllegalAccessException | InvocationTargetException e) {
            e.printStackTrace();
        }
    }
}
/*
Static Static
Non-static Non-static
12
*/
```

#### 2.2.3 `Field`类

`Field`类用于获取类或接口的成员变量，我们可以获取这些成员变量的类型，并且也能重新设值。

示例:

```java
class FieldDemo {

    public static final int CONST = 1;

    public static int static_var = 2;

    private int member_var_1 = 100;

    protected String member_var_2 = "Hello";
    
    volatile public int[][] matrix = new int[4][4];
    
    transient List<Character> list = new ArrayList<>();

}

public class TestClass {

    @Test
    public void testCase() {
        FieldDemo fd = new FieldDemo();
        Class clazz = fd.getClass();

        Field[] fields = clazz.getDeclaredFields();
        for (Field field : fields) {

            //获取字段的名字
            System.out.printf("Field：%-4s|",field.getName());
            //获取字段的类型的Class类，然后获取规范化的名字
            System.out.printf("Type：%-18s|",field.getType().getCanonicalName());
            //获取字段的类型的Type类对象，然后获取类的名字
            System.out.printf("GenericType:%-18s|",field.getGenericType().getTypeName());
            //使用Field.getModifiers()，可获取成员的修饰符编码，
            //然后再使用Modifier.toString(int code)，来解码成字字符串
            System.out.printf("修饰符：%s", Modifier.toString(field.getModifiers()));
            System.out.println();
        }

    }
}
/*
Field：CONST|Type：int               |GenericType:int               |修饰符：public static final
Field：static_var|Type：int               |GenericType:int               |修饰符：public static
Field：member_var_1|Type：int               |GenericType:int               |修饰符：private
Field：member_var_2|Type：java.lang.String  |GenericType:java.lang.String  |修饰符：protected
Field：matrix|Type：int[][]           |GenericType:int[][]           |修饰符：public volatile
Field：list|Type：java.util.List    |GenericType:java.util.List<java.lang.Character>|修饰符：transient
*/
```

这里我们可以获取类的所有基本类型成员变量和引用类型成员变量，并且获取到它们的修饰符。

同样地，我们也可以获取到它们的值：

```java
class FieldDemo {

    private static final int CONST = Integer.MAX_VALUE;

    private double a;
    private String b;

    public FieldDemo(double a, String b) {
        this.a = a;
        this.b = b;
    }

    @Override
    public String toString() {
        return "FieldDemo{" +
                "a=" + a +
                ", b='" + b + '\'' +
                '}';
    }
}

public class TestClass {

    @Test
    public void testCase() {
        FieldDemo fd = new FieldDemo(3.0, "Test");
        Class clazz = fd.getClass();

        Field[] fields = clazz.getDeclaredFields();
        for (Field field : fields) {
            field.setAccessible(true);
            try {
                System.out.println("获取到字段：" + field.getType().getCanonicalName()
                        + "，成员名：" + field.getName() + "，值：" + field.get(fd));
            } catch (IllegalAccessException e) {
                e.printStackTrace();
            }
        }
    }
}
/*
获取到成员：int，成员名：CONST，值：2147483647
获取到成员：double，成员名：a，值：3.0
获取到成员：java.lang.String，成员名：b，值：Test
*/
```

这里由于我们要访问的成员变量呃修饰符为`private`，所以我们要先调用`field.setAccessible(true);`来对访问限制进行解除。

最后我们还可以对成员变量的值进行修改：

```java
public class TestClass {

    @Test
    public void testCase() throws IllegalAccessException {
        FieldDemo fd = new FieldDemo(3.0, "Test");
        Class clazz = fd.getClass();

        Field[] fields = clazz.getDeclaredFields();
        for (Field field : fields) {
            field.setAccessible(true);
            System.out.println("获取到成员：" + field.getType().getCanonicalName()
                    + "，成员名：" + field.getName() + "，值：" + field.get(fd));
        }
        System.out.println(fd);
        System.out.println("================修改后==================");
        for (Field field : fields) {
            field.setAccessible(true);
            if (field.getType() == double.class) {
                field.set(fd, 4.0);
            }
            if (field.getName().equals("b")) {
                field.set(fd, "Over");
            }
        }
        System.out.println(fd);
    }
}
/*
获取到成员：int，成员名：CONST，值：2147483647
获取到成员：double，成员名：a，值：3.0
获取到成员：java.lang.String，成员名：b，值：Test
FieldDemo{a=3.0, b='Test'}
================修改后==================
FieldDemo{a=4.0, b='Over'}
*/
```

#### 2.2.4 `Constructor`类

该类分装了类构造器的有关信息，通过方法`newInstance(Object... args)`来构造对象。

示例:

```java
class ConstructorDemo {

    private static final int CONST = Integer.MAX_VALUE;

    private double a;
    private String b;

    public ConstructorDemo(double a, String b) {
        this.a = a;
        this.b = b;
    }

    public ConstructorDemo(double a) {
        this.a = a;
        this.b = "public Constructor";
    }

    private ConstructorDemo() {
        this.a = 0.0;
        this.b = "private Constructor";
    }

    @Override
    public String toString() {
        return "ConstructorDemo{" +
                "a=" + a +
                ", b='" + b + '\'' +
                '}';
    }
}

public class TestClass {

    @Test
    public void testCase() throws IllegalAccessException, NoSuchMethodException, InvocationTargetException, InstantiationException {
        Class clazz = ConstructorDemo.class;

        // 获取参数列表为double、String的构造器
        Constructor constructor1 = clazz.getDeclaredConstructor(double.class, String.class);
        ConstructorDemo object1 = (ConstructorDemo) constructor1.newInstance(-1.0, "Test1");
        System.out.println(object1);

        // 获取参数列表为double的构造器
        Constructor constructor2 = clazz.getDeclaredConstructor(double.class);
        ConstructorDemo object2 = (ConstructorDemo) constructor2.newInstance(-2.0);
        System.out.println(object2);

        // 获取无参构造器
        Constructor constructor3 = clazz.getDeclaredConstructor();
        // 由于要访问的私有构造器，所以要开启权限
        constructor3.setAccessible(true);
        ConstructorDemo object3 = (ConstructorDemo) constructor3.newInstance();
        System.out.println(object3);
    }
}
/*
ConstructorDemo{a=-1.0, b='Test1'}
ConstructorDemo{a=-2.0, b='public Constructor'}
ConstructorDemo{a=0.0, b='private Constructor'}
*/
```

### 2.3 三种IO模型

Java IO的方式有如下三种：

- BIO：同步阻塞IO
- NIO：同步非阻塞IO
- AIO：异步非阻塞IO

传统的java.io包种提供的输入、输出流，其交互方式是同步、阻塞的。Java 1.4中引入了NIO框架（java.nio），提供了诸如`Channel`、`Selector`和`Buffer`等抽象，可以构建多路复用的同步非阻塞IO，同时提供了更接近操作系统底层的高性能数据操作方式。Java 7中，NIO有了进一步的提升，也就是NIO 2。其引入了异步非阻塞IO方式，也就是AIO（Asynchronous IO）。异步IO基于事件和回调机制。

同步、异步与阻塞、非阻塞：

- 同步：一种可靠有序的运行机制，在进行同步操作时，后续的任务要等待当前调用返回。
- 异步：其他任务不需要等待当前调用返回，依靠事件、回调机制来实现任务调用。
- 阻塞：当前线程处于阻塞状态，则无法从事其他任务，只有当条件就绪后才能继续。
- 非阻塞：不管IO操作是否结束，直接返回，相应的操作在后台继续处理。

#### 2.3.1 BIO

在BIO通信模式下，服务端每收到一个连接（Socket），就会创建专门的线程（serversocket）响应该连接，这个连接会一直存在等待读取发来的数据，这个过程会阻塞所在线程，不能做别的事，直到操作结束返回结果值，因此这是同步阻塞。
BIO模式下，服务端连接多个客户端时，会开启多个线程响应连接。

![BIO工作模式](images\BIO工作模式.png)

线程是较为重量级的资源。当BIO并发量大，而后端服务或客户端处理数据慢时就会产生产生大量线程处于等待中，即上述的阻塞，是非常严重的资源浪费。此外，线程的切换也会导致cpu资源的浪费，单机内存限制也无法过多的线程，只能单向以流的形式读取数据。

当BIO并发量增大时，一个优化的手段是使用线程池，使用线程池管理线程，避免频繁创建、销毁线程造成的开销。

#### 2.3.2 NIO

Java NIO有三大组成部分：`Buffer`、`Channel`、`Selector`，通过事件驱动模式实现了什么时候有数据可读的问题。

**`Channel`**

`Channel`从相当于IO操作中的载体，相当于一个硬件设备、文件、Socket或者是程序中IO的read或write。我们既可以从通道中读取数据，又可以写数据到通道里。通道可以异步地读写。通道中的数据总是要先读到一个`Buffer`，或者总是要从一个`Buffer`中写入。

**`Buffer`**

`Buffer`用于和通道进行交互。数据是从通道读入缓冲区和从缓冲区写入到通道中的。缓冲区本质上是一块可以写入数据，然后可以从中读取数据的内存。这块内存被包装成NIO `Buffer`对象，并提供了一组方法，用来方便的访问该块内存。

![通道与缓存](images\通道与缓存.png)

**`Selector`**

`Selector`是选择器，`Channel`需要注册到`Selector`上，这样选择器就能同事维护多个`Channel`，当某个`Channel`的IO请求就绪时，`Selector`会为其开启一个操作线程，用`Selector`的优势在于它可以同时处理多个连接。

相比于BIO，NIO不需要为每个连接开启一个线程，而是统一由`Selector`管理，当连接没有IO操作时，不需要阻塞线程等待数据，只有当`Selector`检测到哪个`Channel`有有效的IO请求时，再为其开启操作线程，节省线程的开销，操作结束后返回结果值，故为同步非阻塞。

![Selector工作模式](images\Selector工作模式.png)

NIO使用单线程或者只使用少量的多线程，多个连接共用一个线程，消耗的线程资源会大幅减小。并且当处于等待（没有事件）的时候线程资源可以释放出来处理别的请求，通过事件驱动模型当有`accept`/`read`/`write`等事件发生后通知（唤醒）主线程分配资源来处理相关事件。以`Buffer`缓冲区的形式处理数据，处理更为方便。

![NIO工作模式](images\NIO工作模式.png)

#### 2.3.3 AIO

Java 7提出，是NIO的升级版，实现了异步非阻塞的通信模式。

AIO则是特殊的API，与BIO和NIO不同，当应用程序访问内核请求数据后，会直接返回，此时操作系统会完成第一第二阶段，当数据准备完毕后再通知应用程序，此过程应用程序可做任意事情，在这一阶段，和NIO不同的是NIO会主动轮询操作系统数据是否准备完毕，而AIO则是等待系统主动通知，再去取数据。这个过程中AIO仅仅向内核发送了请求，直接返回了函数，所谓异步，整个过程由系统完成数据装配，更没有线程阻塞了。

### 2.4 动态代理

常用的动态代理实现方式有两种，一种是利用JDK的反射机制进行代理，另一种是使用CGLIB进行代理。

#### 2.4.1 JDK动态代理

JDK代理必须要提供接口。

相比于静态代理（利用组合模式），一个代理类可以对多个实现类进行代理。并可以对不同的类型、不同的方法做出不同的代理行为。

示例1：

```java
// 接口
interface A {
    void func();
}

// 实现类
class B implements A {

    @Override
    public void func() {
        System.out.println("Call funcB");
    }
}

// 实现类
class C implements A {

    @Override
    public void func() {
        System.out.println("Call funcC");
    }
}

// 代理类，实现InvocationHandler
class DynamicProxy implements InvocationHandler {

    private Object object;

    public DynamicProxy(Object object) {
        this.object = object;
    }

    @Override
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
        System.out.println("Before...");
        Object invoke = method.invoke(object, args);
        System.out.println("After...");
        return invoke;
    }
}

public class TestClass {

    @Test
    public void testCase() {
        B b = new B();
        C c = new C();
        A proxyB = (A) Proxy.newProxyInstance(b.getClass().getClassLoader(), b.getClass().getInterfaces(), new DynamicProxy(b));
        A proxyC = (A) Proxy.newProxyInstance(c.getClass().getClassLoader(), c.getClass().getInterfaces(), new DynamicProxy(c));
        proxyB.func();
        proxyC.func();
    }
}
/*
Before...
Call funcB
After...
Before...
Call funcC
After...
*/
```

示例2：

```java
// 接口
interface A {
    void func();
}

// 实现类
class B implements A {

    @Override
    public void func() {
        System.out.println("Call funcB");
    }
}

// 实现类
class C implements A {

    @Override
    public void func() {
        System.out.println("Call funcC");
    }
}

class DynamicProxy {

    private Object object;

    public DynamicProxy(Object object) {
        this.object = object;
    }

    public Object getProxy() {
        return Proxy.newProxyInstance(object.getClass().getClassLoader(), object.getClass().getInterfaces(), new InvocationHandler() {
            @Override
            public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
                System.out.println("Before...");
                Object invoke = method.invoke(object, args);
                System.out.println("After...");
                return invoke;
            }
        });
    }
}

public class TestClass {

    @Test
    public void testCase() {
        B b = new B();
        C c = new C();
        A proxyB = (A) new DynamicProxy(b).getProxy();
        A proxyC = (A) new DynamicProxy(c).getProxy();
        proxyB.func();
        proxyC.func();
    }
}

/*
方法执行前...
你好！
方法执行后...
*/
```

#### 2.4.2 CGLIB代理

cglib是针对类来实现代理的，原理是对指定的业务类生成一个子类，并覆盖其中业务方法实现代理。因为采用的是继承，所以不能对`final`修饰的类进行代理，同样也不能代理`final`修饰的方法。

示例:

```java
class A {
    void func() {
        System.out.println("AAA");
    }
}

class B {
    void func() {
        System.out.println("BBB");
    }
}

class DynamicProxy implements MethodInterceptor {
    private Object target; // 现实类的引用

    public Object getInstance(Object target) {
        this.target = target;
        Enhancer enhancer = new Enhancer(); // 创建加强器，用来创建动态代理
        enhancer.setSuperClass(this.target.getClass()); // 为加强器指定要代理的业务类
        enhancer.setCallback(this); // 设置回调
        return enhancer.create(); // 创建动态代理并返回
    }

    @Override
    public Object intercept(Object o, Method method, Object[] args, MethodProxy methodProxy) throws Throwable {
        System.out.println("Before...");
        Object invoke = methodProxy.invokeSuper(o, args);
        System.out.println("After...");
        return invoke;
    }
}

public class TestClass {

    @Test
    public void testCase() {
        DynamicProxy proxy = new DynamicProxy();
        A a = (A) proxy.getInstance(new A());
        B b = (B) proxy.getInstance(new B());
        a.func();
        b.func();
    }
}
/*
Before...
AAA
After...
Before...
BBB
After...
*/
```

### 2.5 多线程

#### 2.5.1 什么是线程，线程与进程的关系

**线程**是指程序在执行过程中，能够执行程序代码的一个执行单元。在Java语言中，线程有6个状态：

- NEW
- RUNNABLE
- BLOCKED
- WAITING
- TIMED_WAITING
- TERMINATED

**进程**是指一段正在执行的程序，它是程序执行的最小单元，一个进程可以拥有多个线程，各个线程之间共享程序的内存空间（代码段、数据段和堆空间）及一些进程级的资源（例如打开的文件），但是各个线程拥有自己的栈空间。

在操作系统级别上，程序的执行都是以进程为单位的，而每个进程中通常会有多个线程互不影响地并发执行。使用多线程为程序的开发带来了巨大的便利，具体而言有如下几个方面：

- 使用多线程可以减少程序的响应时间。在单线程情况下，如果某个操作很耗时，或者陷入长时间的等待，此时程序将不会响应鼠标和键盘等操作，使用多线程可以将这些耗时的操作分配到一个单独的线程中去执行，从而使程序具备了更好的交互性。
- 与进程相比，线程的创建和切换开销更小。由于启动一个新的进程必须给其分配独立的地址空间，建立许多数据结构来维护进程的代码段、数据段等，而运行于同一进程内的线程共享代码段、数据段。同时线程的切换开销也比进程小。
- 多CPU或多核计算机本身就具有执行多线程的能力，如果使用单个线程，将无法重复利用计算机资源，造成资源的巨大浪费。因此在多CPU计算机上使用多线程能提高CPU的利用率。
- 使用多线程能够简化程序的结构，一个复杂的进程可以分成多个线程来执行。

#### 2.5.2 多线程中的同步与异步

在多线程的环境中，经常会碰到数据的共享问题，即多个线程需要访问同一个资源时，它们需要以某种顺序来确保该资源在某一时刻只能被一个线程使用，否则，程序的运行结果将会是不可预料的。在这种情况下就必须对数据进行同步。

要实现同步操作，必须要获得每一个线程对象的锁。获得它可以保证在同一时刻只有一个线程能够进入临界区（访问互斥资源的代码块），并且在这个锁被释放前，其他线程就不能再进入临界区。如果还有其他线程想要获得该对象的锁，只能进入等待队列等待此时进入临界区的线程退出临界区并释放锁。

异步与非阻塞类似，由于每个线程都包含了运行时自身锁需要的数据或方法，因此，在进行输入输出处理时，不必关心其他线程的状态或行为，也不必等到输入输出处理完毕才返回。当应用程序在对象上调用了一个需要花费很长时间来执行的方法，并且不希望让程序等待方法返回时，就应该使用异步编程，异步能够提高程序的效率。

#### 2.5.3 实现多线程

##### 2.5.3.1 继承`Thread`类，重写`run()`方法

`Thread`类本质上是实现了`Runnable`接口的一个类，它代表一个线程的实例。

启动线程的方法就是通过`Thread`类的`start()`实例方法。`start()`方法是一个`native方`法，它将启动一个新线程，并执行`run()`方法。这种方式实现多线程很简单，通过自己的类直接extend Thread，并重写`run()`方法，就可以启动新线程并执行自己定义的`run()`方法。

需要注意的是，调用`start()`方法后并不是立即执行多线程中的代码，而是使得该线程变为可运行态，具体何时会执行多线程代码是由系统决定的。

示例：

```java
public class TestClass {

    @Test
    public void testCase() {
        // 使用匿名内部类
        new Thread() {
            @Override
            public void run() {
                for (int i = 0; i < 100; i++) {
                    System.out.println(i);
                }
            }
        }.start();
        
        for (int i = 100; i < 200; i++) {
            System.out.println(i);
        }
    }
}
```

##### 2.5.3.2 实现`Runnable`接口，实现`run()`方法

如果我们的类已经继承了一个类导致无法再继承`Thread`，此时可以实现`Runnable`接口。

步骤如下：

- 自定义类并实现`Runnable`接口，重写`run()`方法。
- 将实现了`Runnable`接口的自定义类的对象作为参数来实例化`Thread`对象。
- 执行`Thread`对象的`start()`方法。

示例：

```java
public class TestClass {

    @Test
    public void testCase() {
        new Thread(new Runnable() {
            @Override
            public void run() {
                for (int i = 0; i < 100; i++) {
                    System.out.println(i);
                }
            }
        }).start();

        for (int i = 100; i < 200; i++) {
            System.out.println(i);
        }
    }
}
```

事实上，无论是通过继承`Thread`类还是通过使用`Runnable`接口来实现多线程，最终还是通过`Thread`类的API来控制线程的。

##### 2.5.3.3 实现`Callable`接口，实现`call()`方法

`Callable`接口是属于`Executor`框架中的功能接口，该接口只有一个`call()`方法。

```java
public interface Callable<V> {
    /**
     * Computes a result, or throws an exception if unable to do so.
     *
     * @return computed result
     * @throws Exception if unable to compute a result
     */
    V call() throws Exception;
}
```

`Callable`接口与`Runnable`接口类似，但提供了比`Runnable`接口更强大的功能，主要表现为以下3点：

- `Callable`可以在任务结束后提供一个返回值，而`Runnable`无此功能。
- `Callable`中的`call()`方法可以抛出异常，而`Runnable`接口中的`run()`方法不能抛出异常。
- 运行`Callable`可以拿到一个`Future`对象，`Future`对象提供了检查计算是否完成，并可以获取执行的结果。

`Future`接口：

```java
public interface Future<V> {

    boolean cancel(boolean mayInterruptIfRunning);

    boolean isCancelled();

    boolean isDone();

    V get() throws InterruptedException, ExecutionException;

    V get(long timeout, TimeUnit unit)
        throws InterruptedException, ExecutionException, TimeoutException;
}
```

该接口的五个方法：

- `cancel(boolean mayInterruptIfRunning)`：该方法用来取消任务，如果任务取消成功则返回`true`，如果取消任务失败则返回`false`。参数`mayInterruptIfRunning`表示是否允许取消正在执行却没有执行完毕的任务。如果设置为`true`，则表示可以取消正在执行过程中的任务。如果任务已经完成，那么无论设置为`true`还是`false`，该方法肯定返回`false`，即如果取消已经完成的任务，则返回`false`。如果任务正在执行，则`mayInterruptIfRunning`设置为`true`，则返回`true`，设置为`false`，则返回`false`。若任务还没有执行，则无论怎样设置，均返回`true`。
- `isCancelled()`：该方法表示任务是否被取消成功，如果在任务正常完成前被取消成功，则返回`true`。
- `isDone()`：该方法表示任务是否已经完成，若任务完成，则返回`true`。
- `get()`：该方法用来获取执行结果，这个方法会发生阻塞，会一直等到任务执行完毕才返回。
- `get(long timeout, TimeUnit unit)`：如果在指定时间内还没有获取到结果，则直接返回`null`。

示例:

```java
public class TestClass {

    @Test
    public void testCase() {
        ExecutorService executorService = Executors.newCachedThreadPool();
        Future<String> future = executorService.submit(new Callable<String>() {
            @Override
            public String call() throws Exception {
                return "Hello World";
            }
        });

        try {
            System.out.println("Waiting...");
            System.out.println(future.get());
        } catch (InterruptedException e) {
            e.printStackTrace();
        } catch (ExecutionException e) {
            e.printStackTrace();
        }
    }
}
/*
Waiting...
Hello World
*/
```

由于`Future`是接口，无法直接实例化，所以其具有实现类`FutureTask`。

`FutureTask`实现了`RunnableFuture`接口，而`RunnableFuture`接口继承`Runnable`接口和`Future`接口。所以`FutureTask`既可以被当作`Runnable`被线程执行，也可以作为`Future`来得到`Callable`的返回值。

```java
public class TestClass {

    @Test
    public void testCase() {
        FutureTask futureTask = new FutureTask(new Runnable() {
            @Override
            public void run() {
                System.out.println("Test");
            }
        }, null);

        // FutureTask作为Runnable
        new Thread(futureTask).start();
    }
}
```

```java
public class TestClass {

    @Test
    public void testCase() throws ExecutionException, InterruptedException {
        FutureTask<String> futureTask = new FutureTask<>(new Callable<String>() {
            @Override
            public String call() throws Exception {
                return "Test";
            }
        });

        // FutureTask作为Callable
        ExecutorService executorService = Executors.newSingleThreadExecutor();
        executorService.submit(futureTask);
        System.out.println(futureTask.get());
    }
}
```

#### 2.5.4 `Executor`框架

`Executor`框架是Java 5引入的新的启动、调度和管理线程的框架。其内部使用了线程池机制，它在`java.util.cocurrent`包下，通过该框架来控制线程的启动、执行和关闭，可以简化并发编程的操作。因此，在Java 5之后，通过`Executor`来启动线程比使用`Thread`的`start()`方法更好，其更易管理，效率更好。

![Executor框架](images\Executor框架.png)

##### 2.5.4.1 `Executor`与`ExecutorService`

`Executor`接口定义了一个接收`Runnable`对象的方法`execute()`：

```java
public interface Executor {
    void execute(Runnable command);
}
```

该方法用来创建一个线程并执行`Runnable`实例中定义的任务。

`ExecutorService`接口是`Executor`的子接口，其提供了生命周期管理方法，并可以返回`Future`对象：

```java
public interface ExecutorService extends Executor {

    void shutdown();

    List<Runnable> shutdownNow();

    boolean isShutdown();

    boolean isTerminated();

    boolean awaitTermination(long timeout, TimeUnit unit)
        throws InterruptedException;

    <T> Future<T> submit(Callable<T> task);

    <T> Future<T> submit(Runnable task, T result);

    Future<?> submit(Runnable task);

    <T> List<Future<T>> invokeAll(Collection<? extends Callable<T>> tasks)
        throws InterruptedException;

    <T> List<Future<T>> invokeAll(Collection<? extends Callable<T>> tasks,
                                  long timeout, TimeUnit unit)
        throws InterruptedException;

    <T> T invokeAny(Collection<? extends Callable<T>> tasks)
        throws InterruptedException, ExecutionException;

    <T> T invokeAny(Collection<? extends Callable<T>> tasks,
                    long timeout, TimeUnit unit)
        throws InterruptedException, ExecutionException, TimeoutException;
}
```

通过`submit()`方法返回的`Future`对象，可以调用`isDone()`方法查询任务是否已经完成。当任务完成时，它具有一个结果，可以调用`get()`方法来获取该结果。`Future`还提供了`cancel()`方法用来取消执行pending中的任务。

##### 2.5.4.2 `Executors`工厂类

`Executors`类，提供了一系列工厂方法用于创建线程池，返回的线程池都实现了`ExecutorService`接口。

- `ThreadPoolExecutor`：
    + `newCachedThreadPool`：创建一个可缓存线程池，如果线程池长度超过处理需要，可灵活回收空闲线程。而当需求增加时，则可以自动添加新线程，线程池的规模不存在任何限制。
    + `newFiexedThreadPool`：创建一个可重用固定线程数的线程池，采用无界的阻塞队列，所以实际线程数量永远不会变化，适用于可以预测线程数量的业务中。
    + `newScheduledThreadPool`：创建一个定长线程池，支持定时及周期性任务执行，可以延时启动，定时启动的线程池，适用于需要多个后台线程执行周期任务的场景。
    + `newSingleThreadExecutor`：创建一个单线程化的线程池，它只会用唯一的工作线程来执行任务，保证所有任务按照指定顺序(FIFO, LIFO, 优先级)执行
- `ForkJoinPool`：
    + `newWorkStealingPool`：创建一个拥有多个任务队列的线程池，可以减少连接数，创建当前可用cpu数量的线程来并行执行，适用于大耗时的操作，可以并行来执行。

#### 2.5.5 锁

在计算机科学中，锁(lock)或互斥(mutex)是一种同步机制，用于在有许多执行线程的环境中强制对资源的访问限制。锁旨在强制实施互斥排他、并发控制策略。

锁通常需要硬件支持才能有效实施。这种支持通常采取一个或多个原子指令的形式，如"test-and-set", "fetch-and-add" or "compare-and-swap"”。这些指令允许单个进程测试锁是否空闲，如果空闲，则通过单个原子操作获取锁。

##### 2.5.5.1 Java中常用的锁机制

![锁的分类](images\锁的分类.png)

**独享锁/共享锁**

- 独享锁：是指该锁一次只能被一个线程所持有。
- 共享锁：是指该锁可被多个线程锁持有。

**互斥锁/读写锁**

独享锁/共享锁是广义的说法，互斥锁/读写锁分别对应具体的实现。在Java中，`ReentrantLock`就是互斥锁（独享锁），`ReentrantReadWriteLock`就是读写锁（共享锁）。
独享锁与共享锁是通过AQS实现的。

**公平锁/非公平锁**

公平锁是指多个线程按照申请锁的顺序来获取锁。

非公平锁是指多个线程获取锁的顺序并不是按照申请锁的顺序，有可能后申请的线程比先申请的线程优先获取锁。

对于`ReentrantLock`而言，通过构造函数可以指定该锁是否为公平锁（默认是非公平的），非公平锁的吞吐量要大于公平锁。但是，并不是任何场景都是以TPS作为唯一指标，公平锁能够减少“饥饿”发生的概率，等待越久的请求越能够得到优先满足。

对于`synchronized`而言，也是一种非公平锁。由于其并不像`ReentrantLock`是通过AQS的控制线程对锁的获取， 所以并没有任何办法使其变成公平锁。

**可重入锁**

可重入锁又称为递归锁，是指同一个线程在外层方法获取锁的时候，在进入内层方法会自动获取锁。`ReentrantLock`和`synchronized`都是可重入锁。可重入锁的一个好处是可以在一定程度上避免死锁。

实现一个可重入锁:

```java
public class Lock{

    private boolean isLocked;
    private Thread lockedBy;
    private int lockedCount;

    public synchronized void lock() throws InterruptedException{
        Thread thread = Thread.currentThread();
        while(isLocked && lockedBy != thread){
            wait();
        }
        isLocked = true;
        lockedCount++;
        lockedBy = thread;
    }

    public synchronized void unlock(){
        if(Thread.currentThread() == this.lockedBy){
            lockedCount--;
            if(lockedCount == 0){
                isLocked = false;
                notify();
            }
        }
    }
}
```

**乐观锁/悲观锁**

乐观锁和悲观锁并不是指具体类型的锁，而是看待并发的角度。

悲观锁认为存在很多并发更新操作，如果操作时不加锁就一定会发生问题。
乐观锁则认为不存在很多的并发更新操作，不需要加锁。Java中是使用CAS（Compare And Swap）实现乐观锁的。

**分段锁**

分段锁是一种锁的设计，并不是一种具体的锁。`ConcurrentHashMap`就是通过分段锁实现了高效的并发操作。

**自旋锁**

自旋锁是指尝试获取锁的线程不会阻塞，而是采用循环的方式不断地尝试获取锁，直到获取锁才会退出循环。

- 自旋锁的缺点：如果某个线程持有锁的时间过长，就会导致其它等待获取锁的线程进入循环等待，消耗CPU。使用不当会造成CPU使用率极高。
- 自旋锁的优点: 自旋锁不会使线程状态发生切换，一直处于用户态，即线程一直都是active的；不会使线程进入阻塞状态，减少了不必要的上下文切换，执行速度快。

**实现一个可重入的自旋锁：**

```java
class ReentrantSpinLock {
    private AtomicReference<Thread> cas = new AtomicReference<>();
    private int count; // 用于实现可重入，记录获取锁的次数
    public void lock() {
        Thread current = Thread.currentThread();
        if (current == cas.get()) {
            count++;
        } else {
            // 第一个执行lock()方法的线程预测原来的值为null，并设置新值为当前线程，不进入while循环
            // 第二个执行lock()的线程由于此时真值不为null，所以进入while循环
            // 只有当第一个执行lock()的线程执行unlock()将真值重新设为null，后续的线程才能脱离while循环
            while (!cas.compareAndSet(null, current)) {
                // do nothing...
            }
        }
    }
    
    public void unlock() {
        Thread current = Thread.currentThread();
        if (current == cas.get()) {
            if (count > 0) {
                count--;
            } else {
                cas.compareAndSet(current, null);
            }
        }
    }
}
```

**偏向锁/轻量级锁/重量级锁**

首先来看重量级锁，内置锁在Java中被抽象为监视器锁（monitor）。在JDK 1.6之前，监视器锁可以认为直接对应底层操作系统中的互斥量（mutex）。这种同步方式的成本非常高，包括系统调用引起的内核态与用户态切换、线程阻塞造成的线程切换等。因此，后来称这种锁为“重量级锁”。

如果锁竞争激烈，我们不得不依赖于重量级锁，让竞争失败的线程阻塞；如果完全没有实际的锁竞争，那么申请重量级锁都是浪费的。轻量级锁的目标是，减少无实际竞争情况下，使用重量级锁产生的性能消耗，包括系统调用引起的内核态与用户态切换、线程阻塞造成的线程切换等。

轻量级锁是相对于重量级锁而言的。使用轻量级锁时，不需要申请互斥量，仅仅将Mark Word中的部分字节CAS更新指向线程栈中的Lock Record，如果更新成功，则轻量级锁获取成功，记录锁状态为轻量级锁；否则，说明已经有线程获得了轻量级锁，目前发生了锁竞争（不适合继续使用轻量级锁），接下来膨胀为重量级锁。

如果不仅仅没有实际竞争，自始至终，使用锁的线程都只有一个，那么，维护轻量级锁都是浪费的。偏向锁的目标是，减少无竞争且只有一个线程使用锁的情况下，使用轻量级锁产生的性能消耗。轻量级锁每次申请、释放锁都至少需要一次CAS，但偏向锁只有初始化时需要一次CAS。

“偏向”的意思是，偏向锁假定将来只有第一个申请锁的线程会使用锁（不会有任何线程再来申请锁），因此，只需要在Mark Word中CAS记录owner（本质上也是更新，但初始值为空），如果记录成功，则偏向锁获取成功，记录锁状态为偏向锁，以后当前线程等于owner就可以零成本的直接获得锁；否则，说明有其他线程竞争，膨胀为轻量级锁。

偏向锁、轻量级锁、重量级锁适用于不同的并发场景：

- 偏向锁：无实际竞争，且将来只有第一个申请锁的线程会使用锁。
- 轻量级锁：无实际竞争，多个线程交替使用锁；允许短时间的锁竞争。
- 重量级锁：有实际竞争，且锁竞争时间长。

![锁升级](images\锁升级.png)

![锁分配及膨胀问题](images\锁分配及膨胀问题.png)

##### 2.5.5.2 `Lock`与`synchronized`

`Lock`与`synchronized`有以下几点不同：

- `Lock`是一个接口，而`synchronized`是Java中的关键字，`synchronized`是内置的语言实现，`synchronized`是在JVM层面上实现的，不但可以通过一些监控工具监控`synchronized`的锁定，而且在代码执行时出现异常，JVM会自动释放锁定，但是使用`Lock`则不行，`Lock`是通过代码实现的，要保证锁定一定会被释放，就必须将`unLock()`放到`finally{}`中。
- `Lock`可以让等待锁的线程响应中断，线程可以中断去干别的事务，而`synchronized`却不行，使用`synchronized`时，等待的线程会一直等待下去，不能够响应中断。
- 通过`Lock`可以知道有没有成功获取锁，而`synchronized`却无法办到。
- `Lock`可以提高多个线程进行读操作的效率。在性能上来说，如果竞争资源不激烈，两者的性能是差不多的，而当竞争资源非常激烈时（即有大量线程同时竞争），此时`Lock`的性能要远远优于`synchronized`。所以说，在具体使用时要根据适当情况选择。

`synchronized`关键字会让没有得到锁资源的线程进入BLOCKED状态，而后在争夺到锁资源后恢复为RUNNABLE状态，这个过程中涉及到操作系统用户模式和内核模式的转换，代价比较高。

尽管Java1.6为`synchronized`做了优化，增加了从偏向锁到轻量级锁再到重量级锁的过度，但是在最终转变为重量级锁之后，性能仍然较低。

##### 2.5.5.3 `ReentrantLock`

`ReentrantLock`是一个可重入的互斥锁，它具有与使用`synchronized`监视器锁相同的基本行为和语义，但与`synchronized`关键字相比，它更灵活、更强大，增加了轮询、超时、中断等高级功能。`ReentrantLock`，顾名思义，它是支持可重入锁的锁，是一种递归无阻塞的同步机制。除此之外，该锁还支持获取锁时的公平和非公平选择。

类图如下：

![ReentrantLock](images\ReentrantLock.png)

`ReentrantLock`的默认构造函数会使用非公平锁：

```java
public ReentrantLock() {
    sync = new NonfairSync();
}
```

我们也可以手动设置是否使用非公平锁：

```java
public ReentrantLock(boolean fair) {
    sync = fair ? new FairSync() : new NonfairSync();
}
```

`Sync`类是`ReentrantLock`自定义的同步组件，它是`ReentrantLock`里面的一个内部类，它继承自AQS，它有两个子类：公平锁`FairSync`和非公平锁`NonfairSync`。`ReentrantLock`的获取与释放锁操作都是委托给该同步组件来实现的。

`ReentrantLock`类中的方法：

- `lock()`：当前线程尝试获取锁。
- `getHoldCount()`：当前线程调用`lock()`方法的次数。
- `getQueueLength()`：当前正在等待获取`Lock`锁的线程的估计数。
- `getWaitQueueLength(Condition condition)`：当前正在等待状态的线程的估计数，需要传入`Condition`对象。
- `hasWaiters(Condition condition)`：查询是否有线程正在等待与`Lock`锁有关的`Condition`条件。
- `hasQueuedThread(Thread thread)`：查询指定的线程是否正在等待获取`Lock`锁。。
- `hasQueuedThreads()`：查询是否有线程正在等待获取此锁定。
- `isFair()`：判断当前`Lock`锁是不是公平锁。
- `isHeldByCurrentThread()`：查询当前线程是否保持此锁定。
- `isLocked()`：查询此锁定是否由任意线程保持。
- `tryLock()`：线程尝试获取锁，如果获取成功，则返回`true`，如果获取失败（即锁已被其他线程获取），则返回`false`。
- `tryLock(long timeout，TimeUnit unit)`：线程如果在指定等待时间内获得了锁，就返回`true`，否则返回`false`。
- `lockInterruptibly()`：如果当前线程未被中断，则获取该锁定，如果已经被中断则出现异常。即允许在等待时由其它线程调用等待线程的`Thread.interrupt()`方法来中断等待线程的等待而直接返回，这时不用获取锁，而会抛出一个`InterruptedException`。 

#### 2.5.6 CAS与AQS

- CAS：Compare And Swap
- AQS：AbstractQueuedSynchronizer

**CAS**

CAS(Compare And Swap)，即比较并交换。是解决多线程并行情况下使用锁造成性能损耗的一种机制，CAS操作包含三个操作数——内存位置（V）、预期原值（A）和新值（B）。如果内存位置的值与预期原值相匹配，那么处理器会自动将该位置值更新为新值。否则，处理器不做任何操作。

`java.util.concurrent.atomic`中的`AtomicXXX`类都使用了底层的提供的高效CAS操作，而`java.util.concurrent`中的大多数类在实现时都直接或间接地使用了这些原子变量类，这些原子变量都调用了`sun.misc.Unsafe`类中的CAS算法，例如：

```java
// 使用CAS实现无锁自增
public final int getAndIncrement() {  
        for (;;) {  
            int current = get();  
            int next = current + 1;  
            if (compareAndSet(current, next))  
                return current;  
        }  
}  
 
public final boolean compareAndSet(int expect, int update) {  
    return unsafe.compareAndSwapInt(this, valueOffset, expect, update);  
}
```

CAS的ABA问题：如果一个值原来是A，后来变成了B， 后来又变成了A，CAS会认为没有发生变化。

解决方案：

- 加版本号：1A -> 2B -> 3A
- Java 1.5提供了`AtomicStampedReference`来解决这个问题

**AQS**

`AbstractQueuedSynchronizer`，即队列同步器。它是构建锁或者其他同步组件的基础框架，它是JUC并发包中的核心基础组件。

AQS的主要使用方式是继承，子类通过继承同步器并实现它的抽象方法来管理同步状态。

AQS使用一个`int`类型的成员变量`state`来表示同步状态，当`state > 0`时表示已经获取了锁，当`state = 0`时表示释放了锁。它提供了三个方法（`getState()`、`setState(int newState)`、`compareAndSetState(int expect,int update)`）来对同步状态`state`进行操作，当然AQS可以确保对`state`的操作是安全的。

AQS通过内置的FIFO同步队列来完成资源获取线程的排队工作：

同步器包含两个节点类型的应用，一个指向头节点，一个指向尾节点，未获取到锁的线程会创建节点线程安全（compareAndSetTail）的加入队列尾部。同步队列遵循FIFO，首节点是获取同步状态成功的节点。

![同步器1](images\同步器1.png)

未获取到锁的线程将创建一个节点，设置到尾节点。

![同步器2](images\同步器2.png)

首节点的线程在释放锁时，将会唤醒后继节点。而后继节点将会在获取锁成功时将自己设置为首节点。

![同步器3](images\同步器3.png)

#### 2.5.7 `CountDownLatch`、`Semaphore`和`CyclicBarrier`

并发包（JUC）提供了三个类用于同步一批线程的行为，分为是：

- `CountDownLatch`：计数器闭锁
- `Semaphore`：信号量
- `CyclicBarrier`：栅栏

##### 2.5.7.1 `CountDownLatch`

`CountDownLatch`允许一个或多个线程等待一系列指定操作的完成，通过它可以完成类似于阻塞当前线程的功能。

`CountDownLatch`用一个给定的计数器来初始化，该计数器的操作是原子操作，即同时只能有一个线程去操作该计数器。调用该类`await()`方法的线程会一直处于阻塞状态，直到其他线程调用`countDown()`方法使当前计数器的值变为零，每次调用`countDown()`计数器的值减1。当计数器值减至零时，所有因调用`await()`方法而处于等待状态的线程就会继续往下执行。但是，这种现象只会出现一次，因为计数器不能被重置，所以如果业务上需要一个可以重置计数次数的计数器，那么可以考虑使用`CycliBarrier`。

示例：

```java
public class TestClass {

    @Test
    public void testCase() throws InterruptedException {
        int threadCount = 5;
        ExecutorService exec = Executors.newCachedThreadPool();
        CountDownLatch latch = new CountDownLatch(threadCount);
        // 启动5个线程
        for (int i = 0; i < threadCount; i++) {
            exec.submit(() -> {
                try {
                    threadWork();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                } finally {
                    latch.countDown();
                }
            });
        }
        // 主线程等待countDown到0
        latch.await();
        exec.shutdown();
        System.out.println("Finish...");
    }

    private void threadWork() throws InterruptedException {
        TimeUnit.MILLISECONDS.sleep(100);
        System.out.println(Thread.currentThread().getName() + " is Ready...");
    }
}
/*
pool-1-thread-4 is Ready...
pool-1-thread-1 is Ready...
pool-1-thread-2 is Ready...
pool-1-thread-3 is Ready...
pool-1-thread-5 is Ready...
Finish...
*/
```

##### 2.5.7.2 `Semaphore`

`Semaphore`与`CountDownLatch`相似，不同的地方在于`Semaphore`的值被获取到后是可以释放的，并不像`CountDownLatch`那样一直减到底。它也被更多地用来限制流量，类似阀门的功能。可以限定某些资源最多有N个线程可以访问，只有当现有线程结束后，才会允许新的线程进来。有点类似于锁的`lock()`与`unlock()`过程。相对来说他也有两个主要的方法：
用于获取权限的`acquire()`，其底层实现与`CountDownLatch.countdown()`类似;
用于释放权限的`release()`，其底层实现与`acquire()`是一个互逆的过程。

示例:

```java
public class TestClass {

    @Test
    public void testCase() throws InterruptedException {
        int threadCount = 9;
        ExecutorService exec = Executors.newCachedThreadPool();
        Semaphore semaphore = new Semaphore(3);
        // 创建9个线程，但每次最多允许3个进入
        for (int i = 0; i < threadCount; i++) {
            exec.submit(() -> {
                try {
                    semaphore.acquire();
                    threadWork();
                    semaphore.release();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            });
        }
        TimeUnit.MILLISECONDS.sleep(10000);
        exec.shutdown();
        System.out.println("Finish...");
    }

    private void threadWork() throws InterruptedException {
        TimeUnit.MILLISECONDS.sleep(1000);
        System.out.println(Thread.currentThread().getName() + " is working...");
    }
}
/*
每一次会打印出3个
pool-1-thread-3 is working...
pool-1-thread-1 is working...
pool-1-thread-2 is working...

pool-1-thread-5 is working...
pool-1-thread-4 is working...
pool-1-thread-6 is working...

pool-1-thread-9 is working...
pool-1-thread-8 is working...
pool-1-thread-7 is working...

Finish...
*/
```

##### 2.5.7.3 `CyclicBarrier`

`CyclicBarrier`也是一个同步辅助类，它的功能类似于栅栏，它允许一组线程相互等待，直到到达某个公共屏障点（common barrier point）。通过它可以完成多个线程之间相互等待，只有当每个线程都准备就绪后，才能各自继续往下执行后面的操作。

类似于`CountDownLatch`，它也是通过计数器来实现的。当某个线程调用`await()`方法时，该线程进入等待状态，且计数器加1，当计数器的值达到设置的初始值时，所有因调用`await()`进入等待状态的线程被唤醒，继续执行后续操作。因为`CycliBarrier`在释放等待线程后可以重用，所以称为循环barrier。

`CycliBarrier`支持一个可选的`Runnable`，在计数器的值到达设定值后（但在释放所有线程之前），该`Runnable`运行一次，注，`Runnable`在每个屏障点只运行一个。

`CyclicBarrier`与`CountDownLatch`的区别：

- `CountDownLatch`主要是实现了1个或N个线程需要等待其他线程完成某项操作之后才能继续往下执行操作，描述的是1个线程或N个线程等待其他线程的关系。
- `CyclicBarrier`主要是实现了多个线程之间相互等待，直到所有的线程都满足了条件之后各自才能继续执行后续的操作，描述的多个线程内部相互等待的关系。
- `CountDownLatch`是一次性的，而`CyclicBarrier`则可以被重置而重复使用。

示例：

```java
public class TestClass {

    private int threadCount = 5;

    private CyclicBarrier barrier = new CyclicBarrier(threadCount, ()->{
        System.out.println("Callback is running...");
    });

    @Test
    public void testCase() throws InterruptedException {
        ExecutorService exec = Executors.newCachedThreadPool();
        for (int i = 0; i < threadCount; i++) {
            exec.submit(() -> {
                try {
                    threadWork();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                } catch (BrokenBarrierException e) {
                    e.printStackTrace();
                }
            });
        }
        TimeUnit.MILLISECONDS.sleep(10000);
        exec.shutdown();
        System.out.println("Finish ...");
    }

    private void threadWork() throws InterruptedException, BrokenBarrierException {
        TimeUnit.MILLISECONDS.sleep(1000);
        System.out.println(Thread.currentThread().getName() + " is Ready...");
        barrier.await();
        System.out.println(Thread.currentThread().getName() + " is Working...");
    }
}
/*
pool-1-thread-3 is Ready...
pool-1-thread-1 is Ready...
pool-1-thread-5 is Ready...
pool-1-thread-4 is Ready...
pool-1-thread-2 is Ready...
Callback is running...
pool-1-thread-2 is Working...
pool-1-thread-3 is Working...
pool-1-thread-5 is Working...
pool-1-thread-1 is Working...
pool-1-thread-4 is Working...
Finish ...
*/
```

#### 2.5.8 并发容器

##### 2.5.8.1 `Map`

**`ConcurrentHashMap`**

`Hashtable`实现同步是利用`synchronized`关键字进行锁定的，其是针对整张哈希表进行锁定的，即每次锁住整张表让线程独占，在线程安全的背后是巨大的浪费。`ConcurrentHashMap`和`Hashtable`主要区别就是围绕着锁的粒度进行区别以及如何区锁定。

原理对比如下：

![ConcurrentHashMap锁的原理](images\ConcurrentHashMap锁的原理.png)

上图中，左边是`Hashtable`的实现方式，可以看到锁住整个哈希表；而右边则是`ConcurrentHashMap`的实现方式，单独锁住每一个桶（segment）。`ConcurrentHashMap`将哈希表分为16个桶（默认值），诸如`put()`，`remove()`等常用操作只锁当前需要用到的桶，而`size()`方法才锁定整张表。这样原来只能一个线程进入，现在却能同时接受16个写线程并发进入（写线程需要锁定，而读线程几乎不受限制），并发性的提升是显而易见的。

并且在迭代时，`ConcurrentHashMap`使用了不同于传统集合的快速失败迭代器（fast-fail iterator）的另一种迭代方式，而是使用了弱一致迭代器。在这种迭代方式中，当`Iterator`被创建后集合再发生改变就不再是抛出`ConcurrentModificationException`，取而代之的是在改变时实例化出新的数据从而不影响原有的数据，`Iterator`完成后再将头指针替换为新的数据，这样`Iterator`线程可以使用原来老的数据，而写线程也可以并发的完成改变，更重要的，这保证了多个线程并发执行的连续性和扩展性，是性能提升的关键。

**`ConcurrentSkipListMap`**

`ConcurrentSkipListMap`的底层是通过跳表来实现的。跳表是一个链表，但是通过使用“跳跃式”查找的方式使得插入、读取数据时复杂度变成了O(logn)。

`ConcurrentSkipListMap`线程安全的原理是利用底层的插入、删除的CAS原子性操作，通过死循环不断获取最新的节点指针来保证不会出现竞态条件。

##### 2.5.8.2 `Queue`

Java提供的线程安全的`Queue`可以分为阻塞队列和非阻塞队列。

阻塞队列的例子就是`LinkedBlockingQueue`。

由于`LinkedBlockingQueue`实现是线程安全的，实现了先进先出等特性，是作为生产者消费者的首选，`LinkedBlockingQueue`可以指定容量，也可以不指定，不指定的话，默认最大是`Integer.MAX_VALUE`，其中主要用到`put()`和`take()`方法，`put()`方法在队列满的时候会阻塞直到有队列成员被消费，`take()`方法在队列空的时候会阻塞，直到有队列成员被放进来。

Java提供的线程安全的`Queue`中非阻塞队列的典型例子就是`ConcurrentLinkedQueue`。

`ConcurrentLinkedQueue`是一个基于链表的无界非阻塞队列，并且是线程安全的，它采用的是先进先出的规则，当我们增加一个元素时，它会添加到队列的末尾，当我们取一个元素时，它会返回一个队列头部的元素。它采用CAS操作，来保证元素的一致性。

##### 2.5.8.4 `List`

`CopyOnWriteArrayList`，写时复制容器，即copy-on-write，在多线程环境下，写时效率低，读时效率高，适合写少读多的环境。

当我们往一个容器添加元素的时候，不直接往当前容器添加，而是先将当前容器进行Copy，复制出一个新的容器，然后向新的容器里添加元素，添加完元素之后，再将原容器的引用指向新的容器。这样做的好处是我们可以对CopyOnWrite容器进行并发的读，而不需要加锁，因为在当前读的容器中不会添加任何元素。所以CopyOnWrite容器是一种读写分离的思想，读和写对应不同的容器。

`CopyOnWriteArrayList`内部持有一个`ReentrantLock`，增删改都需要获得锁，并且锁只有一把，而读操作不需要获得锁，支持并发。

##### 2.5.8.5 `Set`

`CopyOnWriteArraySet`与`CopyOnWriteArrayList`同理，它基于`CopyOnWriteArrayList`实现，是线程安全的无序不可重复集合。

### 2.6 设计模式

#### 2.6.1 单例模式

单例模式的几种实现：

（1）饿汉式：线程安全，不能延时加载

```java
class Singleton {
    private static Singleton instance = new Singleton();
    private Singleton() {}
    public static Singleton getInstance() {
        return instance;
    }
}
```

（2）懒汉式I：线程不安全，可以延时加载

```java
class Singleton {
    private static Singleton instance;
    private Singleton() {}
    public static Singleton getInstance() {
        if (instance == null) {
            instance = new Singleton();
        }
        return instance;
    }
}
```

（3）懒汉式II：线程安全，效率较低

```java
class Singleton {
    private static Singleton instance;
    private Singleton() {}
    public static synchronized Singleton getInstance() {
        if (instance == null) {
            instance = new Singleton();
        }
        return instance;
    }
}
```

（4）懒汉式III：双重锁判断机制，不推荐，可能会出问题

```java
class Singleton {
    private volatile static Singleton instance;
    private Singleton() {}
    public static Singleton getInstance() {
        if (instance == null) {
            synchronized (Singleton.class) {
                if (instance == null) {
                    instance = new Singleton();
                }
            }
        }
        return instance;
    }
}
```

（5）静态内部类实现：线程安全，可以延时加载

```java
class Singleton {
    private static class SingletonInnerClass {
        private static final Singleton instance = new Singleton();
    }
    
    private Singleton() {}

    public static Singleton getInstance() {
        return SingletonInnerClass.instance;
    }
}
```

（6）枚举实现：线程安全，不能延时加载，可以防止反射和反序列化不一致

```java
enum Singleton {
    INSTANCE;
    public static Singleton getInstance() {
        return INSTANCE;
    }
}
```

#### 2.6.2 适配器模式

示例：

`Enumeration`是java中比较早提供的遍历稽核接口，`Iterator`是新的遍历接口。现在要使一个实现`Enumeration`的类可以通过`Iterator`进行遍历，这就需要适配器来完成。

步骤：

- 新增适配器类，这里要适配Enumeration，增加类`EnumerationAdapter`
- 适配器类实现目标接口，这里需要实现`Iterator`
- 适配器类持有被适配类的引用

```java
class EnumerationAdapter<T> implements Iterator<T> {

    private Enumeration<T> enumeration;

    public EnumerationAdapter(Enumeration<T> enumeration) {
        this.enumeration = enumeration;
    }

    @Override
    public boolean hasNext() {
        return enumeration.hasMoreElements();
    }

    @Override
    public T next() {
        return enumeration.nextElement();
    }

    @Override
    public void remove() {
        throw new UnsupportedOperationException();
    }
}
```

Java的IO流体系使用了适配器模式，例如将字节流转为字符流：

```java
File file = new File("hello.txt");   
FileInputStream in = new FileInputStream(file); 
InputStreamReader inReader = new InputStreamReader(in); // 将字节流适配为字符流
```

#### 2.6.3 装饰者模式

动态地给一个对象添加一些额外的职责。就增加功能来说，装饰器模式相比生成子类更为灵活。

Java的IO体系使用了该模式。

示例：

```java
File file = new File("hello.txt");   
FileInputStream in = new FileInputStream(file); 
InputStreamReader inReader = new InputStreamReader(in); 
BufferedReader bufReader = new BufferedReader(inReader); // 将字符流装饰为带有缓冲区的字符流
```

#### 2.6.4 观察者模式

又称为订阅-发布模式。

示例:

```java
// 被观察对象
class Subject {

    private int state;

    // 观察者列表
    private List<Observer> observerList;

    public Subject() {
        observerList = new ArrayList<>();
    }

    public int getState() {
        return state;
    }

    // 被观察对象状态改变，通知所有观察者
    public void setState(int state) {
        if (this.state != state) {
            this.state = state;
            notifyAllObservers();
        }
    }

    // 注册观察者
    public void attach(Observer o) {
        observerList.add(o);
    }

    // 解除观察者
    public void detach(Observer o) {
        observerList.remove(o);
    }

    // 通知所有观察者
    public void notifyAllObservers() {
        for (Observer observer : observerList) {
            observer.update();
        }
    }
}

interface Observer {
    public abstract void update();
}

// 观察者1
class BinaryObserver implements Observer {

    private Subject subject;

    public BinaryObserver(Subject subject) {
        this.subject = subject;
        subject.attach(this);
    }

    @Override
    public void update() {
        System.out.println("Binary String: " + Integer.toBinaryString(subject.getState()));
    }
}

// 观察者2
class OctalObserver implements Observer {

    private Subject subject;

    public OctalObserver(Subject subject) {
        this.subject = subject;
        subject.attach(this);
    }

    @Override
    public void update() {
        System.out.println("Octal String: " + Integer.toOctalString(subject.getState()));
    }
}

public class TestClass {

    @Test
    public void testCase() {
        Subject sub = new Subject();
        Observer binaryObserver = new BinaryObserver(sub);
        Observer octalObserver = new OctalObserver(sub);
        sub.setState(10);
        sub.detach(binaryObserver);
        sub.setState(11);
    }
}
/*
Binary String: 1010
Octal String: 12
Octal String: 13
*/
```

#### 2.6.5 原型模式

该模式用于创建重复的对象，同时又能保证性能。

简单来说当我们需要创建一个指定的对象时，我们刚好有一个这样的对象，但是又不能直接使用，那么此时就使用`clone()`方法来复制一个一摸一样的对象来使用。

原型模式的本质就是`clone()`，可以解决构建复杂对象的资源消耗问题，能再某些场景中提升构建对象的效率；还有一个重要的用途就是保护性拷贝，可以通过返回一个拷贝对象的形式，实现只读的限制。

#### 2.6.6 工厂模式

##### 2.6.6.1 简单工厂

在工厂模式中，我们在创建对象时不会对客户端暴露创建逻辑，而是通过使用一个共同的接口来指向新创建的对象。

示例:

```java
// 产品接口
interface Shape {
    void draw();
}

// 实体类
class Rectangle implements Shape {

    @Override
    public void draw() {
        System.out.println("Inside Rectangle::draw() method.");
    }
}

// 实体类
class Square implements Shape {

    @Override
    public void draw() {
        System.out.println("Inside Square::draw() method.");
    }
}

// 实体类
class Circle implements Shape {

    @Override
    public void draw() {
        System.out.println("Inside Circle::draw() method.");
    }
}

// 工厂类
class ShapeFactory {
    public static Shape getShape(String shapeType) {
        if (shapeType == null || shapeType.isEmpty()) {
            return null;
        }
        if (shapeType.equalsIgnoreCase("CIRCLE")) {
            return new Circle();
        } else if (shapeType.equalsIgnoreCase("RECTANGLE")) {
            return new Rectangle();
        } else if (shapeType.equalsIgnoreCase("SQUARE")) {
            return new Square();
        } else {
            return null;
        }
    }
}
```

##### 2.6.6.2 工厂方法

简单工厂模式的缺点在于如果需要生产新的产品，那么就需要修改工厂类的方法逻辑，这违背了开闭原则。

这个问题可以使用工厂方法模式解决。

示例:

```java
interface Shape {
    void draw();
}

class Rectangle implements Shape {

    @Override
    public void draw() {
        System.out.println("Inside Rectangle::draw() method.");
    }

}

class Square implements Shape {

    @Override
    public void draw() {
        System.out.println("Inside Square::draw() method.");
    }

}

class Circle implements Shape {

    @Override
    public void draw() {
        System.out.println("Inside Circle::draw() method.");
    }
}

interface Factory {
    Shape produce();
}

class RectangleFactory implements Factory {
    @Override
    public Shape produce() {
        return new Rectangle();
    }
}

class SquareFactory implements Factory {
    @Override
    public Shape produce() {
        return new Square();
    }
}

class CircleFactory implements Factory {
    @Override
    public Shape produce() {
        return new Circle();
    }
}

public class TestClass {

    @Test
    public void testCase() {
        Factory factory = new CircleFactory();
        factory.produce().draw(); // Inside Circle::draw() method.
    }
}
```

这样以来，如果我们有新的商品需要生产，例如`Triangle`，则我们只需要定义`Triangle`和`TriangleFactory`即可，无需更改原有代码。

##### 2.6.6.3 抽象工厂

抽象工厂模式是围绕一个超级工厂创建其他工厂。

示例:

```java
interface Shape {
    void draw();
}

class Rectangle implements Shape {

    @Override
    public void draw() {
        System.out.println("Inside Rectangle::draw() method.");
    }

}

class Square implements Shape {

    @Override
    public void draw() {
        System.out.println("Inside Square::draw() method.");
    }

}

class Circle implements Shape {

    @Override
    public void draw() {
        System.out.println("Inside Circle::draw() method.");
    }
}

interface Color {
    void fill();
}

class Red implements Color {
    @Override
    public void fill() {
        System.out.println("Inside Red::fill() method.");
    }
}

class Green implements Color {
    @Override
    public void fill() {
        System.out.println("Inside Green::fill() method.");
    }
}

class Blue implements Color {
    @Override
    public void fill() {
        System.out.println("Inside Blue::fill() method.");
    }
}

// 抽象工厂
interface Factory {
    Color getColor(String color);
    Shape getShape(String shape);
}

// 形状工厂
class ShapeFactory implements Factory {
    @Override
    public Shape getShape(String shapeType) {
        if (shapeType == null || shapeType.isEmpty()) {
            return null;
        }
        if (shapeType.equalsIgnoreCase("CIRCLE")) {
            return new Circle();
        } else if (shapeType.equalsIgnoreCase("RECTANGLE")) {
            return new Rectangle();
        } else if (shapeType.equalsIgnoreCase("SQUARE")) {
            return new Square();
        }
        return null;
    }

    @Override
    public Color getColor(String color) {
        return null;
    }
}

// 颜色工厂
class ColorFactory implements Factory {

    @Override
    public Shape getShape(String shapeType) {
        return null;
    }

    @Override
    public Color getColor(String color) {
        if (color == null || color.isEmpty()) {
            return null;
        }
        if (color.equalsIgnoreCase("RED")) {
            return new Red();
        } else if (color.equalsIgnoreCase("GREEN")) {
            return new Green();
        } else if (color.equalsIgnoreCase("BLUE")) {
            return new Blue();
        }
        return null;
    }
}

// 生成工厂
class FactoryProducer {
    public static Factory getFactory(String choice) {
        if (choice.equalsIgnoreCase("SHAPE")) {
            return new ShapeFactory();
        } else if (choice.equalsIgnoreCase("COLOR")) {
            return new ColorFactory();
        }
        return null;
    }
}
```

#### 2.6.7 享元模式

该模式主要用于减少创建对象的数量，以减少内存占用和提高性能。

在JAVA语言中，String类型就是使用了享元模式。String对象是final类型，对象一旦创建就不可改变。在JAVA中字符串常量都是存在常量池中的，Java会确保一个字符串常量在常量池中只有一个拷贝。

```java
abstract class Flyweight {
    public abstract void operation();
}

class ConcreteFlyweight extends Flyweight {

    private String str;

    public ConcreteFlyweight(String str) {
        this.str = str;
    }

    @Override
    public void operation() {
        System.out.println("Concrete---Flyweight: " + str);
    }

    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (o == null || getClass() != o.getClass()) return false;
        ConcreteFlyweight that = (ConcreteFlyweight) o;
        return Objects.equals(str, that.str);
    }

    @Override
    public int hashCode() {
        return Objects.hash(str);
    }
}

class FlyweightFactory {
    private Map<String, Flyweight> map = new HashMap<>();

    public Flyweight getFlyweight(String str) {
        Flyweight fw = map.get(str);
        if (fw == null) {
            fw = new ConcreteFlyweight(str);
            map.put(str, fw);
        }
        return fw;
    }

    public int getfwMapSize() {
        return map.size();
    }
}

public class TestClass {

    @Test
    public void testCase() {
        FlyweightFactory fwfactory = new FlyweightFactory();
        Flyweight fw1 = fwfactory.getFlyweight("Google");
        Flyweight fw2 = fwfactory.getFlyweight("Google");
        Flyweight fw3 = fwfactory.getFlyweight("Google");
        Flyweight fw4 = fwfactory.getFlyweight("Bing");
        Flyweight fw5 = fwfactory.getFlyweight("Bing");
        Flyweight fw6 = fwfactory.getFlyweight("Bing");
        Flyweight fw7 = fwfactory.getFlyweight("Bing");
        Flyweight fw8 = fwfactory.getFlyweight("Baidu");
        Flyweight fw9 = fwfactory.getFlyweight("Baidu");
        fw1.operation();
        fw2.operation();
        fw3.operation();
        fw4.operation();
        fw5.operation();
        fw6.operation();
        fw7.operation();
        fw8.operation();
        fw9.operation();
        System.out.println(fwfactory.getfwMapSize());
    }
}
/*
Concrete---Flyweight: Google
Concrete---Flyweight: Google
Concrete---Flyweight: Google
Concrete---Flyweight: Bing
Concrete---Flyweight: Bing
Concrete---Flyweight: Bing
Concrete---Flyweight: Bing
Concrete---Flyweight: Baidu
Concrete---Flyweight: Baidu
3
*/
```

可以看到，我们的9个对象背后实际上是3个对象在支持。

#### 2.6.8 模板方法模式

在该模式中，一个抽象类公开定义了执行它的方法的方式/模板。它的子类可以按需要重写方法实现，但调用将以抽象类中定义的方式进行。

示例：

```java
abstract class Game {
    abstract void init();
    abstract void start();
    abstract void end();

    // 模板
    public final void play() {
        init();
        start();
        end();
    }
}

class Cricket extends Game {

    @Override
    void init() {
        System.out.println("Cricket Game Initialized! Start playing.");
    }

    @Override
    void start() {
        System.out.println("Cricket Game Started. Enjoy the game!");
    }

    @Override
    void end() {
        System.out.println("Cricket Game Finished!");
    }
}

class Football extends Game {

    @Override
    void init() {
        System.out.println("Football Game Initialized! Start playing.");
    }

    @Override
    void start() {
        System.out.println("Football Game Started. Enjoy the game!");
    }

    @Override
    void end() {
        System.out.println("Football Game Finished!");
    }
}

public class TestClass {

    @Test
    public void testCase() {
        Game game = new Cricket();
        game.play();
        System.out.println();
        game = new Football();
        game.play();
    }
}

/*
Cricket Game Initialized! Start playing.
Cricket Game Started. Enjoy the game!
Cricket Game Finished!

Football Game Initialized! Start playing.
Football Game Started. Enjoy the game!
Football Game Finished!
*/
```

## 第三章 高级数据结构

### 3.1 树

#### 3.1.1 BST --- 二叉查找树

特点：

- 若任意节点的左子树不空，则左子树上所有节点的值均小于它的根节点的值。
- 任意节点的右子树不空，则右子树上所有节点的值均大于它的根节点的值。
- 任意节点的左、右子树也分别为二叉查找树。
- 没有值相等的节点。
- 查找所需的最大次数等同于树的高度。

性能：

- N次插入之后（最坏情况）：
    + 查找：N
    + 插入：N
- N次插入之后（平均情况）：
    + 查找：1.39lgN
    + 插入：1.39lgN

简单实现：

```java
public class BST<K extends Comparable<? super K>, V> {

    // 表示节点
    private static class Node<K, V> {
        private K key; // 键
        private V value; // 值
        private Node<K, V> left, right; // 左右子树链接
        private int N; // 以该节点为根的子树中的节点总数

        Node(K key, V value, int n) {
            this.key = key;
            this.value = value;
            this.N = n;
        }
    }

    private Node<K, V> root; // 该二叉查找树的根节点

    // 判断二叉查找树是否为空
    private boolean isEmpty() {
        return size() == 0;
    }

    // 返回二叉查找树的节点总数
    public int size() {
        return size(root);
    }

    // 返回以节点node为根的节点总数
    private int size(Node<K, V> node) {
        return node == null ? 0 : node.N;
    }

    // 判断树中是否包含key
    public boolean containsKey(K key) {
        if (key == null) {
            throw new IllegalArgumentException("Argument to containsKey() can not be null.");
        }
        return get(key) != null;
    }

    // 返回key对应的值
    public V get(K key) {
        if (key == null) {
            throw new IllegalArgumentException("Argument to get() is null.");
        }
        return get(root, key);
    }

    // 递归查找key对应的值
    private V get(Node<K, V> node, K key) {
        if (node == null) {
            return null;
        }
        int cmp = key.compareTo(node.key);
        if (cmp < 0) {
            return get(node.left, key);
        } else if (cmp > 0) {
            return get(node.right, key);
        } else {
            return node.value;
        }
    }

    // 向二叉查找树中插入键值对
    public void put(K key, V value) {
        if (key == null)
            throw new IllegalArgumentException("First argument to put() is null.");
        if (value == null) {
            delete(key);
            return;
        }
        root = put(root, key, value);
    }

    private Node<K, V> put(Node<K, V> node, K key, V value) {

        // 如果节点为null，则创建一个新节点
        if (node == null) {
            return new Node<>(key, value, 1);
        }

        int cmp = key.compareTo(node.key);
        if (cmp < 0) {
            node.left = put(node.left, key, value);
        } else if (cmp > 0) {
            node.right = put(node.right, key, value);
        } else {
            node.value = value;
        }

        // 更新N
        node.N = size(node.left) + size(node.right) + 1;
        return node;
    }

    // 返回最小的key
    public K min() {
        if (isEmpty()) {
            throw new NoSuchElementException("Calls min() with empty BST.");
        }
        return min(root).key;
    }

    private Node<K, V> min(Node<K, V> node) {
        return node.left == null ? node : min(node.left);
    }

    // 返回最大的Key
    public K max() {
        if (isEmpty()) {
            throw new NoSuchElementException("Calls max() with empty BST.");
        }
        return max(root).key;
    }

    private Node<K, V> max(Node<K, V> node) {
        return node.right == null ? node : max(node.right);
    }

    // 删除最小key对应的键值对
    public void deleteMin() {
        if (isEmpty()) {
            throw new NoSuchElementException("BST underflow.");
        }
        root = deleteMin(root);
    }

    private Node<K, V> deleteMin(Node<K, V> node) {
        if (node.left == null) {
            return node.right;
        }
        node.left = deleteMin(node.left);
        node.N = size(node.left) + size(node.right) + 1;
        return node;
    }

    // 删除最大key对应的键值对
    public void deleteMax() {
        if (isEmpty()) {
            throw new NoSuchElementException("BST underflow.");
        }
        root = deleteMax(root);
    }

    private Node<K, V> deleteMax(Node<K, V> node) {
        if (node.right == null) {
            return node.left;
        }
        node.right = deleteMax(node.right);
        node.N = size(node.left) + size(node.right) + 1;
        return node;
    }

    // 删除键值对
    public void delete(K key) {
        if (key == null) {
            throw new IllegalArgumentException("Calls delete() with a null key.");
        }
        if (!containsKey(key)) {
            return;
        }
        root = delete(root, key);
    }

    private Node<K, V> delete(Node<K, V> node, K key) {
        if (node == null) {
            return null;
        }
        int cmp = key.compareTo(node.key);
        if (cmp < 0) {
            node.left = delete(node.left, key);
        } else if (cmp > 0) {
            node.right = delete(node.right, key);
        } else {
            if (node.left == null) {
                return node.right;
            }
            if (node.right == null) {
                return node.left;
            }
            Node<K, V> t = node;
            node = min(t.right);
            node.right = deleteMin(t.right);
            node.left = t.left;
        }

        node.N = size(node.left) + size(node.right) + 1;
        return node;
    }

    // 返回树的高度
    public int height() {
        return height(root);
    }

    private int height(Node<K, V> node) {
        return node == null ? -1 : 1 + Math.max(height(node.left), height(node.right));
    }

    // 支持中序遍历
    public Iterable<K> keys() {
        Queue<K> queue = new LinkedList<>();
        inorder(root, queue);
        return queue;
    }

    private void inorder(Node<K, V> node, Queue<K> queue) {
        if (node == null) {
            return;
        }
        inorder(node.left, queue);
        queue.add(node.key);
        inorder(node.right, queue);
    }

    // 向下取整
    public K floor(K key) {
        if (key == null) {
            throw new IllegalArgumentException("Argument to floor() is null.");
        }

        if (isEmpty()) {
            throw new NoSuchElementException("Calls floor() with empty symbol table.");
        }

        Node<K, V> node = floor(root, key);
        return node == null ? null : node.key;

    }

    private Node<K, V> floor(Node<K, V> node, K key) {
        if (node == null) {
            return null;
        }
        int cmp = key.compareTo(node.key);
        if (cmp == 0) {
            return node;
        }
        if (cmp < 0) {
            return floor(node.left, key);
        }
        Node<K, V> t = floor(node.right, key);
        return t == null ? node : t;
    }

    // 向上取整
    public K ceiling(K key) {
        if (key == null) {
            throw new IllegalArgumentException("Argument to ceiling() is null.");
        }
        if (isEmpty()) {
            throw new NoSuchElementException("Calls ceiling() with empty symbol table.");
        }
        Node<K, V> node = ceiling(root, key);
        return node == null ? null : node.key;
    }

    private Node<K, V> ceiling(Node<K, V> node, K key) {
        if (node == null) {
            return null;
        }
        int cmp = key.compareTo(node.key);
        if (cmp == 0) {
            return node;
        }
        if (cmp > 0) {
            return ceiling(node.right, key);
        }
        Node<K, V> t = ceiling(node.left, key);
        return t == null ? node : t;
    }

    // 返回给定键的排名
    public int rank(K key) {
        if (key == null) {
            throw new IllegalArgumentException("Argument to rank() is null.");
        }
        return rank(root, key);
    }

    private int rank(Node<K, V> node, K key) {
        if (node == null) {
            return 0;
        }
        int cmp = key.compareTo(node.key);
        if (cmp < 0) {
            return rank(node.left, key);
        } else if (cmp > 0) {
            return 1 + size(node.left) + rank(node.right, key);
        } else {
            return size(node.left);
        }
    }

    // 返回排名第k的Key，0 ~ size() - 1
    public K select(int k) {
        if (k < 0 || k >= size()) {
            throw new IllegalArgumentException("Argument to select() is invalid: " + k + ".");
        }
        return select(root, k).key;
    }

    private Node<K, V> select(Node<K, V> node, int k) {
        if (node == null) {
            return null;
        }
        int t = size(node.left);
        if (t > k) {
            return select(node.left, k);
        } else if (t < k) {
            return select(node.right, k - t - 1);
        } else {
            return node;
        }
    }
}
```

#### 3.1.2 AVL --- 平衡二叉树

AVL树本质上是一颗二叉查找树，但是它又具有以下特点：它是一棵空树或它的左右两个子树的高度差的绝对值不超过1，并且左右两个子树都是一棵平衡二叉树。在AVL树中任何节点的两个子树的高度最大差别为一，所以它也被称为平衡二叉树。

AVL树保持平衡的原理在于每次插入、删除都会对树的结构进行再平衡。

简单实现如下：

```java
public class AVLTree<K extends Comparable<? super K>, V> {

    // 表示节点
    private static class Node<K, V> {
        private K key;
        private V value;
        private int height;
        private int size;
        private Node<K, V> left, right;

        public Node(K key, V value, int height, int size) {
            this.key = key; // 键
            this.value = value; // 值
            this.height = height; // 以该节点为根的树的高度
            this.size = size; // 以该节点为根的节点总数
        }
    }

    private Node<K, V> root; // 表示根节点

    // 判断树是否为空
    public boolean isEmpty() {
        return size() == 0;
    }

    // 返回树的节点总数
    public int size() {
        return size(root);
    }

    private int size(Node<K, V> node) {
        return node == null ? 0 : node.size;
    }

    // 返回树的高度
    public int height() {
        return height(root);
    }

    private int height(Node<K, V> node) {
        // 空树的高度记为-1
        return node == null ? -1 : node.height;
    }

    // 判断树中是否包含key
    public boolean containsKey(K key) {
        if (key == null) {
            throw new IllegalArgumentException("Argument to containsKey() can not be null.");
        }
        return get(key) != null;
    }

    // 返回key对应的值
    public V get(K key) {
        if (key == null) {
            throw new IllegalArgumentException("Argument to get() is null.");
        }
        return get(root, key);
    }

    private V get(Node<K, V> node, K key) {
        if (node == null) {
            return null;
        }
        int cmp = key.compareTo(node.key);
        if (cmp < 0) {
            return get(node.left, key);
        } else if (cmp > 0) {
            return get(node.right, key);
        } else {
            return node.value;
        }
    }

    // 插入键值对
    public void put(K key, V value) {
        if (key == null)
            throw new IllegalArgumentException("First argument to put() is null");
        if (value == null) {
            delete(key);
            return;
        }
        root = put(root, key, value);
    }

    private Node<K, V> put(Node<K, V> node, K key, V value) {
        if (node == null) {
            return new Node<>(key, value, 0, 1);
        }
        int cmp = key.compareTo(node.key);
        if (cmp < 0) {
            node.left = put(node.left, key, value);
        } else if (cmp > 0) {
            node.right = put(node.right, key, value);
        } else {
            node.value = value;
            return node;
        }

        node.size = 1 + size(node.left) + size(node.right);
        node.height = 1 + Math.max(height(node.left), height(node.right));
        return balance(node);
    }

    // 按照AVL树的条件平衡节点
    private Node<K, V> balance(Node<K, V> node) {
        if (balanceFactor(node) < -1) {
            if (balanceFactor(node.right) > 0) {
                node.right = rotateRight(node.right);
            }
            node = rotateLeft(node);
        } else if (balanceFactor(node) > 1) {
            if (balanceFactor(node.left) < 0) {
                node.left = rotateLeft(node.left);
            }
            node = rotateRight(node);
        }
        return node;
    }

/* 旋转示例
（1）左左情况，一次右旋转，对应代码:
balanceFactor(node) > 1
balanceFactor(node.left) > 0
node = rotateRight(node);

     (node)30                      20(y)
          / \                     / \
     (y)20  40    右旋转        10  30(node)
       / \        ----->       /   / \
     10  25                   5   25 40
    /
   5

(2) 右右情况，一次左旋转，对应代码：
balanceFactor(node) < -1
balanceFactor(node.right) < 0
node = rotateLeft(node);

      30(node)                  (y)40
     / \                          / \
   20  40(y)    左旋转     (node)30 45
      / \       ----->          / \  \
     35 45                     20 35 50
         \
         50

(3) 左右情况，一次左旋转，一次右旋转，对应代码：
balanceFactor(node) > 1
balanceFactor(node.left) < 0
node.left = rotateLeft(node.left);
node = rotateRight(node);

           30              (node)30                25(y)
          / \                   / \               / \
  (node)20  40   左旋转     (y)25 40   右旋转   20  30(node)
       / \       ----->       /        ----->  / \  \
     10  25(y)              20               10  23 40
        /                  / \
      23                 10  23

(4) 右左情况，一次右旋转，一次左旋转，对应代码：
balanceFactor(node) < -1
balanceFactor(node.right) > 0
node.right = rotateRight(node.right);
node = rotateLeft(node);

      30                        30(node)                 35(y)
     / \                       / \                       / \
   20  40(node)    右旋转     20 35(y)   左旋转   (node)30 40
      / \          ----->         \      ----->        /  / \
  (y)35 45                        40                 20  37 45
      \                          / \
      37                       37  45
 */

    // 左旋转node
    private Node<K, V> rotateLeft(Node<K, V> node) {
        Node<K, V> y = node.right;
        node.right = y.left;
        y.left = node;
        y.size = node.size;
        node.size = 1 + size(node.left) + size(node.right);
        node.height = 1 + Math.max(height(node.left), height(node.right));
        y.height = 1 + Math.max(height(y.left), height(y.right));
        return y;
    }

    // 右旋转node
    private Node<K, V> rotateRight(Node<K, V> node) {
        Node<K, V> y = node.left;
        node.left = y.right;
        y.right = node;
        y.size = node.size;
        node.size = 1 + size(node.left) + size(node.right);
        node.height = 1 + Math.max(height(node.left), height(node.right));
        y.height = 1 + Math.max(height(y.left), height(y.right));
        return y;
    }

    // 返回node的平衡因子
    private int balanceFactor(Node<K, V> node) {
        return height(node.left) - height(node.right);
    }

    // 返回最小的key
    public K min() {
        if (isEmpty()) {
            throw new NoSuchElementException("Calls min() with empty BST.");
        }
        return min(root).key;
    }

    private Node<K, V> min(Node<K, V> node) {
        return node.left == null ? node : min(node.left);
    }

    // 返回最大的Key
    public K max() {
        if (isEmpty()) {
            throw new NoSuchElementException("Calls max() with empty BST.");
        }
        return max(root).key;
    }

    private Node<K, V> max(Node<K, V> node) {
        return node.right == null ? node : max(node.right);
    }

    // 删除最小key对应的键值对
    public void deleteMin() {
        if (isEmpty()) {
            throw new NoSuchElementException("BST underflow.");
        }
        root = deleteMin(root);
    }

    private Node<K, V> deleteMin(Node<K, V> node) {
        if (node.left == null) {
            return node.right;
        }
        node.left = deleteMin(node.left);
        node.size = 1 + size(node.left) + size(node.right);
        node.height = 1 + Math.max(height(node.left), height(node.right));
        return balance(node);
    }

    // 删除最大key对应的键值对
    public void deleteMax() {
        if (isEmpty()) {
            throw new NoSuchElementException("BST underflow.");
        }
        root = deleteMax(root);
    }

    private Node<K, V> deleteMax(Node<K, V> node) {
        if (node.right == null) {
            return node.left;
        }
        node.right = deleteMax(node.right);
        node.size = 1 + size(node.left) + size(node.right);
        node.height = 1 + Math.max(height(node.left), height(node.right));
        return balance(node);
    }

    // 删除键值对
    public void delete(K key) {
        if (key == null) {
            throw new IllegalArgumentException("Calls delete() with a null key.");
        }
        if (!containsKey(key)) {
            return;
        }
        root = delete(root, key);
    }

    private Node<K, V> delete(Node<K, V> node, K key) {
        if (node == null) {
            return null;
        }
        int cmp = key.compareTo(node.key);
        if (cmp < 0) {
            node.left = delete(node.left, key);
        } else if (cmp > 0) {
            node.right = delete(node.right, key);
        } else {
            if (node.left == null) {
                return node.right;
            } else if (node.right == null) {
                return node.left;
            } else {
                Node<K, V> t = node;
                node = min(t.right);
                node.right = deleteMin(t.right);
                node.left = t.left;
            }
        }
        node.size = 1 + size(node.left) + size(node.right);
        node.height = 1 + Math.max(height(node.left), height(node.right));
        return balance(node);
    }

    // 向下取整
    public K floor(K key) {
        if (key == null) {
            throw new IllegalArgumentException("Argument to floor() is null.");
        }

        if (isEmpty()) {
            throw new NoSuchElementException("Calls floor() with empty symbol table.");
        }

        Node<K, V> node = floor(root, key);
        return node == null ? null : node.key;

    }

    private Node<K, V> floor(Node<K, V> node, K key) {
        if (node == null) {
            return null;
        }
        int cmp = key.compareTo(node.key);
        if (cmp == 0) {
            return node;
        }
        if (cmp < 0) {
            return floor(node.left, key);
        }
        Node<K, V> t = floor(node.right, key);
        return t == null ? node : t;
    }

    // 向上取整
    public K ceiling(K key) {
        if (key == null) {
            throw new IllegalArgumentException("Argument to ceiling() is null.");
        }
        if (isEmpty()) {
            throw new NoSuchElementException("Calls ceiling() with empty symbol table.");
        }
        Node<K, V> node = ceiling(root, key);
        return node == null ? null : node.key;
    }

    private Node<K, V> ceiling(Node<K, V> node, K key) {
        if (node == null) {
            return null;
        }
        int cmp = key.compareTo(node.key);
        if (cmp == 0) {
            return node;
        }
        if (cmp > 0) {
            return ceiling(node.right, key);
        }
        Node<K, V> t = ceiling(node.left, key);
        return t == null ? node : t;
    }

    // 返回给定键的排名
    public int rank(K key) {
        if (key == null) {
            throw new IllegalArgumentException("Argument to rank() is null.");
        }
        return rank(root, key);
    }

    private int rank(Node<K, V> node, K key) {
        if (node == null) {
            return 0;
        }
        int cmp = key.compareTo(node.key);
        if (cmp < 0) {
            return rank(node.left, key);
        } else if (cmp > 0) {
            return 1 + size(node.left) + rank(node.right, key);
        } else {
            return size(node.left);
        }
    }

    // 返回排名第k的Key，0 ~ size() - 1
    public K select(int k) {
        if (k < 0 || k >= size()) {
            throw new IllegalArgumentException("Argument to select() is invalid: " + k + ".");
        }
        return select(root, k).key;
    }

    private Node<K, V> select(Node<K, V> node, int k) {
        if (node == null) {
            return null;
        }
        int t = size(node.left);
        if (t > k) {
            return select(node.left, k);
        } else if (t < k) {
            return select(node.right, k - t - 1);
        } else {
            return node;
        }
    }

    // 支持中序遍历
    public Iterable<K> keys() {
        Queue<K> queue = new LinkedList<>();
        inorder(root, queue);
        return queue;
    }

    private void inorder(Node<K, V> node, Queue<K> queue) {
        if (node == null) {
            return;
        }
        inorder(node.left, queue);
        queue.add(node.key);
        inorder(node.right, queue);
    }
}
```

#### 3.1.3 RBT --- 红黑树

特点：

- 红链接均为左链接。
- 没有任何一个节点同时和两条红链接相连。
- 该树是完美黑色平衡，即任意空链接到根节点的路径上黑链接的数量相同。

性能：

- N次插入之后（最坏情况）：
    + 查找：2lgN
    + 插入：2lgN
- N次插入之后（平均情况）：
    + 查找：1.00lgN
    + 插入：1.00lgN

一棵有n个内节点(不包括NIL)的红黑树高度之多为2lg(n+1)。

简单实现：

```java
public class RedBlackTree<K extends Comparable<? super K>, V> {

    // 表示节点
    private static class Node<K, V> {
        private K key; // 键
        private V value; // 值
        private Node<K, V> left, right; // 左右链接
        private boolean color; // 父节点的指向这个节点的链接的颜色
        private int size; // 以该节点为根的节点总数

        public Node(K key, V value, boolean color, int size) {
            this.key = key;
            this.value = value;
            this.color = color;
            this.size = size;
        }
    }

    private static final boolean RED = true;
    private static final boolean BLACK = false;
    private Node<K, V> root; // 根节点

    // 存入键值对
    public void put(K key, V value) {
        if (key == null) {
            throw new IllegalArgumentException("First argument to put() is null");
        }
        if (value == null) {
            delete(key);
            return;
        }
        root = put(root, key, value);
        root.color = BLACK;
    }

    private Node<K, V> put(Node<K, V> node, K key, V value) {
        if (node == null) {
            return new Node<>(key, value, RED, 1);
        }

        int cmp = key.compareTo(node.key);
        if (cmp < 0) {
            node.left = put(node.left, key, value);
        } else if (cmp > 0) {
            node.right = put(node.right, key, value);
        } else {
            node.value = value;
        }

        // 如果右链接是红链接，左链接是黑链接，进行左旋转
        if (isRed(node.right) && !isRed(node.left)) {
            node = rotateLeft(node);
        }

        // 如果左连接是红色的，且左节点的左连接也是红色的，进行右旋转
        if (isRed(node.left) && isRed(node.left.left)) {
            node = rotateRight(node);
        }

        // 如果左右连接均为红色，进行颜色转换
        if (isRed(node.left) && isRed(node.right)) {
            flipColors(node);
        }

        node.size = 1 + size(node.left) + size(node.right);

        return node;
    }

    // 反转指向node和node左右链接的颜色
    private void flipColors(Node<K, V> node) {
        node.color = !node.color;
        node.left.color = !node.left.color;
        node.right.color = !node.right.color;
    }

    // 对node进行右旋转
    private Node<K, V> rotateRight(Node<K, V> node) {
        Node<K, V> x = node.left;
        node.left = x.right;
        x.right = node;
        x.color = x.right.color;
        x.right.color = RED;
        x.size = node.size;
        node.size = size(node.left) + size(node.right) + 1;
        return x;

    }

    // 对node进行左旋转
    private Node<K, V> rotateLeft(Node<K, V> node) {
        Node<K, V> x = node.right;
        node.right = x.left;
        x.left = node;
        x.color = x.left.color;
        x.left.color = RED;
        x.size = node.size;
        node.size = size(node.left) + size(node.right) + 1;
        return x;
    }

    // 判断指向节点node的颜色
    private boolean isRed(Node<K, V> node) {
        return node == null ? false : node.color;
    }

    // 删除键值对
    public void delete(K key) {
        if (key == null) {
            throw new IllegalArgumentException("Calls delete() with a null key.");
        }

        if (!containsKey(key)) {
            return;
        }

        // 如果根节点的两个链接都是黑色，则设置指向根节点的链接为红色
        if (!isRed(root.left) && !isRed(root.right)) {
            root.color = RED;
        }

        root = delete(root, key);

        if (isEmpty()) {
            root.color = BLACK;
        }
    }

    private Node<K, V> delete(Node<K, V> node, K key) {

        if (key.compareTo(node.key) < 0) {
            if (!isRed(node.left) && !isRed(node.left.left)) {
                node = moveRedLeft(node);
            }
            node.left = delete(node.left, key);
        } else {
            if (isRed(node.left)) {
                node = rotateRight(node);
            }
            if (key.compareTo(node.key) == 0 && (node.right == null)) {
                return null;
            }
            if (!isRed(node.right) && !isRed(node.right.left)) {
                node = moveRedRight(node);
            }
            if (key.compareTo(node.key) == 0) {
                Node<K, V> x = min(node.right);
                node.key = x.key;
                node.value = x.value;
                node.right = deleteMin(node.right);
            } else {
                node.right = delete(node.right, key);
            }
        }
        return balance(node);

    }

    private Node<K, V> moveRedLeft(Node<K, V> node) {

        flipColors(node);
        if (isRed(node.right.left)) {
            node.right = rotateRight(node.right);
            node = rotateLeft(node);
            flipColors(node);
        }
        return node;
    }

    private Node<K, V> moveRedRight(Node<K, V> node) {
        flipColors(node);
        if (isRed(node.left.left)) {
            node = rotateRight(node);
            flipColors(node);
        }
        return node;
    }

    // 删除最小键
    public void deleteMin() {
        if (isEmpty()) {
            throw new NoSuchElementException("BST underflow");
        }

        if (!isRed(root.left) && !isRed(root.right)) {
            root.color = RED;
        }

        root = deleteMin(root);

        if (!isEmpty()) {
            root.color = BLACK;
        }
    }

    private Node<K, V> deleteMin(Node<K, V> node) {
        if (node.left == null) {
            return null;
        }

        if (!isRed(node.left) && !isRed(node.left.left)) {
            node = moveRedLeft(node);
        }

        node.left = deleteMin(node.left);
        return balance(node);
    }

    // 删除最大键
    public void deleteMax() {
        if (isEmpty()) {
            throw new NoSuchElementException("BST underflow");
        }

        if (!isRed(root.left) && !isRed(root.right)) {
            root.color = RED;
        }

        root = deleteMax(root);

        if (!isEmpty()) {
            root.color = BLACK;
        }
    }

    private Node<K, V> deleteMax(Node<K, V> node) {
        if (isRed(node.left)) {
            node = rotateRight(node);
        }

        if (node.right == null) {
            return null;
        }

        if (!isRed(node.right) && !isRed(node.right.left)) {
            node = moveRedRight(node);
        }

        node.right = deleteMax(node.right);

        return balance(node);
    }

    private Node<K, V> balance(Node<K, V> node) {

        if (isRed(node.right)) {
            node = rotateLeft(node);
        }
        if (isRed(node.left) && isRed(node.left.left)) {
            node = rotateRight(node);
        }
        if (isRed(node.left) && isRed(node.right)) {
            flipColors(node);
        }

        node.size = size(node.left) + size(node.right) + 1;

        return node;
    }

    /*****************************************************************
     *********** 以下方法不涉及节点的颜色，与二叉查找树实现相同*******
     ****************************************************************/

    // 返回节点总数
    public int size() {
        return size(root);
    }

    private int size(Node<K, V> node) {
        return node == null ? 0 : node.size;
    }

    // 判断红黑树是否为空树
    public boolean isEmpty() {
        return root == null;
    }

    // 判断树中是否包含key
    public boolean containsKey(K key) {
        if (key == null) {
            throw new IllegalArgumentException("Argument to containsKey() can not be null.");
        }
        return get(key) != null;
    }

    // 返回key对应的值
    public V get(K key) {
        if (key == null) {
            throw new IllegalArgumentException("Argument to get() is null.");
        }
        return get(root, key);
    }

    private V get(Node<K, V> node, K key) {
        if (node == null) {
            return null;
        }
        int cmp = key.compareTo(node.key);
        if (cmp < 0) {
            return get(node.left, key);
        } else if (cmp > 0) {
            return get(node.right, key);
        } else {
            return node.value;
        }
    }

    // 返回最小的key
    public K min() {
        if (isEmpty()) {
            throw new NoSuchElementException("Calls min() with empty BST.");
        }
        return min(root).key;
    }

    private Node<K, V> min(Node<K, V> node) {
        return node.left == null ? node : min(node.left);
    }

    // 返回最大的Key
    public K max() {
        if (isEmpty()) {
            throw new NoSuchElementException("Calls max() with empty BST.");
        }
        return max(root).key;
    }

    private Node<K, V> max(Node<K, V> node) {
        return node.right == null ? node : max(node.right);
    }

    // 向下取整
    public K floor(K key) {
        if (key == null) {
            throw new IllegalArgumentException("Argument to floor() is null.");
        }

        if (isEmpty()) {
            throw new NoSuchElementException("Calls floor() with empty symbol table.");
        }

        Node<K, V> node = floor(root, key);
        return node == null ? null : node.key;

    }

    private Node<K, V> floor(Node<K, V> node, K key) {
        if (node == null) {
            return null;
        }
        int cmp = key.compareTo(node.key);
        if (cmp == 0) {
            return node;
        }
        if (cmp < 0) {
            return floor(node.left, key);
        }
        Node<K, V> t = floor(node.right, key);
        return t == null ? node : t;
    }

    // 向上取整
    public K ceiling(K key) {
        if (key == null) {
            throw new IllegalArgumentException("Argument to ceiling() is null.");
        }
        if (isEmpty()) {
            throw new NoSuchElementException("Calls ceiling() with empty symbol table.");
        }
        Node<K, V> node = ceiling(root, key);
        return node == null ? null : node.key;
    }

    private Node<K, V> ceiling(Node<K, V> node, K key) {
        if (node == null) {
            return null;
        }
        int cmp = key.compareTo(node.key);
        if (cmp == 0) {
            return node;
        }
        if (cmp > 0) {
            return ceiling(node.right, key);
        }
        Node<K, V> t = ceiling(node.left, key);
        return t == null ? node : t;
    }

    // 返回给定键的排名
    public int rank(K key) {
        if (key == null) {
            throw new IllegalArgumentException("Argument to rank() is null.");
        }
        return rank(root, key);
    }

    private int rank(Node<K, V> node, K key) {
        if (node == null) {
            return 0;
        }
        int cmp = key.compareTo(node.key);
        if (cmp < 0) {
            return rank(node.left, key);
        } else if (cmp > 0) {
            return 1 + size(node.left) + rank(node.right, key);
        } else {
            return size(node.left);
        }
    }

    // 返回排名第k的Key，0 ~ size() - 1
    public K select(int k) {
        if (k < 0 || k >= size()) {
            throw new IllegalArgumentException("Argument to select() is invalid: " + k + ".");
        }
        return select(root, k).key;
    }

    private Node<K, V> select(Node<K, V> node, int k) {
        if (node == null) {
            return null;
        }
        int t = size(node.left);
        if (t > k) {
            return select(node.left, k);
        } else if (t < k) {
            return select(node.right, k - t - 1);
        } else {
            return node;
        }
    }

    // 支持中序遍历
    public Iterable<K> keys() {
        Queue<K> queue = new LinkedList<>();
        inorder(root, queue);
        return queue;
    }

    private void inorder(Node<K, V> node, Queue<K> queue) {
        if (node == null) {
            return;
        }
        inorder(node.left, queue);
        queue.add(node.key);
        inorder(node.right, queue);
    }
}
```

#### 3.1.4 Trie --- 字典树

基本性质：

- 根节点不包含字符，除根节点外的每一个子节点都包含一个字符。
- 从根节点到某一节点。路径上经过的字符连接起来，就是该节点对应的字符串。
- 每个节点的所有子节点包含的字符都不相同。

优点：利用字符串的公共前缀来减少查询时间，最大限度的减少无谓的字符串比较，查询效率比哈希树高。

简单实现:

```java
// 26向单词查找树，key为小写字母组成的字符串
public class Trie<Value> {

    private static final int R = 26; // lowercase
    private Node<Value> root; // 根节点
    private int n; // 树中键的数量

    @SuppressWarnings("unchecked")
    private static class Node<Value> {
        private Value value;
        private Node<Value>[] next = new Node[R];
    }

    // 返回key对应的值
    public Value get(String key) {
        if (key == null) {
            throw new IllegalArgumentException("Argument to get() is null.");
        }
        Node<Value> x = get(root, key, 0);
        return x == null ? null : x.value;
    }

    private Node<Value> get(Node<Value> x, String key, int d) {
        if (x == null) {
            return null;
        }
        if (d == key.length()) {
            return x;
        }
        int idx = key.charAt(d) - 'a';
        return get(x.next[idx], key, d + 1);
    }

    // 判断是否包含key
    public boolean containsKey(String key) {
        if (key == null) {
            throw new IllegalArgumentException("Argument to containsKey() is null.");
        }
        return get(key) != null;
    }

    // 插入键值对，如果value为null，则删除对应的key
    public void put(String key, Value value) {
        if (key == null) {
            throw new IllegalArgumentException("First argument to put() is null.");
        }
        if (value == null) {
            delete(key);
        } else {
            root = put(root, key, value, 0);
        }
    }

    private Node<Value> put(Node<Value> x, String key, Value value, int d) {
        if (x == null) {
            x = new Node<>();
        }
        if (d == key.length()) {
            if (x.value == null) {
                n++;
            }
            x.value = value;
            return x;
        }
        int idx = key.charAt(d) - 'a';
        x.next[idx] = put(x.next[idx], key, value, d + 1);
        return x;
    }

    // 删除指定的key
    public void delete(String key) {
        if (key == null) {
            throw new IllegalArgumentException("Argument to delete() is null.");
        }
        root = delete(root, key, 0);
    }

    private Node<Value> delete(Node<Value> x, String key, int d) {
        if (x == null) {
            return null;
        }
        if (d == key.length()) {
            if (x.value != null) {
                n--;
            }
            x.value = null;
        } else {
            int idx = key.charAt(d) - 'a';
            x.next[idx] = delete(x.next[idx], key, d + 1);
        }

        // remove subtrie rooted at x if it is completely empty
        if (x.value != null) {
            return x;
        }

        for (int c = 0; c < R; c++) {
            if (x.next[c] != null) {
                return x;
            }
        }
        return null;
    }

    // 返回Trie中键值对的数量
    public int size() {
        return n;
    }

    // 判断符号表是否为空
    public boolean isEmpty() {
        return size() == 0;
    }
}
```

#### 3.1.5 BTree --- B树与B+树

##### 3.1.5.1 B树

B树是对二叉查找树的改进。它的设计思想是，将相关数据尽量集中在一起，以便一次读取多个数据，减少硬盘操作次数。B树为系统最优化大块数据的读和写操作。B树算法减少定位记录时所经历的中间过程，从而加快存取速度。普遍运用在数据库和文件系统。

B树的每个节点最多包含M个孩子，M称为b树的阶。

一个M阶B树具有如下特征：

- 定义任意非叶子节点最多只有M个儿子，且M>2。
- 根节点的儿子数为[2, M]。
- 除根节点以外的非叶子节点的儿子数为[M/2, M]，向上取整。
- 非叶子节点的关键字个数=儿子数-1。
- 所有叶子节点位于同一层。
- k个关键字把节点拆成k+1段，分别指向k+1个儿子，同时满足查找树的大小关系。

B树区别于B+树的特征：

- 关键字分布在整棵树中。
- 任何一个关键字出现且只出现在一个节点中。
- 搜索有可能在非叶子节点结束。
- 其搜索性能等价于在关键字全集内做一次二分查找。

B树与磁盘IO：

磁盘IO相对于内存来说是很慢的。数据库索引是存储在磁盘上的，当数据量大时，就不能把整个索引全部加载到内存了，只能逐一加载每一个磁盘页（对应索引树的节点）。所以我们要减少IO次数，对于树来说，IO次数就是树的高度，而“矮胖”就是b树的特征之一。B树在查询时的比较次数并不比二叉树少，尤其是节点中的数非常多时，但是内存中的比较速度是非常快的，耗时可以忽略，所以只要树的高度低，IO少，就可以提高查询性能，这是B树的优势之一。

##### 3.1.5.2 B+树

B+树是B树的一种变体，查询性能更好。

M阶B+树的特征如下：

- 有n棵子树的非叶子节点中含有n个关键字（B树是n-1个），这些关键字不保存数据，只用来索引，所有数据都保存在叶子节点（B树是每个关键字都保存数据）。
- 所有叶子节点种包含了全部关键字的信息，以及指向含这些关键字记录的指针，并且叶子节点本身依据关键字的大小自小而大的顺序链接（有序链表）。
- 通常在B+树上有两个头指针，一个指向根节点，一个指向关键字最小的叶子节点。
- 同一个关键字会在不同节点中重复出现，根节点的最大元素就是该B+树的最大元素。
- 所有的非叶子结点可以看成是索引部分。

B+树相比于B树的查询优势：

- B+树的中间节点不保存数据，所以磁盘页能容纳更多节点元素。
- B+树查询必须查找到叶子节点，B树只要匹配到即可不用管元素位置，因此B+树查找更稳定。
- 对于范围查找来说，B+树只需遍历叶子节点链表即可，B树却需要重复地中序遍历。

### 3.2 图

// TODO

#### 3.1 无向图

#### 3.2 有向图

### 3.3 堆

// TODO

## 第四章 基础算法

### 4.1 排序

排序算法复杂度总结：

![排序总结](images\排序总结.png)

- n： 数据规模
- k：“桶”的个数
- In-place： 占用常数内存，不占用额外内存
- Out-place： 占用额外内存

#### 4.1.1 冒泡排序

思想：

- 比较相邻的元素。如果第一个比第二个大，就交换他们两个。
- 对每一对相邻元素做同样的工作，从开始第一对到结尾的最后一对。在这一点，最后的元素应该会是最大的数。
- 针对所有的元素重复以上的步骤，除了最后一个。
- 持续每次对越来越少的元素重复上面的步骤，直到没有任何一对数字需要比较。

实现：

```java
public class Bubble {
    public static <T extends Comparable<? super T>> void sort(T[] arr) {

        if (arr == null || arr.length < 2) {
            return;
        }

        for (int i = 0; i < arr.length - 1; i++) {
            for (int j = 0; j < arr.length - 1 - i; j++) {
                if (less(arr[j + 1], arr[j])) {
                    swap(arr, j + 1, j);
                }
            }
        }
    }

    private static <T> void swap(T[] arr, int i, int j) {
        T tmp = arr[i];
        arr[i] = arr[j];
        arr[j] = tmp;
    }

    private static <T extends Comparable<? super T>> boolean less(T v, T w) {
        return v.compareTo(w) < 0;
    }
}
```

#### 4.1.2 选择排序

思想：

第一次从待排序的数据元素中选出最小（或最大）的一个元素，存放在序列的起始位置，然后再从剩余的未排序元素中寻找到最小（大）元素，然后放到已排序的序列的末尾。以此类推，直到全部待排序的数据元素的个数为零。

实现：

```java
public class Selection {
    public static <T extends Comparable<? super T>> void sort(T[] arr) {

        if (arr == null || arr.length < 2) {
            return;
        }

        for (int i = 0; i < arr.length; i++) {
            int min = i;
            for (int j = i + 1; j < arr.length; j++) {
                if (less(arr[j], arr[min])) {
                    min = j;
                }
            }
            swap(arr, min, i);
        }
    }

    private static <T> void swap(T[] arr, int i, int j) {
        T tmp = arr[i];
        arr[i] = arr[j];
        arr[j] = tmp;
    }

    private static <T extends Comparable<? super T>> boolean less(T v, T w) {
        return v.compareTo(w) < 0;
    }
}
```

#### 4.1.3 插入排序

思想：

把n个待排序的元素看成为一个有序表和一个无序表。开始时有序表中只包含1个元素，无序表中包含有n-1个元素，排序过程中每次从无序表中取出第一个元素，将它插入到有序表中的适当位置，使之成为新的有序表，重复n-1次可完成排序过程。

实现：

```java
public class Insertion {
    public static <T extends Comparable<? super T>> void sort(T[] arr) {

        if (arr == null || arr.length < 2) {
            return;
        }

        for (int i = 1; i < arr.length; i++) {
            for (int j = i; j > 0 && less(arr[j], arr[j - 1]); j--) {
                swap(arr, j, j - 1);
            }
        }
    }

    private static <T> void swap(T[] arr, int i, int j) {
        T tmp = arr[i];
        arr[i] = arr[j];
        arr[j] = tmp;
    }

    private static <T extends Comparable<? super T>> boolean less(T v, T w) {
        return v.compareTo(w) < 0;
    }
}
```

#### 4.1.4 希尔排序

希尔排序也是一种插入排序，它是简单插入排序经过改进之后的一个更高效的版本，也称为缩小增量排序，同时该算法是冲破$O(n^2)$的第一批算法之一。它与插入排序的不同之处在于，它会优先比较距离较远的元素。

实现：

```java
public class Shell {

    public static <T extends Comparable<? super T>> void sort(T[] arr) {

        if (arr == null || arr.length < 2) {
            return;
        }

        int h = 1;
        while (h < arr.length / 3) {
            h = 3 * h + 1;
        }
        while (h >= 1) {
            for (int i = h; i < arr.length; i++) {
                for (int j = i; j >= h && less(arr[j], arr[j - h]); j -= h) {
                    swap(arr, j, j - h);
                }
            }
            h /= 3;
        }
    }

    private static <T> void swap(T[] arr, int i, int j) {
        T tmp = arr[i];
        arr[i] = arr[j];
        arr[j] = tmp;
    }

    private static <T extends Comparable<? super T>> boolean less(T v, T w) {
        return v.compareTo(w) < 0;
    }
}
```

#### 4.1.5 归并排序

思路：

是利用递归与分治的技术将数据序列划分为越来越小的半子表,再对半子表排序,最后再用递归方法将排好序的半子表合并成越来越大的有序序列。

实现：

```java
public class Merge {
    
    public static <T extends Comparable<? super T>> void sort(T[] arr) {

        if (arr == null || arr.length < 2) {
            return;
        }

        T[] aux = (T[]) Array.newInstance(arr.getClass().getComponentType(), arr.length);
        sort(arr, aux, 0, arr.length - 1);
    }

    private static <T extends Comparable<? super T>> void sort(T[] arr, T[] aux, int lo, int hi) {
        if (lo >= hi) {
            return;
        }
        int mid = lo + (hi - lo >>> 1);
        sort(arr, aux, lo, mid);
        sort(arr, aux, mid + 1, hi);
        merge(arr, aux, lo, mid, hi);
    }

    private static <T extends Comparable<? super T>> void merge(T[] arr, T[] aux, int lo, int mid, int hi) {

        int i = lo, j = mid + 1;

        System.arraycopy(arr, lo, aux, lo, hi - lo + 1);
        
        for (int k = lo; k <= hi; k++) {
            if (i > mid) {
                arr[k] = aux[j++];
            } else if (j > hi) {
                arr[k] = aux[i++];
            } else if (less(aux[j], aux[i])) {
                arr[k] = aux[j++];
            } else {
                arr[k] = aux[i++];
            }
        }
    }

    private static <T extends Comparable<? super T>> boolean less(T v, T w) {
        return v.compareTo(w) < 0;
    }
}
```

#### 4.1.6 快速排序

思想：

通过一趟排序将要排序的数据分割成独立的两部分，其中一部分的所有数据都比另外一部分的所有数据都要小，然后再按此方法对这两部分数据分别进行快速排序，整个排序过程可以递归进行，以此达到整个数据变成有序序列。

实现：

```java
public class Quick {
    public static <T extends Comparable<? super T>> void sort(T[] arr) {

        if (arr == null || arr.length < 2) {
            return;
        }
        sort(arr, 0, arr.length - 1);
    }

    private static <T extends Comparable<? super T>> void sort(T[] arr, int lo, int hi) {
        if (lo >= hi) {
            return;
        }
        int pivot = partition(arr, lo, hi);
        sort(arr, lo, pivot - 1);
        sort(arr, pivot + 1, hi);
    }

    private static <T extends Comparable<? super T>> int partition(T[] arr, int lo, int hi) {
        int i = lo, j = hi + 1; // 左右扫描指针
        T v = arr[lo]; // 切分元素
        while (true) {

            // 从数组左端开始向右扫描，直到找到一个>=切分元素的元素
            while (less(arr[++i], v)) {
                if (i == hi) {
                    break;
                }
            }

            // 从数组右端开始向右扫描，直到找到一个<=切分元素的元素
            while(less(v, arr[--j])) {
                if (j == lo) {
                    break;
                }
            }

            // 指针相遇，退出循环
            if (i >= j) {
                break;
            }

            // 交换位置
            swap(arr, i, j);
        }
        swap(arr, lo, j); // 将v放入到正确的位置
        return j; // 此时arr[lo...j-1] <= arr[j] <= arr[j+1...hi]
    }

    private static <T> void swap(T[] arr, int i, int j) {
        T tmp = arr[i];
        arr[i] = arr[j];
        arr[j] = tmp;
    }

    private static <T extends Comparable<? super T>> boolean less(T v, T w) {
        return v.compareTo(w) < 0;
    }
}
```

#### 4.1.7 堆排序

堆排序是利用堆这种数据结构而设计的一种排序算法。

大顶堆：arr[i] >= arr[2i+1] && arr[i] >= arr[2i+2]
小顶堆：arr[i] <= arr[2i+1] && arr[i] <= arr[2i+2] 

思想：

建立大顶堆，将堆顶元素与末尾元素进行交换，使末尾元素最大。然后继续调整堆，再将堆顶元素与末尾元素交换，得到第二大元素。如此反复进行交换、重建、交换。

```java
public class Heap {

    public static <T extends Comparable<? super T>> void sort(T[] arr) {
        if (arr == null || arr.length < 2) {
            return;
        }

        for (int i = 0; i < arr.length; i++) {
            // 建堆
            createHeap(arr, arr.length - i - 1);
            swap(arr, 0, arr.length - i - 1);
        }
    }

    private static <T extends Comparable<? super T>> void createHeap(T[] arr, int hi) {
        for (int i = arr.length / 2 - 1; i >= 0; i--) {
            int t = i, b = 2 * i + 1;
            while (b <= hi) {
                if (b < hi) {
                    if (less(arr[b], arr[b + 1])) {
                        b++;
                    }
                }
                if (less(arr[t], arr[b])) {
                    swap(arr, t, b);
                    t = b;
                } else {
                    break;
                }
            }
        }
    }

    private static <T> void swap(T[] arr, int i, int j) {
        T tmp = arr[i];
        arr[i] = arr[j];
        arr[j] = tmp;
    }

    private static <T extends Comparable<? super T>> boolean less(T v, T w) {
        return v.compareTo(w) < 0;
    }
}
```

### 4.2 链表

链表数据结构：

```java
public class ListNode {
    public int val;
    public ListNode next;

    public ListNode(int x) {
        val = x;
    }

    @Override
    public String toString() {
        return next != null ? val + "->" + next.toString() : val + "";
    }

    public static ListNode buildList(int... vals) {
        ListNode dummy = new ListNode(-1), curr = dummy;
        for (int i : vals) {
            curr.next = new ListNode(i);
            curr = curr.next;
        }
        return dummy.next;
    }
}
```

#### 4.2.1 反转链表

```java
public static ListNode reverse(ListNode head) {
    if (head == null || head.next == null) {
        return head;
    }
    
    ListNode p = head.next;
    head.next = null;
    ListNode q = reverse(p);
    p.next = head;
    return q;
}
```

#### 4.2.2 合并两个有序链表

```java
public static ListNode merge(ListNode l1, ListNode l2) {
    ListNode dummy = new ListNode(-1), curr = dummy;
    while (l1 != null && l2 != null) {
        if (l1.val <= l2.val) {
            curr.next = l1;
            l1 = l1.next;
        } else {
            curr.next = l2;
            l2 = l2.next;
        }
        curr = curr.next;
    }
    curr.next = l1 == null ? l2 : l1;
    return dummy.next;
}
```

#### 4.2.3 判断两个链表是否相交

两个相交的链表有共同的尾节点。

```java
public static static boolean isIntersected(ListNode headA, ListNode headB) {
    if (headA == null || headB == null) {
        return false;
    }
    ListNode tailA = headA, tailB = headB;
    while (tailA.next != null) {
        tailA = tailA.next;
    }
    while (tailB.next != null) {
        tailB = tailB.next;
    }
    return tailA == tailB;
}
```

#### 4.2.4 找出两个链表相交的位置

```java
public static ListNode getIntersectionNode(ListNode headA, ListNode headB) {
    if (headA == null || headB == null) {
        return null;
    }
    ListNode a = headA, b = headB;
    while (a != b) {
        a = a != null ? a.next : headB;
        b = b != null ? b.next : headA;
    }
    return a;
}
```

#### 4.2.5 找出链表倒数第K个节点

```java
public ListNode kthFromEnd(ListNode head, int k) {
    ListNode fast = head, slow = head;
    for (int i = 0; i < k - 1; i++) {
        fast = fast.next;
    }
    while (fast.next != null) {
        fast = fast.next;
        slow = slow.next;
    }
    return slow;
}
```

#### 4.2.6 判断链表是否有环

```java
public static boolean hasCycle(ListNode head) {
    ListNode slow = head, fast = head;
    while (fast != null && fast.next != null) {
        slow = slow.next;
        fast = fast.next.next;
        if (slow == fast) {
            return true;
        }
    }
    return false;
}
```

#### 4.2.7 找出有环链表环的入口

```java
public static ListNode getCycleEntrance(ListNode head) {
    ListNode fast = head, slow = head;
    while (true) {
        fast = fast.next.next;
        slow = slow.next;
        if (slow == fast) {
            break;
        }
    }
    fast = head;
    while (slow != fast) {
        fast = fast.next;
        slow = slow.next;
    }
    return slow;
}
```

### 4.3 数组

#### 4.3.1 最大子序列和

```java
public int maxSubArray(int[] nums) {
    if (nums == null || nums.length == 0) {
        return 0;
    }
    int local = nums[0], global = nums[0];
    for (int i = 1; i < nums.length; i++) {
        local = Math.max(nums[i], local + nums[i]);
        global = Math.max(local, global);
    }
    return global;
}
```

#### 4.3.2 最大子序列积

```java
public int maxProduct(int[] nums) {
    int max = nums[0], min = nums[0], res = Integer.MIN_VALUE;
    for (int i = 1; i < nums.length; i++) {
        int mx = max, mn = min;
        max = Math.max(Math.max(nums[i], mx * nums[i]), mn * nums[i]);
        min = Math.min(Math.min(nums[i], mx * nums[i]), mn * nums[i]);
        res = Math.max(res, max);
    }
    return res;
}
```

// TODO

### 4.4 DFS/Backtracking

#### 4.4.1 Letter Combinations of a Phone Number

具体参见[LeetCode17](https://leetcode.com/problems/letter-combinations-of-a-phone-number/description/){:target="_blank"}.

```java
private static String[] TABLE = {"", "", "abc", "def", "ghi", "jkl", "mno", "pqrs", "tuv", "wxyz"};

public List<String> letterCombinations(String digits) {
    List<String> res = new ArrayList<>();
    if (digits != null && !digits.isEmpty()) {
        letterCombinations(digits, res, "", 0);
    }
    return res;
}

private void letterCombinations(String digits, List<String> res, String s, int idx) {
    if (idx == digits.length()) {
        res.add(s);
    } else {
        String letters = TABLE[digits.charAt(idx) - '0'];
        for (int i = 0; i < letters.length(); i++) {
            letterCombinations(digits, res, s + letters.charAt(i), idx + 1);
        }
    }
}
```

#### 4.4.2 Sudoku Solver

具体参见[LeetCode37](https://leetcode.com/problems/sudoku-solver/description/){:target="_blank"}.

```java
public void solveSudoku(char[][] board) {
    solve(board);
}

private boolean solve(char[][] board) {
    for (int i = 0; i < 9; i++) {
        for (int j = 0; j < 9; j++) {
            if (board[i][j] == '.') {
                for (char c = '1'; c <= '9'; c++) {
                    if (isValid(board, i, j, c)) {
                        board[i][j] = c;
                        if (solve(board)) {
                            return true;
                        }
                        board[i][j] = '.';
                    }
                }
                return false;
            }
        }
    }
    return true;
}

private boolean isValid(char[][] board, int i, int j, char c) {
    for (int row = 0; row < 9; row++) {
        if (board[row][j] == c) {
            return false;
        }
    }
    for (int col = 0; col < 9; col++) {
        if (board[i][col] == c) {
            return false;
        }
    }
    for (int row = i / 3 * 3; row < i / 3 * 3 + 3; row++) {
        for (int col = j / 3 * 3; col < j / 3 * 3 + 3; col++) {
            if (board[row][col] == c) {
                return false;
            }
        }
    }
    return true;
}
```

#### 4.4.3 Combination Sum

具体参见[LeetCode39](https://leetcode.com/problems/combination-sum/description/){:target="_blank"}.

```java
public List<List<Integer>> combinationSum(int[] candidates, int target) {
    List<List<Integer>> res = new ArrayList<>();
    if (candidates != null && candidates.length != 0) {
        combinationSum(candidates, target, res, new ArrayList<>(), 0);
    }
    return res;
}

private void combinationSum(int[] candidates, int target, List<List<Integer>> res, List<Integer> list, int idx) {
    if (target < 0) {
        return;
    } else if (target == 0) {
        res.add(new ArrayList<>(list));
    } else {
        for (int i = idx; i < candidates.length; i++) {
            list.add(candidates[i]);
            combinationSum(candidates, target - candidates[i], res, list, i);
            list.remove(list.size() - 1);
        }
    }
}
```

#### 4.4.4 Combination SumII

具体参见[LeetCode40](https://leetcode.com/problems/combination-sum-ii/description/){:target="_blank"}.

```java
public List<List<Integer>> combinationSum2(int[] candidates, int target) {
    List<List<Integer>> res = new ArrayList<>();
    if (candidates != null && candidates.length != 0) {
        Arrays.sort(candidates);
        combinationSum2(candidates, target, res, new ArrayList<>(), 0);
    }
    return res;
}

private void combinationSum2(int[] candidates, int target, List<List<Integer>> res, List<Integer> list, int idx) {
    if (target < 0) {
        return;
    } else if (target == 0) {
        res.add(new ArrayList<>(list));
    } else {
        for (int i = idx; i < candidates.length; i++) {
            if (i > idx && candidates[i] == candidates[i - 1]) {
                continue;
            }
            list.add(candidates[i]);
            combinationSum2(candidates, target - candidates[i], res, list, i + 1);
            list.remove(list.size() - 1);
        }
    }
}
```

#### 4.4.5 Combination SumIII

具体参见[LeetCode216](https://leetcode.com/problems/combination-sum-iii/description/){:target="_blank"}.

```java
public List<List<Integer>> combinationSum3(int k, int n) {
    List<List<Integer>> res = new ArrayList<>();
    combinationSum3(res, new ArrayList<>(), k, n, 1);
    return res;
}

private void combinationSum3(List<List<Integer>> res, List<Integer> list, int k, int n, int idx) {
    if (n < 0 || list.size() > k) {
        return;
    } else if (n == 0 && list.size() == k) {
        res.add(new ArrayList<>(list));
    } else {
        for (int i = idx; i < 10; i++) {
            list.add(i);
            combinationSum3(res, list, k, n - i, i + 1);
            list.remove(list.size() - 1);
        }
    }
}
```

#### 4.4.6 Permutations

具体参见[LeetCode46](https://leetcode.com/problems/permutations/description/){:target="_blank"}.

```java
public List<List<Integer>> permute(int[] nums) {
    List<List<Integer>> res = new ArrayList<>();
    if (nums != null && nums.length != 0) {
        permute(res, new ArrayList<>(), new boolean[nums.length], nums);
    }
    return res;
}

private void permute(List<List<Integer>> res, List<Integer> list, boolean[] visited, int[] nums) {
    if (list.size() == nums.length) {
        res.add(new ArrayList<>(list));
    } else {
        for (int i = 0; i < nums.length; i++) {
            if (!visited[i]) {
                visited[i] = true;
                list.add(nums[i]);
                permute(res, list, visited, nums);
                list.remove(list.size() - 1);
                visited[i] = false;
            }
        }
    }
}
```

#### 4.4.7 Permutations II

具体参见[LeetCode47](https://leetcode.com/problems/permutations-ii/description/){:target="_blank"}.

```java
public List<List<Integer>> permuteUnique(int[] nums) {
    List<List<Integer>> res = new ArrayList<>();
    if (nums != null && nums.length != 0) {
        Arrays.sort(nums);
        permute(res, new ArrayList<>(), new boolean[nums.length], nums);
    }
    return res;
}

private void permute(List<List<Integer>> res, List<Integer> list, boolean[] visited, int[] nums) {
    if (list.size() == nums.length) {
        res.add(new ArrayList<>(list));
    } else {
        for (int i = 0; i < nums.length; i++) {
            if (i > 0 && !visited[i - 1] && nums[i] == nums[i - 1]) {
                continue;
            }
            if (!visited[i]) {
                visited[i] = true;
                list.add(nums[i]);
                permute(res, list, visited, nums);
                list.remove(list.size() - 1);
                visited[i] = false;
            }
        }
    }
}
```

#### 4.4.8 N-Queens

具体参见[LeetCode51](https://leetcode.com/problems/n-queens/description/){:target="_blank"}.

```java
public List<List<String>> solveNQueens(int n) {
    char[][] board = new char[n][n];
    for (char[] chars : board) {
        Arrays.fill(chars, '.');
    }
    List<List<String>> res = new ArrayList<>();
    solveNQueens(res, board, 0);
    return res;
}

private void solveNQueens(List<List<String>> res, char[][] board, int row) {
    if (row == board.length) {
        List<String> list = new ArrayList<>();
        for (char[] chars : board) {
            list.add(String.valueOf(chars));
        }
        res.add(new ArrayList<>(list));
    } else {
        for (int col = 0; col < board.length; col++) {
            if (isValid(board, row, col)) {
                board[row][col] = 'Q';
                solveNQueens(res, board, row + 1);
                board[row][col] = '.';
            }
        }
    }
}

private boolean isValid(char[][] board, int row, int col) {
    for (int i = row - 1; i >= 0; i--) {
        if (board[i][col] == 'Q') {
            return false;
        }
    }

    for (int i = row - 1, j = col - 1; i >= 0 && j >= 0; i--, j--) {
        if (board[i][j] == 'Q') {
            return false;
        }
    }

    for (int i = row - 1, j = col + 1; i >= 0 && j < board.length; i--, j++) {
        if (board[i][j] == 'Q') {
            return false;
        }
    }
    
    return true;
}
```

#### 4.4.9 Combinations

具体参见[LeetCode77](https://leetcode.com/problems/combinations/description/){:target="_blank"}.

```java
public List<List<Integer>> combine(int n, int k) {
    List<List<Integer>> res = new ArrayList<>();
    combine(res, new ArrayList<>(), n, k, 1);
    return res;
}

private void combine(List<List<Integer>> res, List<Integer> list, int n, int k, int idx) {
    if (list.size() == k) {
        res.add(new ArrayList<>(list));
    } else {
        for (int i = idx; i <= n; i++) {
            list.add(i);
            combine(res, list, n, k, i + 1);
            list.remove(list.size() - 1);
        }
    }
}
```

#### 4.4.10 Subsets

具体参见[LeetCode77](https://leetcode.com/problems/subsets/description/){:target="_blank"}.

```java
public List<List<Integer>> subsets(int[] nums) {
    List<List<Integer>> res = new ArrayList<>();
    if (nums != null && nums.length != 0) {
        subsets(res, new ArrayList<>(), nums, 0);
    }
    return res;
}

private void subsets(List<List<Integer>> res, List<Integer> list, int[] nums, int level) {
    res.add(new ArrayList<>(list));
    for (int i = level; i < nums.length; i++) {
        list.add(nums[i]);
        subsets(res, list, nums, i + 1);
        list.remove(list.size() - 1);
    }
}
```

#### 4.4.11 SubsetsII

具体参见[LeetCode90](https://leetcode.com/problems/subsets-ii/description/){:target="_blank"}.

```java
public List<List<Integer>> subsetsWithDup(int[] nums) {
    List<List<Integer>> res = new ArrayList<>();
    if (nums != null && nums.length != 0) {
        Arrays.sort(nums);
        subsetsWithDup(res, new ArrayList<>(), nums, 0);
    }
    return res;
}

private void subsetsWithDup(List<List<Integer>> res, List<Integer> list, int[] nums, int level) {
    res.add(new ArrayList<>(list));
    for (int i = level; i < nums.length; i++) {
        if (i > level && nums[i] == nums[i - 1]) {
            continue;
        }
        list.add(nums[i]);
        subsetsWithDup(res, list, nums, i + 1);
        list.remove(list.size() - 1);
    }
}
```

#### 4.4.12 Word Search

具体参见[LeetCode79](https://leetcode.com/problems/word-search/description/){:target="_blank"}.

```java
public boolean exist(char[][] board, String word) {

    for (int i = 0; i < board.length; i++) {
        for (int j = 0; j < board[0].length; j++) {
            if (search(board, word, i, j, 0)) {
                return true;
            }
        }
    }
    return false;
}

private boolean search(char[][] board, String word, int i, int j, int idx) {

    if (idx == word.length()) {
        return true;
    }

    if (i < 0 || j < 0 || i >= board.length || j >= board[0].length || board[i][j] != word.charAt(idx)) {
        return false;
    }

    char c = board[i][j];
    board[i][j] = '#';
    boolean res = search(board, word, i - 1, j, idx + 1)
            || search(board, word, i + 1, j, idx + 1)
            || search(board, word, i, j - 1, idx + 1)
            || search(board, word, i, j + 1, idx + 1);
    board[i][j] = c;
    return res;
}
```

#### 4.4.13 Palindrome Partitioning

具体参见[LeetCode131](https://leetcode.com/problems/palindrome-partitioning/description/){:target="_blank"}.

```java
public List<List<String>> partition(String s) {
    List<List<String>> res = new ArrayList<>();
    if (s != null && !s.isEmpty()) {
        partition(res, new ArrayList<>(), s, 0);
    }
    return res;
}

private void partition(List<List<String>> res, List<String> list, String s, int start) {
    if (start == s.length()) {
        res.add(new ArrayList<>(list));
    } else {
        for (int i = start; i < s.length(); i++) {
            String sub = s.substring(start, i + 1);
            if (isPalindrome(sub)) {
                list.add(sub);
                partition(res, list, s, i + 1);
                list.remove(list.size() - 1);
            }
        }
    }
}

private boolean isPalindrome(String s) {
    int i = 0, j = s.length() - 1;
    while (i < j) {
        if (s.charAt(i++) != s.charAt(j--)) {
            return false;
        }
    }
    return true;
}
```

#### 4.4.14 Word BreakII

具体参见[LeetCode140](https://leetcode.com/problems/word-break-ii/description/){:target="_blank"}.

```java
public List<String> wordBreak(String s, List<String> wordDict) {
    List<String> res = new ArrayList<>();
    Set<String> wordSet = new HashSet<>(wordDict);
    if (wordBreak(s, new HashSet<>(wordSet))) {
        dfs(s, wordSet, res, "");
    }
    return res;
}

public void dfs(String s, Set<String> wordSet, List<String> res, String word) {

    if (s.isEmpty()) {
        res.add(word.substring(0, word.length() - 1));
    } else {
        for (int i = 0; i < s.length(); i++) {
            String sub = s.substring(0, i + 1);
            if (wordSet.contains(sub)) {
                dfs(s.substring(i + 1), wordSet, res, word + sub + " ");
            }
        }
    }
}

public boolean wordBreak(String s, Set<String> wordDict) {
    Set<String> hs = new HashSet<>(wordDict);
    boolean[] dp = new boolean[s.length() + 1];
    dp[0] = true;
    for (int i = 0; i < dp.length; ++i) {
        for (int j = 0; j < i; ++j) {
            if (dp[j] && hs.contains(s.substring(j, i))) {
                dp[i] = true;
                break;
            }
        }
    }
    return dp[dp.length - 1];
}
```

### 4.5 树

TreeNode：

```java
public class TreeNode {
    public int val;
    public TreeNode left, right;

    public TreeNode(int x) {
        val = x;
    }
}
```

#### 4.5.1 Traversal

##### 4.5.1.1 中序遍历（Inorder）

具体参见[LeetCode94](https://leetcode.com/problems/binary-tree-inorder-traversal/description/){:target="_blank"}.

中序遍历顺序：左->中->右

递归实现：

```java
public List<Integer> inorderTraversal(TreeNode root) {
    List<Integer> res = new ArrayList<>();
    inorderTraversal(res, root);
    return res;
}

private void inorderTraversal(List<Integer> res, TreeNode root) {
    if (root == null) {
        return;
    } 
    inorderTraversal(res, root.left);
    res.add(root.val);
    inorderTraversal(res, root.right);
}
```

非递归实现：

```java
public List<Integer> inorderTraversal(TreeNode root) {

    List<Integer> res = new LinkedList<>();
    LinkedList<TreeNode> stack = new LinkedList<>();
    TreeNode curr = root;
    while (!stack.isEmpty() || curr != null) {
        while (curr != null) {
            stack.push(curr);
            curr = curr.left;
        }
        curr = stack.pop();
        res.add(curr.val);
        curr = curr.right;
    }
    return res;
}
```

##### 4.5.1.2 前序遍历（Preorder）

具体参见[LeetCode144](https://leetcode.com/problems/binary-tree-preorder-traversal/description/){:target="_blank"}.

前序遍历：中->左->右

递归实现：

```java
public List<Integer> preorderTraversal(TreeNode root) {
    List<Integer> res = new ArrayList<>();
    preorderTraversal(root, res);
    return res;
}

private void preorderTraversal(TreeNode node, List<Integer> res) {
    if (node == null) {
        return;
    }
    res.add(node.val);
    preorderTraversal(node.left, res);
    preorderTraversal(node.right, res);
}
```

非递归实现：

```java
public List<Integer> preorderTraversal(TreeNode root) {
    List<Integer> res = new ArrayList<>();
    if (root != null) {
        LinkedList<TreeNode> stack = new LinkedList<>();
        stack.push(root);
        while (!stack.isEmpty()) {
            TreeNode node = stack.pop();
            res.add(node.val);
            if (node.right != null) {
                stack.push(node.right);
            }
            if (node.left != null) {
                stack.push(node.left);
            }
        }
    }
    return res;
}
```

##### 4.5.1.3 后序遍历（Postorder）

具体参见[LeetCode145](https://leetcode.com/problems/binary-tree-postorder-traversal/description/){:target="_blank"}.

后序遍历：左->右->中

递归实现：

```java
public List<Integer> postorderTraversal(TreeNode root) {
    List<Integer> res = new ArrayList<>();
    postorderTraversal(root, res);
    return res;
}

private void postorderTraversal(TreeNode node, List<Integer> res) {
    if (node == null) {
        return;
    }
    postorderTraversal(node.left, res);
    postorderTraversal(node.right, res);
    res.add(node.val);
}
```

非递归实现：

```java
public List<Integer> postorderTraversal(TreeNode root) {
    List<Integer> res = new ArrayList<>();
    LinkedList<TreeNode> stack = new LinkedList<>();
    if (root != null) {
        stack.push(root);
        while (!stack.isEmpty()) {
            TreeNode node = stack.pop();
            res.add(0, node.val);
            if (node.left != null) {
                stack.push(node.left);
            }
            if (node.right != null) {
                stack.push(node.right);
            }
        }
    }
    return res;
}
```

##### 4.5.1.4 层级遍历（Levelorder）

具体参见[LeetCode102](https://leetcode.com/problems/binary-tree-level-order-traversal/description/){:target="_blank"}.

```java
public List<List<Integer>> levelOrder(TreeNode root) {
    List<List<Integer>> res = new ArrayList<>();
    if (root != null) {
        Queue<TreeNode> queue = new LinkedList<>();
        queue.add(root);
        while (!queue.isEmpty()) {
            int size = queue.size();
            List<Integer> list = new ArrayList<>();
            for (int i = 0; i < size; i++) {
                TreeNode node = queue.poll();
                list.add(node.val);
                if (node.left != null) {
                    queue.add(node.left);
                }
                if (node.right != null) {
                    queue.add(node.right);
                }
            }
            res.add(list);
        }
    }
    return res;
}
```

##### 4.5.1.5 逆序层级遍历（ReverseLevelorder）

具体参见[LeetCode107](https://leetcode.com/problems/binary-tree-level-order-traversal-ii/description/){:target="_blank"}.

```java
public List<List<Integer>> levelOrderBottom(TreeNode root) {
    List<List<Integer>> res = new ArrayList<>();
    if (root != null) {
        Queue<TreeNode> queue = new LinkedList<>();
        queue.add(root);
        while (!queue.isEmpty()) {
            int size = queue.size();
            List<Integer> list = new ArrayList<>();
            for (int i = 0; i < size; i++) {
                TreeNode node = queue.poll();
                list.add(node.val);
                if (node.left != null) {
                    queue.add(node.left);
                }
                if (node.right != null) {
                    queue.add(node.right);
                }
            }
            res.add(0, list);
        }
    }
    return res;
}
```

#### 4.5.2 Validate Binary Search Tree

具体参见[LeetCode98](https://leetcode.com/problems/validate-binary-search-tree/description/){:target="_blank"}.

```java
public boolean isValidBST(TreeNode root) {
    return root == null || isValidBST(root.left, Long.MIN_VALUE, root.val) && isValidBST(root.right, root.val, Long.MAX_VALUE);
}

private boolean isValidBST(TreeNode node, long min, long max) {
    return node == null || node.val > min && node.val < max && isValidBST(node.left, min, node.val) && isValidBST(node.right, node.val, max);
}
```

#### 4.5.3 Same Tree

具体参见[LeetCode100](https://leetcode.com/problems/same-tree/description/){:target="_blank"}.

```java
public boolean isSameTree(TreeNode p, TreeNode q) {
    if (p == null && q == null) {
        return true;
    } else if (p == null || q == null) {
        return false;
    } else {
        return p.val == q.val && isSameTree(p.left, q.left) && isSameTree(p.right, q.right);
    }
}
```

#### 4.5.4 Symmetric Tree

具体参见[LeetCode101](https://leetcode.com/problems/symmetric-tree/description/){:target="_blank"}.

```java
public boolean isSymmetric(TreeNode root) {
    return root == null || isSymmetric(root.left, root.right);
}

private boolean isSymmetric(TreeNode p, TreeNode q) {
    if (p == null && q == null) {
        return true;
    } else if (p == null || q == null) {
        return false;
    } else if (p.val != q.val) {
        return false;
    } else {
        return isSymmetric(p.left, q.right) && isSymmetric(p.right, q.left);
    }
}
```

#### 4.5.5 Maximum Depth of Binary Tree

具体参见[LeetCode104](https://leetcode.com/problems/maximum-depth-of-binary-tree/description/){:target="_blank"}.

```java
public int maxDepth(TreeNode root) {
    return root == null ? 0 : 1 + Math.max(maxDepth(root.left), maxDepth(root.right));
}
```

#### 4.5.6 Convert Sorted Array to Binary Search Tree

具体参见[LeetCode108](https://leetcode.com/problems/convert-sorted-array-to-binary-search-tree/description/){:target="_blank"}.

```java
public TreeNode sortedArrayToBST(int[] nums) {
    if (nums == null || nums.length == 0) {
        return null;
    }
    return sortedArrayToBST(nums, 0, nums.length - 1);
}

private TreeNode sortedArrayToBST(int[] nums, int lo, int hi) {
    if (lo > hi) {
        return null;
    }

    int mid = lo + (hi - lo >>> 1);
    TreeNode root = new TreeNode(nums[mid]);
    root.left = sortedArrayToBST(nums, lo, mid - 1);
    root.right = sortedArrayToBST(nums, mid + 1, hi);
    return root;
}
```

#### 4.5.7 Balanced Binary Tree

具体参见[LeetCode110](https://leetcode.com/problems/balanced-binary-tree/description/){:target="_blank"}.

```java
public boolean isBalanced(TreeNode root) {
    if (root == null) {
        return true;
    } else {
        int l = maxDepth(root.left), r = maxDepth(root.right);
        return Math.abs(l - r) <= 1 && isBalanced(root.left) && isBalanced(root.right);
    }
}

public int maxDepth(TreeNode root) {
    return root == null ? 0 : 1 + Math.max(maxDepth(root.left), maxDepth(root.right));
}
```

#### 4.5.8 Minimum Depth of Binary Tree

具体参见[LeetCode111](https://leetcode.com/problems/minimum-depth-of-binary-tree/description/){:target="_blank"}.

```java
public int minDepth(TreeNode root) {
    if (root == null) {
        return 0;
    }
    int l = minDepth(root.left), r = minDepth(root.right);
    return l == 0 || r == 0 ? 1 + l + r : 1 + Math.min(l, r);
}
```

#### 4.5.9 Invert Binary Tree

具体参见[LeetCode226](https://leetcode.com/problems/invert-binary-tree/description/){:target="_blank"}.

```java
public TreeNode invertTree(TreeNode root) {
    if (root == null) {
        return null;
    }
    root.left = invertTree(root.left);
    root.right = invertTree(root.right);
    return root;
}
```

#### 4.5.10 Binary Tree Paths

具体参见[LeetCode257](https://leetcode.com/problems/binary-tree-paths/description/){:target="_blank"}.

```java
public List<String> binaryTreePaths(TreeNode root) {
    List<String> res = new ArrayList<>();
    binaryTreePaths(root, res, "");
    return res;
}

private void binaryTreePaths(TreeNode node, List<String> res, String s) {

    if (node == null) {
        return;
    } else if (node.left == null && node.right == null) {
        res.add(s + node.val);
    } else {
        s += node.val + "->";
        binaryTreePaths(node.left, res, s);
        binaryTreePaths(node.right, res, s);
    }
}
```

#### 4.5.11 Path Sum

具体参见[LeetCode112](https://leetcode.com/problems/path-sum/description/){:target="_blank"}.

```java
public boolean hasPathSum(TreeNode root, int sum) {

    if (root == null) {
        return false;
    }

    if (sum == root.val && root.left == null && root.right == null) {
        return true;
    }

    return hasPathSum(root.left, sum - root.val) || hasPathSum(root.right, sum - root.val);
}
```

#### 4.5.11 Path Sum II

具体参见[LeetCode113](https://leetcode.com/problems/path-sum-ii/description/){:target="_blank"}.

```java
public List<List<Integer>> pathSum(TreeNode root, int sum) {
    List<List<Integer>> res = new ArrayList<>();
    if (root != null) {
        pathSum(res, new ArrayList<>(), root, sum);
    }
    return res;
}

private void pathSum(List<List<Integer>> res, ArrayList<Integer> list, TreeNode node, int sum) {
    if (node != null) {
        list.add(node.val);
        if (sum == node.val && node.left == null && node.right == null) {
            res.add(new ArrayList<>(list));
            return;
        }
        pathSum(res, list, node.left, sum - node.val);
        pathSum(res, list, node.right, sum - node.val);
        list.remove(list.size() - 1);
    }
}
```

#### 4.5.12 Lowest Common Ancestor of a Binary Search Tree

具体参见[LeetCode235](https://leetcode.com/problems/lowest-common-ancestor-of-a-binary-search-tree/description/){:target="_blank"}.

```java
public TreeNode lowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q) {
    if (p.val > root.val && q.val > root.val) {
        return lowestCommonAncestor(root.right, p, q);
    } else if (p.val < root.val && q.val < root.val) {
        return lowestCommonAncestor(root.left, p, q);
    } else {
        return root;
    }
}
```

#### 4.5.13 Lowest Common Ancestor of a Binary Tree

具体参见[LeetCode236](https://leetcode.com/problems/lowest-common-ancestor-of-a-binary-tree/description/){:target="_blank"}.

```java
public TreeNode lowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q) {
    if (root == null || p == root || q == root) {
        return root;
    }
    TreeNode left = lowestCommonAncestor(root.left, p, q);
    TreeNode right = lowestCommonAncestor(root.right, p, q);
    if (left != null && right != null) {
        return root;
    }
    return left != null ? left : right;
}
```

## 第五章 框架

## 第六章 数据库

## 第七章 计算机网络

## 第八章 Linux


