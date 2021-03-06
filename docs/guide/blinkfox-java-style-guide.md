# 📖 Blinkfox Java 编程风格指南

## 1 前言

这份文档是 Blinkfox 根据 [Google Java 编程风格](https://checkstyle.sourceforge.io/styleguides/google-java-style-20180523/javaguide.html#s3.3.3-import-ordering-and-spacing)规范而根据自身使用情况而修改的一份 Java 编程风格指南。本文档相对于 Google 的 Java 编程风格文档而言，修改了部分规范，增加了一些更加确定性的规则说明，这样能最大程度上的让代码风格统一、易读。且本文档的语言描述会更加的精简，文档最后也罗列了一些具体的[新增或修改点](guide/blinkfox-java-style-guide?id=modification-point)。

## 2 源文件基础

- 源文件名必须与顶级（最外层的）类的名称相同，且使用大驼峰（`UpperCamelCase`）的方式来命名，源文件扩展名为 `.java`。
- 源文件使用 **`UTF-8`** 编码。
- 源文件中只能使用**换行符**和**空格符**（ASCII 字符：`0x20`）作为空白符，**禁止使用 `Tab` 制表符**来用于缩进。
- 对于具有[特殊转义序列](http://docs.oracle.com/javase/tutorial/java/data/characters.html)（例如：`\b`, `\t`, `\n`, `\f`, `\r`, `\"`, `\'`, `\\`等）的字符，都采用这种转义字符的方式表示，而不采用对应字符的八进制数（如：`\012`）或 Unicode （如：`\u000a`）来表示。
- 对于其余的非 ASCII 字符，就直接使用 Unicode 字符即可（如：`∞`）

## 3 源文件结构

### 3.1 结构组成

- 源文件按**从上到下的顺序**，由以下几部分组成，且每个部分之间应该**只有一个空行**作为分隔。：
  1. 许可证 (`License`）或版权信息（`copyright`）（该组成部分是可选的）
  2. 包声明（`package`）语句
  3. 包导入（`import`）语句
  4. 类（`class`）声明
- 如果源文件中需要包含许可证或版权信息，那么应该把它放在文件最前面。
- 包声明（`package`）语句和包导入（`import`）语句不受制于列长度限制，都是独立成行。
- 包导入（`import`）语句中**都不使用通配符**），不管是否是静态导入（`static import`）。
- 包导入（`import`）语句按从上到下的顺序分为**静态导入**（`static import`）块和**非静态导入**块两部分，如果同时具有静态和非静态导入，则**使用一个空行将两块分开。每个块的若干导入语句之间不空行，且导入的顺序按名称的 ASCII 顺序排序**。
- 静态内部类中不使用静态导入（`static import`）。
- 每个源文件中只能有一个顶级类（`class`）声明。

### 3.2 类成员顺序

- 类成员的顺序对代码的易读性有很大的影响，这里归纳总结了一些类成员从上到下的建议排列顺序：
  1. 具有 `static final` 关键字修饰的属性；
  2. 具有 `static` 关键字修饰的属性；
  3. 普通字段属性从上到下再按 `public`、`protected`、`default`、`private` 的访问级别顺序排列；
  4. 同访问级别的属性之间再按引用对象类型和原始类型（或包装）类型的顺序排列；
  5. `static {}` 初始化块；
  6. 一个或多个构造方法，从上到下按参数由少到多的顺序排列；
  7. 其他方法；
- **重载方法必须按顺序排列在一起**，中间不要插入其它方法或私有成员。

## 4 格式

### 4.1 语句块的格式

- 在 `if`, `else`, `for`, `do` 和 `while` 等语句中，即使只有一条语句或者内容为空时，也需要写上大括号。
- **对于语句块，大括号都遵循 `K&R` 风格**（Kernighan 和 Ritchie 风格）：
  - 左大括号前不换行
  - 左大括号后换行
  - 右大括号前换行
  - 如果右大括号结束是一个语句块或者方法体、构造函数体或者有命名的类体，则需要换行。当右括号后面接 `else` 或逗号（`,`）时，不换行。示例：

```java
return () -> {
    while (condition()) {
        method();
    }
};

return new MyClass() {
    @Override public void method() {
        if (condition()) {
            try {
                something();
            } catch (ProblemException e) {
                recover();
            }
        } else if (otherCondition()) {
            somethingElse();
        } else {
            lastThing();
        }
    }
};
```

- **对于空语句块，也需要遵循 `K&R` 风格**，如果是**无参构造方法**，内容可以保持为空，或者使用 `super();` 填充内容，或者默认不写该构造方法。如果是其他类型的空方法或空语句块，需要在空语句块中使用注释说明为什么是空的。示例：

```java
// 这是不可以的
void doNothing() {}

// 这是不可以的
void doNothingElse() {
}

// 这是可以的
void doNothingElse() {
    // 这里需要写明内容为空的现实原因和真正意图.
}
```

### 4.2 缩进机制

- **使用 `4` 个空格作为基础尺度来缩进代码**。即每产生一个新的语句块，缩进就增加 `4` 个空格。当这个语句块结束时，缩进恢复到上一层级的缩进级别。此缩进要求对整个语句块中的代码和注释都适用。
- **每条语句结束都需要换行，换行缩进需 `+8` 个空格**。一条语句自动换行时，第一行后的每一行都比上一行多缩进 `8` 个空格。如果两个连续行中使用了同级别的语法元素（如：`.` 操作符），则他们要使用相同的缩进。

代码示例如下：

```java
// 下面的示例演示了一条语句换行时的缩进为 8 个空格.
private void assertMethod(int[] array1, int[] array2, boolean expected) {
    Assertions.assertEquals(expected,
        new LongLongLongTestClassName().callLongLongLongTestMethodName(array1, array2));
}
```

### 4.3 列长度限制

- 代码的**列长度限制为 `120` 个字符**，该“字符”表示任何 Unicode 代码点。但也有一些例外情况：
  - Javadoc 文档注释中的一个长 URL；
  - 如前面所述的 `package` 和 `import` 语句；
  - 注释中的命令行代码，便于剪切粘贴到 shell 中；

### 4.4 换行机制

- 换行有助于使代码更清晰、易读，换行的主要原则是：**选择在更高级的语法逻辑处换行**。包括下面的一些情形：
  - 当在一个非赋值运算的语句换行时，在运算符号之前换行，如：在字符串拼接的 `+` 号前换行。下面的情况也类似：
    - 在点（`.`）操作符之前换行
    - 在方法引用中双冒号（`::`）操作符之前换行
    - 在与或逻辑运算符（`&&`、`||`）之前换行
    - 在 `catch` 块中的管道符号之前换行（`catch (FooException | BarException e)`）
  - 当要在赋值运算符语句处换行时，一般在赋值符号之后换行（即 `=` 号之后换行）。
  - 在增强 for 循环（`foreach`）语句中的冒号（`:`）之后换行。
  - 方法名或构造函数名与仍然左括号（`(`）留在同一行。
  - 逗号（`,`）与其前面的内容留在同一行。
  - 在 `Lambda` 表达式中的箭头旁边永远不要换行，除非当 `Lambda` 表达式的主体由单个无括号的表达式组成时，可能会在箭头之后换行。示例：

```java
MyLambda<String, Long, Object> lambda =
        (String label, Long value, Object obj) -> {
            ...
        };

Predicate<String> predicate = str ->
        longExpressionInvolving(str);
```

- **类成员之间需要单个空行隔开**：例如：字段，构造函数，方法，内部类，静态初始化块，实例初始化块。在类的第一个成员之前或最后一个成员之后，使用空行。两个连续属性之间的空行是可选的，根据需要使用空行来创建字段间的逻辑分组。但是，如果是业务属性字段，强烈建议对其进行文档注释，且这些业务属性字段之间也需要空行。
- **一个空行也可以出现在任何提高可读性的地方**，例如，在一个方法或代码块中，不同的子逻辑之间用空行隔开。
- 不要使用多个连续的空行，**最多只能空一行**。
- 建议在每个 Java 源文件末尾留一个空行，**建议而不强制**。这也是由于“历史原因”引起的，当然如果你有心的话，你会发现 JDK 的源码中全是这样的。

### 4.5 空格机制

除了语法、其他规则、词语分隔、注释和 Javadoc 外，水平的 ASCII 空格只在以下情况出现：

- 所有保留的关键字与紧接它之后同一行的左小括号（`(`）之间需要用空格隔开，例如：`if`, `for` `catch`。
- 所有保留的关键字与在它之前的右大括号（`}`）之间需要空格隔开，例如：`else`、`catch`。
- 在左大括号之前都需要空格隔开。
  - **例外情况**：`@SomeAnnotation({a, b})`(不使用空格）。
  - **例外情况**：`String[][] x = {{"foo"}};`（第二个 `{` 之前不使用空格）。
- 所有的二元和三元运算符的两边，都需要使用空格隔开。以下“类似操作符”的情况也适用：
  - 连接泛型类型的 `&` 符号：`<T extends Foo & Bar>`
  - 处理多个异常 `catch` 中的管道操作符：`catch (FooException | BarException e)`
  - 增强 `for` 循环（`foreach`）中的冒号：`:`
  - `Lambda` 表达式中的箭头符号（`->`）
  例外情况：
  - 方法引用中的双冒号（`::`），写法类似于 `Object::toString`
  - 点号操作符（`.`），写法类似于 `object.toString()`
- 逗号(`,`)、冒号(`:`)、分号(`;`)和右小括号(`)`)、Lambda箭头符号(`->`)之后，需要空格隔开。
- 使用`//`双斜线开始一行注释时，双斜线两边都应该用空格隔开。并且可使用多个空格。（可选的，例如：`a = 0; // 赋值为0`）
- 在声明的变量类型和变量名之间需要用空格隔开：`List<String> list`
- 初始化一个数组时，大括号内的空格是可选的。
  - `new int[] {5, 6}` 和 `new int[] { 5, 6 }` 这两种都是可以的
- 在类型注解和 `[]` 或者 `...` 之后使用空格。

示例如下：

```java
/**
 * 这是一个用来演示代码格风格和格式的测试示例类.
 *
 * <p>注意：你不需要去管代码逻辑，着重看代码块、缩进、每一个空行以及空格等的使用.</p>
 *
 * @author intelij idea on 2019-09-25.
 * @author blinkfox on 2020-03-18.
 * @since v1.2.0
 */
@Slf4j
@Annotation(param1 = "value1", param2 = "value2")
@SuppressWarnings( {"ALL"})
public class Foo<T extends Bar & Abba, U> {

    /**
     * 这是一个用于 xxxx 目的的数组.
     */
    public static final int[] X = new int[] {1, 3, 5, 6, 7, 87, 1213, 2};

    private int[] empty = new int[] {};

    /**
     * 这是一个用于测试的测试示例方法，你不需要去管代码逻辑.
     *
     * @param x 变量 x
     * @param y 变量 y
     * @author intelij idea on 2019-09-25.
     * @since v1.2.0
     */
    public void foo(int x, int y) {
        Runnable run1 = () -> {
            System.out.println("Hello World!");
            System.out.println("Test Lambda.");
        };
        Runnable run2 = this::bar;

        // 遇到晦涩难懂的代码，你需要写明注释，为什么要这么写.
        for (int i = 0; i < x; i++) {
            y += (y ^ 0x123) << 2;
        }

        // 这是这下面这一块儿处理逻辑的注释.
        try (MyResource r1 = getResource();
                FileInputStream r2 = new FileInputStream(r1);
                BufferedOutputStream r3 = new BufferedOutputStream(r2)) {
            if (0 < x && x < 10) {
                while (x != y) {
                    x = f(x * 3 + 5);
                }
            } else {
                // 一些重要的业务逻辑处理，你也需要写明注释.
                synchronized (this) {
                    switch (e.getCode()) {
                        //...
                    }
                }
            }
        } catch (MyException | MyException2 e) {
            log.error("This is your error msg.", e);
        } catch (YourException expected) {
            // 如果异常被命名为 'expected' 或者 'ignore'，则意味着可以不处理，但需用注释写明原因.
        } finally {
            int[] arr = (int[]) g(y);
            x = y >= 0 ? arr[y] : -1;
            Map<String, String> sMap = new HashMap<String, String>();
            Bar.<String, Integer>mess(null);
        }

        // 这是一个新的子逻辑代码块，也需要写明注释.
        while (true) {
            if (empty.length == 0) {
                break;
            }
        }
    }

}

```

### 4.6 不使用水平对齐

- **尽量不使用水平对齐**。水平对齐是指通过添加多个空格，使本行的某一符号与上一行的某一符号上下对齐。以下是没有水平对齐和做了水平对齐的例子：

```java
// 不使用水平对齐.
private int x; // 这种挺好
private Color color; // 同上

// 使用了水平对齐.
private int   x;     // 不推荐，未来变量名修改了，会继续编辑，增加了维护难度.
private Color color; // 可能会使它对不齐
```

> **提示**：水平对齐虽然可以提高代码的可读性，但是增加了未来维护代码的麻烦。考虑到维护时只需要改变一行代码，之前的对齐可以不需要改动。为了对齐，你更有可能改了一行代码，同时需要更改附近的好几行代码，而这几行代码的改动，可能又会引起一些为了保持对齐的代码改动。那原本这行改动，我们称之为**爆炸半径**。这种改动，在最坏的情况下可能会导致大量的无意义的工作，即使在最好的情况下，也会影响版本历史信息，减慢代码 `review` 的速度，引起更多合并代码的冲突。

### 4.7 其他格式

- **推荐使用分组小括号**。除非作者和审阅人（`reviewer`）都认为去掉小括号也不会使代码被误解，或是去掉小括号能让代码更易于阅读，否则我们不应该去掉小括号。
- **枚举常量间用逗号隔开，多个枚举实例之间需要空行**。由于每个枚举实例默认都是 `public static final` 的，所以也需要附加文档注释，且需要换行，并添加额外的空行。示例如下：

```java
public enum MyEnum {

    /**
     * 枚举实例 1 的注释.
     */
    INSTANCE1,

    /**
     * 枚举实例 2 的注释.
     */
    INSTANCE2,

    /**
     * 枚举实例 3 的注释.
     */
    INSTANCE3

    // 其他方法...
}
```

- **每个变量声明（字段或局部变量）只声明一个变量**，不使用诸如 `int a, b;` 之类的声明。
  - **例外情况**: `for` 循环的初始化头中可以使用多个变量声明。如：`for (int i = 0, len = arr.length; i < len; i++) {`
- **局部变量在需要使用它的地方再声明**。不要在代码块的开头把所有局部变量一次性全部进行声明（不同于 C 语言），而是在第一次需要使用它时才声明，这样能最大程度地减小其作用范围。局部变量在声明时最好就进行初始化，或者声明后尽快进行初始化。
- 数组的类型不同于 C 语言，中括号是类型的一部分：`String[] args`， 而非 `String args[]`。
- switch 大括号之后缩进 `4` 个字符。每个 switch `case` 语句之后，有一个换行符，并按照大括号相同的处理方式缩进 `4` 个字符。在某个 `case` 结束后，恢复到之前的缩进，类似大括号的结束符。
- 每个 switch 语句中，都需要显式声明 `default` 语句，即使没有任何代码。
- 注解应用到类、属性、构造方法、方法上时，应紧接 Javadoc 之后，且独立成行。如果注解应用在参数或者局部变量上时，建议与他们写到一行。示例：

```java
// 推荐写法. 多个注解独立成行。
@Override
@Nullable
public String getNameIfPresent() { ... }

// 不推荐. (注解和成员在同一行了)
@Override public int hashCode() { ... }

// 不推荐. (多个注解放在了同一行了)
@Partial @Mock
private DataLoader loader;

// 推荐写法，注解修饰参数或局部变量时，放在同一行.
public ModelAndView getUserInfo(
        @RequestParam("userId") String userId,
        @RequestParam("userName") String userName) {
    // this is content.
}
```

- 长整型（`long`）的数字字面量使用大写的 `L` 作为后缀，不得使用小写（避免与数字 `1` 混淆）。例如：使用 `3000000000L`，而不是 `3000000000l`。
- **不使用行尾注释**，建议在代码行或逻辑行之上做单行注释即可。
- 类和成员变量的修饰符，按 `Java Lauguage Specification` 中介绍的先后顺序排序。具体是：

```java
public protected private abstract default static final transient volatile synchronized native strictfp
```

## 5 命名

### 5.1 驼峰命名法（CamelCase）的定义

**驼峰式命名法**分大驼峰式命名法（`UpperCamelCase`）和小驼峰式命名法（`lowerCamelCase`）。有时，我们有多种合理的方式将一个英语词组转换成驼峰形式，如缩略语或不寻常的结构，例如：`IPv6` 或 `iOS`。以下是一个（几乎）确定性的转换方案。

名字从散文形式（`prose form`）开始:

1. 把短语转换为纯 ASCII 码，并且移除任何单引号。例如：`Müller’s algorithm` 将变成 `Muellers algorithm`。
2. 把这个结果切分成单词，在空格或其它标点符号（通常是连字符）处分割开。
  - **推荐**：如果某个单词已经有了常用的驼峰表示形式，按它的组成将它分割开(如`AdWords`将分割成`ad words`)。需要注意的是 `iOS` 并不是一个真正的驼峰表示形式，因此该推荐对它并不适用。
3. 现在将所有字母都小写(包括缩写)，然后将单词的第一个字母大写：
  - 每个单词的第一个字母都大写，来得到大驼峰式命名。
  - 除了第一个单词，每个单词的第一个字母都大写，来得到小驼峰式命名。
4. 最后将所有的单词连接起来得到一个标识符。

请注意，几乎完全忽略了原始单词的大小写。示例：

| 散文形式  | 正确  | 不正确  |
| ------------ | ------------ | ------------ |
| "XML HTTP request"  | XmlHttpRequest  | XMLHTTPRequest  |
| "new customer ID"  | newCustomerId  | newCustomerID  |
| "inner stopwatch"  | innerStopwatch  | innerStopWatch  |
| "supports IPv6 on iOS?"  | supportsIpv6OnIos  | supportsIPv6OnIOS  |
| "YouTube importer"  | YouTubeImporter YoutubeImporter*  | 无   |

加 `*` 号处表示可以接受，但不推荐。

> **注意**：在英语中，某些带有连字符的单词形式不唯一。例如：`nonempty` 和 `non-empty` 都是正确的，因此方法名`checkNonempty`和`checkNonEmpty`也都是正确的。

### 5.2 类名、方法名

- 标识符只能使用 ASCII 字母和数字，不使用特殊的前缀或后缀，如`name_`, `cName`, `c_name`。
- **包名全部小写，连续的单词只是简单地连接起来，不使用下划线**。例如：使用 `com.example.deepspace`，而不是 `com.example.deepSpace` 或者 `com.example.deep_space`。
- 类名、接口名、枚举类名、注解名都以大驼峰（`UpperCamelCase`）风格编写，但领域模型实体类（以 DO、DTO、BO、VO 等结尾）仍然可以使用连续大写，不建议大量使用 `DO`、`DTO`、`VO` 等，视业务情况使用，且考虑建立公共属性的父类，使用**继承**的方式，减少重复属性的定义和不同类之间的属性复制。
- 抽象类命名使用 `Abstract` 开头，枚举类后建议以 `Enum` 或 `Type` 结尾，枚举实例值的单词之间用下划线（`_`）来分割。异常类命名使用`Exception`结尾；测试类命名以它要测试的类的名称开始，以 `Test` 结尾。接口名**不建议**使用 `I` 作为前缀，跟 JDK 学习，应该考虑使用名词或形容词。
- 类名、枚举类名通常是名词或名词短语。接口名称也可以是名词或名词短语，也可以是形容词或形容词短语（例如：`Readable`）。自定义注解名可以是名称，动词或形容词。
- 属性名、方法名、参数名、局部变量名都以小驼峰（`lowerCamelCase`）风格编写。属性名通常是是名称，方法名是动词或动词短语。
- 泛型名以单个的大写字母命名（如：`E`, `K`, `V`）。
- 类名、属性名、方法名等通常使用英文单词或短语来命名。但是，如果该词语是某些**专业领域**的中文名称（如：政法相关的内容），且**难以翻译**成简单而又准确的英语单词时，不推荐直接使用英文或中文汉字来命名，而是使用**中文拼音**或**中文简拼**，并且写上准确含义 Javadoc 注释。

### 5.3 常量名

- 常量名的命名模式为 `CONSTANT_CASE`，字母全部大写，使用下划线来分隔单词，且常量的名字通常是名词或名词短语。

每个常量都是一个静态 `final` 字段，其内容是不可变的，且没有可检测的副作用。这包括原始类型、字符串、不可变类型和不可变类型的不可变集合。如果任何一个实例的观测状态是可变的，则它肯定不会是一个常量。**不可变对象不一定是常量**。例如：

```java
// 下面是常量.
static final int NUMBER = 5;
static final ImmutableList<String> NAMES = ImmutableList.of("Ed", "Ann");
static final ImmutableMap<String, Integer> AGES = ImmutableMap.of("Ed", 35, "Ann", 32);
static final Joiner COMMA_JOINER = Joiner.on(','); // because Joiner is immutable
static final SomeMutableType[] EMPTY_ARRAY = {};
enum SomeEnum { ENUM_CONSTANT }

// 下面的情况不是常量.
static String nonFinal = "non-final";
final String nonStatic = "non-static";
static final Set<String> mutableCollection = new HashSet<String>();
static final ImmutableSet<SomeMutableType> mutableElements = ImmutableSet.of(mutable);
static final ImmutableMap<String, SomeMutableType> mutableValues =
    ImmutableMap.of("Ed", mutableInstance, "Ann", mutableInstance2);
static final Logger logger = Logger.getLogger(MyClass.getName());
static final String[] nonEmptyArray = {"these", "can", "change"};
```

> **注**：根据这里常量的定义，阿里规约插件常量识别机制是有问题的，阿里规约插件认为只要有**static final**修饰符的就是常量，必须要大写，曲解了常量。

## 6 编程实践

- 只要是合法的方法，就要加上 `@Override` 注解，这包括重写超类方法的类方法，实现接口方法的类方法以及重新指定父类接口方法的接口方法。**例外**：如果父方法为 `@Deprecated` 时，可以省略 `@Override`。
- 不能忽视捕获的异常，**例外**：如果一个捕获的异常被命名为 `expected` 或者 `ignore`，则它可以不用处理，但仍然需要使用注释说明原因。

```java
try {
    emptyStack.pop();
    fail();
} catch (NoSuchElementException expected) {
}
```

- 使用类名调用静态的类成员，而不是具体某个对象或表达式。示例如下：

```java
Foo aFoo = ...;
Foo.aStaticMethod(); // 赞同的方式.
aFoo.aStaticMethod(); // 糟糕的方式.
somethingThatYieldsAFoo().aStaticMethod(); // 非常糟糕的方式.
```

- 极少会去重写 `Object.finalize`。不要这么做。如果你非要使用它，请先仔细阅读和理解 [Effective Java](http://books.google.com/books?isbn=8131726592) 第 7 条：“Avoid Finalizers”，然后不要使用它。

## 7 Javadoc

### 7.1 如何写文档注释

Javadoc 即文档注释，可以是多行，也可以是单行。当被注释的 Javadoc 只有简单的文字描述时，可以使用单行形式。请注意，这仅适用于没有 `@param`、`@return` 等块标记的情况。Javadoc 块的基本格式如下:

```java
/**
 * Javadoc 摘要示例.
 *
 * <p>这是详细描述示例，这是行内注释的代码 {@code int} 的使用.
 * 这是注释内链接 {@link java.util.concurrent.atomic} 的使用示例.</p>
 *
 * 这是一个 HTML <a href="http://baidu.com">超链接</a>的使用示例.
 *
 * @param p1 参数说明
 * @return 返回值说明
 * @see java.util.concurrent.atomic.AtomicInteger
 * @since v1.2.0
 * @author blinkfox add on 2020-03-20.
 * @author zhangshan modify on 2020-04-05.
 */
public int method(String p1) { ... }
```

或者是以下单行形式：

```java
/** 一小段的 Javadoc. */
```

- 每个 Javadoc 都以一个简短的**摘要片段**开始，用于对所注释的类、属性、方法等进行简要的描述。并以**英文句号**（`.`）结尾。因为 Java 在生成 Javadoc 时，会以注释中第一个英文句号作为拆分点，解析出摘要片段和详细描述信息。
- 空行是只包含最左侧星号（`*`）的空行会出现在段落之间和 Javadoc 注解标记（`@XXX`）之前（如果有的话）。除了第一个段落，每个段落第一个单词前都有标签`<p>`，并且它和第一个单词间没有空格。
- Javadoc 中几个重要的注解标记：
  - 对于具有泛型参数的类或方法，对于具有参数的方法，Javadoc 中都必须加上 `@param` 注解标记和对应的注释；
  - 对于具有返回值的方法，Javadoc 中都必须加上 `@return` 注解标记和对应的注释；
  - 如果方法签名中有抛出异常，则必须有 `@throws` 注解标记和对应的注释；
  - 对于要弃用的类或方法，则使用 `@deprecated` 注解标记，并解释为什么弃用的原因。
  - 如果你的类、接口、方法等需要参考或关联其他类或接口，可以使用 `@see` 标签，在 Javadoc 注释内容中，则可以通过 `@link` 去链接，但注意，`@link` 只能链接该类或方法可访问类、属性或方法。
  - 当是第一次新增类或方法时。需要再新增的类或方法上额外加上作者、日期和新增该代码时的版本的注解，即要加上 `@author`、`@since` 注解，其中 `@author` 可以写多个，由于 Javadoc 中没有日期相关的 Javadoc 标签，建议将日期写在 `@author`的作者之后，格式形如：`@author blinkfox add on 2020-03-20.`。**注**：如果写上 Javadoc 不支持的 `@date` 标签，在生成 Javadoc 时会报错，且 `@date` 所表示的日期与具体的作者难以对应上。
- 标准的 Javadoc 注解标记按以下顺序出现：`@param`, `@return`, `@throws`, `@deprecated`、`@author`, `@see`, `@since`, 这几种标记如果出现，描述都不能为空。当描述无法在一行中容纳，连续行需要至少在 `@` 符号的位置处缩进 `4` 个空格。
- 行内的注释中如果包含“代码”，则可以使用 `@code` 包裹起来.

### 7.2 什么时候需要写 Javadoc

- 在每个 `public` 类及它的每个 `public` 和 `protected` 成员处使用 Javadoc。
- 对于**具有业务含义**的属性字段建议写上 Javadoc。
- 对于包外不可见的类和方法，如有需要，也是要使用 Javadoc 的。
- 如果一个注释是用来定义一个类，方法，字段的整体目的或行为，即时是 `private` 的，那么这个注释应该写成 Javadoc，这样更统一、更友好。

### 7.3 可以不需要 Javadoc 的例外情况

- 对于 `getter` 和 `setter` 方法，Javadoc 是可选的，但建议在其字段属性上写明 Javadoc 注释，比如：`canonicalName` 属性，如果不加注释的话，可能后续的维护者难以理解其真正含义。
- 如果一个方法重写了超类中的方法，那么 Javadoc 也是可选的。

### 7.4 JDK 源码中的 Javadoc 示例

下面是 JDK 源码中 `Callable` 接口的 Javadoc 示例，大家也可以参考借鉴下：

```java
/**
 * The {@code String} class represents character strings. All
 * string literals in Java programs, such as {@code "abc"}, are
 * implemented as instances of this class.
 * <p>
 * Strings are constant; their values cannot be changed after they
 * are created. String buffers support mutable strings.
 * Because String objects are immutable they can be shared. For example:
 * <blockquote><pre>
 *     String str = "abc";
 * </pre></blockquote><p>
 * is equivalent to:
 * <blockquote><pre>
 *     char data[] = {'a', 'b', 'c'};
 *     String str = new String(data);
 * </pre></blockquote><p>
 * Here are some more examples of how strings can be used:
 * <blockquote><pre>
 *     System.out.println("abc");
 *     String cde = "cde";
 *     System.out.println("abc" + cde);
 *     String c = "abc".substring(2,3);
 *     String d = cde.substring(1, 2);
 * </pre></blockquote>
 * <p>
 * The class {@code String} includes methods for examining
 * individual characters of the sequence, for comparing strings, for
 * searching strings, for extracting substrings, and for creating a
 * copy of a string with all characters translated to uppercase or to
 * lowercase. Case mapping is based on the Unicode Standard version
 * specified by the {@link java.lang.Character Character} class.
 * <p>
 * The Java language provides special support for the string
 * concatenation operator (&nbsp;+&nbsp;), and for conversion of
 * other objects to strings. String concatenation is implemented
 * through the {@code StringBuilder}(or {@code StringBuffer})
 * class and its {@code append} method.
 * String conversions are implemented through the method
 * {@code toString}, defined by {@code Object} and
 * inherited by all classes in Java. For additional information on
 * string concatenation and conversion, see Gosling, Joy, and Steele,
 * <i>The Java Language Specification</i>.
 *
 * <p> Unless otherwise noted, passing a <tt>null</tt> argument to a constructor
 * or method in this class will cause a {@link NullPointerException} to be
 * thrown.
 *
 * <p>A {@code String} represents a string in the UTF-16 format
 * in which <em>supplementary characters</em> are represented by <em>surrogate
 * pairs</em> (see the section <a href="Character.html#unicode">Unicode
 * Character Representations</a> in the {@code Character} class for
 * more information).
 * Index values refer to {@code char} code units, so a supplementary
 * character uses two positions in a {@code String}.
 * <p>The {@code String} class provides methods for dealing with
 * Unicode code points (i.e., characters), in addition to those for
 * dealing with Unicode code units (i.e., {@code char} values).
 *
 * @author  Lee Boynton
 * @author  Arthur van Hoff
 * @author  Martin Buchholz
 * @author  Ulf Zibis
 * @see     java.lang.Object#toString()
 * @see     java.lang.StringBuffer
 * @see     java.lang.StringBuilder
 * @see     java.nio.charset.Charset
 * @since   JDK1.0
 */
public final class String
    implements java.io.Serializable, Comparable<String>, CharSequence {

    ...
}

```

## 8 相对于 Google 风格指南的修改点 :id=modification-point

本文档相对于 Google 的 Java 编程风格文档而言，修改了部分规范，增加了一些更加确定性的规则说明，这样能最大程度上的让代码风格统一、易读。且本文档的语言描述会更加的精简。

### 8.1 [新增] 类成员的排列顺序约定

> **新增原因**：相比 Google 风格指南文档中“模糊而又正确”的说法，采用了更为“确定性、可明确实施的”约定。

### 8.2 [新增] 不使用行尾注释，建议在代码行或逻辑行之上做单行注释即可

> **新增原因**：不容易首先阅读到，且行尾注释会占列长度，且容易导致超过列长度限制，后期维护注释麻烦。

### 8.3 [新增] 命名规则建议

抽象类命名使用 `Abstract` 开头，枚举类后建议以 `Enum` 或 `Type` 结尾，枚举实例值的单词之间用下划线（`_`）来分割。异常类命名使用`Exception`结尾；测试类命名以它要测试的类的名称开始，以 `Test` 结尾。接口名**不建议**使用 `I` 作为前缀，跟 JDK 学习，应该考虑使用名词或形容词。

> **新增原因**：对类更易于识别。

### 8.4 [新增] 命名选择使用英文单词还是中文词语

> **新增原因**：本人及工作的团队也都是中国人，在编程领域的命名通常使用英文单词即可。但如果涉及到专业领域的词汇，翻译不出简单而又准确的英文时，需要考察实际情况选择使用中文拼音或中文简拼来命名，且附上准确的 Javadoc 注释，不要使用晦涩难懂或存在严重歧义的英文单词。

### 8.5 [新增] 新增了换行时，末尾以空行结尾的建议

> **新增原因**：这只是一个建议的写法，非强制，这样写也是由于“历史原因”引起的，我这里的目的也是跟 Java 源码的风格保持一致。

### 8.6 [新增] 新增了 `@author`、`@since`、`@see` 等 Javadoc 标签

> **新增原因**：在写 Javadoc 时这些注解也是十分有用的，便于大家写出更好的 Javadoc 注释代码。

### 8.7 [修改] 如果空语句块，也需要遵守 `K&R` 风格，且增加了语句为空的理由注释

> **修改原因**：更加明确的规范，利于大家写出“更加一致”的代码，且有助于后来人理解为什么要保持语句块是空的。

### 8.8 [修改] 缩进空格数改为了 4 个空格

> **修改原因**：根据我实际的编程经验，`2` 个空格的代码缩进，在当前大屏幕流行的时代，会显得十分拥挤（挤成一坨），反而使得代码`臃肿`不够美观。所以，我这里建议使用`4`个空格来缩进，会使得更加美观、协调，而且能侧面督促开发人员减少代码的嵌套层数。

### 8.9 [修改] 列长度限制改为了 120 个字符

> **修改原因**：Java 相比其他编程语言而言，类和方法的命名都要“长”很多，这样所占的列数就更多，如果列限制仅设置为 `100` 的话，代码被折行的几率会更大。且在当前大屏幕流行的时代，屏幕右边会空出一大部分空间并没有被代码真正的填充，简直就是浪费，而且使得阅读代码时，视线会重复“跳行”。

### 8.10 [修改] 枚举实例之间需要换行，且附加文档注释

> **修改原因**：枚举本质上也是类，所以，类中的很多规则也适用于枚举。由于枚举实例都是 `public static final` 的，所以，也必然需要添加 Javadoc 注释，所以，实例与实例之间需要使用换行来分割。

### 8.11 [修改] 注解是否换行

注解应用到类、属性、构造方法、方法上时，应紧接 Javadoc 之后，且独立成行。如果注解应用在参数或者局部变量上时，建议与他们写到一行。

> **修改原因**：易于识别和阅读。

### 8.12 [修改] 领域模型类的命名可以使用连续大写

类命名如果是以 `DO`、`DTO`、`BO` 等结尾的领域模型类时，可以考虑使用连续大写。

> **修改原因**：这里修改的主要原因是为了“**兼容**”阿里规约，但我本人建议少使用这些对象，不仅刻板，而且会造成大量的重复代码、属性来回的设值或复制等，后续维护也要同时维护多个实体。程序员虽然遵循规范，但也不应该是机械的，应该视具体的业务场景建立对应的相关实体类和该类的专属行为。所以，我的个人建议是除了数据库层的实体类，其他实体类应该使用“充血模型”，这样更加面向对象编程。因此考验程序员的难点就成了“如何设计充血模型”，或者说“如何从复杂的业务中分离出恰到好处的逻辑行为放到 `VO` 之类的方法中”。但如果你的团队程序员以初级工程师为主，且业务都是 `CRUD` 的话，可以规范使用“贫血模型”。

### 8.13 [修改]泛型名都以单个大写字母开头

> **修改原因**：跟 JDK 中的命名风格保持一致。
