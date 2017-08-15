## Marionette.View

Marionette has a base Marionette.View class that other views extend from.
This base view provides some common and core functionality for
other views to take advantage of.

Marionette 有一个扩展自其他 views 的基础 Marionette.View 类。
该类得用其他 views 提供了一组常用和核心的功能。

Note: The Marionette.View class is not intended to be
used directly. It exists as a base view for other view classes
to be extended from, and to provide a common location for
behaviors that are shared across all views.

注： Marionette.View 类并不是可以直接使用，他的存在是作为其他 view 类基类。提供一个公共的位置行为分享给所有的 views。

## Binding To View Events {视图绑定事件}

Marionette.View extends `Backbone.View`. It is recommended that you use
the `listenTo` method to bind model, collection, or other events from Backbone
and Marionette objects.

Marionette.View 扩展于 Backbone.View，建议你使用 `listenTo` 方法绑定 `model`, `collection` 或者 Backbone 和 Marionette 对象的其他事件。

```
    var MyView = Marionette.ItemView.extend({
      initialize: function(){
        this.listenTo(this.model, "change:foo", this.modelChanged);
        this.listenTo(this.collection, "add", this.modelAdded);
      },

      modelChanged: function(model, value){
      },

      modelAdded: function(model){
      }
    });
```

The context (this) will automatically be set to the view. You can
optionally set the context by using _.bind.

该上下文（this）自动设置为该 view，你可以通过 `_.bind` 随意的设置上下文。

```
    // Force the context of the "reconcileCollection" callback method to be the collection
    // itself, for this event handler only (does not affect any other use of the
    // "reconcileCollection" method)
    this.listenTo(this.collection, "add", _.bind(this.reconcileCollection, this.collection));
```

## View onShow {通过 onShow 显示}

"show" / onShow - Called on the view instance when the view has been rendered and displayed.
This event can be used to react to when a view has been shown via a region.
All views that inherit from the base Marionette.View class have this functionality, notably ItemView, CollectionView, CompositeView, and LayoutView.

在 view 实例渲染并显示之后调用。
当 view 通过 region 显示的时候，该事件可以用来做一些反应。
所有继承于 Marionette.View 的类都会有该功能。特别是 `ItemView`, `CollectionView`, `CompositeView` 以及 `LayoutView`

```
    Marionette.ItemView.extend({
      onShow: function(){
        // react to when a view has been shown
        // 当一个 view 渲染完成之后，可以在此做一些反应
      }
    });
```

A common use case for the onShow method is to use it to add children views.

`onShow` 通常用法之一是用于添加子级视图

```
    var LayoutView = Marionette.LayoutView.extend({
       regions: {
         Header: 'header',
         Section: 'section'
       },
       onShow: function() {
          this.Header.show(new Header());
          this.Section.show(new Section());
       }
    });
```

## View destroy {视图的销毁}

View implements a `destroy` method, which is called by the region managers automatically.
As part of the implementation, the following are performed:

视图实现了一个 `destroy` 方法，在 region managers 中自动调用。
以下是自动执行的一部分的方法：

* call an `onBeforeDestroy` event on the view, if one is provided
  > 如果提供了 `onBeforeDestroy`, 在视图中调用。

* call an `onDestroy` event on the view, if one is provided
  > 如果提供了 `onDestroy` 事件，在视图中调用。

* unbind all custom view events
  > 取消所有自定义事件的绑定

* unbind all DOM events
  > 移出所有 DOM 事件

* remove `this.el` from the DOM
  > 从DOM上删除 `this.el`

* unbind all `listenTo` events
  > 删除所有 `listenTo` 事件

* returns the view.
  > 返回 view


By providing an `onDestroy` method in your view definition, you can
run custom code for your view that is fired after your view has been
destroyed and cleaned up. The `onDestroy` method will be passed any arguments
that `destroy` was invoked with. This lets you handle any additional clean
up code without having to override the `destroy` method.

如果 view 提供了一个 `onDestroy` 方法。
你可以在 view 被销毁和清理之前运行自定义的代码。
将会给 `onDestroy` 方法传入所有调用 `destroy` 方法时的参数。
这可以让你在不覆写 `destroy` 方法的情况下，做一些附加的清理代码。

```
    var MyView = Marionette.ItemView.extend({
      onDestroy: function(arg1, arg2){
        // custom cleanup or destroying code, here
        // 这里写一些自定义的清理或销毁的代码
      }
    });

    var v = new MyView();
    v.destroy(arg1, arg2);
```

## View onBeforeDestroy {视图的 onBeforeDestroy 方法}

When destroying a view, an `onBeforeDestroy` method will be called, if it
has been provided, just before the view destroys. It will be passed any arguments
that `destroy` was invoked with.

销毁一个视图的时候，如果提供了 `onBeforeDestroy` 方法，该方法将会在 view 被销毁之前被调用。
该方法将会传入 `destroy` 方法调用时的所有参数。


## View "attach" / onAttach event {视图的 attach 事件}

Every view in Marionette has a special event called "attach," which is triggered anytime that showing
the view in a Region causes it to be attached to the `document`. Like other Marionette events, it also
executes a callback method, `onAttach`, if you've specified one. The "attach" event is great for jQuery
plugins or other logic that must be executed after the view is attached to the `document`.

Marionette 中所有的 view 都有一个特殊的事件，称为 `attach`。该事件在 Region 实例中将 view 添加到 `document` 上时触发。
同其他 Marionette 事件一样，也会执行一个 `onAttach` 回调方法，前提是你已经指定了该方法。
'attach` 事件对 jQuery 插件或其他必须在 view 添加到 `document` 上执行的逻辑非常有用。

The attach event is only fired when the view becomes a child of the `document`.
If the Region you're showing the view in is not a child of the `document` at the time that
you call `show` then the attach event will not fire until the Region is a child of the `document`.

attach 事件只会在 view 成为 `document` 子元素的时候被触发。
在你调用 `show` 方法时，如果该 region 显示的 view 不是 `document` 的一个子元素，则不会触发该事件。
（译注：也就是说，如果你只是渲染了，但是并没有将其添加到 `document` 上，都是不会触发该事件的，真他妈罗嗦!）

This event is unique in that it propagates down the view tree. For instance, when a CollectionView's
attach event is fired, all of its children views will have the attach event fired as well.
In addition, deeply nested Layout View structures will all have their attach event fired at the proper time, too.

该方法在 view 树上只会传播一次。对一个实例来说，当一个 CollectionView 的 attach 事件触发了，其所有的子视图的 attach 事件同样也会被触发。
此处，深层嵌套布局结构也会触发它们的 attach 事件。

For more on efficient, deeply-nested view structures, refer to the LayoutView docs.

更多高效、深度的嵌套视图结构，参见 LayoutView 文档

## View "before:attach" / onBeforeAttach event {视图的 "before:attach" / onBeforeAttach 事件}

This is just like the attach event described above, but it's triggered right before the view is
attached to the document.

和上文所述的 attach 事件一样，只是该事件是在 view 添加到 document 之前触发。


## View "dom:refresh" / onDomRefresh event {视图的 "dom:refresh" / onDomRefresh 事件}

Triggered after the view has been rendered, has been shown in the DOM via a Marionette.Region, and has been re-rendered.

view 已经渲染后触发，经由 Marionette.Region 显示在 DOM 上，并且已经重新渲染。

This event / callback is useful for DOM-dependent UI plugins such as jQueryUI or KendoUI.

在DOM依赖一些UI插件诸如 jQueryUi 或 KendoUI 的时候，该事件/回调很有用。

```
    Marionette.ItemView.extend({
      onDomRefresh: function(){
        // manipulate the `el` here. it's already
        // been rendered, and is full of the view's
        // HTML, ready to go.

        // 在此处操纵 `el` 属性。它已经被渲染，并且并 view 的 HTML 填充。
      }
    });
```
For more information about integration Marionette w/ KendoUI (also applicable to jQueryUI and other UI
widget suites), see this blog post on KendoUI + Backbone.

更多关于集成 Marionette 的 UI，见[博客](http://www.kendoui.com/blogs/teamblog/posts/12-11-26/backbone_and_kendo_ui_a_beautiful_combination.aspx)

## View.events

Since Views extend from backbone's view class, you gain the benefits of the events hash.

由于 view 是从 Backbone.View 类，所以你可以使用 events 散列做一些事件

Some preprocessing sugar is added on top to add the ability to cross utilize the ui hash.

一些预处理语法糖通过利用 ui 散列，使其成为可用。

```
    var MyView = Marionette.ItemView.extend({
      // ...

      ui: {
        "cat": ".dog"
      },

      events: {
        "click @ui.cat": "bark" //is the same as "click .dog":  等同于 "click .dog"
      }
    });
```

## View.triggers

Views can define a set of triggers as a hash, which will convert a DOM event into a view.triggerMethod call.

Views 可以在一个hash上定义一个触发的集合，在 view.triggerMethod 调用时将其转换为 DOM 事件。

The left side of the hash is a standard Backbone.View DOM event configuration,
while the right side of the hash is the view event that you want to trigger from the view.

hash 的左边是一个标准的 Backbone.View DOM 事件配置。
右边是你想从 view 上触发的 view 事件。（译注：真尼玛绕啊，直接说 view 事件名不就得了）。

```
    var MyView = Marionette.ItemView.extend({
      // ...

      triggers: {
        "click .do-something": "something:do:it"
      }
    });

    var view = new MyView();
    view.render();

    view.on("something:do:it", function(args){
      alert("I DID IT!");
    });

    // "click" the 'do-something' DOM element to
    // demonstrate the DOM event conversion

    // 点击 'do-something' DOM 元素展示了 DOM 事件的转换
    view.$(".do-something").trigger("click");
```

The result of this is an alert box that says, "I DID IT!" Triggers can also be
executed using the 'on{EventName}' attribute.

结果会弹出提示信息面板，显示 "I DID IT!"，触发器也可以用 'on{EventName}' 属性执行。

By default all triggers are stopped with `preventDefault` and
`stopPropagation` methods. But you can manually configure the triggers using
hash instead of event name. Example below triggers an event and prevents
default browser behaviour using `preventDefault` method.

默认情况下，所有的触发事件都可以通过 `stopPropagation` 和 `preventDefault` 方法停止。
但是你可以使用 hash 替代事件名，手动的配置触发器。在下面的例子中，触发一个事件并且使用 `preventDefault` 阻止浏览器默认行为。

```
    Marionette.CompositeView.extend({
      triggers: {
        "click .do-something": {
          event: "something:do:it",
          preventDefault: true, // this param is optional and will default to true
          stopPropagation: false
        }
      }
    });
```

You can also specify the triggers as a function that returns a hash of trigger configurations

你也可以指定一个返回触发器配置 hash 的函数。


```
    Marionette.CompositeView.extend({
      triggers: function(){
        return {
          "click .that-thing": "that:i:sent:you"
        };
      }
    });
```

Trigger keys can be configured to cross utilize the ui hash.

触发器的 key 可以通过选用 ui hash 配置

```
    Marionette.ItemView.extend({
      ui: {
         'monkey': '.guybrush'
      },
      triggers: {
        'click @ui.monkey': 'see:LeChuck' // equivalent of "click .guybrush"
      }
    });
```

Triggers work with all View classes that extend from the base Marionette.View.

触发器可以在所有扩展自基础 Marionette.View 的 View 类中运行。


## Trigger Handler Arguments {触发器句柄参数}

A trigger event handler will receive a single argument that includes the following:

一个触发器事件句柄接收单个参数，包含如下内容

view

model

collection


These properties match the view, model, and collection properties of the view that triggered the event.

这些属性匹配触发该事件的 view 的 view, model 和 collection 属性。

```
    var MyView = Marionette.ItemView.extend({
      // ...

      triggers: {
        "click .do-something": "some:event"
      }
    });

    var view = new MyView();

    view.on("some:event", function(args){
      args.view; // => the view instance that triggered the event
      // 触发该事件的view实例
      args.model; // => the view.model, if one was set on the view
      // view.model(如果在view中设置了的话)
      args.collection; // => the view.collection, if one was set on the view
      // view.collection(如果在view中设置了的话)
    });
```

Having access to these allows more flexibility in handling events from multiple views.
For example, a tab control or expand/collapse widget such as a panel bar could trigger the same event from many different views
and be handled with a single function.

从多个视图中访问这些有更多灵活的事件句柄。
如：一个 tab 控制器或者展开/收缩小部件中的面板栏在不同的视图中使用一个函数触发相同的事件


## View.modelEvents and View.collectionEvents

Similar to the events hash, views can specify a configuration
hash for collections and models. The left side is the event on
the model or collection, and the right side is the name of the
method on the view.

同 events hash 类似，views 也能为 collections 和 models 指定一个配置 hash。
左边是在 model 或 collection 上的事件，右边是 view 上的方法名。

```
    Marionette.CompositeView.extend({

      modelEvents: {
        "change:name": "nameChanged"
        // equivalent to view.listenTo(view.model, "change:name", view.nameChanged, view)
        // 等价于调用 view.listenTo(view.model, "change:name", view.nameChanged, view)
      },

      collectionEvents: {
        "add": "itemAdded"
        // equivalent to view.listenTo(view.collection, "add", view.itemAdded, view)
        // 等价于 view.listenTo(view.collection, "add", view.itemAdded, view)
      },

      // ... event handler methods
      nameChanged: function(){ /* ... */ },
      itemAdded: function(){ /* ... */ },

    })
```

These will use the memory safe `listenTo`, and will set the context
(the value of this) in the handler to be the view.
Events are bound at the time of instantiation, and an exception will be thrown
if the handlers on the view do not exist.

这会使用内存安全的 `listenTo` 方式绑定，且会将句柄的上下文设置为该 view。
事件在实例化的时候绑定，如果 view 上没有这个句柄，将会抛出一个异常。

The `modelEvents` and `collectionEvents` will be bound and
unbound with the Backbone.View delegateEvents and undelegateEvents
method calls. This allows the view to be re-used and have
the model and collection events re-bound.

`modelEvents` 和 `collectionEvents` 将会调用 Backbone.View 的 `delegateEvents` 和 `undelegateEvents` 方法绑定和解除绑定。
这可以使得该 view 可重复利用，model 和 collection 事件可重新绑定。

## Multiple Callbacks {多个回调}

Multiple callback functions can be specified by separating them with a space.

可以使用空格隔开函数名来指定多个回调函数。

```
    Marionette.CompositeView.extend({

      modelEvents: {
        "change:name": "nameChanged thatThing"
      },

      nameChanged: function(){ },

      thatThing: function(){ },
    });
```

This works in both `modelEvents` and `collectionEvents`.



## Callbacks As Function {函数做为回调}

A single function can be declared directly in-line instead of specifying a
callback via a string method name.

单个函数可以公开的直接在线声明，而不用通过一个函数名的字符串去指定。

```
    Marionette.CompositeView.extend({

      modelEvents: {
        "change:name": function(){
          // handle the name changed event here
        }
      }

    });
```

This works for both `modelEvents` and `collectionEvents`.

这种方式在 `modelEvents` 和 `collectionEvents` 上都可以运用。


## Event Configuration As Function {函数作为事件的配置}

A function can be used to declare the event configuration as long as
that function returns a hash that fits the above configuration options.

可以公开声明一个函数作为事件的配置，只要该函数返回和符合上面所言的配置的 hash 即可。

```
    Marionette.CompositeView.extend({

      modelEvents: function(){
        return { "change:name": "someFunc" };
      }

    });
```

This works for both `modelEvents` and `collectionEvents`.

此方式在 `modelEvents` 和 `collectionEvents` 上都有效


## View.serializeModel

The serializeModel method will serialize a model that is passed in as an argument.

`serializeModel` 方法将序列化一个 model 作为参数传递。（译注：啥意思？）

## View.bindUIElements

In several cases you need to access ui elements inside the view
to retrieve their data or manipulate them. For example you have a
certain div element you need to show/hide based on some state,
or other ui element that you wish to set a css class to it.
Instead of having jQuery selectors hanging around in the view's code
you can define a `ui` hash that contains a mapping between the
ui element's name and its jQuery selector. Afterwards you can simply
access it via `this.ui.elementName`.

在一些情况下,你需要访问 ui 元素内部的视图，检索数据或操作。
例如你有一个确定存在的 div 元素需要基于一些状态来显示/隐藏, 或者你想给其他 ui 元素设置一个css类。
替代使用 jQuery 选择器挂在视图的代码里，你可以定义一个包含一个 ui 元素名与 jQuery 选择器之间的映射的 `ui` hash。
之后，你可以经由 `this.ui.elementName` 方便的访问它。

See ItemView documentation for examples.

参见 ItemView 文档下的例子。

This functionality is provided via the `bindUIElements` method.
Since View doesn't implement the render method, then if you directly extend
from View you will need to invoke this method from your render method.
In ItemView and CompositeView this is already taken care of.

该功能是通过 `bindUIElements` 方法提供。
因为 view 没有实现 `render` 方法，如果你直接从 view 上扩展，你需要从你的 render 方法中调用该方法。
在 `ItemView` 和 `CompositeView` 已经处理该问题。

## View.mergeOptions

The preferred way to manage your view's options is with `mergeOptions`.
It accepts two arguments: the options object and the keys to merge onto the instance directly.

管理你的 views 首选方案是用 `mergeOptions`。
它接收两个参数：配置对象和直接合并到实例上的键。

```
    var ProfileView = Marionette.ItemView.extend({
      profileViewOptions: ['user', 'age'],

      initialize: function(options) {
        this.mergeOptions(options, this.profileViewOptions);

        console.log('The merged options are:', this.user, this.age);
      }
    });
```

More information mergeOptions

更多关于 [mergeOptions](http://marionettejs.com/docs/v2.4.2/marionette.functions.html#marionettemergeoptions) 的信息

## View.getOption

Retrieve an object's attribute either directly from the object, or from the object's this.options, with `this.options` taking precedence.

在另一个对象或依据 `this.options` 的优先级在另一个对象的`this.options`上，检索一个对象的属性。

More information getOption

更多 [getOption](http://marionettejs.com/docs/v2.4.2/marionette.functions.html#marionettegetoption) 信息


## View.bindEntityEvents

Helps bind a backbone "entity" to methods on a target object. bindEntityEvents is used to support modelEvents and collectionEvents.

用于绑定一个 backbone 实体到目标对象上。该方法也支持 `modelEvents` 和 `collectionEvents`

More information bindEntityEvents

bindEntityEvents [更多信息](http://marionettejs.com/docs/v2.4.2/marionette.functions.html#marionettebindentityevents)

## View.templateHelpers

There are times when a view's template needs to have some
logic in it and the view engine itself will not provide an
easy way to accomplish this. For example, Underscore templates
do not provide a helper method mechanism while Handlebars
templates do.

有些时候，view 模版需要一些逻辑，但是 view 引擎本身并没有提供一个简单的方式去完成该这些工作。
比如，当需要对模版做一些事情的时候，Underscore 模版没有提供一个辅助方法机制。

A templateHelpers attribute can be applied to any View object that renders a template.
When this attribute is present its contents will be mixed in to the data object that comes back from the serializeData method.
This will allow you to create helper methods that can be called from within your templates.
This is also a good place to add data not returned from serializeData, such as calculated values.

templateHelpers 属性可以应用于任意需要渲染模版的视图对象。
当该属性是现在它的内容时将会混淆入 `serializeData` 方法返回的数据对象。（译注：完全不懂...留待大神）。
该特性允许你创建一个辅助方法在你的模板中调用。
这也是一个添加数据（不是从`serializeData`方法中返回的数据）的好地方，比如计算值。

## Basic Example {基本示例}

```
    <script id="my-template" type="text/html">
      I <%= percent %>% think that <%= showMessage() %>
    </script>
```

```
    var MyView = Marionette.ItemView.extend({
      template: "#my-template",

      templateHelpers: function () {
        return {
          showMessage: function(){
            return this.name + " is the coolest!";
          },

          percent: this.model.get('decimal') * 100
        };
      }
    });

    var model = new Backbone.Model({
      name: "Marionette",
      decimal: 1
    });
    var view = new MyView({
      model: model
    });

    view.render(); //=> "I 100% think that Marionette is the coolest!";
```

The `templateHelpers` can also be provided as a constructor parameter

`templateHelpers` 也可以用作提供构造函数的参数。

for any Marionette view class that supports the helpers.

一个 Marionette view 类支持如下辅助方法

```
    var MyView = Marionette.ItemView.extend({
      // ...
    });

    new MyView({
      templateHelpers: {
        doFoo: function(){ /* ... */ }
      }
    });
```


## Accessing Data Within The Helpers {通过helpers访问数据}

In order to access data from within the helper methods, you
need to prefix the data you need with `this`. Doing that will
give you all of the methods and attributes of the serialized
data object, including the other helper methods.

为了通过 helper 方法访问数据，你需要在数据前添加 `this` 前缀。
这样做，将可以取得序列化对象的所有的属性和方法，包括 helper 方法。

```
    templateHelpers: {
      something: function(){
        return "Do stuff with " + this.name + " because it's awesome.";
      }
    }
```

## Object Or Function As templateHelpers {使用 Object 或者 Function 作为 templateHelpers}

You can specify an object literal (as shown above), a reference
to an object literal, or a function as the templateHelpers.

你可以指定一个如上方所示的对象字面量，一个引用的对象字面量，或者一个函数作为 templateHelpers 的值。

If you specify a function, the function will be invoked
with the current view instance as the context of the
function. The function must return an object that can be
mixed in to the data for the view.

如果指定一个函数，该函数会在使用当前 view 作为上下文来调用。
该函数必须返回一个能将数据混入该 view 的对象（译注：应该是 key 值不要乱起，不要和已有的属性值相冲突）。

```
    Marionette.ItemView.extend({
      templateHelpers: function(){
        return {
          foo: function(){ /* ... */ }
        }
      }
    });
```

## Change Which Template Is Rendered For A View {改变一个视图模板的程现}

There may be some cases where you need to change the template that is
used for a view, based on some simple logic such as the value of a
specific attribute in the view's model. To do this, you can provide
a `getTemplate` function on your views and use this to return the
template that you need.

有时候你可能需要改变一个 view 的模板，基于一些简单的逻辑，比如一个视图模型的指定属性的值。
你可以在你的 view 中提供一个 `getTemplate` 函数，用它返回你们需要的模板。

```
    var MyView = Marionette.ItemView.extend({
      getTemplate: function(){
        if (this.model.get("foo")){
          return "#some-template";
        } else {
          return "#a-different-template";
        }
      }
    });
```

This applies to all view classes.

这可以应用于所的有 view 类。


## UI Interpolation {修改UI}

Marionette UI offers a convenient way to reference jQuery elements.
UI elements can also be interpolated into event and region selectors.

Marionette UI 提义一个方便的方式依赖 jQuery 元素。
UI元素也也可以插入事件和 region 选择器。

In this example, the buy button is referenced in a DOM event and the checkout section is referenced in the region selector.

下面的例子中，buyButton 是通过一个 DOM 事件插入，checkoutSection 通过 region 选择器插入。
(译注：不懂是怎么插入的，这里只是定义了，难道定义之后，就会往DOM上添加？)

```
    var MyView = Marionette.ItemView.extend({

      ui: {
        buyButton: '.buy-button',
        checkoutSection: '.checkout-section'
      },

      events: {
        'click @ui.buyButton': 'onClickBuyButton'
      },

      regions: {
        checkoutSection: '@ui.checkoutSection'
      },

      onShow: function() {
        this.getRegion('checkoutSection').show(new CheckoutSection({
          model: this.checkoutModel
        }));
      }
    });
```