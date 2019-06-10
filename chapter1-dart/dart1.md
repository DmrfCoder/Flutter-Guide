@[toc]
### 变量

下面是声明变量并赋值的方法：

```dart
var name = 'Bob';
```

变量实际上是一个引用，上面名字为 `name` 的变量引用了 一个内容为 `“Bob”` 的 `String` 对象。

#### 默认值

没有初始化的变量会自动获取一个默认值 `null`。类型为数字的变量如果没有初始化其值也是 `null`，因为数字类型也是对象。

```dart
int lineCount;
assert(lineCount == null);
```

**注意：** `assert()`起到断言的作用，当`assert()`中的条件为`true`时语句会正常执行，如果条件为`false` 则会抛出一个异常。

#### 可选的类型

在声明变量的时候可以选择性地加上具体的类型，比如：

```dart
String name = 'Bob';
```

添加类型可以更加清晰的表达你的意图。 `IDE` 编译器等工具有可以通过你声明的类型为你提供代码补全、提前发现 `bug` 等功能。

**注意：**根据 **代码风格推荐** 部分的建议，对于局部变量我们应该使用 `var` 而不是具体的类型。

#### final以及const

如果你之后不打算修改某一个变量，则应该使用 `final` 或者 `const`修饰该变量。 一个 `final` 变量只能赋值一次，一个 `const` 变量是编译时常量（`const` 变量是特殊的 `final` 变量）， 顶级的 final 变量或者类中的 final 变量在 第一次使用的时候初始化。

这里可能有读者会有疑惑，什么是编译时常量？我们用一个例子来说明final和const的真正区别：

```dart
buildName() {
  return 'running name';
}

finalAndConstDemo() {
  final finalName = buildName();
  const constName = 'Joey';
}
```

如上代码，`buildName()`是一个方法，作用是返回一个字符串，注意，只有在程序真正运行的时候才会知道这个`buildName()`方法到底返回的是什么，即这个`buildName()`方法返回的不是"编译时常量"，因此，不能使用其对`const`修饰的变量进行赋值，但是，`final`的要求比`const`低一点，它只要求只能对其修饰的变量赋值一次，至于是编译时赋值还是运行时赋值是不要求的，所以上面的代码块会通过编辑并正确执行，倘若使用：

```dart
const constName=buildName()
```

则编译器会毫不留情地报错，因为`buildName()`的返回值不能再编译时被确定。

如果 `const` 变量在类中，则应该定义为 `static const`。 可以直接定义 `const` 和其值，也可以使用其他 `const` 变量的值来初始化一个 `const` 变量：

```dart
const bar = 1000000;       
const atm = 1.01325 * bar; 
```

`const` 关键字不仅可以用来修饰常量，还可以用来修饰构造函数，这种类型的构造函数创建的对象是不可改变的，比如：

```dart
var foo = const [];   
final bar = const []; 
const baz = const []; 
```

`[]` 的作用是创建一个空的`list`，`const []` 的作用是创建一个空的、不可改变的`list`（`empty, immutable list (EIA)`），对上面的三行代码解释如下：

1. 第一行代码比表示`foo`当前引用的是一个空的、不可改变的`list`，但是我们可以在之后让`foo`引用其他对象，比如`foo=name`，这是合法的，但是在此之前不能进行像`foo.add("name")`这样的操作，因为此时foo指向的是一个不可改变的`list`。
2. 第二行代码表示`bar`将会一直是一个空的、不可改变的`list`，在之后的代码中不能对`bar`进行修改操作，例如`bar.add("name")`将会抛出异常。
3. 第三行代码表示`baz`将会在编译时就是一个空的、不可改变的`list`，在之后的代码中不能对`baz`进行修改操作，例如`baz.add("name")`将会抛出异常。

#### 内置的类型

Dart 内置支持下面这些类型：

- numbers
- strings
- booleans
- lists (也被称之为 arrays)
- maps
- runes (用于在字符串中表示 Unicode 字符)
- symbols

你可以直接使用字面量来初始化上面的这些类型。 例如 `'this is a string'` 是一个字符串字面量， `true` 是一个布尔字面量。

由于 Dart 中每个变量引用的都是一个对象 （一个类的实例）， 所以你通常应该使用构造函数来初始化变量。 一些内置的类型具有自己的构造函数。例如， 可以使用 `Map()`构造函数来创建一个 map， 比如： `new Map()`。

##### Numbers（数值）

Dart 支持两种类型的数字：

- `int`

  整数值，其取值通常位于 `-253` 和 `253` 之间。

- `double`

  `64-bit` (双精度) 浮点数，符合 `IEEE 754` 标准。

`int` 和 `double` 都是 `num`的子类。 `num` 类型定义了基本的操作符，例如 `+, -, /,  *`， 还定义了 `abs()`、` ceil()`、和 `floor()` 等函数。 (位操作符，例如 `>>` 定义在 `int` 类中) 如果 `num` 或者其子类型具有的的基本运算不满足你的需求，请查找 `dart:math`（地址：`https://api.dartlang.org/stable/dart-math/dart-math-library.html`）库以寻找更多的运算支持。

整数是不带小数点的数字。下面是一些定义整数的方式：

```dart
var x = 1;
var hex = 0xDEADBEEF;
var bigInt = 34653465834652437659238476592374958739845729;
```

如果一个数带小数点，则其为 `double`， 下面是定义 `double` 的一些方式：

```dart
var y = 1.1;
var exponents = 1.42e5;

```

下面是字符串和数字之间转换的方式：

```dart
// String -> int
var one = int.parse('1');
assert(one == 1);

// String -> double
var onePointOne = double.parse('1.1');
assert(onePointOne == 1.1);

// int -> String
String oneAsString = 1.toString();
assert(oneAsString == '1');

// double -> String
String piAsString = 3.14159.toStringAsFixed(2);
assert(piAsString == '3.14');

```

整数类型支持传统的位移操作符，比如`左移（<<）`,`右移(>>)`, `与 (&)`, `或 (|)`：

```dart
assert((3 << 1) == 6);  // 0011 << 1 == 0110
assert((3 >> 1) == 1);  // 0011 >> 1 == 0001
assert((3 | 4)  == 7);  // 0011 | 0100 == 0111

```

数字字面量为编译时常量。 很多算术表达式只要其操作数是常量，则表达式结果也是编译时常量：

```dart
const msPerSecond = 1000;
const secondsUntilRetry = 5;
const msUntilRetry = secondsUntilRetry * msPerSecond;

```

如上代码，`msPerSecond`和`secondsUntilRetry`在编译时时常量，所以由`secondsUntilRetry * msPerSecond`赋值的`msUntilRetry`在编译时也会是常量。

##### Strings（字符串）

Dart 字符串是 `UTF-16` 编码的字符序列。 可以使用单引号或者双引号来创建字符串：

```dart
var s1 = 'Single quotes work well for string literals.';
var s2 = "Double quotes work just as well.";
var s3 = 'It\'s easy to escape the string delimiter.';
var s4 = "It's even easier to use the other delimiter.";

```

可以在字符串中使用表达式，用法为： `${expression}`。

如果表达式是一个变量，可以省略 `{}`。 如果表达式的结果为一个对象，则 Dart 会调用对象的 `toString()` 函数来获取一个字符串。

```dart
var s = 'string interpolation';

assert('Dart has $s, which is very handy.' ==
       'Dart has string interpolation, ' +
       'which is very handy.');
assert('That deserves all caps. ' +
       '${s.toUpperCase()} is very handy!' ==
       'That deserves all caps. ' +
       'STRING INTERPOLATION is very handy!');

```

**注意：** `==` 操作符判断两个对象的**内容**是否一样。 如果两个字符串包含一样的字符编码序列， 则他们是相等的。

拼接字符串有两种方法：

1. 使用 `+` 操作符来把多个字符串链接为一个
2. 直接把多个字符串放到一起

```dart
var s1 = 'String ' 'concatenation'
         " works even over line breaks.";//直接将字符串放在一起，会进行自动拼接
assert(s1 == 'String concatenation works even over '
             'line breaks.');

var s2 = 'The + operator '
         + 'works, as well.';//使用+运算符来进行字符串拼接
assert(s2 == 'The + operator works, as well.');

```

使用三个单引号或者三个双引号来创建多行字符串对象：

```dart
var s1 = '''
You can create
multi-line strings like this one.
''';

var s2 = """This is also a
multi-line string.""";

```

通过提供一个 `r` 前缀可以创建一个原始`(raw）`字符串，即字符串中的转义字符会不起作用：

```dart
stringDemo() {
  var s1 = r'\n1234';
  var s2 = '\n1234';
  print('s1:' + s1);
  print('s2:' + s2);
}
```

上面代码的执行结果为：

![image-20190530214504777](http://picture-pool.oss-cn-beijing.aliyuncs.com/2019-06-06-092813.png)

可以看到，加了`r`前缀后`\n`中的转义符`\`失效了。

##### Booleans（布尔值）

为了代表布尔值，Dart 有一个 `bool` 类型。 只有两个对象是布尔类型的：`true` 和 `false` ， 这两个对象也都是编译时常量。

当 Dart 需要一个布尔值的时候，只有 `true` 对象才被认为是 `true`。 所有其他的值都是 `flase`，像 `1`、 `"aString"`、 以及 `someObject` 等值都被认为是 `false`。

例如：

```dart
var name = 'Bob';
if (name) {
  print('You have a name!');
}
```

在Dart**生产模式**下运行上述代码，不会输出`'You have a name!'`，因为`name`的值不是`true`，所以就会认为是`false`。

##### Lists（列表）

 `array` （或者有序集合）几乎是所有编程语言中最常见的集合类型。  Dart 中的`List`对象指的就是数组，通常我们都称之为 `lists`。

下面是一个 Dart `list` 的示例：

```dart
var list = [1, 2, 3];

```

`Lists` 的下标索引从 0 开始，第一个元素的索引是 0，最后一个元素的索引是`list.length - 1` 。 

List类还有很多操作，我会在后面讲解泛型和集合的时候展开叙述。

##### Maps

通常来说，`Map` 是一个键值对相关的对象。 键和值可以是任何类型的对象。每个键只出现一次， 而一个值则可以出现多次。Dart 通过 `map` 字面量 和 `Map`类支持 `map`。

下面是一些创建简单 `map` 的示例：

```dart
var gifts = {
	// Keys      Values
  'first' : 'partridge',
  'second': 'turtledoves',
  'fifth' : 'golden rings'
};

var nobleGases = {
	// Keys  Values
  2 :   'helium',
  10:   'neon',
  18:   'argon',
};

```

使用 `Map` 构造函数也可以实现同样的功能：

```dart
var gifts = new Map();
gifts['first'] = 'partridge';
gifts['second'] = 'turtledoves';
gifts['fifth'] = 'golden rings';

var nobleGases = new Map();
nobleGases[2] = 'helium';
nobleGases[10] = 'neon';
nobleGases[18] = 'argon';

```

往 `map` 中添加新的键值对：

```dart
var gifts = {'first': 'partridge'};
gifts['fourth'] = 'calling birds'; // Add a key-value pair

```

获取 `map` 中的对象：

```dart
var gifts = {'first': 'partridge'};
assert(gifts['first'] == 'partridge');

```

如果所查找的键不存在，则返回 `null`：

```dart
var gifts = {'first': 'partridge'};
assert(gifts['fifth'] == null);

```

使用 `.length` 来获取 `map` 中键值对的数目：

```dart
var gifts = {'first': 'partridge'};
gifts['fourth'] = 'calling birds';
assert(gifts.length == 2);

```

同样使用 `const` 可以创建一个 编译时常量的 `map`：

```dart
final constantMap = const {
  2: 'helium',
  10: 'neon',
  18: 'argon',
};

// constantMap[2] = 'Helium'; // Uncommenting this causes an error.

```

##### Runes

在`Dart`中，`Runes`代表字符串的`UTF-32`字符集, `Unicode`为每一个字符、标点符号、表情符号等都定义了一个唯一的数值，由于`Dart`字符串是`UTF-16`的字符序列，所以在字符串中表达32位的的字符序列时就需要新的语法了。通常使用`\uXXXX`的方式来表示, 这里的`XXXX`是4个16进制的数, 如，心形符号`(♥)`是`\u2665`，对于非4个数值的情况，把编码值放到大括号中即可, 如笑脸`emoji` (😆) 是`\u{1f600}`

`String`类有一些属性可以提取 `rune` 信息：

1. `codeUnitAt` 和 `codeUnit` 属性返回16位字符
2. 使用 `runes` 属性来获取字符串的 `runes` 信息

```dart
runesDemo() {
  Runes runes = new Runes('\u2665, \u{1f605}, \u{1f60e}');
  print(runes);
  print(new String.fromCharCodes(runes));
}

```

上述代码的输出为：

![image-20190530220322602](http://picture-pool.oss-cn-beijing.aliyuncs.com/2019-06-06-092814.png)
### 函数
Dart 是一个面向对象语言，函数也是对象并且具有一种类型：`Function`。 这意味着，函数可以赋值给变量，也可以当做其他函数的参数，同时也可以把 Dart 类的实例当做函数来调用。 

下面是定义函数的示例：

```dart
bool isNoble(int atomicNumber) {
  return _nobleGases[atomicNumber] != null;
}
```

你也可以选择不显示指定返回类型：

```dart
isNoble(atomicNumber) {
  return _nobleGases[atomicNumber] != null;
}
```

对于只有一个表达式的函数，你可以选择使用缩写语法来定义：

```dart
bool isNoble(int atomicNumber) => _nobleGases[atomicNumber] != null;
```

这个 `=> expr` 语法是 `{ return expr; }` 形式的缩写。`=>` 形式有时候也称之为**胖箭头**语法。

**注意：** 在箭头 (`=>`) 和分号 (`;`) 之间只能使用一个 **表达式**而 – 不能使用**语句**。

函数可以有两种类型的参数：**必需的**和**可选的**。 必需的参数在参数列表前面，可选的参数在参数列表后面。

#### 可选参数

可选参数可以是**命名参数**或者**基于位置**的参数，但是这两种参数**不能同时当做可选参数**，也就是说，可选参数要么是命名参数，要么是基于位置的参数，不能说某一个函数中的可选参数既有命名参数，也有基于位置的参数。

##### （可选）命名参数

调用函数的时候，你可以使用这种形式 `paramName: value` 来指定命名参数。例如：

```dart
enableFlags(bold: true, hidden: false);
```

在定义函数的时候，使用 `{param1, param2, …}` 的形式来指定命名参数：

```dart
enableFlags({bool bold, bool hidden}) {
  // ...
}
```

##### （可选）位置参数

把一些函数的参数放到 `[]` 中就变成可选位置参数了：

```dart
String say(String from, String msg, [String device]) {
  var result = '$from says $msg';
  if (device != null) {
    result = '$result with a $device';
  }
  return result;
}
```

下面是不使用可选参数调用上面函数的示例：

```dart
assert(say('Bob', 'Howdy') == 'Bob says Howdy');
```

下面是使用可选参数调用上面函数的示例：

```dart
assert(say('Bob', 'Howdy', 'smoke signal') ==
    'Bob says Howdy with a smoke signal');
```

#### 默认参数值

在定义函数的时候，可以使用 `=` 来定义**可选参数的默认值**。 默认值只能是编译时常量。 如果没有提供默认值，则默认值为 `null`。

下面是设置可选参数默认值的示例：

```dart
void enableFlags({bool bold = false, bool hidden = false}) {
  // ...
}
// bold将会是true; hidden将会是false.
enableFlags(bold: true);

```

**版本问题：** 旧版本代码可能需要使用一个冒号 (`:`) 而不是 `=` 来设置参数默认值。 原因在于 Dart SDK 1.21 之前的版本，命名参数只支持 `:`。 `:` 设置命名默认参数值在以后版本中将不能使用， 所以推荐你**使用 = 来设置默认值， 并指定 Dart SDK 版本为 1.21 或者更高的版本。**

下面的示例显示了如何设置位置参数的默认值：

```dart
String say(String from, String msg,
    [String device = 'carrier pigeon', String mood]) {
  var result = '$from says $msg';
  if (device != null) {
    result = '$result with a $device';
  }
  if (mood != null) {
    result = '$result (in a $mood mood)';
  }
  return result;
}

assert(say('Bob', 'Howdy') ==
    'Bob says Howdy with a carrier pigeon');

```

还可以为 `list` 或者 `map` 设置默认值。 下面的示例定义了一个函数 `doStuff()`， 并分别为 `list` 和 `gifts` 参数指定了 默认值：

```dart
void doStuff(
    {List<int> list = const [1, 2, 3],
    Map<String, String> gifts = const {
      'first': 'paper',
      'second': 'cotton',
      'third': 'leather'
    }}) {
  print('list:  $list');
  print('gifts: $gifts');
}

```

#### 入口函数

每个应用都需要有个顶级的 `main()` 入口函数才能执行。 `main()` 函数的返回值为 `void` 并且有个可选的 `List<String>` 参数。

下面是一个 `web` 应用的 `main()` 函数：

```dart
void main() {
  querySelector("#sample_text_id")
    ..text = "Click me!"
    ..onClick.listen(reverseText);
}

```

**注意：** 前面代码中的 `..` 语法为 **级联调用（cascade）**。 使用级联调用语法， 你可以在一个对象上执行多个操作。

下面是一个命令行应用的 `main()` 函数，并且使用了函数参数作为输入参数：

```dart
// Run the app like this: dart args.dart 1 test
void main(List<String> arguments) {
  print(arguments);
  assert(arguments.length == 2);
  assert(int.parse(arguments[0]) == 1);
  assert(arguments[1] == 'test');
}

```

#### 一等函数对象

可以把函数当做参数调用另外一个函数。例如：

```dart
printElement(element) {
  print(element);
}

var list = [1, 2, 3];

// Pass printElement as a parameter.
list.forEach(printElement);

```

函数也可以赋值给一个变量：

```dart
var loudify = (msg) => '!!! ${msg.toUpperCase()} !!!';
assert(loudify('hello') == '!!! HELLO !!!');

```

上面的函数为下面即将介绍的匿名函数。

#### 匿名函数

大部分函数都带有名字，例如 `main()` 或者 `printElement()`。 你有可以创建没有名字的函数，称之为 **匿名函数**，有时候也被称为 *lambda* 或者 *closure 闭包*。 你可以把匿名函数赋值给一个变量， 然后你可以使用这个函数，比如添加到集合或者从集合中删除。

匿名函数和命名函数看起来有点类似，都是在括号之间可以定义一些参数，参数使用逗号分割，也可以是可选参数。 后面大括号中的代码为函数体：

```
([[Type] param1[, …]]) {   codeBlock; }; 

```

下面的代码定义了一个参数为`i` （该参数没有指定类型）的匿名函数。 `list` 中的每个元素都会调用这个函数来 打印出来，同时来计算了每个元素在 `list` 中的索引位置。

```dart
var list = ['apples', 'oranges', 'grapes', 'bananas', 'plums'];
list.forEach((i) {
  print(list.indexOf(i).toString() + ': ' + i);
});

```

如果函数只包含一个语句，可以使用胖箭头语法缩写：

```dart
list.forEach((i) => print(list.indexOf(i).toString() + ': ' + i));

```

#### 静态作用域

Dart 是静态作用域语言，变量的作用域在写代码的时候就确定过了。 大括号里面定义的变量就只能在大括号里面访问，和 Java 作用域类似。

下面是作用域的一个示例：

```dart
var topLevel = true;

main() {
  var insideMain = true;

  myFunction() {
    var insideFunction = true;

    nestedFunction() {
      var insideNestedFunction = true;

      assert(topLevel);
      assert(insideMain);
      assert(insideFunction);
      assert(insideNestedFunction);
    }
  }
}

```

注意 `nestedFunction()` 可以访问所有的变量， 包含顶级变量，但是在`main()`函数中就不能访问`myFunction()`以及`nestedFunction()`中的变量，也就是说，变量是向下可见、向上不可见的。

#### 词法闭包

一个 **闭包** 是一个函数对象，不管该对象在何处被调用， 该对象都可以访问其作用域内的变量。

函数可以封闭定义到其作用域内的变量。 下面的示例中，`makeAdder()` 捕获到了变量 `addBy`。 不管你在那里执行 `makeAdder()` 所返回的函数，都可以使用 `addBy` 参数。

```dart
/// Returns a function that adds [addBy] to the
/// function's argument.
Function makeAdder(num addBy) {
  return (num i) => addBy + i;
}

main() {
  // Create a function that adds 2.
  var add2 = makeAdder(2);

  // Create a function that adds 4.
  var add4 = makeAdder(4);

  assert(add2(3) == 5);
  assert(add4(3) == 7);
}

```

#### 测试函数是否相等

下面是测试顶级函数、静态函数和实例函数相等的示例：

```dart
foo() {}               // A top-level function

class A {
  static void bar() {} // A static method
  void baz() {}        // An instance method
}

main() {
  var x;

  // Comparing top-level functions.
  x = foo;
  assert(foo == x);

  // Comparing static methods.
  x = A.bar;
  assert(A.bar == x);

  // Comparing instance methods.
  var v = new A(); // Instance #1 of A
  var w = new A(); // Instance #2 of A
  var y = w;
  x = w.baz;

  // These closures refer to the same instance (#2),
  // so they're equal.
  assert(y.baz == x);

  // These closures refer to different instances,
  // so they're unequal.
  assert(v.baz != w.baz);
}

```

#### 返回值

所有的函数都返回一个值。如果没有指定返回值，则默认把语句 `return null;` 作为函数的最后一个语句执行。

### 操作符

下表是 Dart 中定义的操作符。 很多操作符都可以重载：

| 描述                     | 操作符                                                       |
| :----------------------- | :----------------------------------------------------------- |
| 一元后缀                 | `expr++`    `expr--`    `()`    `[]`    `.`    `?.`          |
| 一元前缀                 | `-expr`    `!expr`    `~expr`    `++expr`    `--expr`        |
| 乘除余                   | `*`    `/`    `%`  `~/`                                      |
| 加减                     | `+`    `-`                                                   |
| 移位                     | `<<`    `>>`                                                 |
| 位运算                   | `&` `^`   `|`                                                |
| relational and type test | `>=`    `>`    `<=`    `<`    `as`    `is`    `is!`          |
| equality                 | `==`    `!=`                                                 |
| logical AND              | `&&` `||`                                                    |
| if null                  | `??`                                                         |
| conditional              | `expr1 ? expr2 : expr3`                                      |
| cascade                  | `..`                                                         |
| assignment               | `=`    `*=`    `/=`    `~/=`    `%=`    `+=`    `-=`    `<<=`    `>>=`    `&=`    `^=`    `|=`    `??=` |

在使用操作符的时候，会自动创建表达式。下面是一些操作符表达式：

```dart
a++
a + b
a = b
a == b
a ? b: c
a is T

```

在上表中所列的操作符都是按照优先级顺序从左到右，从上到下的方式来列出的， 上面和左边的操作符优先级要高于下面和右边的。 例如 `%` 操作符优先级高于 `==`，而 等号 高于 `&&`。所以下面的代码执行结果是一样的：

```dart
// 1: Parens improve readability.
if ((n % i == 0) && (d % i == 0))

// 2: Harder to read, but equivalent.
if (n % i == 0 && d % i == 0)

```

**警告：** 对于有两个操作数的操作符，左边的操作数决定了操作符的功能。 例如，如果有一个 Vector 对象和一个 Point 对象， `aVector + aPoint` 使用的会是`Vector` 对象中定义的 + 操作符。

##### 算术操作符

Dart 支持常用的算术操作符，如下：

| 操作符    | 解释                   |
| :-------- | :--------------------- |
| `+`       | 加号                   |
| `–`       | 减号                   |
| `-*expr*` | 负号                   |
| `*`       | 乘号                   |
| `/`       | 除号                   |
| `~/`      | 除号，但是返回值为整数 |
| `%`       | 取模                   |

示例：

```dart
assert(2 + 3 == 5);
assert(2 - 3 == -1);
assert(2 * 3 == 6);
assert(5 / 2 == 2.5);   // Result is a double
assert(5 ~/ 2 == 2);    // Result is an integer
assert(5 % 2 == 1);     // Remainder

print('5/2 = ${5~/2} r ${5%2}'); // 5/2 = 2 r 1

```

Dart 还支持递增、递减前缀 和后缀操作：

| 操作符  | 解释                                                         |
| :------ | :----------------------------------------------------------- |
| `++var` | `var = var + 1` (表达式的值是 `var + 1`，因为先进行`+1`操作，再执行表达式) |
| `var++` | `var = var + 1` (表达式的值是 `var`，因为先执行表达式，再进行`+1`操作) |
| `--var` | `var = var – 1` (表达式的值是 `var – 1`，因为先进行`-1`操作，再执行表达式) |
| `var--` | `var = var – 1` (表达式的值是 `var`，因为先执行表达式，再进行`+1`操作) |

示例：

```dart
var a, b;

a = 0;
b = ++a;        // Increment a before b gets its value.
assert(a == b); // 1 == 1

a = 0;
b = a++;        // Increment a AFTER b gets its value.
assert(a != b); // 1 != 0

a = 0;
b = --a;        // Decrement a before b gets its value.
assert(a == b); // -1 == -1

a = 0;
b = a--;        // Decrement a AFTER b gets its value.
assert(a != b); // -1 != 0

```

##### 相等相关的操作符

下表是和相等操作相关的操作符。

| 操作符 | 解释     |
| :----- | :------- |
| `==`   | 相等     |
| `!=`   | 不等     |
| `>`    | 大于     |
| `<`    | 小于     |
| `>=`   | 大于等于 |
| `<=`   | 小于等于 |

要测试两个对象代表的是否为同样的内容，使用 `==` 操作符。如果你需要知道两个对象是否是同一个对象，应该使用`identical()`方法。

 下面是 `==` 操作符工作原理解释：

1. 如果 *x* 或者 *y* 是 null：如果两个都是 null 则返回 true，如果只有一个是 null 返回 false。
2. 如果x和y都不是null：返回 `x.==(y)`这个函数的返回值（像 `==` 这种操作符是定义在左边对象上的方法。 你甚至还可以重写这些操作符对应的方法。，在后面的章节将会具体介绍。）

下面是相等关系操作符的 使用示例：

```dart
assert(2 == 2);
assert(2 != 3);
assert(3 > 2);
assert(2 < 3);
assert(3 >= 3);
assert(2 <= 3);
```

##### 类型判定操作符

`as`、 `is`、 和 `is!` 操作符是在运行时判定对象类型的操作符：

| 操作符 | 解释                             |
| :----- | :------------------------------- |
| `as`   | 类型转换                         |
| `is`   | 如果对象是指定的类型返回 `True`  |
| `is!`  | 如果对象是指定的类型返回 `False` |

只有当 `obj` 实现了 `T` 的接口， `obj is T` 才是 `true`。例如 `obj is Object` 总是 `true`。

使用 `as` 操作符把对象转换为特定的类型。 一般情况下，你可以把它当做用 `is` 判定类型然后调用所判定对象的函数的缩写形式。例如下面的 示例：

```dart
if (emp is Person) { // Type check
  emp.firstName = 'Bob';
}
```

使用 `as` 操作符可以简化上面的代码：

```dart
(emp as Person).firstName = 'Bob';
```

**注意：** 上面这两个代码效果是有区别的。如果 `emp` 是 null 或者不是 Person 类型， 则第一个示例使用 `is` 则不会执行条件里面的代码，而第二个情况使用 `as` 则会**抛出一个异常**。

##### 赋值操作符

使用 `=` 操作符来赋值，用 `??=` 操作符用来指定值为 null 的变量的值。

```dart
a = value;   // 给 a 变量赋值
b ??= value; // 如果 b 是 null，则将value赋值给 b；
             // 如果不是 null，则 b 的值保持不变
```

还有复合赋值操作符 `+=` 等可以 赋值：

| `=`  | `-=` | `/=`  | `%=`  | `>>=` | `^=` |
| ---- | ---- | ----- | ----- | ----- | ---- |
| `+=` | `*=` | `~/=` | `<<=` | `&=`  | `|=` |

下面是复合赋值操作符工作原理解释：

|                     | 复合赋值操作符 | 对应的完整表达式 |
| :------------------ | :------------- | :--------------- |
| **对于 操作符 op:** | `a op = b`     | `a = a op b`     |
| **示例:**           | `a += b`       | `a = a + b`      |

比如：

```dart
var a = 2;           // Assign using =
a *= 3;              // Assign and multiply: a = a * 3
assert(a == 6);
```

##### 逻辑操作符

可以使用逻辑操作符来操作布尔值：

| 操作符  | 解释                                                         |
| :------ | :----------------------------------------------------------- |
| `!expr` | 对表达式结果取反（`true` 变为 `false` ，`false` 变为 `true`） |
| `||`    | 逻辑或                                                       |
| `&&`    | 逻辑与                                                       |

下面是使用示例：

```dart
if (!done && (col == 0 || col == 3)) {
  // ...Do something...
}

```

##### 位和移位操作符

在 Dart 中可以单独操作数字的某一位， 下面操作符同样应用于整数：

| 操作符  | 解释        |
| :------ | :---------- |
| `&`     | 与          |
| `|`     | 或          |
| `^`     | 异或        |
| `~expr` | 按位取反    |
| `<<`    | Shift left  |
| `>>`    | Shift right |

使用位于和移位操作符的示例：

```dart
final value = 0x22;
final bitmask = 0x0f;

assert((value & bitmask)  == 0x02);  // AND
assert((value & ~bitmask) == 0x20);  // AND NOT
assert((value | bitmask)  == 0x2f);  // OR
assert((value ^ bitmask)  == 0x2d);  // XOR
assert((value << 4)       == 0x220); // Shift left
assert((value >> 4)       == 0x02);  // Shift right
```

##### 条件表达式

Dart 有两个特殊的操作符可以用来替代 `if-else`语句：

- `condition ? expr1 : expr2`

  如果 *condition* 是 true，执行 *expr1* (并返回执行的结果)， 否则执行 *expr2* 并返回其结果。

- `expr1?? expr2`

  如果 *expr1* 不为null，返回其值， 否则执行 *expr2* 并返回其结果。

如果你需要基于布尔表达式的值来赋值， 考虑使用 `?:`。

```dart
var finalStatus = m.isFinal ? 'final' : 'not final';
```

如果布尔表达式是测试值是否为 null， 考虑使用 `??`。

```dart
String toString() => msg ?? super.toString();
```

下面是一样效果的实现， 但是代码不是那么简洁：

```dart
// Slightly longer version uses ?: operator.
String toString() => msg == null ? super.toString() : msg;

// Very long version uses if-else statement.
String toString() {
  if (msg == null) {
    return super.toString();
  } else {
    return msg;
  }
}
```

##### 级联操作符

级联操作符 (`..`) 可以在同一个对象上连续调用多个函数以及访问成员变量。 使用级联操作符可以避免创建临时变量， 并且写出来的代码看起来更加流畅：

例如下面的代码：

```dart
querySelector('#button') // Get an object.
  ..text = 'Confirm'   // Use its members.
  ..classes.add('important')
  ..onClick.listen((e) => window.alert('Confirmed!'));

```

第一个方法 `querySelector()` 返回了一个 `selector` 对象。 后面的级联操作符都是调用这个对象的成员， 并忽略每个操作所返回的值。

上面的代码和下面的代码功能一样：

```dart
var button = querySelector('#button');
button.text = 'Confirm';
button.classes.add('important');
button.onClick.listen((e) => window.alert('Confirmed!'));

```

级联调用也可以嵌套：

```dart
final addressBook = (new AddressBookBuilder()
      ..name = 'jenny'
      ..email = 'jenny@example.com'
      ..phone = (new PhoneNumberBuilder()
            ..number = '415-555-0100'
            ..label = 'home')
          .build())
    .build();

```

在方法上使用级联操作符需要非常小心， 例如下面的代码就是不合法的：

```dart
// Does not work
var sb = new StringBuffer();
sb.write('foo')..write('bar');

```

`sb.write()` 函数返回一个 `void`， 无法在 `void` 上使用级联操作符。

**注意：** 严格来说， 两个点的级联语法不是一个操作符， 只是一个 Dart 特殊语法。

##### 其他操作符

下面是其他操作符：

| Operator | Name          | Meaning                                                      |
| :------- | :------------ | :----------------------------------------------------------- |
| `()`     | 使用方法      | 代表调用一个方法                                             |
| `[]`     | 访问 `List`   | 访问 `list` 中特定位置的元素                                 |
| `.`      | 访问 `Member` | 访问元素，例如 `foo.bar` 代表访问 `foo` 的 `bar` 成员        |
| `?.`     | 条件成员访问  | 和 `.` 类似，但是左边的操作对象不能为 `null`，例如 `foo?.bar` 如果 `foo` 为 `null` 则返回 `null`，否则返回 `bar` 成员 |

关于 `.`、 `?.`、和 `..` 的详情，请参考 [Classes](http://dart.goodev.org/guides/language/language-tour#classes)。

### 流程控制语句

可以使用下面的语句来控制 Dart 代码的流程：

- `if` 以及 `else`
- `for` 循环
- `while`以及 `do`-`while` 循环
- `break` 和 `continue`
- `switch` 和 `case`
- `assert`

##### If和else

Dart 支持 `if` 语句以及可选的 `else`，例如下面的示例：

```dart
if (isRaining()) {
  you.bringRainCoat();
} else if (isSnowing()) {
  you.wearJacket();
} else {
  car.putTopDown();
}
```

##### For循环

可以使用标准的 `for` 循环：

```dart
var message = new StringBuffer("Dart is fun");
for (var i = 0; i < 5; i++) {
  message.write('!');
}
```

Dart `for` 循环中的闭包会捕获循环的 `index` 索引值，例如：

```dart
var callbacks = [];
for (var i = 0; i < 2; i++) {
  callbacks.add(() => print(i));
}
callbacks.forEach((c) => c());

```

输出的结果为所期望的 `0` 和 `1`。

如果要遍历的对象实现了 `Iterable` 接口，则可以使用 `forEach()`方法。如果没必要使用当前遍历的索引，则使用 `forEach()` 方法是个非常好的选择：

```dart
candidates.forEach((candidate) => candidate.interview());

```

`List` 和 `Set` 等实现了 `Iterable` 接口，另外实现了`Iterable`的类还支持 `for-in` 形式的遍历：

```dart
var collection = [0, 1, 2];
for (var x in collection) {
  print(x);
}

```

##### While 以及 do-while

`while` 循环在执行循环之前先判断条件是否满足：

```dart
while (!isDone()) {
  doSomething();
}

```

而 `do`-`while` 循环是先执行循环代码再判断条件：

```dart
do {
  printLine();
} while (!atEndOfPage());

```

##### Break 和 continue

使用 `break` 来终止循环：

```dart
while (true) {
  if (shutDownRequested()) break;
  processIncomingRequests();
}

```

使用 `continue` 来开始下一次循环：

```dart
for (int i = 0; i < candidates.length; i++) {
  var candidate = candidates[i];
  if (candidate.yearsExperience < 5) {
    continue;
  }
  candidate.interview();
}

```

上面的代码在实现 `Iterable` 接口的对象上可以使用下面的写法：

```dart
candidates.where((c) => c.yearsExperience >= 5)
          .forEach((c) => c.interview());

```

##### Switch 和 case

Dart 中的 Switch 语句使用 `==` 比较 integer、string、或者编译时常量。 比较的对象必须都是同一个类的实例（也不能是其子类），而且对应类必须没有重写 `==` 操作符。

每个非空的 `case` 语句都必须有一个 `break` 语句。 另外还可以通过 `continue`、 `throw` 或者 `return` 来结束非空 `case` 语句。

当没有 `case` 语句匹配的时候，可以使用 `default` 语句来匹配这种默认情况。

```dart
var command = 'OPEN';
switch (command) {
  case 'CLOSED':
    executeClosed();
    break;
  case 'PENDING':
    executePending();
    break;
  case 'APPROVED':
    executeApproved();
    break;
  case 'DENIED':
    executeDenied();
    break;
  case 'OPEN':
    executeOpen();
    break;
  default:
    executeUnknown();
}
```

下面的示例代码在 `case` 中省略了 `break` 语句， 编译的时候将会出现一个错误：

```dart
var command = 'OPEN';
switch (command) {
  case 'OPEN':
    executeOpen();
    // ERROR: Missing break causes an exception!!

  case 'CLOSED':
    executeClosed();
    break;
}
```

但是，在 Dart 中的空 `case` 语句中可以不要 `break` 语句：

```dart
var command = 'CLOSED';
switch (command) {
  case 'CLOSED': // Empty case falls through.
  case 'NOW_CLOSED':
    // Runs for both CLOSED and NOW_CLOSED.
    executeNowClosed();
    break;
}
```

如果你需要实现这种继续到下一个 `case` 语句中继续执行，则可以 使用 `continue` 语句跳转到对应的标签（`label`）处继续执行：

```dart
var command = 'CLOSED';
switch (command) {
  case 'CLOSED':
    executeClosed();
    continue nowClosed;
    // Continues executing at the nowClosed label.

nowClosed:
  case 'NOW_CLOSED':
    // Runs for both CLOSED and NOW_CLOSED.
    executeNowClosed();
    break;
}

```

每个 `case` 语句都可以有局部变量，局部变量只有在这个语句内可见。

##### Assert（断言）

如果条件表达式结果不满足需要，则可以使用 `assert` 语句来打断代码的执行。 下面介绍如何使用断言。 下面是一些示例代码：

```dart
// Make sure the variable has a non-null value.
assert(text != null);

// Make sure the value is less than 100.
assert(number < 100);

// Make sure this is an https URL.
assert(urlString.startsWith('https'));

```

**注意：** 断言只在**检查模式**下运行有效，如果在生产模式运行，则断言不会执行。

`assert` 方法的参数可以为任何返回布尔值的表达式或者方法。 如果返回的值为 true， 断言执行通过，执行结束。 如果返回值为 `false`， 断言执行失败，会抛出一个`AssertionError`异常 。

### 异常（Exceptions）

代码中可以出现异常和捕获异常。异常表示一些未知的错误情况。如果没有对异常进行捕获， 则异常会被抛出，导致抛出异常的代码会终止执行。

和 Java 不同的是，所有的 Dart 异常是非检查异常。 方法不一定声明了他们所抛出的异常， 并且不要求你捕获任何异常。

Dart 提供了 `Exception` 和 `Error` 类型， 以及一些子类型。你还可以定义自己的异常类型。但是， Dart 代码可以抛出任何非 `null` 对象作为为异常，而不仅仅是实现了 `Exception` 或者 `Error` 的对象。

#### Throw

下面是抛出一个异常的示例：

```dart
throw new FormatException('Expected at least 1 section');

```

还可以抛出任意的对象：

```dart
throw 'Out of llamas!';

```

由于抛出异常是一个表达式，所以可以在 => 语句中使用，也可以在其他能使用表达式的地方抛出异常。

```dart
distanceTo(Point other) =>
    throw new UnimplementedError();

```

#### Catch

捕获异常可以避免异常继续传递（你在`catch`语句中又重新抛出异常除外）。 捕获异常给你一个处理该异常的机会：

```dart
try {
  breedMoreLlamas();
} on OutOfLlamasException {
  buyMoreLlamas();
}

```

对于可以抛出多种类型异常的代码，你可以指定多个捕获语句。每个语句分别对应一个异常类型， 如果捕获语句没有指定异常类型，则该语句可以捕获任何异常类型：

```dart
try {
  breedMoreLlamas();
} on OutOfLlamasException {
  // A specific exception
  buyMoreLlamas();
} on Exception catch (e) {
  // Anything else that is an exception
  print('Unknown exception: $e');
} catch (e) {
  // No specified type, handles all
  print('Something really unknown: $e');
}

```

如之前代码所示，你可以使用`on` 或者 `catch` 来声明捕获语句，也可以同时使用。使用 `on` 来指定异常类型，使用 `catch` 来 捕获异常对象。

函数 `catch()` 可以带有一个或者两个参数， 第一个参数为抛出的异常对象， 第二个为堆栈信息 (一个 `StackTrace` 对象)。

```dart
  ...
} on Exception catch (e) {
  print('Exception details:\n $e');
} catch (e, s) {
  print('Exception details:\n $e');
  print('Stack trace:\n $s');
}
```

使用 `rethrow` 关键字可以 把捕获的异常给 重新抛出。

```dart
final foo = '';

void misbehave() {
  try {
    foo = "You can't change a final variable's value.";
  } catch (e) {
    print('misbehave() partially handled ${e.runtimeType}.');
    rethrow; // Allow callers to see the exception.
  }
}

void main() {
  try {
    misbehave();
  } catch (e) {
    print('main() finished handling ${e.runtimeType}.');
  }
}
```

#### Finally

要确保某些代码执行，不管有没有出现异常都需要执行，可以使用 一个 `finally` 语句来实现。如果没有 `catch` 语句来捕获异常， 则在执行完 `finally` 语句后， 异常被抛出了：

```dart
try {
  breedMoreLlamas();
} finally {
  // Always clean up, even if an exception is thrown.
  cleanLlamaStalls();
}
```

定义的 `finally` 语句在任何匹配的 `catch` 语句之后执行：

```dart
try {
  breedMoreLlamas();
} catch(e) {
  print('Error: $e');  // Handle the exception first.
} finally {
  cleanLlamaStalls();  // Then clean up.
}
```