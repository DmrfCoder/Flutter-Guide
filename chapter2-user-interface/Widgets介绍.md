# Widgets介绍

`Flutter Widget`采用现代响应式框架构建，这是从 `React`中获得的灵感，核心思想是用`Widget`构建你的`UI`。 `Widget`描述了对应视图在当前配置和状态下应该是什么样子，当`Widget`的状态发生变化时，`Widget`会重新构建`UI`，`Flutter`会对比前后变化的不同， 以确定底层渲染树从一个状态转换到下一个状态所需的**最小更改**。

### Hello World

一个最简单的`Flutter App`可以调用`runApp()`函数，使用一个`Widget`作为`runApp（）`方法的参数传递进去：

```dart
import 'package:flutter/material.dart';
void main() {
  runApp(
    Center(
      child: Text(
        'Hello, world!',
        textDirection: TextDirection.ltr,
      ),
    ),
  );
}
```

上述代码运行起来的截图如下：

![Simulator Screen Shot - iPhone Xʀ - 2019-06-08 at 19.42.05](http://picture-pool.oss-cn-beijing.aliyuncs.com/2019-06-08-114212.png)

`runApp()`函数的作用是：接收一个`Widget`类型的参数，使其成为整个`Widget`树的根节点。在上面这个例子中，`Widget`树包含了2个`Widget`，分别是`Center Widget`和其孩子——`Text Widget`。框架会强制使根`Widget`覆盖整个屏幕，也就意味着`"Hello，World"`这行字符串会在整个屏幕的正中间。在这个实例中，`Text Widget`中文字显示的方向需要被显式指定，当使用`MaterialApp Widget`的时候，`Text`中文字的方向将不用手动指定(会自动设定)，这一点会在后面展开讲解。

> 注意：这里的`MaterialApp Widget`是`Flutter`开发中最常用的复合`Material Design`设计理念的入口`Widget`，所谓`Material Design`直观理解就是一种更加生动的、好看的视觉设计。

当开发一个`App`的时候，你通常通常会创建新的`Widget`，这些自定义的`Widget`必须是`StatelessWidget`(无状态的)或者`StatefulWidget`(有状态的)的子类， 具体的选择取决于你的`Widget`是否需要管理状态。`Widget`类的核心是实现一个`build()`函数，在该函数中一般是使用其他Widget当构建当前`Widget`，这样就会形成一个`Widget`树，`Flutter`框架将依次构建这些`Widget`，直到构建到最底层的子`Widget`，这些最底层的`Widget`通常为`RenderObject`，它会计算并描述`Widget`的几何形状。

> 注意：`StatelessWidget`和`StatefulWidget`，对应无状态和有状态，可能有的读者不太清除这里的状态时什么意思，直观地来说，如果某个组件从其出生到其死亡整个过程的状态都没有变化(其显示的文字内容没有变化、其背景没有变化…)，那么就称之为无状态的，相反，如果某个组件其状态有可能变化(比如一个按钮点击之后其显示的文字需要产生变化)，那么这种类型的`Widget`就是有状态的。

### 基础Widget

`Flutter`有一套丰富而又强大的基础`Widget`，下面列出了一些常用的：

- `Text`：`Text` 这个`Widget`可以让你在你的`App`中创建一个带格式的文本（说白了就是展示文字用的）；
- `Row`，`Column`：这两个`Widget`让你分别在水平方向（`Row`）和垂直方向（`Column`）创建自适应的灵活布局，即线性布局（当我们需要创建一些处于同一行或者同一列的Widgets时需要用到这两个），其设计使基于`web`开发中的`Flexbox`布局模型；
- `Stack`：与线性布局不同的是，`Stack Widget`允许子`Widget`进行堆叠（比如我们需要在一个背景图片上放置一个按钮，这时背景图片就是第一层，按钮覆盖背景图片作为第二层），你可以在`Stack`的子`Widget`中使用`Positioned Widget`来定位子`Widget`相对于`Stack Widget`上、下、左、右四条边的位置。`Stack Widget`是基于`Web`开发中的绝对定位布局模型设计的；
- `Container`：`Container Widget`是你可以创建一个矩形的可视元素，一个`Container`可以被一个`BoxDecoration`装饰，比如背景（`background`）、边框（`border`）、阴影（`shadow`）。`Container`也具有`margin`、`padding`和应用于其大小的约束(`Constraints`)。另外，`Container`可以使用矩阵在三维空间中进行变换。

以下是一些简单的`Widget`相互组合为新的`Widget`的例子：

```dart
import 'package:flutter/material.dart';

class MyAppBar extends StatelessWidget {
  MyAppBar({this.title});

  // Widget子类中的字段通常被定义为final的
  final Widget title;

  @override
  Widget build(BuildContext context) {
    return Container(
      height: 56.0, // 单位是逻辑上的像素（非绝对像素）
      padding: const EdgeInsets.symmetric(horizontal: 8.0),
      decoration: BoxDecoration(color: Colors.blue[500]),
      // 使用Row构建一个水平方向的线性布局
      child: Row(
        // <Widget>是下面的列表项的类型
        children: <Widget>[
          IconButton(
            icon: Icon(Icons.menu),
            tooltip: 'Navigation menu',
            onPressed: null, // null表示忽略/禁用当前IconButton的点击事件
          ),
          // Expanded会填充满可用的所有空间 
          Expanded(
            child: title,
          ),
          IconButton(
            icon: Icon(Icons.search),
            tooltip: 'Search',
            onPressed: null,
          ),
        ],
      ),
    );
  }
}

class MyScaffold extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    // Material在概念上相当于一张”纸“ ，用户界面是显示在这张”纸“上面的
    return Material(
      // Column是一个垂直方向的线性布局
      child: Column(
        children: <Widget>[
          MyAppBar(
            title: Text(
              'Example title',
              style: Theme.of(context).primaryTextTheme.title,
            ),
          ),
          Expanded(
            child: Center(
              child: Text('Hello, world!'),
            ),
          ),
        ],
      ),
    );
  }
}

void main() {
  runApp(MaterialApp(
    title: 'My App', 
    home: MyScaffold(),
  ));
}
```

请确保在你的`pubspec.yaml`文件中声明了`uses-material-design:true`，这个声明确保我们可以使用一些`Material icons`：

```
name: my_App
flutter:
  uses-material-design: true
```

很多`Material Design`风格的`Widget`需要处于`MaterialApp`的内部才能正确地显示，这样才能继承`Material`不同主题下的数据，所以我们通过`MaterialApp`来运行`App`。

上面的代码运行起来的效果是这样的：

![image-20190608202119521](http://picture-pool.oss-cn-beijing.aliyuncs.com/2019-06-08-122119.png)

可以看到，我们在在`MyAppBar`中创建一个`Container`，高度为56像素（逻辑像素），其左侧和右侧均有8像素的填充。在容器内部， `MyAppBar`使用`Row` 布局来排列其子项。 中间的` Widget`被标记为`Expanded` ，这意味着它会填充尚未被其他子项占用的的剩余可用空间。`Expanded`可以拥有多个`children`， 然后使用`flex`参数来确定他们占用剩余空间的比例。

`MyScaffold` 通过一个`Column Widget` ，在垂直方向排列其子项。在`Column`的顶部，放置了一个`MyAppBar`实例，将一个`Text Widget`作为其标题传递给应用程序栏。将`Widget`作为参数传递给其他`Widget`是一种强大的机制，它可以让您创建各种复杂的`Widget`。最后，`MyScaffold`使用了一个正中间包含一条`"Hello world"`信息的`Expanded`来填充剩余的空间，

*更多信息请参阅布局篇(正在完成中)。*

### 使用Material 组件

`Flutter`提供了许多`Widgets`，可帮助您构建遵循`Material Design`的应用程序。构建一个`Material`应用程序应该从使用`MaterialApp Widget`开始， 它在应用程序的根部创建了许多有用的`Widget`，其中包括一个`Navigator`， 它管理由字符串标识的`Widget`栈（即页面路由栈）。`Navigator`可以让您的应用程序在页面之间的平滑地切换。 当然，是否使用`MaterialApp`完全是可选的，但是使用它是一个很好的做法。

```dart
import 'package:flutter/material.dart';

void main() {
  runApp(MaterialApp(
    title: 'Flutter Tutorial',
    home: TutorialHome(),
  ));
}

class TutorialHome extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    // Scaffold是Material中主要的布局组件
    return Scaffold(
      appBar: AppBar(
        leading: IconButton(
          icon: Icon(Icons.menu),
          tooltip: 'Navigation menu',
          onPressed: null,
        ),
        title: Text('Example title'),
        actions: <Widget>[
          IconButton(
            icon: Icon(Icons.search),
            tooltip: 'Search',
            onPressed: null,
          ),
        ],
      ),
      // body属性值屏幕的主体
      body: Center(
        child: Text('Hello, world!'),
      ),
      floatingActionButton: FloatingActionButton(
        tooltip: 'Add', 
        child: Icon(Icons.add),
        onPressed: null,
      ),
    );
  }
}
```

上面的代码运行效果如下：

![屏幕快照 2019-06-08 20.35.42](http://picture-pool.oss-cn-beijing.aliyuncs.com/2019-06-08-123551.png)

现在我们的代码已经从`MyAppBar`和`MyScaffold`变为了`AppBar`和 `Scaffold`， 我们的应用程序现在看起来已经有一些`“Material”`了！例如，应用栏有一个阴影，标题文本会自动继承正确的样式。我们还添加了一个浮动操作按钮，以便进行相应的操作处理。

请注意，我们再次将`Widget`作为参数传递给其他`Widget`。`Scaffold Widget` 会以命名参数的形式接收许多不同的`Widget`参数，传递给`Scaffold`的每一个`Widget`参数对应的`Widget`都会被放置在`Scaffold`布局中相应的位置，类似的，`AppBar`中，我们也给参数`leading`、`actions`、`title`分别传一个`Widget`作为参数。 这种模式在整个框架中会经常出现，这也可能是您在设计自己的`Widget`时会考虑到一点。

### 处理手势

大多数应用程序都包括某种形式的用户与系统的交互。构建交互式应用程序的第一步是检测用户输入的手势。让我们通过创建一个简单的按钮来了解这是如何工作的：

```dart
class MyButton extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return GestureDetector(
      onTap: () {
        print('MyButton was tApped!');
      },
      child: Container(
        height: 36.0,
        padding: const EdgeInsets.all(8.0),
        margin: const EdgeInsets.symmetric(horizontal: 8.0),
        decoration: BoxDecoration(
          borderRadius: BorderRadius.circular(5.0),
          color: Colors.lightGreen[500],
        ),
        child: Center(
          child: Text('Engage'),
        ),
      ),
    );
  }
}
```



这里的这个`GestureDetector Widget`并不具有显示效果（即用户看不到），而是检测由用户的手势。 当用户点击其`Child`时(这里是`Container`）， `GestureDetector`会调用它的`onTap`回调方法， 在回调方法中，将消息打印到控制台。您可以使用`GestureDetector`来检测各种输入手势，包括点击、拖动和缩放。

许多`Widget`都会使用一个`GestureDetector`为其他`Widget`提供可选的回调。 例如，`IconButton`、 `RaisedButton`、 和`FloatingActionButton` ，它们都有一个`onPressed`回调，它会在用户点击该`Widget`时被触发。

我们将上面代码所描述的这个`Button`放置在屏幕中间：

![image-20190608204320884](http://picture-pool.oss-cn-beijing.aliyuncs.com/2019-06-08-124321.png)

点击按钮之后：

![image-20190608204620163](http://picture-pool.oss-cn-beijing.aliyuncs.com/2019-06-08-124620.png)

### 改变Widget状态以响应户的输入

到目前为止，我们只使用了无状态的`Widget`。无状态`Widget`从它们的父`Widget`接收参数， 这些参数被存储在`final`型的成员变量中。 当一个`Widget`被要求构建时，它使用这些存储的值作为参数来构建`Widget`。

为了构建更复杂的体验 （例如，对用户输入事件做出响应）， 应用程序通常会携带一些状态。 `Flutter`使用`StatefulWidgets`来满足这种需求。`StatefulWidgets`是特殊的`Widget`，它知道如何生成`State`对象，然后用它来保持状态。 下面是个简单的例子，其中使用了前面提到`RaisedButton`：

```dart
class Counter extends StatefulWidget {
  //这个类的作用是配置状态(State)，它保存着由父级提供并由状态的build（）方法使用的值(在本例中暂时没有)，Widget子类中的字段总是标记为“final”。
  @override
  _CounterState createState() => _CounterState();
}

class _CounterState extends State<Counter> {
  int _counter = 0;

  void _increment() {
    setState(() {
      /* 调用setState()的作用是告诉flutter框架此状态发生了一些更改，这将导致下面的build()方法被重新调用，以便显示更新后的值。如果你调用setState()方法而直接改变_counter的值，则不会调用build方法，自然界面上什么都不会改变。*/
      _counter++;
    });
  }

  @override
  Widget build(BuildContext context) {
    /**
    每次调用setState()时都会重新运行此方法(例如，调用上面的_increment方法)
Flutter框架对build（）方法的运行进行了优化，使得其可以快速运行，这样你就可以只重建任何需要更新的东西，不必单独更改需要更新的Widget的实例。
    */
    return Row(
      children: <Widget>[
        RaisedButton(
          onPressed: _increment,
          child: Text('Increment'),
        ),
        Text('Count: $_counter'),
      ],
    );
  }
}
```

您可能想知道为什么`StatefulWidget`和`State`是两个不同的对象。在`Flutter`中，这两种类型的对象具有不同的生命周期： `Widget`是临时对象，用于构建当前状态下的应用程序（也就是说使用每调用一次`build（）`，都会产生新的`Widget`对象以替换旧的`Widget`对象），而`State`对象在多次调用`build()`时保持不变，允许它们记住信息(状态)。

上面的例子接受用户点击，并在点击时使`_counter`自增，然后直接在其`build`方法中使用`_counter`值。在更复杂的应用程序中，`Widget`结构层次的不同部分可能有不同的职责。例如，一个`Widget`可能呈现一个复杂的用户界面，其目标是收集特定信息（如日期或位置），而另一个`Widget`可能会使用该信息来更改整体的显示。

在`Flutter`中，事件流是**“向上”**传递的，而状态流是**“向下”**传递的即子Widget到父Widget是通过事件通信，而父到子是通过状态进行通信，重定向这一流程的共同父元素是State。让我们看看这个稍微复杂的例子是如何工作的：

```dart
class CounterDisplay extends StatelessWidget {
  CounterDisplay({this.count});

  final int count;

  @override
  Widget build(BuildContext context) {
    return Text('Count: $count');
  }
}

class CounterIncrementor extends StatelessWidget {
  CounterIncrementor({this.onPressed});

  final VoidCallback onPressed;

  @override
  Widget build(BuildContext context) {
    return RaisedButton(
      onPressed: onPressed,
      child: Text('Increment'),
    );
  }
}

class Counter extends StatefulWidget {
  @override
  _CounterState createState() => _CounterState();
}

class _CounterState extends State<Counter> {
  int _counter = 0;

  void _increment() {
    setState(() {
      ++_counter;
    });
  }

  @override
  Widget build(BuildContext context) {
    return Row(children: <Widget>[
      CounterIncrementor(onPressed: _increment),
      CounterDisplay(count: _counter),
    ]);
  }
}
```

注意我们是如何创建了两个新的无状态`Widget`的，我们分离了显示计数器（`CounterDisplay`）和更改计数器（`CounterIncrementor`）的逻辑， 尽管最终效果与前一个示例相同，但责任分离允许将复杂性逻辑封装在各个单个的`Widget`中，同时保持父`Widget`的简单性。

### 将所有内容整合到一起

让我们考虑一个更完整的例子，将上面介绍的概念汇集在一起。我们假设一个购物应用程序，该应用程序显示出售的各种产品，并维护一个购物车。 我们先来定义`ShoppingListItem`：

```dart
class Product {
  const Product({this.name});
  final String name;
}

typedef void CartChangedCallback(Product product, bool inCart);

class ShoppingListItem extends StatelessWidget {
  ShoppingListItem({Product product, this.inCart, this.onCartChanged})
      : product = product,
        super(key: ObjectKey(product));

  final Product product;
  final bool inCart;
  final CartChangedCallback onCartChanged;

  Color _getColor(BuildContext context) {
    // 由于Widget树的不同部分可以有不同的主题（theme），所以这里需要一个context来标识build（）方法对应在界面上的位置，这样就能确定当前的主题（theme）。
		 return inCart ? Colors.black54 : Theme.of(context).primaryColor;
  }

  TextStyle _getTextStyle(BuildContext context) {
    if (!inCart) return null;

    return TextStyle(
      color: Colors.black54,
      decoration: TextDecoration.lineThrough,
    );
  }

  @override
  Widget build(BuildContext context) {
    return ListTile(
      onTap: () {
        onCartChanged(product, !inCart);
      },
      leading: CircleAvatar(
        backgroundColor: _getColor(context),
        child: Text(product.name[0]),
      ),
      title: Text(product.name, style: _getTextStyle(context)),
    );
  }
}
```

该`ShoppingListItem Widget` 是无状态的。它将其在构造函数中接收到的值存储在`final`成员变量中，然后在`build`函数中使用它们。 例如，`inCart`布尔值表示在两种视觉展示效果之间切换：一个使用当前主题的主色，另一个使用灰色。

当用户点击列表项（`ListView Item`）时，`Widget`不会直接修改其`inCart`的值。相反，`Widget`会调用其父`Widget`给它的`onCartChanged`回调函数。 此模式可让您在`Widget`层次结构中存储更高的状态，从而使状态持续更长的时间。在极端情况下，存储传给`runApp()`应用程序的`Widget`的状态将在的整个程序的生命周期中持续存在。

当父`Item`收到`onCartChanged`回调时，父`Item`将更新其内部状态，这将触发父`Item`使用新`inCart`值重建`ShoppingListItem`实例。 虽然父 `ShoppingListItem`在重建时创建了一个新实例，但该操作开销很小，因为`Flutter`框架会将新构建的`Widget`与先前构建的`Widget`进行比较，并仅将差异部分应用于底层的`RenderObject`。

我们来看看父`Widget`存储可变状态的示例：

```dart
class ShoppingList extends StatefulWidget {
  ShoppingList({Key key, this.products}) : super(key: key);

  final List<Product> products;

  /**
  框架在Widget树中的给定位置首次出现小部件时调用CreateState。如果父对象重建并使用相同类型的Widget（具有相同的Key），框架将重用状态对象，而不是创建新的状态对象。
  */
  @override
  _ShoppingListState createState() => _ShoppingListState();
}

class _ShoppingListState extends State<ShoppingList> {
  Set<Product> _shoppingCart = Set<Product>();

  void _handleCartChanged(Product product, bool inCart) {
    setState(() {
      /**
      当用户更改购物车中的内容时，您需要在setstate()中更改购物车以触发rebuild。然后框架会调用下面build()，更新应用程序的外观。
      */

      if (!inCart)
        _shoppingCart.add(product);
      else
        _shoppingCart.remove(product);
    });
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text('Shopping List'),
      ),
      body: ListView(
        padding: EdgeInsets.symmetric(vertical: 8.0),
        children: widget.products.map((Product product) {
          return ShoppingListItem(
            product: product,
            inCart: _shoppingCart.contains(product),
            onCartChanged: _handleCartChanged,
          );
        }).toList(),
      ),
    );
  }
}

void main() {
  runApp(MaterialApp(
    title: 'Shopping App',
    home: ShoppingList(
      products: <Product>[
        Product(name: 'Eggs'),
        Product(name: 'Flour'),
        Product(name: 'Chocolate chips'),
      ],
    ),
  ));
}
```

`ShoppingList`类继承自`StatefulWidget`，这意味着这个`Widget`可以存储状态。 当`ShoppingList`首次插入到`Widget`树中时，框架会调用其 `createState` 函数以创建一个新的`_ShoppingListState`实例来与该`Widget`树中的相应位置关联（请注意，我们通常命名`State`子类时带一个下划线，这表示其是私有的）。 当这个`Widget`的父级`Widget`重建时，父级`Widget`将创建一个新的`ShoppingList`实例，`Flutter`框架将重用已经在`Widget`树中的`_ShoppingListState`实例，而不是再次调用`createState`创建一个新的。

要访问当前`ShoppingList`的属性，`_ShoppingListState`可以借助它的`widget`属性（比如上面代码中的`widget.products`）。 

处理`onCartChanged`回调时，`_ShoppingListState`通过添加或删除产品来改变其内部`_shoppingCart`状态。 为了通知框架它内部的状态发生了改变，需要调用`setState`（）方法。调用`setState`将该`Widget`标记为”`dirty`”(脏的)，并且计划在下次应用程序需要更新屏幕时重新构建它。 如果在修改`Widget`的内部状态后忘记调用`setState`，框架将不知道您的`Widget`是”`dirty`”(脏的)，并且可能不会调用`Widget`的`build`方法，这意味着用户界面可能不会更新以展示新的状态。

通过以这种方式管理状态，您不需要编写用于创建和更新子`Widget`的单独代码。相反，您只需实现可以处理这两种情况的`build()`函数。

上述代码运行起来后如下图所示：

![image-20190608212911411](http://picture-pool.oss-cn-beijing.aliyuncs.com/2019-06-08-132911.png)

### 响应Widget的生命周期事件

在`StatefulWidget`调用`createState`之后，框架将新的状态对象插入`Widget`树中，然后调用`state`对象的`initState()`方法。 子类`State`可以重写`initState()`以完成一些仅需要执行一次的工作。 例如，您可以重写`initState()`以配置动画或进行一些全局配置。重写`initState()`的时候需要注意调用`super.initState()`。

当一个`state`对象不再需要时，框架会调用该`state`对象的`dispose()`方法。 您可以重写该`dispose()`方法来执行清理工作。例如，您可以重写`dispose()`方法以取消定时器。 `dispose()`典型的实现是直接调用`super.dispose()`。

### Key

使用`key`可以控制当前`Widget`在重建时与哪个其他的`Widget`匹配（比如，当前有一个`Widget A1`和`Widget B1`，`key`的作用就是当下一次`build（）`发生时，`Widget A2`是应该基于`WidgetA1`重建还是基于`Widget B1`重建）。默认情况下，`Flutter`框架根据`Widget`的`runtimeType`和它们的显示顺序来匹配。 使用`key`时，框架要求两个`Widget`具有相同的`key`和`runtimeType`.

`Key`在构建相同类型`Widget`的多个实例时很有用。例如，`ShoppingList`构建很多个`ShoppingListItem`实例以填充其可见区域时：

- 如果没有`key`，当前构建（`build`）中的第一个条目（`Item`）将始终与前一个构建（`build`）中的第一个条目同步，即使在语义上，列表中的第一个条目如果滚动出屏幕，那么它将不会再在窗口中可见。
- 通过给列表中的每个条目分配为“语义” `key`，无限列表可以更高效，因为框架将同步条目与匹配的语义`key`并因此具有相似（或相同）的可视外观。 此外，语义上同步条目意味着在有状态子`Widget`中，保留的状态将附加到相同的语义条目上，而不是附加到相同数字位置上的条目。

### Global Key

您可以使用全局`key`来唯一标识子`Widget`。全局`key`在整个`Widget`层次结构中必须是全局唯一的，这与局部`key`不同，后者只需要在同级中唯一。由于它们是全局唯一的，因此可以使用全局`key`来检索与`Widget`关联的状态。

