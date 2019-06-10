[TOC]

# 在Flutter中构建布局



> 首先明确几点概念：
>
> - `Widget`是构建`UI`的类
> - `Widget`在构建`UI`元素和构建布局时都会用到
> - 将简单的`Widget`组合起来可以构建复杂的`Widget`

`Flutter`布局机制的核心是`Widget`。在`Flutter`中，几乎所有东西都是一个`Widget`，即使布局模型也不例外。在`Flutter`应用程序中看到的图像（`image`）、图标（`icon`）和文本（`text`）都是`Widget`。那些你看不到的东西也是`Widget`，比如用来排列（`arrange`）、约束（`constrain`）和对齐（`align`）可见`Widget`的行（`Row`）、列（`Column`）和网格（`grid`），都是`Widget`。

你可以通过组合多个`Widget`来创建布局，这样可以构建出更复杂的`Widget`。例如，下图显示了3个图标（`icon`），每个图标下都有一个标签（`label`）：

![Sample layout](http://picture-pool.oss-cn-beijing.aliyuncs.com/2019-06-09-070647.png)

我们透过现象看本质：

![Sample layout with visual debugging](http://picture-pool.oss-cn-beijing.aliyuncs.com/2019-06-09-070733.png)

上图显示了真正的视觉布局，它有一行三列，其中每列包含一个图标（`icon`）和一个标签（`label`），就是这种简单`Widget`的组合最终构成了我们看到的复杂`Widget`。

> 注意：在本文档中的大多数插图都是在`debugPaintSizeEnabled=true`的前提下获取的，这个选项可以在屏幕上绘制出视觉布局（如上图，绘制出了每一个可见、不可见`Widget`的分隔线，即实际的布局）。

下图画出了上图布局的`Widget`树：

![Node tree](http://picture-pool.oss-cn-beijing.aliyuncs.com/2019-06-09-071244.png)

这其中的大部分看起来和我们预想的差不多，但是你可能会对`Container`（粉红色的圈）感到疑惑，`Container`是一个`Widget`类，允许我们自定义其子`Widget`。如果要添加填充(`padding`)、页边距(`margin`)、边框(`border`)或背景色(`background color`)，请使用`Container`来设置对应功能。
在本例中，每个`Text Widget`都放置在一个`Container`中以添加页边距（`margin`）。整个行（`Row`）也放置在一个`Container`中，以便在该行的周围添加填充（`padding`）。
本例中的其余UI元素均由属性控制。使用`Icon`的`color`属性设置图标的颜色。使用`Text.Style`属性设置字体、颜色、粗细等。`Row`和`Column`的属性允许您指定其子级垂直或水平对齐的方式，以及子级应占用的空间。

> 注意：可能有的读者对`margin`和`padding`的区别不是很清楚，`margin`指的是当前`Widget`距离其他`Widget`的距离，`padding`指的是当前`Widget`中的内容距离当前`Widget`边界的距离(比如`Text`中的文字显示出来距离`Text Widget`外边缘的距离)。

### 对Widget进行布局

在`Flutter`中你应该如何对一个简单的`Widget`进行布局？这节将向你介绍如何创建并展示一个简单的`Widget`，同时会向你介绍`Hello World`示例`app`的全部代码。

在`Flutter`中，只需要很少的步骤就可以将`Text`、`Icon`或者`Image`放置在屏幕上：

#### 1. 选择一个布局Widget

根据您希望如何对齐或约束可见的`Widget`从各种布局`Widget`中进行选择，因为这些布局`Widget`的特性通常会传递给他们所包含的`Widget`。
此示例使用`Center`这个`Widget`，该`Widget`的特点是将其包裹的内容水平和垂直居中。

#### 2. 创建一个可见的Widget

比如创建一个`Text Widget`:

```dart
Text('Hello World'),
```

创建一个`Image Widget`：

```dart
Image.asset(
  'images/lake.jpg',
  fit: BoxFit.cover,
),
```

创建一个`Icon Widget`：

```dart
Icon(
  Icons.star,
  color: Colors.red[500],
),
```

#### 3. 将可见的Widget添加到布局Widget中

所有的布局`Widget`都具有以下特性：

- 如果修饰的是单个`Widget`则具有一个`child`属性，比如`Center`或者`Container`
- 如果修饰的是多个`Widget`则具有一个`children`属性，比如`Row`、`Column`、`ListView`、`Stack`。

将`Text Widget`添加到`Center Widget`中：

```dart
Center(
  child: Text('Hello World'),
),
```

#### 4. 将布局Widget添加到页面中

一个`Flutter App`本身就是一个`Widget`，而大多数`Widget`都具有一个`build()`方法，所以我们可以在`app`的`build()`方法中实例化并返回一个`Widget`，这样被返回的Widget就可以被显示出来。

##### Material app

对于`Material`风格的`app`，你可以使用一个`Scaffold Widget`，这个`Widget`提供了一个默认的横栏、默认的背景色，而且还有一些像添加`drawer`、添加`snack bar`、添加`bottom sheet`这样的`API`。你可以直接将`Center Widget`作为`Scaffold`的`body`属性进而添加到页面中：

```dart
class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Flutter layout demo',
      home: Scaffold(
        appBar: AppBar(
          title: Text('Flutter layout demo'),
        ),
        body: Center(
          child: Text('Hello World'),
        ),
      ),
    );
  }
}
```

> 注意：`Material`库（地址：`https://api.flutter.dev/flutter/material/material-library.html`）中实现的`Widget`都是遵循`Material`设计准则的，当你设计你的`UI`的时候，你可以仅仅只使用标准的`Widget`库（地址：`https://api.flutter.dev/flutter/widgets/widgets-library.html`），你也可以使用`Material`库中的`Widget`。当然，你也可以混合使用这两种`Widget`库中的`Widget`，你可以在现有`Widget`的基础上自定义`Widget`，也可以不基于现有的`Widget`构建完全属于自己的`Widget`。

##### Non-Material app

对于非`Material`风格的`app`，你可以直接在你`app`的`build()`方法中返回`Center Widget`：

```dart
class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Container(
      decoration: BoxDecoration(color: Colors.white),
      child: Center(
        child: Text(
          'Hello World',
          textDirection: TextDirection.ltr,
          style: TextStyle(
            fontSize: 32,
            color: Colors.black87,
          ),
        ),
      ),
    );
  }
}
```

默认的非`Material`风格的`App`是没有`AppBar`、没有标题、没有背景色的，如果你想在非`Material`风格的`app`中显示上述元素，就只有自己手动去构建了。在这个示例中手动将背景色设置成为了白色、将`Text`中的文字颜色设置成了黑色以模仿`Material`风格的`app`，运行起来是这样的：

![Hello World](http://picture-pool.oss-cn-beijing.aliyuncs.com/2019-06-09-080219.png)

### 在垂直和水平方向构建多个Widget

最常见的布局模式是垂直或水平排列多个`Widget`。可以使用`Row Widget`水平排列`Widget`，使用`Column Widget`垂直排列`Widget`。

> 首先明确几点概念：
>
> - `Row`和`Column`是最常用的2种布局模式
> - `Row`和`Column`这两个`Widget`都可以包裹多个`Widget`
> - `Row`和`Column`的子`Widget`可以是`Row`或`Column`，或者其他任何复杂的`Widget`
> - 你可以指定`Row`和`Column`如何垂直或者水平排列其子`Widget`
> - 你可以拉伸（`stretch`）或者约束（`constrain`）指定的子`Widget`
> - 你可以指定子`Widget`如何使用`Row`或者`Column`的剩余可用空间

为了在`Flutter`中创建一个行或者列，你应该将子`Widget`组成的`List`放进`Row Widget`或者`Column Widget`中。每个子元素本身可以是`Row`或`Column`，下面的示例展示了如何在`Row`或`Column`中嵌套`Row`或`Column`：

![Screenshot with callouts showing the row containing two children](http://picture-pool.oss-cn-beijing.aliyuncs.com/2019-06-09-081700.png)

此布局整体上为一行，这一行包含了两个子元素：左侧的列和右侧的图像，而左侧的列中的`Widget`树又是由多个行和列组成的：

![Diagram showing a left column broken down to its sub-rows and sub-columns](http://picture-pool.oss-cn-beijing.aliyuncs.com/2019-06-09-081754.png)

> 注意：`Row`和`Column`是水平和垂直布局的基本`Widget`，这些基础的`Widget`允许最大程度的自定义。`Flutter`还提供了功能更专一的，更高级别的`Widget`，足以满足你的需要。例如，你有时可能更喜欢`ListTile`而不是`Row`，它是一个易于使用的`Widget`，具有用于前导（`leading`）和尾随图标（`trailing icon`）的属性，并且最多有3行文本。再比如，相比于`Column`，您有时可能更喜欢`ListView`，它是一种类似于`Column`的布局，如果其包裹的内容太长导致可用空间无法完全容纳，它会进行自动滚动。

#### 设置行和列中子Widget的对其方式

将多个Widget放在同一行或同一列之后，我们有时还需要对这些同一行或者同一列的Widget进行对齐方式的设置，比如：

![image-20190609163653308](http://picture-pool.oss-cn-beijing.aliyuncs.com/2019-06-09-083654.png)

上图中黑色框代表`Row`(行)，1、2、3分别是3个子`Widget`，可以看到`a`、`b`、`c`展示出了3中不不同的效果，这是由于`a`、`b`、`c`中对子`Widget`的对其方式的设置策略不同导致的，所以，在`Flutter`中为了在`Row`和`Column`中对子`Widget`进行更加明确的排列设置，我们可以通过`mainAxisAlignment`和`crossAxisAlignment`属性来控制`Row`和`Column`如何排列子`Widget`。对于`Row`，`mainAxis`指的是水平方向，`crossAxis`指的是垂直方向。对于`Column`，`mainAxis`指的是垂直方向，`crossAxis`指的是水平方向。比如像上图中`a`、`b`、`c`三种效果，分别对应给`Row`的`crossAxisAlignment`属性设置了`top`、`center`、`bottom`。

![image-20190609162752853](http://picture-pool.oss-cn-beijing.aliyuncs.com/2019-06-09-082753.png)

`MainAxisAlignment`和`CrossAxisAlignment`这两个类提供了各种用于控制对齐的常量，一般设置的时候我们会这样设置：

```dart
new Column(
	mainAxisAlignment: MainAxisAlignment.center,
  ...
）;
```

> 注意：当你将一张本地图片添加到你的项目中以后，你应该更新你项目的`pubspec`文件以让你的代码可以访问到你添加的图片，如果你引用的是网络图片的话就不用更新`pubspec`文件了。

在下面的例子中，每一张图片都是`100px`宽，而其容器（在这里是整个屏幕）的宽度是大于`300px`的，将`mainAxis`设置为`spaceEvently`后可以保证这3张图片中的每一张在之前、之间、之后将水平方向的剩余空间自由划分掉：

```dart
Row(
  mainAxisAlignment: MainAxisAlignment.spaceEvenly,
  children: [
    Image.asset('images/pic1.jpg'),
    Image.asset('images/pic2.jpg'),
    Image.asset('images/pic3.jpg'),
  ],
);
```

效果图：

![Row with 3 evenly spaced images](http://picture-pool.oss-cn-beijing.aliyuncs.com/2019-06-09-084826.png)

`Column`和`Row`具有相同的工作逻辑，下面的这3张图片，每一张的高度是`100px`，而其容器（在这里是整个屏幕）的高度大于`300px`，将`mainAxis`设置为`spaceEvenly`之后可以保证这3张图片中的每一张在之上、之间、之下将垂直方向的剩余空间自由划分掉：

```dart
Column(
  mainAxisAlignment: MainAxisAlignment.spaceEvenly,
  children: [
    Image.asset('images/pic1.jpg'),
    Image.asset('images/pic2.jpg'),
    Image.asset('images/pic3.jpg'),
  ],
);
```

效果图：

![Column showing 3 images spaced evenly](http://picture-pool.oss-cn-beijing.aliyuncs.com/2019-06-09-085152.png)

#### 设置Widget的尺寸

当一个`layout`的尺寸太大以至于超过了设备屏幕的尺寸，超出的屏幕边缘会出现黄色和黑色条纹图案，就像下面这样：

![Overly-wide row](http://picture-pool.oss-cn-beijing.aliyuncs.com/2019-06-09-085627.png)、

在`Row`或者`Column`中可以使用`Expanded` 这个`Widget`来设置子`Widget`的尺寸，为了修复上图中图像尺寸比其容器尺寸还大的现象，我们可以使用`Expanded`来包裹每一个`Image Widget`，这样就可以保证这些图片平均分割可用空间而不至于尺寸太大导致出现上图尺寸越界的问题：

```dart
Row(
  crossAxisAlignment: CrossAxisAlignment.center,
  children: [
    Expanded(
      child: Image.asset('images/pic1.jpg'),
    ),
    Expanded(
      child: Image.asset('images/pic2.jpg'),
    ),
    Expanded(
      child: Image.asset('images/pic3.jpg'),
    ),
  ],
);
```

效果如下：

![Row of 3 images that are too wide, but each is constrained to take only 1/3 of the space](http://picture-pool.oss-cn-beijing.aliyuncs.com/2019-06-09-085839.png)

有时你也许想让一个`Widget`占用的空间是其相邻`Widget`所占空间的两倍，这时，你可以使用`Expanded Widget`的`flex`属性，该属性的类型是一个整数类型，默认为1，表示当前`Expanded Widget`所占的比例。以下代码将中间图像的弹性系数设置为2：

```dart
Row(
  crossAxisAlignment: CrossAxisAlignment.center,
  children: [
    Expanded(
      child: Image.asset('images/pic1.jpg'),
    ),
    Expanded(
      flex: 2,
      child: Image.asset('images/pic2.jpg'),
    ),
    Expanded(
      child: Image.asset('images/pic3.jpg'),
    ),
  ],
);
```

由于其他两个`Expanded`默认别的`flex`为1，所以最终的效果是中间图像的尺寸是两边图像的2倍：

![Row of 3 images with the middle image twice as wide as the others](http://picture-pool.oss-cn-beijing.aliyuncs.com/2019-06-09-144548.png)

#### 使子Widget更加紧密

默认情况下`Row`或`Column`会尽可能在主轴方向占据尽可能多的可用空间，这样就会导致如果可用空间很大但是`Row`或`Column`中的子`Widget`很少时出现子`Widget`之间距离很大的情况（分布很分散），如果你希望让`Row`或`Column`中的子`Widget`们尽可能近地出现，可以将`mainAxisSize`属性设置为`MainAxisSize.min`，下面的示例通过此属性的设置将星形图标紧密地放置在了一起：

```dart
Row(
  mainAxisSize: MainAxisSize.min,
  children: [
    Icon(Icons.star, color: Colors.green[500]),
    Icon(Icons.star, color: Colors.green[500]),
    Icon(Icons.star, color: Colors.green[500]),
    Icon(Icons.star, color: Colors.black),
    Icon(Icons.star, color: Colors.black),
  ],
)
```

运行效果：

![Row of 5 stars, packed together in the middle of the row](http://picture-pool.oss-cn-beijing.aliyuncs.com/2019-06-09-145025.png)

#### 嵌套Row和Column

`Flutter`的布局框架允许你根据你的需要不限量地在`Row/Column`中嵌套`Row/Column`，让我们来看看下图中红色框圈出来的部分：

![Screenshot of the pavlova app, with the ratings and icon rows outlined in red](http://picture-pool.oss-cn-beijing.aliyuncs.com/2019-06-09-145332.png)

红色框中的内容是由2个`Row`构成的，上面的`Row Widget`包含了5个星星和一个数字`Reviews`，对应的`Widget`树如下图所示：

![Ratings row widget tree](http://picture-pool.oss-cn-beijing.aliyuncs.com/2019-06-09-145527.png)



对应的代码实现如下：

```dart
var stars = Row(
  mainAxisSize: MainAxisSize.min,
  children: [
    Icon(Icons.star, color: Colors.green[500]),
    Icon(Icons.star, color: Colors.green[500]),
    Icon(Icons.star, color: Colors.green[500]),
    Icon(Icons.star, color: Colors.black),
    Icon(Icons.star, color: Colors.black),
  ],
);

final ratings = Container(
  padding: EdgeInsets.all(20),
  child: Row(
    mainAxisAlignment: MainAxisAlignment.spaceEvenly,
    children: [
      stars,
      Text(
        '170 Reviews',
        style: TextStyle(
          color: Colors.black,
          fontWeight: FontWeight.w800,
          fontFamily: 'Roboto',
          letterSpacing: 0.5,
          fontSize: 20,
        ),
      ),
    ],
  ),
);
```

可以看到，主要的组成部分是5个`Icon`(用于展示星星)和一个`Text`(用于展示数字`+Reviews`)。

> 提示：为了避免由于布局的大量嵌套导致的布局代码混乱的问题，我们可以使用变量或者函数定义一些`Widget`，比如上面代码的`ratings`。

我们再来看下面的一行，该`Row Widget`包含了3个`Column`，每个`Column`都包含有一个`icon`和2行`Text`，对应的`Widget`树如下：

![Icon widget tree](http://picture-pool.oss-cn-beijing.aliyuncs.com/2019-06-09-150205.png)

对应的实现代码如下：

```dart
final descTextStyle = TextStyle(
  color: Colors.black,
  fontWeight: FontWeight.w800,
  fontFamily: 'Roboto',
  letterSpacing: 0.5,
  fontSize: 18,
  height: 2,
);

// 借助DefaultTextStyle.merge()来创建一个Text的主题样式
final iconList = DefaultTextStyle.merge(
  style: descTextStyle,
  child: Container(
    padding: EdgeInsets.all(20),
    child: Row(
      mainAxisAlignment: MainAxisAlignment.spaceEvenly,
      children: [
        Column(
          children: [
            Icon(Icons.kitchen, color: Colors.green[500]),
            Text('PREP:'),
            Text('25 min'),
          ],
        ),
        Column(
          children: [
            Icon(Icons.timer, color: Colors.green[500]),
            Text('COOK:'),
            Text('1 hr'),
          ],
        ),
        Column(
          children: [
            Icon(Icons.restaurant, color: Colors.green[500]),
            Text('FEEDS:'),
            Text('4-6'),
          ],
        ),
      ],
    ),
  ),
);
```

再看这张图：

![Screenshot of the pavlova app, with the ratings and icon rows outlined in red](http://picture-pool.oss-cn-beijing.aliyuncs.com/2019-06-09-145332.png)

整体是一个`Row`，左边是一个`Column`，该`Column`由一个标题文字、一个摘要文字、以及红色框中的2个`Row`组成，所以左边这个`Column`的整体代码如下：

```dart
final leftColumn = Container(
  padding: EdgeInsets.fromLTRB(20, 30, 20, 20),
  child: Column(
    children: [
      titleText,//标题(Strawberry Paova)
      subTitle,//摘要文字(Pavlova is a....)
      ratings,//红色框中的第一个Row
      iconList,//红色框中的第二个Row
    ],
  ),
);
```

然后就可以把左边的这个leftColumn放置在整个布局中了：

```dart
body: Center(
  child: Container(
    margin: EdgeInsets.fromLTRB(0, 40, 0, 30),
    height: 600,
    child: Card(
      child: Row(
        crossAxisAlignment: CrossAxisAlignment.start,
        children: [
          Container(
            width: 440,
            child: leftColumn,//左边的Column
          ),
          mainImage,//右边的那个图像
        ],
      ),
    ),
  ),
),
```

可以看到左边的`Column`被整个放置在了一个`Container Widget`中，这样做的目的是通过`Container`来设置该`Column`的宽度。

### 常用的布局Widget

`Flutter`有丰富的布局`Widget`库，下面是一些最常用的布局`Widget`。为了避免你被一整张布局`Widget`的清单吓到，这里只列举出了常见的布局`Widget`，目的是让你快速上手，如果你希望了解更多`Flutter`中的布局`Widget`，你可以阅读官方的`Widget`文档（地址：`https://flutter.dev/docs/development/ui/widgets`）。
以下小部件分为两类：来自`Widget`库的标准`Widget`和来自`Material`库的专用`Widget`。任何应用程序都可以使用标准的`Widget`库，但只有`Material`应用程序可以使用`Material`库中的`Widget`。

#### 标准Widget

- `Container`：为一个`Widget`添加向小部件添加填充（`padding`）、页边距（`margin`）、边框（`border`）、背景色（`background color`）等
- `GridView`：在一个可滚动的网格中放置子`Widget`
- `ListView`：在一个可滚动的列表中放置子`Widget`
- `Stack`：将一个Widget重叠在另一个`Widget`之上

#### Material风格的Widget

- `Card`：将相关内容布局到具有圆角和阴影的框中。
- `ListTile`：最多将3行文本（`text`）以及可选的前导（`leading`）和尾随图标（`trailing icon`）布局到一行。

#### Container

许多布局都充分利用`Container`来的`padding`属性来添加填充，或者其`border`属性添加边框或利用`margin`属性添加页边距。你可以通过将整个布局放置到`Container`中并更改其背景色或背景图像来更改屏幕的背景。

##### Container总结

- 添加填充(`padding`)、页边距(`margin`)、边框(`border`)
- 改变背景色或者背景图片
- 包裹单个简单的`Widget`或者像`Row`、`Column`这样复杂的`Widget`，甚至包裹`Widget`树的根`Widget`

![Diagram showing: margin, border, padding, and content](http://picture-pool.oss-cn-beijing.aliyuncs.com/2019-06-09-153057.png)

##### Container实例

下面的这个布局包含了一个`Column`，这个`Column`中包含了2个`Row`，每一个Row`包含了`2个`Image`，使用`Container`达到了将`Column`的背景颜色改为亮灰色的目的：

```dart
Widget _buildImageColumn() => Container(
      decoration: BoxDecoration(
        color: Colors.black26,
      ),
      child: Column(
        children: [
          _buildImageRow(1),
          _buildImageRow(3),
        ],
      ),
    );
```

效果图如下：

![Screenshot showing 2 rows, each containing 2 images](http://picture-pool.oss-cn-beijing.aliyuncs.com/2019-06-09-153347.png)

同样也可以借助`Container`来实现为每个`Image`添加圆形边框和页边距：

```dart
Widget _buildDecoratedImage(int imageIndex) => Expanded(
      child: Container(
        decoration: BoxDecoration(
          border: Border.all(width: 10, color: Colors.black38),
          borderRadius: const BorderRadius.all(const Radius.circular(8)),
        ),
        margin: const EdgeInsets.all(4),
        child: Image.asset('images/pic$imageIndex.jpg'),
      ),
    );

Widget _buildImageRow(int imageIndex) => Row(
      children: [
        _buildDecoratedImage(imageIndex),
        _buildDecoratedImage(imageIndex + 1),
      ],
    );
```

#### GridView

使用`GridView`可以构建一个二维的列表，`GridView`默认提供两个列，当然你也可以构建自己的自定义网格。当`GridView`检测到它的内容太长时，会自动进行滚动。

##### GridView总结

- 将`Widget`布局在网格中
- 检测列内容何时超出可见框，并自动提供滚动
- 构建自己的自定义网格，或者使用系统提供的网格：
  - `GridView.count`允许你设置网格有多少个列
  - `GridView.extent`允许你设置tile的最大像素

##### GridView实例

使用`GridView.extent` 创建一个`tile`最大为`150px`的网格：

```dart
Widget _buildGrid() => GridView.extent(
    maxCrossAxisExtent: 150,
    padding: const EdgeInsets.all(4),
    mainAxisSpacing: 4,
    crossAxisSpacing: 4,
    children: _buildGridTileList(30));

// The images are saved with names pic0.jpg, pic1.jpg...pic29.jpg.
// The List.generate() constructor allows an easy way to create
// a list when objects have a predictable naming pattern.
List<Container> _buildGridTileList(int count) => List.generate(
    count, (i) => Container(child: Image.asset('images/pic$i.jpg')));
```

效果图：

![A 3-column grid of photos](http://picture-pool.oss-cn-beijing.aliyuncs.com/2019-06-09-154242.png)



#### ListView

`ListView`是一个用于显示多行的`Widget`，当内容多到其容器不能一次性显示完全的时候会自动提供滚动机制。

##### ListView总结

- 用于构建列表框的`Widget`
- 可以设置为水平或垂直滚动
- 当内容的尺寸比容器的尺寸大时会提供滚动机制
- 相比于`Column`配置更加简单，而且使用滚动机制会更方便

##### ListView实例

使用`ListView`显示一个企业列表：

```dart
Widget _buildList() => ListView(
      children: [
        _tile('CineArts at the Empire', '85 W Portal Ave', Icons.theaters),
        _tile('The Castro Theater', '429 Castro St', Icons.theaters),
        _tile('Alamo Drafthouse Cinema', '2550 Mission St', Icons.theaters),
        _tile('Roxie Theater', '3117 16th St', Icons.theaters),
        _tile('United Artists Stonestown Twin', '501 Buckingham Way',
            Icons.theaters),
        _tile('AMC Metreon 16', '135 4th St #3000', Icons.theaters),
        Divider(),
        _tile('Kescaped_code#39;s Kitchen', '757 Monterey Blvd', Icons.restaurant),
        _tile('Emmyescaped_code#39;s Restaurant', '1923 Ocean Ave', Icons.restaurant),
        _tile(
            'Chaiya Thai Restaurant', '272 Claremont Blvd', Icons.restaurant),
        _tile('La Ciccia', '291 30th St', Icons.restaurant),
      ],
    );

ListTile _tile(String title, String subtitle, IconData icon) => ListTile(
      title: Text(title,
          style: TextStyle(
            fontWeight: FontWeight.w500,
            fontSize: 20,
          )),
      subtitle: Text(subtitle),
      leading: Icon(
        icon,
        color: Colors.blue[500],
      ),
    );
```

对应效果图：

![ListView containing movie theaters and restaurants](http://picture-pool.oss-cn-beijing.aliyuncs.com/2019-06-09-155130.png)

#### Stack

使用`stack`在基`Widget`（通常是`Image`）上排列`Widget`。`Widget`可以完全或部分地与基`Widget`重叠。

##### Stack总结

- 当一个`Widget`需要位于另一个`Widget`的上层时使用
- 子列表中的第一个`Widget`是基`Widget`，之后的子`Widget`覆盖在该基`Widget`的顶部。
- 堆栈的内容无法滚动
- 可以选择切割超出渲染框的子`Widget`

##### Stack实例

借助`Stack`实现将一个`Container`（在半透明黑色背景上显示`text`）覆盖在`CircleAvatar`的顶部：

```dart
Widget _buildStack() => Stack(
    alignment: const Alignment(0.6, 0.6),
    children: [
      CircleAvatar(
        backgroundImage: AssetImage('images/pic.jpg'),
        radius: 100,
      ),
      Container(
        decoration: BoxDecoration(
          color: Colors.black45,
        ),
        child: Text(
          'Mia B',
          style: TextStyle(
            fontSize: 20,
            fontWeight: FontWeight.bold,
            color: Colors.white,
          ),
        ),
      ),
    ],
  );
```

效果图：

![Circular avatar image with a label](http://picture-pool.oss-cn-beijing.aliyuncs.com/2019-06-09-155610.png)

#### Card

来自`Material`库的`Card Widget`用来包裹相关的信息块，被包裹的信息块可以由几乎任何`Widget`组成，但通常与`ListTile`一起使用。`Card`只有一个`child`属性，即被包裹的`Widget`，但它的这个`child`属性可以接收支持包含多个子`Widget`的`Row`、`Column`、`ListView`、`GridView`的`Widget`。默认情况下，如果不设置`Card`的`child`属性，即不被设置`Card`包裹的`Widget`，`Card`的大小将缩小到0 x 0像素，但可以使用`sizedbox`限制`Card`的大小。
在`Flutter`中，一个`Card`默认有轻微的圆角和阴影，这样使其具有`3D`效果。更改`Card`的`Elevation`属性可以控制阴影的效果。例如，将`“elevation”`设置为24，可以从视觉上将`Card`提升到离表面更远的位置，并使阴影变得更加分散。

##### Card总结

- `Material Card`的实现类
- 用于呈现相关信息块
- 接受单个子项，但该子项可以是`Row`、`Column`或其他包含子项列表的`Widget`
- 显示圆角和阴影
- `Card`中的内容无法滚动
- 来自于`Material`库

##### Card实例

使用一个SizedBox包裹的包含3个列表块的Card，Card中使用一个Divider将第1个和后2个列表块分割开：

```dart
Widget _buildCard() => SizedBox(
    height: 210,
    child: Card(
      child: Column(
        children: [
          ListTile(
            title: Text('1625 Main Street',
                style: TextStyle(fontWeight: FontWeight.w500)),
            subtitle: Text('My City, CA 99984'),
            leading: Icon(
              Icons.restaurant_menu,
              color: Colors.blue[500],
            ),
          ),
          Divider(),
          ListTile(
            title: Text('(408) 555-1212',
                style: TextStyle(fontWeight: FontWeight.w500)),
            leading: Icon(
              Icons.contact_phone,
              color: Colors.blue[500],
            ),
          ),
          ListTile(
            title: Text('costa@example.com'),
            leading: Icon(
              Icons.contact_mail,
              color: Colors.blue[500],
            ),
          ),
        ],
      ),
    ),
  );
```

效果图：

![Card containing 3 ListTiles](http://picture-pool.oss-cn-beijing.aliyuncs.com/2019-06-09-160721.png)

#### ListTile

`ListTile`是一个`Material`库中的特殊行(`Row`)，该`Widget`的特性是可以很容易地创建最多包括3行`Text`和可选引导（`leading`）、可选尾标（`trailing icon`）的`widget`。`ListTile`大多数情况下被用在`Card`或者`ListView`中。

##### ListTile总结

- 一个特殊的`Row`，可以包含最多3行`Text`以及引导、尾标
- 比`Row`更易配置，使用起来更简单
- 是`Material`库中的`Widget`

##### ListTile实例

上面Card中的实例就是赤裸裸的`ListTile`的使用：

![Card containing 3 ListTiles](http://picture-pool.oss-cn-beijing.aliyuncs.com/2019-06-09-161102.png)