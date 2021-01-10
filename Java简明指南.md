# Java简明指南

## 一、对Java的基本认知

Java是一门完全面向对象的，支持分布式，健壮安全，跨平台的程序开发语言。

使用Java编写的程序可以运行在任何的操作系统上，这个特性称为Java语言的跨平台性。该特性JVM实现，我们写的程序运行在JVM上，而JVM运行在操作系统上。不同的操作系统有着相应的JVM，JVM对着同一份Java程序有着同样的实现，JVM本身不具备跨平台性。

Java虚拟机（JVM）、Java运行时环境（JRE）和Java开发包（JDK）有着以下的包含关系：

<img src="https://raw.githubusercontent.com/Daffupman/markdown-img/master/20190710195510.png" alt="JVM、JRE和JDK" style="zoom:67%;" />

> Java9不再支持windows的32位系统了;
>
> Java11取消JRE独立安装包。

## 二、写Java代码的一些基本操作

### 注释

注释是对程序的解释说明，可以分成三种注释：

1. 单行注释

   ```java
   // 注释内容
   ```

2. 多行注释

   ```java
   /* 
    注释内容 
   */
   ```

3. 文档注释

   ```java
   /**
    * 注释内容
    */
   ```

### 关键字

关键字（keyword）是指在程序中，Java已经定义好的单词，具有特殊意义。特点是全部都是小写字母。

### 常量

常量（constant）：在程序执行的过程中，其值不可以发生改变的量。常量分为自定义常量和字面值常量。自定义常量是通过 `final` 关键字定义的，字面值常量包括字符串常量、整数常量、浮点常量、字符常量、布尔常量和空常量（null）。

### 变量

变量（variable）：在程序执行过程中，其值可以发生改变。

```java
数据类型 变量名 = 数据值;
```

### 数据类型

Java的数据类型分为基本数据类型和引用数据类型。

#### 基本数据类型

大致可以分为四种八类：四种有整数，浮点数，字符和布尔。八类又是把四种具体化，整数再分为byte、short、int和long，浮点类型分为float和double，字符有char类型，布尔有boolean类型。

| 数据类型 | 关键字           | 占用内存 | 取值范围                           |
| -------- | ---------------- | -------- | ---------------------------------- |
| 整数     | byte             | 1        | [-128,127]                         |
| 整数     | short            | 2        | [-2<sup>15</sup>,2<sup>15</sup>-1] |
| 整数     | **int(默认)**    | 4        | [-2<sup>31</sup>,2<sup>31</sup>-1] |
| 整数     | long             | 8        | [-2<sup>63</sup>,2<sup>63</sup>-1] |
| 浮点数   | float            | 4        |                                    |
| 浮点数   | **double(默认)** | 8        |                                    |
| 字符     | char             | 2        | [0,65535]                          |
| 布尔     | boolean          | 1        | true,false                         |

#### 引用数据类型

包括数组、类和接口。

### 标识符

标识符就是程序中自定义的内容，如：类名、方法名、变量名等。命名规则如下：

1. 标识符可以包含`a-z和A-Z`、`0-9的数字`、`$`和`_`等；
2. 标识符不能以数字开头；
3. 标识符不能是关键字。

类名一般是大驼峰，方法名和变量名一般是小驼峰。

变量定义时需要注意：

1. 如果创建多个变量，那么变量之间的名称不能重复；
2. 对于float和double，不能忘记F和L；
3. 如果使用byte和short类型的变量，右侧的数不能超过左侧的范围；
4. 不能直接使用没有赋值的变量；
5. 变量的使用不能超过作用域的范围。

### 基本数据类型之间的转换

较大范围的数据类型和较小范围的数据类型是可以互转的。将`取值范围小的类型`自动转换成`取值范围较大的类型`，称为自动类型转换（或隐式类型转换）。

![自动类型转换](https://raw.githubusercontent.com/Daffupman/markdown-img/master/20190710202813.png)

将`取值范围大的类型`自动转换成`取值范围较小的类型`，称为强制类型转换（或显式类型转换）。

> 数据类型 变量名 = （数据类型）被强转数据值；

注意事项：

1. 强制类型转换一般不推荐使用，因为有可能发生精度损失和数据溢出；	
2. byte、short、char这三种类型进行运算时，会先直接提升为int类型，然后再计算；
3. boolean类型不能发生类型转换。

### 运算符

| 分类       | 运算符                                       |
| ---------- | -------------------------------------------- |
| 算术运算符 | + 、 - 、 * 、 / 、 % 、 ++ 、 --            |
| 赋值运算符 | = 、 += 、 -= 、 *= 、 /= 、 %=              |
| 关系运算符 | > 、 >= 、 < 、 <= 、 == 、 !=               |
| 逻辑运算符 | &、\|、^、！、&&、\|\|                       |
| 三元运算符 | 数据类型 变量名 = (关系表达式)?结果1:结果2； |
| 位运算符   | & 、\|、~、^、<<、>>、>>>                    |

1. `+`的两种用法
    - 运算符两边都是数值，加法运算
    - 运算符两边至少有一个字符串，拼接

2. `i++` 和 `i++`
    - ``i++` ：先引用，再自增
    - `++i` ：先自增，再引用
3. `+= `类的运算符会有一步隐藏的强制类型转换。

### 流程控制

代码的执行步骤一般是从上到下的，但也有一些代码结构和关键字用于控制代码的运行顺序。

#### 顺序结构

```java
boolean condition = true;

// 格式一
if(condition) {
	// 语句体;
}

// 格式二
if(condition) {
    // 语句体1;
}else {
    // 语句体2;
}

// 格式三
if (判断条件1) {
    // 执行语句1;
} else if (判断条件2) {
    // 执行语句2;
}
...
} else if (判断条件n) {
    // 执行语句n;
} else {
    // 执行语句n+1;
}
```

#### 选择结构

```java
switch(表达式) {
    case 数据值1:
        语句体1;
        break;
    case 数据值2:
        语句体2;
        break;
    default:
        语句体n+1;
        break;
}
```

switch语句中，表达式结果的数据类型只能是有限的 `四种基本数据类型` 和 `两种引用数据类型`

> - 基本数据类型：byte、short、int、long
> - 引用数据类型：枚举类（jdk5）、字符串（jdk7）

#### 循环结构

for循环

```java
for(初始化语句; 循环条件语句; 迭代语句){
	循环体语句
}
```

while循环

```java
while (循环条件语句) {
	循环体语句；
}
```

do-while循环

```java
do {
      循环体语句；
} while (循环条件语句);
```

#### 控制语句

 - break：跳出循环
 - continue：跳过之后的语句体，直接开始下次的循环
 - return：终止整个方法

### 数组的定义和访问

数组就是存储数据长度固定的容器，保证多个数据的数据类型要一致。

#### 定义语法

```java
数据类型[] 数组名 = new 数组存储的数据类型[长度];
数据类型[] 数组名 = new 数据类型[]{元素1,元素2,元素3...};
数据类型[] 数组名 = {元素1,元素2,元素3...};
```

#### 数组访问

1. 索引： 每一个存储到数组的元素，都会自动的拥有一个编号，从0开始，这个自动编号称为数组索引(index)，可以通过数组的索引访问到数组中的元素。通过 ` 数组名.length` 可以获取数组的长度，通过 `数组名[索引]` 可以访问数组中的元素。
2. 数组中的异常
   1. 数组越界异常：ArrayIndexOutOfBoundsException
   2. 数组空指针异常： NullPointerException

## 三、面向对象

面向对象思想是一种更符合我们思考的习惯，它可以将复杂的事情简单化，可以将我们从执行者转换成指挥者。面向对象的三大特征：封装、继承和多态。

### 一些概念

- `类`：是一组相关属性和行为的集合。可以看成是一类事物的模板，使用事物的属性特征和行为特征来描述该事物。

- `属性`：事物的状态信息

- `行为`：该事物能做什么

- `对象`：是一类事物的具体体现，对象是类的一个实例。

  > 类和对象的关系：
  >
  > 类是对一类事物的描述，是抽象的，
  >
  > 对象是一类事物的实例，是具体的，
  >
  > 类是对象的抽象，对象是类的实例。

### 定义类

类的定义语法：

```java
public class ClassName {
    // 成员变量
    // 成员方法
}
```

#### 成员变量

成员变量是有默认值的，引用数据类型默认为null。null是常量，但是不能调用方法和被打印。

> 成员变量和局部变量的区别：
>
> 1. 在类中的位置
> 2. 作用的范围
> 3. 初始化的值不同
> 4. 在内存中的位置

#### 成员方法

在定义方法时需要明确`返回值类型`和`参数列表`，成员方法分为：

- 实例方法（对象方法）
- 静态方法（类方法）

在JDK1.5之后，定义参数时可以使用可变参数

```java
修饰符 返回值类型 方法名(参数类型... 形参名){ }
```

> 注意：如果在方法书写时，这个方法拥有多参数，参数中包含可变参数，可变参数一定要写在参数列表的末尾位置。

方法重载指在同一个类中，允许存在一个以上的同名方法，只要它们的参数列表不同即可，与修饰符和返回值类型无关

### 封装

#### private

封装的原则是私有化属性，并提供公共的访问方法。可以使用 `private` 关键字私有化封装。使用private后会有以下效果：

- private是一个权限修饰符，代表最小权限；
- 可以修饰成员变量和成员方法；
- 被private修饰后的成员变量和成员方法，只在本类中才能访问。

> 在Java中提供了三种权限修饰符，四种访问权限：
>
> - public：公共的，不同包的无关类都可以访问
> - protected：受保护的，不同包的子类都可以访问了
> - 缺省：同一个包下
> - private：同一个类中

#### this

this代表所在类的当前对象的引用（地址值），即对象自己的引用。

#### 构造方法

创建对象的时候使用构造器初始化对象。如果没有写构造器，JVM会自动添加一个无参的构造器，如果显式地写入构造器，就不自动添加构造器。

#### 标准的对象——JavaBean

```java
public class ClassName{
    // 成员变量
    // 构造方法
    // 无参构造方法【必须】
    // 有参构造方法【建议】
    // 成员方法
    // getXxx()
    // setXxx()
}
```

### 继承

#### extends

通过 `extends` 关键字，可以声明一个子类继承另外一个父类。

此时子类和父类中的构造方法、成员变量和成员方法会有一些特点：

- 构造方法：使用 `super()` 访问父类的构造。如果不写，JVM也会在子类构造器的第一行(并且只能在第一行)加上super();
- 成员变量：
  1. 私有化：父类中的所有成员（包括私有成员），都会被子类继承。但是子类是不能直接访问父类的私有成员变量，可以通过公共的getter方法获取。
  2. 成员变量重名：子类的成员变量会覆盖父类的成员变量，但是如果要访问父类被覆盖的成员变量，需要使用`super`关键字。
- 成员方法：
  1. 当子类和父类出现重名的方法时，会发生重写（Override）；
  2. 对于重写需要注意的是：
     - 必须保证父子类之间方法的名称相同，参数列表也相同。（@Override会帮助检查）
     - 子类方法的返回值类型必须【小于等于】父类方法的返回值类型
     - 子类方法的权限必须【大于等于】父类方法的权限修饰符。

#### 继承中的super和this

父类空间优先于子类对象产生

![父类与子类](https://raw.githubusercontent.com/Daffupman/markdown-img/master/20190713124240.png)

super和this的含义

- super ：代表父类的存储空间标识(可以理解为父亲的引用)。
- this ：代表当前对象的引用(谁调用就代表谁)。

### 多态

#### 抽象类

在没有不使用抽象类的之前，会有这样几个问题：

- 通过继承关系抽取的父类是多个子类共有的属性和行为，理论上不应该被实例化；
- 通过继承关系抽取的父类的行为方法，需要被子类重写时，写完父类后很容易忘记写子类的；
- 通过继承关系抽取的父类的行为方法，因为需要被子类重写，所以方法体显得多余。

实际上，把需要重写的方法声明为抽象方法即可，另外，包含抽象方法的类需要被声明为抽象类。抽象类是接口和类的中庸之道。

对方法使用 `abstract` 修饰，这个方法就成了抽象方法，也就不需要方法体了。同时包含这个方法的类也必须使用abstract修饰。

继承抽象类的子类必须重写父类的所有的抽象方法，否则子类也必须声明为抽象类。最终，必须有子类实现该父类的抽象方法，否则，从最初的父类到最终的子类都不能创建对象，失去意义。此时的方法重写，是子类对父类抽象方法的完成实现，我们将这种方法重写的操作，也叫做实现方法。

> 注意：
>
> - 抽象类不能创建对象，如果创建，编译无法通过而报错。只能创建其非抽象子类的对象；
> - 抽象类中，可以有构造方法，是供子类创建对象时，初始化父类成员使用的；
> - 抽象类中，不一定包含抽象方法，但是有抽象方法的类必定是抽象类；
> - 抽象类的子类，必须重写抽象父类中所有的抽象方法，否则，编译无法通过而报错。除非该子类也是抽象类。

#### 接口

接口是一种引用数据类型，使用 `interface` 关键字声明。它不能创建对象，但可以被实现。接口内部主要是封装了方法，包含抽象方法（jdk7及其之前），默认方法和静态方法（jdk8），私有方法（jdk9）。

接口的定义语法：

```java
public interface 接口名称 {
    // 自定义常量，默认使用public static final修饰
    // 抽象方法，默认使用public abstract修饰
    // 默认方法，jdk8,需要使用default修饰，当子类重写父类方法的方法体一样的时候使用
    // 静态方法，jdk8,使用static修饰，定义工具方法，一般不用
    // 私有方法，jdk9，如果默认方法和静态方法有可抽取的部分，可将这部分封装成私有方法
}
```

类可以实现多个接口，若实现的多个接口中有重复的抽象方法，只需要重写一次即可，有重复的默认方法，必须重写，有重复的静态方法，不影响。

当一个类既继承父类又实现接口，其中父类和接口中有重名的方法，那么这个类会选择执行父类中的。

接口可以多继承，接口中还有其他的特点：

- 无法定义成员变量，但可以定义常量；
- 没有构造方法；
- 没有代码块。

#### 多态

多态是指同一行为，具有多个不同表现形式。多态可以发生在存在继承或实现中，而且需要方法重写，同时父类引用指向子类时。多态更具拓展性。

多态的体现格式：

```java
父类类型 变量名 = new 子类对象；
变量名.方法名();
```

具有多态特征的对象可以实现向上或向下的类型转换，转型中可能出现`ClassCastException`，可以使用关键字`intanceof`检查。

### 其他

#### final

使用final修饰的类、方法和变量后，都不可更改。主要体现为：

- 类：该类不能被继承
- 方法：该方法不能被重写
- 变量：该变量不能被再赋值

#### static

用来修饰的 `变量` 和 `方法` ，被修饰的成员会直接属于类。通过类就可以调用。static修饰的变量存储于方法区中的静态区，为所有本类对象的共享数据。

构造代码块：与构造器类似，先于构造器执行。

静态代码块：随着类的加载而执行且执行一次，优先于main方法和构造方法的执行。

## 四、常用API

### Scanner类

一个可以解析基本类型和字符串的简单文本扫描器。位于`java.util`包下。创建从键盘获取输入Scanner对象：

```java
Scanner scan = new Scanner(System.in);
```

查看api，Scanner可以获取int、double、String等类型的数据。当出现`MismatchInputExeception`异常后，重新获取输入前，要么重新创建一个Scanner对象，要么使用一些方法跳过当前行（scan.nextLine()）；

### Random类

随机数生成器，位于`java.util`包下。

```java
Random rand = new Random();
int num = rand.nextInt(10);// 获取一个[0,10）区间的数
```

> 注意：要获取一个[a,b)之间的数：
>
> ```java
> int num = rand.nextInt(b-a+1)+a;
> ```

### Arrays类

用于操作数组的工具类

- `public static String toString(int[] arr)`：返回指定数组内容的字符串表示形式
- `public static void sort(int[] arr)`：对指定的int数组升序排序

### Math类

数学工具类

- `public static double abs(double a)`：返回绝对值
- `public static double ceil(double a)`：向上取整
- `public static double floor(double a)`：向下取整
- `public static long round(double a)`：四舍五入

### Object类

`java.lang.Object`在Java中是所有类的根类，对象实例化后，最终找的是Object类。如果一个类没有特别指定父类，那么默认继承Object类。常用的方法：

- `public String toString()`：返回对象的字符串表示，在打印该类的类名时，默认调用该方法
- `public boolean equals(Object obj)`：知识其他对象是否与此对象相等。如果该类没有重写该方法，会执行Object类的`equals()`，即比较对象的地址值

### 包装类

Java的基本数据类型都有对应的包装类型，包装类型属于引用数据类型，可以做更多的操作：

| 基本数据类型 | 对应的包装数据类型 |
| ------------ | ------------------ |
| byte         | Byte               |
| short        | Short              |
| int          | Integer            |
| long         | Long               |
| float        | Float              |
| double       | Double             |
| char         | Character          |
| boolean      | Boolean            |

基本类型与对应的包装类型，来回转换的过程称为“装箱”和"拆箱"

- 装箱：从基本数据类型转为包装数据类型

    ```java
    Integer i1 = new Integer(1);		// 使用构造函数
    Integer i2 = Integer.valueOf(4);	// 使用包装类中的valueOf()方法
    ```

- 拆箱：从包装数据类型转为基本数据类型

    ```java
    int num = i.intValue();
    ```

自动装箱和自动拆箱：从jdk1.5开始，支持自动装箱和自动拆箱，基本数据类型和字符串之间的转换

- 基本类型 -> String
    - 使用`“”`拼接基本类型的值
- String -> 基本类型
    - 包装类的静态方法`parseXxx()`
- 如果字符串参数的内容无法正确转换为对应的基本类型，则会抛出 `java.lang.NumberFormatException`异常

### String类

字符串是不可变的字符序列，可以被共享。`"abc"`等效于`char[] data = {'a','b','c'}`，常用方法有：

- 构造方法

    - `public String()`
    - `public String(char[] value)`
    - `public String(byte[] bytes)`

- 判断功能

    - `public boolean equals(Object anthor)`
    - `public boolean qualsIgnoreCase(Object anthor)`

- 获取功能

    - `public int length()`
    - `public String concat(String str)`
    - `public char charAt(int index)`
    - `public int indexOf(String str)`
    - `public String substring(int beginIndex)`
    - `public String substring(int beginIndex, int endIndex)`

- 转换功能

    - `public char[] toCharArray()`

    - `public byte[] getBytes()`

    - `public String replace(CharSequence target, CharSequence replacement)`

        CharSequence 是一个接口，也是一种引用类型。作为参数类型，可以把String对象传递到方法中

- 分隔功能

    - `public String[] split(String regex)`

### StringBuilder类

因为字符串是不可变的，所以每次拼接都是在创建一个新的String对象，浪费性能。使用`StringBuilder`就可以解决。`StringBuilder`是字符可变序列，是一个类似于String字符串的缓冲区，通过方法可以改变字符序列的长度和内容，创建时的默认空间为16，常用方法：

- 构造方法

    - `public StringBuilder()`

    - `public StringBuilder(String str)`

- 成员方法

    - `public StringBuilder append(...)`：添加任意类型的数据
    - `public String toString()`

StringBuffer与StringBuilder的API一样，区别如下：

- StringBuilder：适用于在多线程下在字符缓冲区进行大量操作，但不保证安全
- StringBuffer：适用于在单线程下在字符缓冲区进行大量操作，保证安全

### System类

可以获取与系统相关的操作

- `public static long currentTimeMillis()`

### 日期时间类

1. Date类

    `java.util.Data`表示特定的瞬间，精确到秒

    - `public Date()`：分配Date对象并初始化此对象，以表示分配它的时间（精确到毫秒）。
    - `public Date(long date)`：分配Date对象并初始化此对象，以表示自从标准基准时间（称为“历元
        （epoch）”，即1970年1月1日00:00:00 GMT）以来的指定毫秒数。
    - `public long getTime()`：把日期对象转换成对应的时间毫秒值

2. DateFormat类

    `java.text.DateFormat`是日期/时间格式化子类的抽象类，通过这个类可以帮助我们完成日期和文本之间的转换，即`Date`对象和`String`对象

    - 格式化：按照指定格式，Date对象转String对象
    - 解析：按照指定格式，String对象转Date对象

    指定格式化或解析标准

    - `public SimpleDateFormat(String pattern)`

    常用方法

    - `public String format(Date date)`
    - `public Date parse(String source)`

3. Calendar类

    `java.util.Calendar`是日历类，替换了Date的很多方法，使用静态调用。并且该类不能直接创建，而是使用以下方法创建的

    - `public Calendar getInstance()`：使用默认时区和语言环境获得一个日历

    常用方法

    - `public int get(int field)`：返回给定日历字段的值
    - `public void set(int field, int value)`：将给定日历字段设为给定值
    - `public abstract void add(int field, int amount)`
    - `public Date getTime()`：返回一个表示此Calendar时间值（从历元到现在的毫秒偏移量）的Date对象

    日历字段

    YEAR,MONTH,DAY_OF_MONTH,HOUR,HOUR_OF_DAY,MINUTE,SECOND,DAY_OF_WEEK

4. 第三种日期类

    | 第三种日期类                        | 对应的类         |
    | ----------------------------------- | ---------------- |
    | LocalDate、LocalTime、LocalDateTime | Calendar         |
    | Instant                             | Date             |
    | DateTimeFormatter                   | SimpleDateFormat |

## 五、异常

异常：指的是程序在执行过程中，出现的非正常的情况，最终会导致JVM的非正常停止。在Java等面向对象的编程语言中，异常本身是一个类，产生异常就是创建异常对象并抛出了一个异常对象。Java处理
异常的方式是中断处理。

### 异常体系和分类

Throwable体系：

- Error：严重错误，无法处理，只能尽可能的避免
- Exception：异常产生后，可以通过代码纠正，使程序正常运行

Throwable常用方法

- `public void printStackTrace()`
- `public String getMessage()`
- `public String toString()`：获取异常的类型和异常描述信息

按照检查异常的的时机，可以将异常分为：

- 编译时异常：checked异常，编译时期就会检查，如果不处理异常就会编译失败。
- 运行时异常：runtime异常，在程序运行时会检查错误。

### 异常处理

Java异常处理的五个关键字：`try`、`catch`、`finally`、`throws`、`throw`。

在Java中使用关键字 `throw` ，抛出一个指定的异常对象（封装自定义的提示信息），声明异常的格式：

```java
修饰符 返回值类型 方法名(参数) throws 异常类名1,异常类名2…{  }
```

使用`try-catch`来处理可能发生异常的代码，防止程序停止运行。其中try里面放着可能发生异常的代码，catch里写上异常处理的代码。另外 `try-catch` 还可以有`finally`代码块，这里经常用来处理一些程序不管有没有出异常都要执行的代码，比如：关闭物理资源（磁盘文件，网络连接，数据库连接等）。但是在`try-catch`中直接退出JVM就不会执行finally代码块。

> 注意：
>
> 1. 多个异常推荐一次捕获多次处理；
> 2. 运行时异常可以不处理；
> 3. 如果finally有return语句,永远返回finally中的结果,避免该情况.；
> 4. 如果父类抛出了多个异常,子类重写父类方法时,抛出和父类相同的异常或者是父类异常的子类或者不抛出异常；
> 5. 父类方法没有抛出异常，子类重写父类该方法时也不可抛出异常。此时子类产生该异常，只能捕获处理，不能声明抛出。

### 自定义异常

自定义异常的步骤

- 自定义编译时异常：需要继承`java.lang.Exeception`
- 自定义运行时异常：需要继承`java.lang.RuntimeExeception`

示例：

```java
// 业务逻辑异常
public class RegisterException extends Exception {
    public RegisterException() {}
    public RegisterException(String message) {
        super(message);
    }
}
```

## 六、枚举类

jdk1.5之前需要自定义枚举类，枚举类实际上就是一组有明确数量的对象。

### 自定义枚举类

自定义枚举的步骤为：第一，私有化构造器，第二，本类内部创建一组对象,并添加public static修饰符，对外暴露该对象

```java
// 构造器：无参  示例：性别类
class Gender {
    // 1.构造器私有化
    private Sex() {}
    
    // 2.本类内部创建一组对象,并添加public static修饰符，对外暴露该对象
    public static Sex boy = new Sex();
    public static Sex girl = new Sex();
}
```

### enum关键字

jdk1.5开始，支持枚举类，使用enum关键字声明

```java
enum 枚举类类名 {
	对象名1，对象名2；
}
```

- 如果该类不显式提供构造器，则系统将默认 提供一个`private`的无参构造器，也就是如果该类需要的是无参构造器，则可以省略
- 创建对象的语句，必须放在类体中的第一句
- 对象默认的修饰符是：`public static final,`也就是对象为常量，所以建议大写
- 如果调用的构造器为无参构造器，则参数列表也可以省

常见的方法：

- `toString()`：返回的是常量名，可以继续重写这个方法
- `name()`：返回的是常量名，很少用
- `ordinal()`：返回常量的次序，默认从0开始
- `values()`：返回该枚举类的所有的常量对象

枚举类不可以显式地继承其他类，因为它已经继承了`Enum`，枚举类可以实现其他的多个接口。

## 七、注解

jdk1.5的新特性，注解又称元数据，可以修饰包、类、方法、属性、构造等。注解不会改变程序的原有逻辑，是给程序员看的，提高代码的可阅读性。常见的Java注解有：@Override、@Deprecated和@SuppressWarnings等。

### 自定义注解

```java
[修饰符] @interface 注解类名{
	参数类型 方法名() [default 值];
}
```

- 参数类型：只能是八大基本数据类型，String、枚举、Class类型以及它们的数组类型
- 方法名：遵守方法名的命名规则和规范，如果方法名为`value`，使用注解的时候可以省略该名称
- [default 参数值]：可选的，如果不写，那么在使用时必须传参，反之，可以不传参。

### 元注解

用于修饰注解的注解，分别如下：

- @Target：用于表明被修饰的注解可以用于修饰哪些数据。
  `ElementType[]:TYPE/FIELD/METHOD/PACKAGE/PARAMETER/CONSTRUCTOR/LOCAL_VARIABLE`
  如果未指定该元注解，则默认代表可以修饰任何数据；
- @Rentation：用于表明被修饰的注解可以保留多久`RetentionPolicy：SOURCE/CLASS/RUNTIME`
  如果不指定该元注解，则模板是`CLASS`；
- @Documented：用于表明被修饰的注解是否在生成帮助文档时显示出来。