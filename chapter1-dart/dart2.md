@[toc]
### 类

` Dart` 是一个面向对象的编程语言，同时支持基于 mixin 的继承机制。 每个对象都是一个类的实例，所有的类都继承于 `Object`类。 *基于 Mixin 的继承* 意味着每个类（Object 除外） 都只有一个超类，一个类的代码可以在其他 多个类继承中重复使用。

使用 `new` 关键字和构造方法来创建新的对象。 构造方法名字可以为 `ClassName` 或者 `ClassName.identifier`。例如：

```dart
var jsonData = JSON.decode('{"x":1, "y":2}');

// Create a Point using Point().
var p1 = new Point(2, 2);

// Create a Point using Point.fromJson().
var p2 = new Point.fromJson(jsonData);
```

对象的成员包括方法和数据 (方法 和 示例变量)。当你调用一个方法的时候，你是在一个对象上调用： 需要访问对象的方法和数据。

使用点`.`来引用对象的变量或者方法：

```dart
var p = new Point(2, 2);

// Set the value of the instance variable y.
p.y = 3;

// Get the value of y.
assert(p.y == 3);

// Invoke distanceTo() on p.
num distance = p.distanceTo(new Point(4, 4));
```

使用 `?.` 来替代 `.` 可以避免当左边对象为 `null` 时候抛出异常，当`？.`左边的对象不为`null`时`?.`后面的方法或者对象才会被执行：

```dart
// If p is non-null, set its y value to 4.
p?.y = 4;
```

有些类提供了常量构造方法。使用常量构造方法可以创建编译时常量，要使用常量构造方法只需要用 `const` 替代 `new` 即可：

```dart
var p = const ImmutablePoint(2, 2);
```

两个一样的编译时常量其实是 同一个对象：

```dart
var a = const ImmutablePoint(1, 1);
var b = const ImmutablePoint(1, 1);

assert(identical(a, b)); // They are the same instance!
```

可以使用 `Object` 的 `runtimeType` 属性来判断实例的类型，该属性返回一个 `Type`对象。

```dart
print('The type of a is ${a.runtimeType}');
```

下面介绍如何实现一个类。

#### 实例变量

下面是如何定义实例变量的示例：

```dart
class Point {
  num x; // Declare instance variable x, initially null.
  num y; // Declare y, initially null.
  num z = 0; // Declare z, initially 0.
}
```

所有没有初始化的变量值都是 `null`。

每个实例变量都会自动生成一个 `getter` 方法（隐含的）。非`final`实例变量还会自动生成一个 `setter` （隐含的）方法，注意，这里提到的"隐含"的意思是，你可以通过`.`的形式直接对对象的实例变量取值或者设置值，形式上看起来是直接对实例变量进行操作，但是实际上是通过隐含的`getter`以及`setter`方法进行的，比如：

```dart
class Point {
  num x;
  num y;
}

main() {
  var point = new Point();
  point.x = 4;          // Use the setter method for x.
  assert(point.x == 4); // Use the getter method for x.
  assert(point.y == null); // Values default to null.
}
```

这个getter以及setter方法我会在下面专门展开讲解。

如果你在实例变量定义的时候初始化该变量（不是在构造方法或者其他方法中初始化），该值是在实例创建的时候 初始化的，也就是在构造方法和初始化参数列表执行之前。

#### 构造方法

定义一个和类名字一样的方法就定义了一个构造方法，当然还可以带有其他可选的标识符，详情参考命名构造方法一节。 常见的构造方法生成一个对象的新实例：

```dart
class Point {
  num x;
  num y;

  Point(num x, num y) {
    // There's a better way to do this, stay tuned.
    this.x = x;
    this.y = y;
  }
}
```

`this` 关键字指当前的实例。

**注意：** 只有当名字冲突的时候才使用 `this`。否则的话， ` Dart` 代码风格样式**推荐忽略** `this`。

由于把构造方法参数赋值给实例变量的场景太常见了， ` Dart` 提供了一个**语法糖**来简化这个操作：

```dart
class Point {
  num x;
  num y;

  // Syntactic sugar for setting x and y
  // before the constructor body runs.
  Point(this.x, this.y);
}
```

##### 默认构造方法

如果你没有定义构造方法，则会有个默认构造方法。 默认构造方法没有参数，并且会调用超类的没有参数的构造方法。

##### 构造方法不会继承

子类不会继承超类的构造方法。 子类如果没有定义构造方法，则只有一个默认构造方法 （没有名字没有参数），这里的"子类不会继承超类的方法"可能有点抽象，我们来看如下代码：

```dart
class Child extends Dad {
  Child(name) : super(name);
}

class Dad {
  var nama = 'Joey';
  var age = 0;

  Dad(name) {
    this.nama = nama;
  }

  Dad.fromNameAndAge(name, age) {
    this.nama = nama;
    this.age = age;
  }
}

void main() {
  
  var child=Child('tony');
  var child2=Child.fromNameAndAge('name',12);
  
}

```

Child是Dad的子类，Child继承了Dad的默认构造方法，但是不能继承Dad的其他构造方法

##### 命名构造方法

使用命名构造方法可以为一个类实现多个构造方法（你只能声明一个默认构造方法）， 或者使用命名构造方法来更清晰的表明你的意图：

```dart
class Point {
  num x;
  num y;

  Point(this.x, this.y);

  // Named constructor
  Point.fromJson(Map json) {
    x = json['x'];
    y = json['y'];
  }
}
```

注意：构造方法不能继承，所以超类的命名构造方法也不会被继承。如果你希望子类也有超类一样的命名构造方法， 你必须在子类中自己实现该构造方法。

##### 调用超类构造方法

默认情况下，子类的构造方法会自动调用超类的无名无参数的默认构造方法。 超类的构造方法在子类构造方法体开始执行的位置调用。 如果提供了一个初始化参数列表 ，则初始化参数列表在超类构造方法执行之前执行。 下面是构造方法执行顺序：

1. 子类的具有初始化参数列表的构造方法
2. 超类的无名默认构造方法
3. 本类的无名默认构造方法

如果超类没有无名无参数构造方法， 则你需要手工的调用超类的其他构造方法。 在构造方法参数后使用冒号 (`:`) 可以调用超类构造方法。

下面的示例中，`Employee` 类的构造方法调用 了超类 `Person` 的命名构造方法，因为在调用构造方法之前会计算超类构造方法的参数，所以参数可以是一个表达式，例如方法调用：

```dart
class Employee extends Person {
  // ...
  Employee() : super.fromJson(findDefaultData());
}
```

**注意：** 如果在构造方法的初始化列表中使用 `super()`，需要把它放到最后。

**警告：** 传入超类构造方法中的参数无法访问 `this`。 例如，参数可以为静态方法但是不能是实例方法，比如：

![image-20190531220648183](http://picture-pool.oss-cn-beijing.aliyuncs.com/2019-06-06-092846.png)

如图，`a（）`是`Child`类中的一个实例方法，试图将`a()`传入`Child`的超类的构造方法中，编译器会提示`"Only static members can be accessed in initalizers"`，但是我们将`b()`作为参数传入是没有任何问题的，编译器不会报错，因为`b()`是一个静态方法：

![image-20190531220833533](http://picture-pool.oss-cn-beijing.aliyuncs.com/2019-06-06-092844.png)

##### 初始化列表

在构造方法体执行之前除了可以调用超类构造方法之外，还可以初始化实例参数， 使用逗号分隔初始化表达式。

初始化列表非常适合用来设置 `final` 变量的值。 下面示例代码中初始化列表设置了三个 `final` 变量的值：

```dart
class Point {
  final num x;
  final num y;
  final num distanceFromOrigin;

  Point(x, y)
      : x = x,
        y = y,
        distanceFromOrigin = sqrt(x * x + y * y);
}

```

**警告：** 初始化表达式等号右边的部分不能访问 `this`。

##### 重定向构造方法

有时候一个构造方法会调动类中的其他构造方法。 一个重定向构造方法是没有代码的，在构造方法声明后，使用 冒号调用其他构造方法。

```dart
class Point {
  num x;
  num y;

  // The main constructor for this class.
  Point(this.x, this.y);

  // Delegates to the main constructor.
  Point.alongXAxis(num x) : this(x, 0);
}
```

##### 常量构造方法

如果你的类提供一个状态不变的对象，你可以把这些对象定义为编译时常量。要实现这个功能，需要定义一个 `const` 构造方法， 并且声明所有变量为 `final`的：

```dart
class ImmutablePoint {
  final num x;
  final num y;
  const ImmutablePoint(this.x, this.y);
  static final ImmutablePoint origin =
      const ImmutablePoint(0, 0);
}
```

##### 工厂方法构造方法

如果一个构造方法并不总是返回一个新的对象，则使用 `factory` 来定义这个构造方法。例如，一个工厂构造方法 可能从缓存中获取一个实例并返回，或者返回一个子类型的实例。

下面代码演示工厂构造方法如何从缓存中返回对象。

```dart
class Logger {
  final String name;
  bool mute = false;

  // _cache is library-private, thanks to the _ in front
  // of its name.
  static final Map<String, Logger> _cache =
      <String, Logger>{};

  factory Logger(String name) {
    if (_cache.containsKey(name)) {
      return _cache[name];
    } else {
      final logger = new Logger._internal(name);
      _cache[name] = logger;
      return logger;
    }
  }

  Logger._internal(this.name);

  void log(String msg) {
    if (!mute) {
      print(msg);
    }
  }
}
```

**注意：** 工厂构造方法无法访问 `this`。

使用 `new` 关键字来调用工厂构造方法。

```dart
var logger = new Logger('UI');
logger.log('Button clicked');
```

#### 方法

肯定有的读者看到这里心里会有疑惑，前面不是讲了**Function(函数)**了吗，怎么这里又出来了个**方法**，方法和函数有什么区别？其实说白了，**"方法"**是相对于**"类"**来说的，我们说方法，一定指的是类中的方法，熟悉`Java`的同学可能都听过一句话，叫做**"Java中没有函数、只有方法"**，为什么这么说？因为`Java`中你写的所有函数都一定是处于某个类中的，那么既然函数处于类中，就应该称之为方法，而不是函数。但是在`` Dart``中就不一定了，`` Dart``中你可以在类中写函数，此时写的函数我们称之为方法，你也可以不将函数置于类中，比如：

```dart
void main() {
  var child = Child('tony');
}
```

这个函数可以脱离于类单独存在，它就可以称为**函数**。但是在`Java`中是不能这么写的，就算是`Java`中的主方法，也一定要依附于一个类。

所以总结一下，方法是类中定义的函数，是类对应的对象的行为。

##### 实例方法

对象的实例方法可以访问 `this`。 例如下面示例中的 `distanceTo()` 方法 就是实例方法：

```dart
import '` Dart`:math';

class Point {
  num x;
  num y;
  Point(this.x, this.y);

  num distanceTo(Point other) {
    var dx = x - other.x;
    var dy = y - other.y;
    return sqrt(dx * dx + dy * dy);
  }
}
```

##### Getter、Setter

`Getter` 和 `Setter` 是用来设置和访问对象属性的特殊方法。每个实例变量都隐含的具有一个 `getter`， 如果变量不是 `final` 的则还有一个 `setter`。 你使用 `get` 和 `set` 关键字来定义 `getter` 和 `setter`，这样你可以在`getter`或`setter`中添加一些额外的操作：

```dart
class Rectangle {
  num left;
  num top;
  num width;
  num height;

  Rectangle(this.left, this.top, this.width, this.height);

  // Define two calculated properties: right and bottom.
  num get right             => left + width;
      set right(num value)  => left = value - width;
  num get bottom            => top + height;
      set bottom(num value) => top = value - height;
}

main() {
  var rect = new Rectangle(3, 4, 20, 15);
  assert(rect.left == 3);
  rect.right = 12;
  assert(rect.left == -8);
}
```

**注意：** 像 (`++`) 这种操作符不管是否定义 `getter` 都会正确的执行。 为了避免其他副作用， 操作符只调用 `getter` 一次，然后 把其值保存到一个临时变量中。

##### 抽象方法

实例方法、 `getter`、和 `setter` 方法可以为抽象方法， 抽象方法是**只定义方法接口但是没有实现**的方法，由子类来实现该方法。如果用分号来替代方法体则这个方法就是抽象方法。

```dart
abstract class Doer {
  // ...Define instance variables and methods...

  void doSomething(); // Define an abstract method.
}

class EffectiveDoer extends Doer {
  void doSomething() {
    // ...Provide an implementation, so the method is not abstract here...
  }
}
```

调用一个没实现的抽象方法会导致运行时异常。

##### 可覆写的操作符

下表中的操作符可以被覆写。 例如，如果你定义了一个 `Vector` 类， 你可以定义一个 `+` 方法来实现两个向量相加。

| `<`  | `+`  | `|`  | `[]`  |
| ---- | ---- | ---- | ----- |
| `>`  | `/`  | `^`  | `[]=` |
| `<=` | `~/` | `&`  | `~`   |
| `>=` | `*`  | `<<` | `==`  |
| `–`  | `%`  | `>>` |       |

下面是覆写了 `+` 和 `-` 操作符的示例：

```dart
class Vector {
  final int x;
  final int y;
  const Vector(this.x, this.y);

  /// Overrides + (a + b).
  Vector operator +(Vector v) {
    return new Vector(x + v.x, y + v.y);
  }

  /// Overrides - (a - b).
  Vector operator -(Vector v) {
    return new Vector(x - v.x, y - v.y);
  }
}

main() {
  final v = new Vector(2, 3);
  final w = new Vector(2, 2);

  // v == (2, 3)
  assert(v.x == 2 && v.y == 3);

  // v + w == (4, 5)
  assert((v + w).x == 4 && (v + w).y == 5);

  // v - w == (0, 1)
  assert((v - w).x == 0 && (v - w).y == 1);
}
```

如果你覆写了 `==` ，则还应该覆写对象的 `hashCode` getter 方法。 

#### 抽象类

使用 `abstract` 修饰符定义一个 抽象类：一个不能被实例化的类。 抽象类通常用来定义接口，以及**部分实现**。如果你希望你的抽象类是可实例化的，则可以定义一个工厂构造方法。

**抽象类通常具有抽象方法**。 下面是定义具有抽象方法的抽象类的示例：

```dart
// 这个类被声明为抽象类，不能被实例化
abstract class AbstractContainer {
  // ...定义构造方法、实例变量...

  void updateChildren(); // 抽象方法
}
```

下面的类不是抽象的，但是定义了一个抽象方法，这样的类是可以被实例化的：

```dart
class SpecializedContainer extends AbstractContainer {
  // ...Define more constructors, fields, methods...

  void updateChildren() {
    // ...Implement updateChildren()...
  }

  //实例化具有抽象方法的非抽象类时会导致警告，但不会报错
  void doSomething();
}
```

#### 隐式接口

每个类都隐式的定义了一个包含所有实例成员的接口， 并且这个类实现了这个接口，也就是说，只要你定义了一个类，都会随之产生一个该类逇隐式接口，这样你不仅可以**继承（extends）**该类，还可以**实现（implements）**该类，比如你想让类 `A` 具有类 `B` 中定义的 `api`，一种方法是你可以让类`A`继承（`extends`）类`B`，然后在类`A`中覆写类`B`中的`api`，另一种方法是你可以直接实现（`implements`）`B`的隐式接口。

一个类可以通过 `implements` 关键字来实现一个或者多个接口， 并实现每个接口定义的 API。 例如：

```dart
// Person类，其隐式接口中含有一个greet（）方法(_name是私有成员，其隐式接口中不可见)
class Person {

  final _name;

  // 构造方法不在隐式接口中
  Person(this._name);

  String greet(who) => 'Hello, $who. I am $_name.';
}

// Person 接口的实现类.
class Imposter implements Person {
  // We have to define this, but we don't use it.
  final _name = "";

  String greet(who) => 'Hi $who. Do you know who I am?';
}

greetBob(Person person) => person.greet('bob');

main() {
  print(greetBob(new Person('kathy')));
  print(greetBob(new Imposter()));
}
```

下面是实现多个接口的示例：

```dart
class Point implements Comparable, Location {
  // ...
}
```

#### 扩展类

使用 `extends` 定义子类， `supper` 引用超类：

```dart
class Television {
  void turnOn() {
    _illuminateDisplay();
    _activateIrSensor();
  }
  // ...
}

class SmartTelevision extends Television {
  void turnOn() {
    super.turnOn();
    _bootNetworkInterface();
    _initializeMemory();
    _upgradeApps();
  }
  // ...
}
```

子类可以覆写父类的**实例方法**，**getter** 和 **setter**。 下面是覆写 `Object` 类的 `noSuchMethod()` 方法的例子， 如果调用了对象上不存在的方法，则就会触发 `noSuchMethod()` 函 数。

```dart
class A {
  // 除非你覆写noSuchMethod方法，否则如果你调用A的对象的一个不存在的方法，就会抛出NoSuchMethodError.
  void noSuchMethod(Invocation mirror) {
    print('You tried to use a non-existent member:' +
          '${mirror.memberName}');
  }
}
```

还可以使用 `@override` 注解来显式表明你的方法是想覆写超类的一个方法：

```dart
class A {
  @override
  void noSuchMethod(Invocation mirror) {
    // ...
  }
}
```

如果你使用 `noSuchMethod()` 方法来实现每个可能的 `getter` 、`setter`、 以及其他类型的方法，你可以使用 `@proxy` 注解来避免警告信息：

```dart
@proxy
class A {
  void noSuchMethod(Invocation mirror) {
    // ...
  }
}
```

如果你知道编译时的具体类型，则可以实现这些类来避免警告，和 使用 `@proxy` 效果一样：

```dart
class A implements SomeClass, SomeOtherClass {
  void noSuchMethod(Invocation mirror) {
    // ...
  }
}
```

#### 枚举类型

枚举类型通常称之为 `enumerations` 或者 `enums`， 是一种特殊的类，用来表现一个固定数目的常量。

使用 `enum` 关键字来定义枚举类型：

```dart
enum Color {
  red,
  green,
  blue
}
```

枚举类型中的每个值都有一个 `index`  的`getter` 方法， 该方法返回该值在枚举类型定义中的位置（从 0 开始）。 例如，第一个枚举值的位置为 0， 第二个为 1.

```dart
assert(Color.red.index == 0);
assert(Color.green.index == 1);
assert(Color.blue.index == 2);
```

枚举的 `values` 常量可以返回 所有的枚举值。

```dart
List<Color> colors = Color.values;
assert(colors[2] == Color.blue);
```

可以在 `switch` 语句中使用枚举。 当 `switch (e)` 中的 *e* 的类型为枚举类， 而你又没有处理所有该枚举类型的值，则会抛出一个警告：

```dart
enum Color {
  red,
  green,
  blue
}
// ...
Color aColor = Color.blue;
switch (aColor) {
  case Color.red:
    print('Red as roses!');
    break;
  case Color.green:
    print('Green as grass!');
    break;
  default: // Without this, you see a WARNING.
    print(aColor);  // 'Color.blue'
}
```

枚举类型具有如下的限制：

- 无法继承枚举类型、无法使用 `mixin`（下面介绍）、无法实现（`implements`）一个枚举类型
- 无法显式地初始化一个枚举类型

#### 为类添加新的功能(`class: mixins`)

在介绍mixins的语法之前，我先来解释一下什么是mixins、为什么需要mixins：

![image-20190531231553813](http://picture-pool.oss-cn-beijing.aliyuncs.com/2019-06-06-92847.png)

如上图是一个类图，我们直接看重点，软件工程师和信息老师具有修电脑的技能，建筑工程师和美术老师都具有绘图的技能，我们如何去实现这种关系？最直接的方法是我们将修电脑和绘图写成两个类，然后让软件工程师、信息老师继承修电脑这个类，让建筑工程师、美术老师继承绘图这个类，但是注意，软件工程师已经继承了工程师这个类，在` Dart`中是单继承的，他不可能在继承工程师这个类的基础上再继承修电脑类，建筑工程师、美术老师、信息老师也有一样的问题，所以不能通过继承的方法实现这几类人群和技能的耦合关系。

可能有读者会想到用接口，没错，可以用接口，那么我们想一下如果使用接口需要我们怎么去实现，我们应该定义修电脑和绘图这两个接口，然后让软件工程师、信息老师实现修电脑这个接口，让建筑工程师、美术老师实现绘图这个接口，这样做功能是实现了，但是问题在于，软件工程师、信息老师他们两一共实现了两次修电脑这个接口，而实现代码中的内容是一样的，也就是说出现了冗余的代码，现在是2个类，如果有100个类都具有修电脑的功能呢？实现100次相同的代码吗？为了解决这一问题，` Dart`引入了mixins特性，我们可以分别实现一个修电脑和绘图对应的`mixins`类，然后使用`with` 关键字来直接引入修电脑或绘图中的代码，这样相当于代码重用，我们来看看具体用法：

使用 `with` 关键字，后面为一个或者多个使用 `mixin` 修饰的类的类名。 下面是示例显示了如何使用 `mixin`：

```dart
class Maestro extends Person
    with Musical, Aggressive, Demented {
 ...
}
```

要实现`mixin`，请创建一个继承自`Object`的类，并且不声明构造函数。除非你希望`mixin`可用作常规类，否则请使用`mixin`关键字而不是`class`。例如：

```dart
mixin Musical {
  bool canPlayPiano = false;
  bool canCompose = false;
  bool canConduct = false;

  void entertainMe() {
    if (canPlayPiano) {
      print('Playing piano');
    } else if (canConduct) {
      print('Waving hands');
    } else {
      print('Humming to self');
    }
  }
}
```

如果要指定仅仅特定类可以通过with关键字使用本mixins类的代码，则可以在实现mixins类的时候加上关键字on：

```dart
mixin MusicalPerformer on Musician {
  // ···
}
```

如上代码，这样只有`Musician`类可以使用`with MusicalPerformer`。

注意： 从 `` Dart` 1.13` 开始， 这两个限制在 `` Dart` VM` 上 没有那么严格了：

- `Mixins` 可以继承其他类，不再限制为继承 `Object`
- `Mixins` 可以调用 `super()`。

#### 类变量和方法

使用 `static` 关键字来实现类级别的变量和方法。

##### 静态变量

静态变量（类变量）对于类范围的状态和常量是很有用的：

```dart
class Color {
  static const red =
      const Color('red'); // A constant static variable.
  final String name;      // An instance variable.
  const Color(this.name); // A constant constructor.
}

main() {
  assert(Color.red.name == 'red');
}
```

静态变量在第一次使用的时候才被初始化。

##### 静态方法

静态方法和对象是解耦合的，也就是说，不能通过类的对象来调用类的静态方法，不能在静态方法中访问类的非静态变量：

```dart
import '` Dart`:math';

class Point {
  num x;
  num y;
  Point(this.x, this.y);

  static num distanceBetween(Point a, Point b) {
    var dx = a.x - b.x;
    var dy = a.y - b.y;
    return sqrt(dx * dx + dy * dy);
  }
}

main() {
  var a = new Point(2, 2);
  var b = new Point(4, 4);
  var distance = Point.distanceBetween(a, b);
  assert(distance < 2.9 && distance > 2.8);
}
```

**注意：**对于常见或广泛使用的实用程序和功能，请考虑使用顶级函数（全局函数，注意是函数而不是方法）而不是静态方法。

静态方法还可以当做编译时常量使用。例如， 你可以把静态方法当做常量构造方法的参数来使用。

### 泛型

如果你查看`List`类的 `API` 文档， 则可以看到其实际的类型为 `List<E>`。 这个 `<…>` 声明 `list` 是一个 **泛型** (或者 **参数化**) 类型。 通常情况下，使用一个字母来代表类型参数， 例如 `E, T, S, K, 和 V` 等。

#### 为何使用泛型

类型安全通常需要泛型，但它们比仅允许代码运行有更多好处：

- 正确指定泛型类型会产生更好的生成代码。
- 你可以使用泛型来减少代码的重复。

例如，如果你希望一个 `list` 只包含字符串对象，你可以 定义为 `List<String>` (代表 `“list of string”)`。这样你、 你的同事、以及所使用的工具 ( `IDE` 以及 检查模式的 `` Dart` VM` )可以帮助你检查你的代码是否把非字符串类型对象给放到 这个 `list` 中了。下面是一个示例：

```dart
var names = new List<String>();
names.addAll(['Seth', 'Kathy', 'Lars']);
// ...
names.add(42); // Fails in checked mode (succeeds in production mode).
```

另外一个使用泛型的原因是减少重复的代码。 泛型可以在多种类型之间定义同一个实现， 同时还可以使用检查模式和静态分析工具提供的代码分析功能。 例如，你创建一个保存缓存对象的接口：

```dart
abstract class ObjectCache {
  Object getByKey(String key);
  setByKey(String key, Object value);
}
```

后来你发现你需要一个用来缓存字符串的实现， 则你又定义另外一个接口：

```dart
abstract class StringCache {
  String getByKey(String key);
  setByKey(String key, String value);
}
```

然后，你又需要一个用来缓存数字的实现， 在后来，你又需要另外一个类型的缓存实现，等等。。。

泛型可以避免这种重复的代码。 你只需要创建一个接口即可：

```dart
abstract class Cache<T> {
  T getByKey(String key);
  setByKey(String key, T value);
}

```

在上面的代码中，`T` 是一个备用类型。这是一个类型占位符， 在开发者调用该接口的时候会指定具体类型。

#### 使用集合字面量

`List` 和 `map` 字面量也是可以参数化的。 参数化定义 `list` 需要在中括号之前 添加 `<type>` ， 定义 `map` 需要在大括号之前 添加 `<keyType, valueType>`。 如果你需要更加安全的类型检查，则可以使用参数化定义。下面是一些示例：

```dart
var names = <String>['Seth', 'Kathy', 'Lars'];
var pages = <String, String>{
  'index.html': 'Homepage',
  'robots.txt': 'Hints for web robots',
  'humans.txt': 'We are people, not machines'
};

```

#### 在构造方法中使用泛型

在调用构造方法的时候， 在类名字后面使用尖括号(`<...>`)来指定 泛型类型。例如：

```dart
var names = new List<String>();
names.addAll(['Seth', 'Kathy', 'Lars']);
var nameSet = new Set<String>.from(names);

```

下面代码创建了一个 key 为 integer， value 为 View 类型 的 map：

```dart
var views = new Map<int, View>();
```

### 通用集合及其包含的类型

` Dart` 的泛型类型是固化的，在运行时有也可以判断具体的类型。例如在运行时也可以检测集合里面的对象类型：

```dart
var names = new List<String>();
names.addAll(['Seth', 'Kathy', 'Lars']);
print(names is List<String>); // true

```

注意 `is` 表达式只是判断集合的类型，而不是集合里面具体对象的类型。

**注意：** `Java` 中的泛型信息是编译时的，泛型信息在运行时是不纯在的（**类型擦除**）。 在 `Java` 中你可以测试一个对象是否为 `List`， 但是你无法测试一个对象是否为 `List<String>`。

### 限制泛型类型

当使用泛型类型的时候，你可能想限制泛型的具体类型。 使用 `extends` 可以实现这个功能：

```dart
// T must be SomeBaseClass or one of its descendants.
class Foo<T extends SomeBaseClass> {...}

class Extender extends SomeBaseClass {...}

void main() {
  // It's OK to use SomeBaseClass or any of its subclasses inside <>.
  var someBaseClassFoo = new Foo<SomeBaseClass>();
  var extenderFoo = new Foo<Extender>();

  // It's also OK to use no <> at all.
  var foo = new Foo();

  // Specifying any non-SomeBaseClass type results in a warning and, in
  // checked mode, a runtime error.
  // var objectFoo = new Foo<Object>();
}

```

### 使用泛型方法

一开始，泛型只能在 ` Dart` 类中使用。 新的语法也支持在方法上使用泛型了：

```dart
T first<T>(List<T> ts) {
  // ...Do some initial work or error checking, then...
  T tmp ?= ts[0];
  // ...Do some additional checking or processing...
  return tmp;
}

```

这里的 `first` (`<T>`) 泛型可以在如下地方使用 参数 `T` ：

- 方法的返回值类型 (`T`).
- 参数的类型 (`List<T>`).
- 局部变量的类型 (`T tmp`).

**版本说明：** 在  `Dart SDK 1.21`开始可以使用泛型方法。

如果你使用了泛型方法，则需要 设置 `SDK` 版本号为 `1.21` 或者更高版本。

### 库和可见性

使用 `import` 和 `library` 指令可以帮助你创建模块化的可共享的代码。库不仅仅提供 `API`， 还是一个私有单元：以下划线 (`_`) 开头的标识符只有在库内部可见。*每个 ` Dart` `app` 都是一个库*， 即使没有使用 `library` 命令也是一个库。

##### 使用库

使用 `import` 来指定一个库如何使用另外一个库。

例如， ` Dart` web 应用通常使用 `Dart:html`库，然后可以这样导入库：

```dart
import 'Dart:html';

```

`import` 必须参数为库 的 `URI`。 对于内置的库，`URI` 使用特殊的 ` Dart:scheme`。 对于其他的库，你可以使用文件系统路径或者 `package: scheme`。 `package:` scheme 指定的库通过包管理器来提供， 例如 `pub` 工具。

```dart
import ' Dart:io';
import 'package:mylib/mylib.Dart';
import 'package:utils/utils.`Dart`';

```

**注意：** *URI* 代表 uniform resource identifier。 *URLs* (uniform resource locators) 是一种常见的 URI。

##### 指定库前缀

如果你导入的两个库具有冲突的标识符， 则你可以使用库的前缀来区分。 例如，如果 `library1` 和 `library2` 都有一个名字为 `Element` 的类， 你可以这样使用：

```dart
import 'package:lib1/lib1.Dart';
import 'package:lib2/lib2.Dart' as lib2;
// ...
Element element1 = new Element();           // Uses Element from lib1.
lib2.Element element2 = new lib2.Element(); // Uses Element from lib2.

```

##### 导入库的一部分

如果你只使用库的一部分功能，则可以选择需要导入的 内容。例如：

```dart
// Import only foo.
import 'package:lib1/lib1.` Dart`' show foo;

// Import all names EXCEPT foo.
import 'package:lib2/lib2.` Dart`' hide foo;

```

#### 延迟载入库

`Deferred loading` (也称之为 `lazy loading`) 可以让应用在需要的时候再加载库。 下面是一些使用延迟加载库的场景：

- 减少 APP 的启动时间。
- 执行 A/B 测试，例如 尝试各种算法的不同实现。
- 加载很少使用的功能，例如可选的屏幕和对话框。

要延迟加载一个库，需要先使用 `deferred as` 来 导入：

```dart
import 'package:deferred/hello.` Dart`' deferred as hello;
```

当需要使用的时候，使用库标识符调用 `loadLibrary()` 方法来加载库，在一个库上你可以多次调用 `loadLibrary()` 方法， 但是该库只载入一次：

```dart
greet() async {
  await hello.loadLibrary();
  hello.printGreeting();
}

```

在前面的代码， 使用 `await` 关键字暂停代码执行一直到库加载完成。 关于 `async` 和 `await` 会在下面的"异步支持"一节介绍。

使用延迟加载库的时候，请注意以下问题：

- 延迟加载库的常量在导入的时候是不可用的， 只有当库加载完毕的时候，库中常量才可以使用。
- 在导入文件的时候无法使用延迟库中的类型， 如果你需要使用类型，则考虑把接口类型移动到另外一个库中， 让两个库都分别导入这个接口库。
- ` Dart` 隐含的把 `loadLibrary()` 方法导入到使用 `deferred as namespace` 中。 `loadLibrary()` 方法返回一个 `Future`。

### 异步支持

` Dart` 有一些语言特性来支持异步编程，最常见的特性是 `async` 方法和 `await` 表达式。

` Dart` 库中有很多返回 `Future` 或者 `Stream` 对象的方法。 这些方法是 异步的： 这些方法在设置完基本的操作后就返回了， 而无需等待操作执行完成。 例如读取一个文件，在打开文件后就返回了。

有两种方式可以使用 `Future` 对象中的数据：

- 使用 `async` 和 `await`
- 使用 `Future API`

同样，从 `Stream` 中获取数据也有两种 方式：

- 使用 `async` 和一个 异步 for 循环 (`await for`)
- 使用 `Stream API`

使用 `async` 和 `await` 的代码是异步的， 但是看起来有点像同步代码。 例如，下面是一些使用 `await` 来等待异步方法返回的示例：

```dart
await lookUpVersion()
```

要使用 `await`，其宿主方法必须带有 `async` 关键字：

```dart
checkVersion() async {
  var version = await lookUpVersion();
  if (version == expectedVersion) {
    // Do something.
  } else {
    // Do something else.
  }
}

```

可以使用 `try`, `catch`, 和 `finally` 来处理使用 `await` 的异常：

```dart
try {
  server = await HttpServer.bind(InternetAddress.LOOPBACK_IP_V4, 4044);
} catch (e) {
  // React to inability to bind to the port...
}

```

### 声明异步方法

一个 `async` 方法是方法体被标记为 `async` 的方法。 虽然异步方法的执行可能需要一定时间，但是异步方法会立刻返回( 在方法体还没执行之前就返回了）：

```dart
checkVersion() async {
  // ...
}

lookUpVersion() async => /* ... */;

```

在一个方法上添加 `async` 关键字，则这个方法返回值为 `Future`。 例如，下面是一个返回字符串 的同步方法：

```dart
String lookUpVersionSync() => '1.0.0';

```

如果使用 `async` 关键字，则该方法 返回一个 `Future`，并且认为该方法是一个耗时的操作。

```dart
Future<String> lookUpVersion() async => '1.0.0';

```

注意，方法的方法体并不需要使用 `Future API`。 ` Dart` 会自动在需要的时候创建 `Future` 对象。

### 使用 await 表达式

`await` 表达式具有如下的形式：

```
await expression
```

在一个异步方法内可以使用多次 `await` 表达式。 例如，下面的示例使用了三次 `await` 表达式 来执行相关的功能：

```dart
var entrypoint = await findEntrypoint();
var exitCode = await runExecutable(entrypoint, args);
await flushThenExit(exitCode);

```

在 `await expression` 中， `expression` 的返回值通常是一个 `Future`， 如果返回的值不是 `Future`，则 ` Dart` 会自动把该值放到 `Future` 中返回。 `Future` 对象代表返回一个对象的承诺（`promise`）。 `await expression` 执行的结果为这个返回的对象。 `await expression` 会阻塞，直到需要的对象返回为止。

**如果 await 无法正常使用，请确保是在一个 async 方法中。** 例如要在 `main()` 方法中使用 `await`， 则 `main()` 方法的方法体必须标记为 `async`：

```dart
main() async {
  checkVersion();
  print('In main: version is ${await lookUpVersion()}');
}

```

### 在循环中使用异步

异步 `for` 循环具有如下的形式：

```
await for (variable declaration in expression) {
  // Executes each time the stream emits a value.
}
```

上面 `expression` 返回的值必须是 `Stream` 类型的。 执行流程如下：

1. 等待直到 `stream` 返回一个数据
2. 使用 stream 返回的参数 执行 `for` 循环代码，
3. 重复执行 1 和 2 直到 `stream` 数据返回完毕。

使用 `break` 或者 `return` 语句可以 停止接收 `stream` 的数据， 这样就跳出了 for 循环并且 从 `stream` 上取消注册了。

**如果异步 for 循环不能正常工作， 确保是在一个 async 方法中使用。** 例如，要想在 `main()` 方法中使用异步 for 循环，则需要把 `main()` 方法的方法体标记为 `async`：

```dart
main() async {
  ...
  await for (var request in requestServer) {
    handleRequest(request);
  }
  ...
}

```

## 可调用的类

如果 ` Dart` 类实现了 `call()` 方法则 可以当做方法来调用。

在下面的示例中，`WannabeFunction` 类定义了一个 `call()` 方法，该方法有三个字符串参数，并且返回三个字符串串联起来的结果。：

```dart
class WannabeFunction {
  call(String a, String b, String c) => '$a $b $c!';
}

main() {
  var wf = new WannabeFunction();
  var out = wf("Hi","there,","gang");
  print('$out');
}
```

### Isolates

现代的浏览器以及移动浏览器都运行在多核 CPU 系统上。 要充分利用这些 CPU，开发者一般使用共享内存数据来保证多线程的正确执行。然而， 多线程共享数据通常会导致很多潜在的问题，并导致代码运行出错。

所有的 ` Dart` 代码在 `isolates`中运行而不是线程。 每个 `isolate` 都有自己的堆内存，并且确保每个 `isolate` 的状态都不能被其他 `isolate` 访问。

### Typedefs

在 ` Dart` 语言中，方法也是对象。 使用 `typedef` 或者 `function-type alias`来为方法类型命名， 然后可以使用命名的方法。 当把方法类型赋值给一个变量的时候，`typedef` 保留类型信息。

下面的代码没有使用 `typedef`：

```dart
class SortedCollection {
  Function compare;

  SortedCollection(int f(Object a, Object b)) {
    compare = f;
  }
}

 // Initial, broken implementation.
 int sort(Object a, Object b) => 0;

main() {
  SortedCollection coll = new SortedCollection(sort);

  // 我们只知道 compare 是一个 Function 类型，
  // 但是不知道具体是何种 Function 类型？
  assert(coll.compare is Function);
}

```

当把 `f` 赋值给 `compare` 的时候， 类型信息丢失了。 `f` 的类型是 `(Object, Object)` → `int` (这里 → 代表返回值类型)， 当然该类型是一个 `Function`。如果我们使用显式的名字并保留类型信息， 开发者和工具可以使用 这些信息：

```dart
typedef int Compare(Object a, Object b);

class SortedCollection {
  Compare compare;

  SortedCollection(this.compare);
}

 // Initial, broken implementation.
 int sort(Object a, Object b) => 0;

main() {
  SortedCollection coll = new SortedCollection(sort);
  assert(coll.compare is Function);
  assert(coll.compare is Compare);
}

```

**注意：** 目前，typedefs 只能使用在 `function` 类型上，但是将来可能会有变化。

由于 `typedefs` 只是别名，他们还提供了一种判断任意 `function` 的类型的方法。例如：

```dart
typedef int Compare(int a, int b);

int sort(int a, int b) => a - b;

main() {
  assert(sort is Compare); // True!
}

```

## 元数据

使用元数据给你的代码添加其他额外信息。 元数据注解是以 `@` 字符开头，后面是一个编译时常量(例如 `deprecated`)或者 调用一个常量构造方法。

所有的 ` Dart` 代码都可以使用三个注解： `@deprecated`、 `@override`、 和 `@proxy`。关于 `@override` 和 `@proxy` 类的拓展部分的示例，下面是使用 `@deprecated` 的 示例：

```dart
class Television {
  /// _Deprecated: Use [turnOn] instead._
  @deprecated
  void activate() {
    turnOn();
  }

  /// Turns the TV's power on.
  void turnOn() {
    print('on!');
  }
}

```

你还可以定义自己的元数据注解。 下面的示例定义了一个带有两个参数的 `@todo` 注解：

```dart
library todo;

class todo {
  final String who;
  final String what;

  const todo(this.who, this.what);
}

```

使用 `@todo` 注解的示例：

```dart
import 'todo.` Dart`';

@todo('seth', 'make this do something')
void doSomething() {
  print('do something');
}

```

元数据可以在 `library`、 `class`、 `typedef`、 `type parameter`、 `constructor`、 `factory`、 `function`、 `field`、 `parameter`、或者 `variable` 声明之前使用，也可以在 `import` 或者 `export` 指令之前使用。 使用反射可以在运行时获取元数据信息。

## 注释

` Dart` 支持单行注释、多行注释和 文档注释。

### 单行注释

单行注释以 `//` 开始。 `//` 后面的一行内容 为 ` Dart` 代码注释。

```dart
main() {
  // TODO: refactor into an AbstractLlamaGreetingFactory?
  print('Welcome to my Llama farm!');
}

```

### 多行注释

多行注释以 `/*` 开始， `*/` 结尾。 多行注释 可以 嵌套。

```dart
main() {
  /*
   * This is a lot of work. Consider raising chickens.

  Llama larry = new Llama();
  larry.feed();
  larry.exercise();
  larry.clean();
   */
}

```

### 文档注释

文档注释可以使用 `///` 开始， 也可以使用 `/**` 开始 并以 `*/` 结束。

在文档注释内， ` Dart` 编译器忽略除了中括号以外的内容。 使用中括号可以引用 `classes`、 `methods`、 `fields`、 `top-level variables`、 `functions`、  `parameters`。中括号里面的名字使用当前注释出现地方的语法范围查找对应的成员。

下面是一个引用其他类和成员 的文档注释：

```dart
/// A domesticated South American camelid (Lama glama).
///
/// Andean cultures have used llamas as meat and pack
/// animals since pre-Hispanic times.
class Llama {
  String name;

  /// Feeds your llama [Food].
  ///
  /// The typical llama eats one bale of hay per week.
  void feed(Food food) {
    // ...
  }

  /// Exercises your llama with an [activity] for
  /// [timeLimit] minutes.
  void exercise(Activity activity, int timeLimit) {
    // ...
  }
}
```