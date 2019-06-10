# 给你的App添加交互

本文的主要内容：

- 如何响应点击事件
- 如何自定义`Widget`
- `Stateless Widget`和`Stateful Widget`的不同

`Flutter`中按照是否自身可直接响应用户交互可以将`Widget`分为两类：

- 第一类：自身可直接响应用户交互，指的是`Widget`自身具有如`onTap`这类的属性，可以通过这类属性直接监听用户的点击等事件，典型的比如`FlatButton`等
- 第二类：自身不能直接响应用户交互，指的是`Widget`自身没有入`onTap`这类属性，不能直接监听用户的点击等事件，典型的比如`Icon`等

由于第一类比较简单，本文重点介绍一下第二类，即如何为非交互性（不能直接响应用户交互）的`Widget`添加交互性， 具体来说，我们将通过创建一个自定义的`Statful Widget`来让`Icon`具有交互性。

在上一篇文章中我们介绍了如何构建一个下面这样的`UI`页面：

![The finished app](http://picture-pool.oss-cn-beijing.aliyuncs.com/2019-06-10-050013.jpg)

当这个`app`第一次运行的时候那个星星是红色的，代表这个屏幕中展示的那个图片被用户点击了喜欢，星星后面的数字47代表一共有47个用户点击了喜欢。本文将实现，点击星星后移除喜欢状态，用空心星星替换实心星星并减少星星后面的计数。 再次点击空心星星代表添加喜欢，会绘制一颗实心的星星并增加星星后的数字。

![The custom widget you'll create](http://picture-pool.oss-cn-beijing.aliyuncs.com/2019-06-10-090817.png)

要实现此功能，您将创建一个包含星星和计数的自定义`Widget`， 点击星星会更改两个子`Widget`的状态，因此自定义的Widget应该同时管理这两个子`Widget`（星星和计数）。

首先我将会介绍一点前备知识，如果你只对最终的代码实现感兴趣，你可以直接跳到[第2步：创建StatefulWidget的子类](#subclass-statefulWidget)，如果你想尝试其他的管理状态的方法，可以直接跳到[管理状态](#manage-state)一节。

### `Stateful Widget`和`Stateless Widget`

一个`Widget`要么是有状态（`stateful`）的，要么是无状态（`stateless`）的，如果一个`Widget`是可改变的，比如当用户与其交互的时候其会产生变化，这个`Widget`就是有状态的(`stateful`)。

一个无状态(`stateless`)的Widget是永远不会发生改变的，`Icon`、`IconButton`、`Text`都是典型的无状态的`Widget`，无状态(`stateless`)的`Widget`都是`StatelessWidget`的子类。

一个有状态(`stateful`)的`Widget`是动态的，比如它可以更改其外观以响应用户交互或接收数据时触发的事件。`CheckBox`、`Radio`、`Slider`、`InkWell`、`Form`、`TextField`都是典型的有状态的`Widget`，有状态(`stateful`)的`Widget`都是`StatefulWidget`的子类。

`Widget`的状态都是保存在`State`对象中的，从外观上分析小部件的状态。 状态由可以更改的值组成，例如滑块（`slider`）的当前值、是否选中复选框（`CheckBox`）。 当`Widget`的状态发生变化时，`State`对象调用会`setState（）`方法来告诉框架重绘该`Widget`。

### 创建一个有状态的（`stateful`）`Widget`

明确几点概念：

- 一个有状态的`Widget`一定实现了2个类：`StatefulWidget`、`State`
- `State`类包含`Widget`的可变状态以及`build()`方法
- 当`Widget`的状态(`state`)发生了改变，`State`对象将会调用`setState()`方法高速`Flutter`框架需要重绘当前`Widget`

本节将创建一个自定义的有状态(`Stateful`)的`Widget`，我们将用我们自定义的包含一个`IconButton`和一个`Text`的`Widget`来替代原有的红色星星`Widget`和计数`Widget`。

实现一个自定义的`Widget`需要创建2个类：

- 一个`StatefulWidget`类的子类，用于定义`Widget`
- 一个`State`类的子类，包含了`State`对象，并且定义`build()`方法

我们通过简单的几步来构建一个名为`FavoriteWidget`的自定义`Widget`：

#### 第1步：决定由那个对象来管理`Widget`的状态(`State`)

`Widget`的状态(`State`)可以有多种管理方式，在此处由于切换星星的状态（实心还是空心）是一个独立的操作，不会影响父`Widget`或`UI`的其余部分，所以我们让`Widget`自己管理自己的状态(`State`)。

关于详细的状态管理的内容，我会在后面的[管理状态](#manage-state)一节介绍。

#### <span id="subclass-statefulWidget">第2步：创建`StatefulWidget`的子类</span>

由于第1步我们已经决定了`FavoriteWidget`自己管理自己的状态(`State`)，所以我们应该重写`createState()`方法来创建一个`State`对象。`Flutter`框架会在构建`Widget`的时候调用对应`Widget`的`createState()`方法。在这个例子中，我们应该在`createState()`方法中返回一个我们将在下一步定义的`_FavoriteWidgetState`类的实例对象：

```dart
class FavoriteWidget extends StatefulWidget {
  @override
  _FavoriteWidgetState createState() => _FavoriteWidgetState();
}
```

> 注意：这里的_开头指的是定义的对应类是私有的。

#### 第3步：创建`State`类的子类

我们定义一个`_FavoriteWidgetState`类来存储会在`Widget`不同生命周期变化的数据，当`app`第一次运行的时候，`UI`界面应该展示红色的实心星星，代表当前已经选择了"喜欢"状态，并且傍边展示的文字为"41"，我们本别使用`bool _isFavorited`和`int _favoriteCount`变量来存储这两个状态：

```dart
class _FavoriteWidgetState extends State<FavoriteWidget> {
  bool _isFavorited = true;
  int _favoriteCount = 41;
  // ···
}
```

`_FavoriteWidgetState`类同样也定义了一个`build()`方法，在该方法中创建一个`Row`（行），`Row`中包含有一个`Iconbutton`和一个`Text`，我们使用`Iconbutton`而不是`Icon`的原因是`IconButton`有`onPressed`属性，我们可以通过这个`onPressed`属性定义处理点击事件的回调函数(` _toggleFavorite`)，我们将在后面具体定义这个` _toggleFavorite`函数：

```dart
class _FavoriteWidgetState extends State<FavoriteWidget> {
  // ···
  @override
  Widget build(BuildContext context) {
    return Row(
      mainAxisSize: MainAxisSize.min,
      children: [
        Container(
          padding: EdgeInsets.all(0),
          child: IconButton(
            icon: (_isFavorited ? Icon(Icons.star) : Icon(Icons.star_border)),
            color: Colors.red[500],
            onPressed: _toggleFavorite,
          ),
        ),
        SizedBox(
          width: 18,
          child: Container(
            child: Text('$_favoriteCount'),
          ),
        ),
      ],
    );
  }
}
```

注意：我们这里将`Text`作为子`Widget`放置在了`SizedBox`中，并且设置了`SizedBox`的宽度，这样做的作用是固定`Text`的宽度，设想一下，当`Text`中只显示1位数字的时候`Text`的宽度和显示2位数字的宽度一定是不一样的，如果不固定`Text`的宽度，当数字变化的时候就会出现`Text`宽度发生跳变的情况，导致视觉效果很不好。

当`IconButton`被点击的时候将会调用` _toggleFavorite()`方法，我们在_toggleFavorite()方法中调用setstate()方法并更新状态，这样Flutter框架就会知道需要重新绘制当前`Widget`了，从而达到更新界面的效果：

```dart
void _toggleFavorite() {
  setState(() {
    if (_isFavorited) {
      _favoriteCount -= 1;
      _isFavorited = false;
    } else {
      _favoriteCount += 1;
      _isFavorited = true;
    }
  });
}
```

`setState()`方法中的代码逻辑很简单，首先判断当前` _isFavorited`的状态，然后对` _isFavorited`和` _isFavorited`的值进行更新。

#### 第4步：将我们自定义的`Stateful Widget`加入到`Widget`树中

我们应该在`app`的`build()`方法中将我们自定义的`Stateful Widget`加入到`Widget` 树中，首先找到原先`Icon`和`Text`的位置，然后删除原来的代码，加入新的我们创建的`Stateful Widget`：

```dart
 Widget titleSection = Container(
    padding: const EdgeInsets.all(32),
    child: Row(
      children: [
        Expanded(
          /*1*/
          child: Column(
            crossAxisAlignment: CrossAxisAlignment.start,
            children: [
              /*2*/
              Container(
                padding: const EdgeInsets.only(bottom: 8),
                child: Text(
                  'Oeschinen Lake Campground',
                  style: TextStyle(
                    fontWeight: FontWeight.bold,
                  ),
                ),
              ),
              Text(
                'Kandersteg, Switzerland',
                style: TextStyle(
                  color: Colors.grey[500],
                ),
              ),
            ],
          ),
        ),
        FavoriteWidget(),
      ],
    ),
  );
```

然后运行代码(推荐使用热更新)，可以看到效果图：

![img](http://picture-pool.oss-cn-beijing.aliyuncs.com/2019-06-10-123912.gif)

### <span id="manage-state">管理状态</span>

在我们的设计中，到底应该由谁来管理`Widget`的状态(`State`)？是`Widget`本身？是`Widget`的父`Widget`？还是二者共同管理？还是另一个对象来管理？ 事实上有不止一种有效的方法可以使你的`Widget`小部件具有交互性， 作为`Widget`的设计者，你可以根据预期的`Widget`的使用方式做出决策。 以下是几种最常用的管理状态的方法：

- 第一种：[Widget自己管理自己本身的State](#manage-it-own)
- 第二种：[Widget的父级Widget管理其State](#parent-manage)
- 第三种：[混合使用前两种方法](#mix-and-match)

你可能会有疑问，你应该如何决定具体使用哪一种状态管理方法？这里提供几个原则供你参考：

- 如果状态代表的是用户数据，比如`CheckBox`是否被选中，或者`Slider`（进度条）的当前进度，这种情况下最好让`Widget`的父级`Widget`去管理其状态
- 如果状态代表的是界面展示方面的，比如动画，这种情况最好由`Widget`自己来管理自己的状态

如果你不太确定自己的场景属于以上哪种，可以直接使用父级`Widget`管理的方法，因为这个方法是通用的。

接下来我将通过创建三个简单示例（`TapboxA`，`TapboxB`和`TapboxC`）来举例说明管理状态的不同方法。 这几个示例的工作方式类似： 每个都创建了一个`Container`，当点击时，可以在绿色或灰色框之间切换， `_active`布尔值确定颜色：`true`代表绿色，`false`代表灰色。

![image-20190610193224492](http://picture-pool.oss-cn-beijing.aliyuncs.com/2019-06-10-113224.png)

#### <span id="manage-it-own">`Widget`自己管理自己本身的`State`</span>

有时，由`Widget`自己管理自己的状态可以产生很强大的功能。例如，`ListView`在其内容的总尺寸超出其最大渲染框的尺寸时会自动进行滚动，这个滚动的状态是由`ListView`自己管理的，不需要我们开发人员去手动设置它什么时候应该开始滚动、什么时候应该停止滚动。

我们通过一个示例来进行说明，我们创建一个`_TapboxAState`类：

- 管理`TapboxA`的状态
- 定义布尔值`_activity`，代表当前`Widget`的颜色
- 定义`_handleTap()`方法，当`Widget`被点击时在该方法中调用`setState（）`并更新 `_activity`的值从而达到更新UI的目的
- 实现`Widget`的所有交互行为

代码如下：

```dart
// TapboxA 自己管理自己的状态

//------------------------- TapboxA ----------------------------------

class TapboxA extends StatefulWidget {
  TapboxA({Key key}) : super(key: key);

  @override
  _TapboxAState createState() => _TapboxAState();
}

class _TapboxAState extends State<TapboxA> {
  bool _active = false;

  void _handleTap() {
    setState(() {
      _active = !_active;
    });
  }

  Widget build(BuildContext context) {
    return GestureDetector(
      onTap: _handleTap,
      child: Container(
        child: Center(
          child: Text(
            _active ? 'Active' : 'Inactive',
            style: TextStyle(fontSize: 32.0, color: Colors.white),
          ),
        ),
        width: 200.0,
        height: 200.0,
        decoration: BoxDecoration(
          color: _active ? Colors.lightGreen[700] : Colors.grey[600],
        ),
      ),
    );
  }
}

//------------------------- MyApp ----------------------------------

class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Flutter Demo',
      home: Scaffold(
        appBar: AppBar(
          title: Text('Flutter Demo'),
        ),
        body: Center(
          child: TapboxA(),
        ),
      ),
    );
  }
}
```

运行效果如下图所示：

![a8o2c-uqnut](http://picture-pool.oss-cn-beijing.aliyuncs.com/2019-06-10-114407.gif)

#### <span id="parent-manage">`Widget`的父级`Widget`管理其`State`</span>

父`Widget`管理子`Widget`状态的最大用处是在合适的时机通知子`Widget`进行`UI`更新。 例如，`IconButton`允许你将`Icon`视为可点击的按钮， `IconButton`是一个无状态的`Widget`，所以我们应该通过父`Widget`来确定`Iconutton`是否已被点击。

在以下例子中，`TapboxB`将其状态回调给父`Widget`，因为`TapboxB`不管理任何状态，所以它是`StatelessWidget`的子类。

在这个示例中我们应该实现2个类：`ParentWidgetState`(代表父`Widget`)、`TapboxB`(代表子`Widget`)

`ParentWidgetState`的主要功能：

- 为`TapboxB`管理`_activity`状态
- 实现 `_handleTapboxChanged()`方法，该方法会在`TapboxB`被点击时调用
- 当状态发生改变时调用`setState()`来更新`UI`

`TapboxB`的主要功能：

- 继承`StatelessWidget`类，因为`TapboxB`不用管理自己的状态
- 当点击事件(`tap`)被触发的时候通知父`Widget`

代码实现如下：

```dart
// ParentWidget为TapboxB管理状态.

//------------------------ ParentWidget --------------------------------

class ParentWidget extends StatefulWidget {
  @override
  _ParentWidgetState createState() => _ParentWidgetState();
}

class _ParentWidgetState extends State<ParentWidget> {
  bool _active = false;

  void _handleTapboxChanged(bool newValue) {
    setState(() {
      _active = newValue;
    });
  }

  @override
  Widget build(BuildContext context) {
    return Container(
      child: TapboxB(
        active: _active,
        onChanged: _handleTapboxChanged,
      ),
    );
  }
}

//------------------------- TapboxB ----------------------------------

class TapboxB extends StatelessWidget {
  TapboxB({Key key, this.active: false, @required this.onChanged})
      : super(key: key);

  final bool active;
  final ValueChanged<bool> onChanged;

  void _handleTap() {
    onChanged(!active);
  }

  Widget build(BuildContext context) {
    return GestureDetector(
      onTap: _handleTap,
      child: Container(
        child: Center(
          child: Text(
            active ? 'Active' : 'Inactive',
            style: TextStyle(fontSize: 32.0, color: Colors.white),
          ),
        ),
        width: 200.0,
        height: 200.0,
        decoration: BoxDecoration(
          color: active ? Colors.lightGreen[700] : Colors.grey[600],
        ),
      ),
    );
  }
}
```

代码的运行效果如下：

![a8o2c-uqnut](http://picture-pool.oss-cn-beijing.aliyuncs.com/2019-06-10-114407.gif)

#### <span id="mix-and-match">混合使用前两种管理方法</span>

对于某些`Widget`，使用混合的方法管理其状态很有有意义。 在这种情况下，有状态（`stateful`）的`Widget`和其父`Widget`分别管理其一部分状态(`State`)。

在`TapboxC`示例中，在点击时，框周围会出现深绿色边框，点击后，边框消失，框的颜色也会改变。 `TapboxC`将其 `_active`状态导出到其父`Widget`，在内部管理只其 `_highlight`状态，所以 此示例有两个`State`对象，`_ParentWidgetState`和`_TapboxCState`：

`_ParentWidgetState`的功能：

- 管理`_activity`状态
- 实现`_handleTapboxChanged()`方法，该方法会在方框被点击后调用
- 当点击事件发生后调用`setState()`并改变`_activity`的值以更新`UI`

`_TapboxCState`的功能：

- 管理`_highlight`状态
- `GestureDetector`监听所有的点击事件，当用户手指点下的时候添加高亮边框，当用户手指抬起的时候取消高亮边框
- 当点击事件发生时，根据父`Widget`传递的状态进行相应操作

```dart
//---------------------------- ParentWidget ----------------------------

class ParentWidget extends StatefulWidget {
  @override
  _ParentWidgetState createState() => _ParentWidgetState();
}

class _ParentWidgetState extends State<ParentWidget> {
  bool _active = false;

  void _handleTapboxChanged(bool newValue) {
    setState(() {
      _active = newValue;
    });
  }

  @override
  Widget build(BuildContext context) {
    return Container(
      child: TapboxC(
        active: _active,
        onChanged: _handleTapboxChanged,
      ),
    );
  }
}

//----------------------------- TapboxC ------------------------------

class TapboxC extends StatefulWidget {
  TapboxC({Key key, this.active: false, @required this.onChanged})
      : super(key: key);

  final bool active;
  final ValueChanged<bool> onChanged;

  _TapboxCState createState() => _TapboxCState();
}

class _TapboxCState extends State<TapboxC> {
  bool _highlight = false;

  void _handleTapDown(TapDownDetails details) {
    setState(() {
      _highlight = true;
    });
  }

  void _handleTapUp(TapUpDetails details) {
    setState(() {
      _highlight = false;
    });
  }

  void _handleTapCancel() {
    setState(() {
      _highlight = false;
    });
  }

  void _handleTap() {
    widget.onChanged(!widget.active);
  }

  Widget build(BuildContext context) {
    // This example adds a green border on tap down.
    // On tap up, the square changes to the opposite state.
    return GestureDetector(
      onTapDown: _handleTapDown, // Handle the tap events in the order that
      onTapUp: _handleTapUp, // they occur: down, up, tap, cancel
      onTap: _handleTap,
      onTapCancel: _handleTapCancel,
      child: Container(
        child: Center(
          child: Text(widget.active ? 'Active' : 'Inactive',
              style: TextStyle(fontSize: 32.0, color: Colors.white)),
        ),
        width: 200.0,
        height: 200.0,
        decoration: BoxDecoration(
          color:
              widget.active ? Colors.lightGreen[700] : Colors.grey[600],
          border: _highlight
              ? Border.all(
                  color: Colors.teal[700],
                  width: 10.0,
                )
              : null,
        ),
      ),
    );
  }
}
```

运行效果如下所示：

![img](http://picture-pool.oss-cn-beijing.aliyuncs.com/2019-06-10-120917.gif)

替代实现可能已将高亮状态导出到父级，同时保持活动状态为内部，但如果您要求某人使用该分接框，他们可能会抱怨它没有多大意义。 开发人员关心该框是否处于活动状态。 开发人员可能并不关心如何管理突出显示，并且更喜欢点按框处理这些细节。

### 其他的交互式`Widget`

`Flutter`提供了很多按钮和类似的交互式`Widget`。 这些`Widget`中的大多数都实现了`Material Design`准则，该准则定义了一组具有固定用户界面的组件。

如果您愿意，可以使用`GestureDetector`在任何自定义的`Widget`中构建交互性。 您可以在[管理状态](#manage-state)一节中找到`GestureDetector`的使用示例。

> 提示：`Flutter`还提供了一些`IOS`风格的`Widget`，称之为`Cupertino`，具体地址：`https://api.flutter.dev/flutter/cupertino/cupertino-library.html`

当您需要交互性时，最简单的方法是使用`Flutter`已经给我提供好的`Widget`，下面是一个部分列表：

#### 标准库中的`Widget`

- `Form`，地址：`https://api.flutter.dev/flutter/widgets/Form-class.html`
- `FormField`，地址：`https://api.flutter.dev/flutter/widgets/FormField-class.html`

#### Material库中的Widget

- `Checkbox` 地址：`https://api.flutter.dev/flutter/material/Checkbox-class.html`
- `DropdownButton`  ，地址：`https://api.flutter.dev/flutter/material/DropdownButton-class.html`
- `FlatButton` ， 地址：`https://api.flutter.dev/flutter/material/FlatButton-class.html`
- `FloatingActionButton` ， 地址：`https://api.flutter.dev/flutter/material/FloatingActionButton-class.html`
- `IconButton`  ，地址：`https://api.flutter.dev/flutter/material/IconButton-class.html`
- `Radio`  ，地址：`https://api.flutter.dev/flutter/material/Radio-class.html`
- `RaisedButton`  ，地址：`https://api.flutter.dev/flutter/material/RaisedButton-class.html`
- `Slider`  ，地址：`https://api.flutter.dev/flutter/material/Slider-class.html`
- `Switch`  ，地址：`https://api.flutter.dev/flutter/material/Switch-class.html`
- `TextField` ，地址：`https://api.flutter.dev/flutter/material/TextField-class.html`

好了，本文的全部内容到这里就结束了，总结一下，本文我们主要介绍了如何给`Flutter`中的`Widget`添加交互性，下一篇文章我们将介绍如何油压地给`Flutter`项目中添加并引用资源和图片，欢迎大家关注。