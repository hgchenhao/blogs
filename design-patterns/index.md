### 什么是设计模式
设计模式代表了最佳的实践，通常被有经验的面向对象的软件开发人员所采用。设计模式是软件开发人员在软件开发过程中面临的一般问题的解决方案。这些解决方案是众多软件开发人员经过相当长的一段时间的试验和错误总结出来的。

设计模式是一套被反复使用的、多数人知晓的、经过分类编目的、代码设计经验的总结。使用设计模式是为了重用代码、让代码更容易被他人理解、保证代码可靠性。 毫无疑问，设计模式于己于他人于系统都是多赢的，设计模式使代码编制真正工程化，设计模式是软件工程的基石，如同大厦的一块块砖石一样。

### 设计模式原则
- S – Single Responsibility Principle 单一职责原则
  - 一个程序只做好一件事
  - 不应该有一个以上的理由去修改某个程序
  - 如果功能过于复杂就拆分开，每个部分保持独立
>  通常情况下，我们会倾向于往一个类当中塞入过多的功能，就像当你只能往航班上携带一个行李箱的时候。这里的问题在于，这个类不再是概念上的内聚，从而导致了未来可能有很多理由会去修改它。尽可能少地去修改某个类是非常重要的，因为如果在一个类里面包含了过多的功能，那么当你修改其中的某一部分，就会难以理解新的修改将如何影响代码库中的其他依赖模块

```js
// bad

class UserSettings {
  constructor(user) {
    this.user = user
  }

  changeSettings(settings) {
    if (this.verifyCredentials(user)) {
      // ...
    }
  }

  verifyCredentials(user) {
    // ...
  }
}

//good

class UserAuth {
  constructor(user) {
    this.user = user
  }

  verifyCredentials() {
    // ...
  }
}

class UserSettings {
  constructor(user) {
    this.user = user
    this.auth = new UserAuth(user)
  }

  changeSettings(settings) {
    if (this.auth.verifyCredentials()) {
      // ...
    }
  }
}

```

- O – OpenClosed Principle 开放/封闭原则
  - 对扩展开放，对修改封闭
  - 增加需求时，扩展新代码，而非修改已有代码

> 软件实体（类、模块、函数等等）应该对扩展开放，而对修改封闭。换句话说，该原则的基本含义就是当用户在扩展你的模块功能时，没有必要去打开 .js 源文件并手动修改源代码。

```js
// bad 
class AjaxRequester {
  constructor() {
    // What if we wanted another HTTP Method, like DELETE? We would have to
    // open this file up and modify this and put it in manually.
    this.HTTP_METHODS = ['POST', 'PUT', 'GET']
  }

  get(url) {
    // ...
  }
}

// good 

class AjaxRequester {
  constructor() {
    this.HTTP_METHODS = ['POST', 'PUT', 'GET']
  }

  get(url) {
    // ...
  }

  addHTTPMethod(method) {
    this.HTTP_METHODS.push(method)
  }
}
```

- L – Liskov Substitution Principle 里氏替换原则
  - 子类能覆盖父类
  - 父类能出现的地方子类就能出现

> 这个原则听起来有点儿拗口，但其实概念非常简单。正式的定义就是「如果 S 为 T 的子类型，那么 T 类型的对象可以被 S 类型的对象所替换（也就是说类型 S 的对象可以替换类型 T 的对象），而不会改变该程序的任何预期特性（正确性，执行的任务等）」。不得不说这是一个更拗口的定义。

> 最好的一种解释就是，我们所创建的父类与其子类应当可交换地使用而不会引起异常。可能这还是会使人感到困惑，所以让我们来看一个经典的 Square-Rectangle 例子。从数学上来说，一个 Square（正方形）也是一个 Rectangle（矩形），但如果你通过继承来运用 “is-a” 关系对其进行建模，你很快就会遇到麻烦。

```js
// bad

class Rectangle {
  constructor() {
    this.width = 0
    this.height = 0
  }

  setColor(color) {
    // ...
  }

  render(area) {
    // ...
  }

  setWidth(width) {
    this.width = width
  }

  setHeight(height) {
    this.height = height
  }

  getArea() {
    return this.width * this.height
  }
}

class Square extends Rectangle {
  constructor() {
    super()
  }

  setWidth(width) {
    this.width = width
    this.height = width
  }

  setHeight(height) {
    this.width = height
    this.height = height
  }
}

function renderLargeRectangles(rectangles) {
  rectangles.forEach((rectangle) => {
    rectangle.setWidth(4)
    rectangle.setHeight(5)
    let area = rectangle.getArea() // BAD: Will return 25 for Square. Should be 20.
    rectangle.render(area)
  })
}

let rectangles = [new Rectangle(), new Rectangle(), new Square()]
renderLargeRectangles(rectangles)

// good

class Shape {
  constructor() {}

  setColor(color) {
    // ...
  }

  render(area) {
    // ...
  }
}

class Rectangle extends Shape {
  constructor() {
    super()
    this.width = 0
    this.height = 0
  }

  setWidth(width) {
    this.width = width
  }

  setHeight(height) {
    this.height = height
  }

  getArea() {
    return this.width * this.height
  }
}

class Square extends Shape {
  constructor() {
    super()
    this.length = 0
  }

  setLength(length) {
    this.length = length
  }

  getArea() {
    return this.length * this.length
  }
}

function renderLargeShapes(shapes) {
  shapes.forEach((shape) => {
    switch (shape.constructor.name) {
      case 'Square':
        shape.setLength(5)
      case 'Rectangle':
        shape.setWidth(4)
        shape.setHeight(5)
    }

    let area = shape.getArea()
    shape.render(area)
  })
}

let shapes = [new Rectangle(), new Rectangle(), new Square()]
renderLargeShapes(shapes)
```

- I – Interface Segregation Principle 接口隔离原则
  - 保持接口的单一独立
  - 类似单一职责原则，这里更关注接口

> JavaScript 语言本身并不包含对于接口语法的支持，因此也无法像其他语言那样达到严格限制的程度。不过鉴于 JavaScript 本身类型系统的缺失，遵循接口隔离原则还是非常重要的。
> 不应该强制客户端去依赖于他们不需要的接口」，由于 JavaScript 的「鸭子类型」，JavaScript 当中的接口只是一种隐性的契约而已。这一点在 JavaScript 中较为典型的例子就是那些需要大量配置信息的类。其实使用者并不需要去关心每一个配置项，不强制他们设置大量的选项能够节省大量的时间，保持设置选项的可选性能够有助于防止「胖接口」。


```js

// bad 
class DOMTraverser {
  constructor(settings) {
    this.settings = settings
    this.setup()
  }

  setup() {
    this.rootNode = this.settings.rootNode
    this.animationModule.setup()
  }

  traverse() {
    // ...
  }
}

let $ = new DOMTraverser({
  rootNode: document.getElementsByTagName('body'),
  animationModule: function () {}, // Most of the time, we won't need to animate when traversing.
  // ...
})

// good

class DOMTraverser {
  constructor(settings) {
    this.settings = settings
    this.options = settings.options
    this.setup()
  }

  setup() {
    this.rootNode = this.settings.rootNode
    this.setupOptions()
  }

  setupOptions() {
    if (this.options.animationModule) {
      // ...
    }
  }

  traverse() {
    // ...
  }
}

let $ = new DOMTraverser({
  rootNode: document.getElementsByTagName('body'),
  options: {
    animationModule: function () {},
  },
})

```

- D – Dependency Inversion Principle 依赖倒转原则
  - 上层模块不需要依赖下层模块，两者依赖于抽象
  - 抽象不应该依赖于细节。细节应当依赖于抽象

> JavaScript 语言本身没有接口，从而抽象只能依赖于隐性的契约。也就是指，一个对象/类所暴露给另一个对象/类的方法和属性。以下例子中所隐含的契约就是，InventoryTracker 所依赖的任意 Request 模块都要有一个 requestItems 方法。

```js
// bad

ss InventoryTracker {
  constructor(items) {
    this.items = items

    // BAD: We have created a dependency on a specific request implementation.
    // We should just have requestItems depend on a request method: `request`
    this.requester = new InventoryRequester()
  }

  requestItems() {
    this.items.forEach((item) => {
      this.requester.requestItem(item)
    })
  }
}

class InventoryRequester {
  constructor() {
    this.REQ_METHODS = ['HTTP']
  }

  requestItem(item) {
    // ...
  }
}

let inventoryTracker = new InventoryTracker(['apples', 'bananas'])
inventoryTracker.requestItems()

// good

class InventoryTracker {
  constructor(items, requester) {
    this.items = items
    this.requester = requester
  }

  requestItems() {
    this.items.forEach((item) => {
      this.requester.requestItem(item)
    })
  }
}

class InventoryRequesterV1 {
  constructor() {
    this.REQ_METHODS = ['HTTP']
  }

  requestItem(item) {
    // ...
  }
}

class InventoryRequesterV2 {
  constructor() {
    this.REQ_METHODS = ['WS']
  }

  requestItem(item) {
    // ...
  }
}

// By constructing our dependencies externally and injecting them, we can easily
// substitute our request module for a fancy new one that uses WebSockets.
let inventoryTracker = new InventoryTracker(['apples', 'bananas'], new InventoryRequesterV2())
inventoryTracker.requestItems()

```

### 设计模式分类
- 创建型
  - 构造器模式
  - [工厂模式](https://github.com/hgchenhao/blogs/blob/master/design-patterns/behavior/工厂模式.md)
  - 抽象工厂模式
  - [单例模式](https://github.com/hgchenhao/blogs/blob/master/design-patterns/behavior/单例模式.md)
  - 原型模式
  - 建造者模式
- 结构型
  - [适配器模式](https://github.com/hgchenhao/blogs/blob/master/design-patterns/behavior/适配器模式.md)
  - 装饰器模式
  - 代理模式
  - [外观模式](https://github.com/hgchenhao/blogs/blob/master/design-patterns/behavior/外观模式.md)
  - 桥接模式
  - 组合模式
  - 享元模式
- 行为型
  - [观察者模式](https://github.com/hgchenhao/blogs/blob/master/design-patterns/behavior/观察者模式.md)
  - 迭代器模式
  - [策略模式](https://github.com/hgchenhao/blogs/blob/master/design-patterns/behavior/策略模式.md)
  - 模板方法模式
  - 模板方法模式
  - 命令模式
  - 备忘录模式
  - 状态模式
  - 访问者模式
  - 中介者模式
  - 解释器模式



