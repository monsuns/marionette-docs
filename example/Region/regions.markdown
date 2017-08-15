Regions provide consistent methods to manage, show and destroy
views in your applications and layouts. They use a jQuery selector
to show your views in the correct place.

`Regions` 提供统一的方法，用于管理、显示、销毁应用和布局的 `views`。
使用 `jQuery` 选择器来确定 `views` 的位置。


Using the LayoutView class you can create nested regions。

使用 `LayoutView` 类能创建嵌套的 `Regions`

## Defining An Application Region {定义应用Region}

You can add regions to your applications by calling the addRegions method on
your application instance. This method expects a single hash parameter, with
named regions and either jQuery selectors or Region objects. You may
call this method as many times as you like, and it will continue adding regions
to the app.

在应用例化后，可以使用 addRegions 为其添加 regions。
该方法接收一个 hash 参数，使用jQuery选择器或者 region 对象定义 region。
可以多次调用该方法，给应用持续添加region。

```JavaScript
    app.addRegions({
        mainRegion: "#main-content",
        navigationRegion: "#navigation"
    });
```
As soon as you call addRegions, your regions are available on your
app object. In the above, example MyApp.mainRegion and MyApp.navigationRegion
would be available for use immediately.

当你调用 addRegions 方法后，应用对象上的 regions 立即可用。
上文中的例子中 <code>app.mainRegion</code> 与 <code>app.navigationRegion</code> 立马可以使用

If you specify the same region name twice, the last one in wins.

如果你定义一个重名的 region，将以最后一次定义为准

You can also add regions via LayoutViews:

你也可以通过 <code>LayoutViews</code> 添加 regions

```JavaScript
    var AppLayoutView = Marionette.LayoutView.extend({
        template: "#layout-view-template",

        regions: {
            menu: "#menu",
            content: "#content"
        }
    });
    var layoutView = new AppLayoutView();
    layoutView.render();
    layoutView.menu.show(new MenuView());
    layoutView.content.show(new MainContentView());
```

## Region Configuration Types {Region配置参数类型}

Marionette supports multiple ways to define regions on your Application or LayoutView.

Marionette 支持多种方法在 `Application` 和 `LayoutView` 上定义 regions


## String Selector {字符选择器}

You can use a jQuery string selector to define regions.

你可以使用一个 jQuery 选择器定义 regions

```
    app.addRegions({
        mainRegion: '#main'
    });
```

## Region Class {Region 类}

If you've created a custom region class, you can use it to
define your region.

如果你已经创建了一个自定义的 region 类，你可以直接使用

NOTE: Make sure the region class has an el
property set or it won't work!

注：确认该 region 类有一个 `el` 属性

```JavaScript
    var MyRegion = Marionette.Region.extend({
      el: '#main-nav'
    });

    App.addRegions({
      navigationRegion: MyRegion
    });
```

## Object Literal {对象字面量}

Finally, you can define regions with an object literal. Object
literal definitions normally expect a selector or el
property. The selector property is a selector string, and
the el property can be a selector string, a jQuery object,
or an HTML node.

最后，你可以用对象字面量来定义 regions 。对象字面量定义通常使用一个选择器或一个 `el` 属性。
选择器为 string 类型， `el` 属性可以是一个 string 类型的选择器，也可以是一个 jQuery 对象。
还可以是一个 HTML 节点。

You may also supply a regionClass property for a custom region
class. If your regionClass already has el set, then you do
not need to supply a selector or el property on the object
literal.

你可以提供一个自定义的 region 类给 `regionClass` 属性。
如果对象字面量的 `regionClass` 已经拥有 `el` 属性， 则可以不用提供带 selector 的 `el` 属性。

Any other properties you set on the object literal will be
used as options passed to the region instance, including the
allowMissingEl option.

如果在该对象字面量上设置了其他属性，那么在该 region 实例化的时候，这些属性都会
传递给 region 的实例，包括 `allowMissingEl` 配置

Ordinarily regions enforce the presence of a backing DOM element.
In some instances it may be desirable to allow regions to be
instantiated and used without an element, such as when regions
defined by a parent LayoutView class are used by only some of its
subclasses. In these instances, the region can be defined with the
allowMissingEl option, suppressing the missing element error and
causing show calls to the region to be treated as no-ops.

通常 regions 对象强制依赖于一个 DOM 元素。
但在一些情况下，需要允许实例化 region 的时候不指定一个元素。
例如：当 region 对象是 `LayoutView` 类的一个子类的时候，当其实例化时，region 对象可
以配置 `allowMissingEl` 项，忽略未定义元素的错误。

```JavaScript
    var MyRegion      = Marionette.Region.extend();
    var MyOtherRegion = Marionette.Region.extend();
    var MyElRegion    = Marionette.Region.extend({ el: '#footer' });

    App.addRegions({
        contentRegion: {
            el: '#content',
            regionClass: MyRegion
        },

        navigationRegion: {
            el: '#navigation',
            regionClass: MyOtherRegion,

            // Options passed to instance of `MyOtherRegion` for
            // the `navigationRegion` on `App`

            // 在 `App` 中对 `navigationRegion` 实例化 `MyOtherRegion` 的时候
            // 会将这个配置传入

            navigationOption: 42,
            anotherNavigationOption: 'foo'
        },

        footerRegion: {
            regionClass: MyElRegion
        }
    });
```

Take note that one of the primary benefits of using regionClass
with an el already set is to also provide options to the region
instance. This isn't possible when using the region class directly
like earlier.

注意：使用 regionClass 的主要好处之一。
在于一个提前定义一个 `el`，提供给 region 的实例。
没有其他方法可以更早的定义可以直接使用的 region 类。

```JavaScript
    var MyRegion = Marionette.Region.extend({
      el: '#content',
    });

    App.addRegions({
      contentRegion: {
        regionClass: MyRegion,
        myRegionOption: 'bar',
        myOtherRegionOption: 'baz'
      }
    });
```

## Mix-and-match {混合、匹配}

Of course you can mix-and-match the region configuration types.

当然你也可以多种方法混合配置 region 的类型

```JavaScript
    var MyRegion = Marionette.Region.extend({
      el: '#content'
    });

    var MyOtherRegion = Marionette.Region.extend();

    App.addRegions({
      contentRegion: MyRegion,

      navigationRegion: '#navigation',

      footerRegion: {
        el: '#footer',
        regionClass: MyOtherRegion
      }
    });
```

## Initialize A Region With An el {用 el 初始化一个 Region}

You can specify an el for the region to manage at the time
that the region is instantiated:

在 region 实例化的时，你可以指定一个 `el`。

```
    var mgr = new Marionette.Region({
      el: "#someElement"
    });
```

The el option can also be a raw DOM node reference:

`el` 配置也可以使用一个原生的 DOM 节点的引用

```
    var mgr = new Marionette.Region({
      el: document.querySelector("body")
    });
```

Or the el can also be a jQuery wrapped DOM node:

也可以使用一个 jQuery 包装的 DOM 节点

```
    var mgr = new Marionette.Region({
      el: $("body")
    });
```

## Showing a View {显示视图}

Once a region is defined, you can call its show
and empty methods to display and shut-down a view:

region 定义一次后，你可以调用它的 `show` 和 `empty` 方法来显示和关闭一个视图

```
    var myView = new MyView();

    // render and display the view
    MyApp.mainRegion.show(myView);

    // empties the current view
    MyApp.mainRegion.empty();
```

## preventDestroy {阻止销毁}

If you replace the current view with a new view by calling show,
by default it will automatically destroy the previous view.
You can prevent this behavior by passing {preventDestroy: true} in the options
parameter. Several events will also be triggered on the views; see
Region Events And Callbacks for details.

当你调用 `show` 方法，使用一个新的 view 替换掉当前 view 时，默认会自动销毁上一个 view。
你可以通过给 options 设置 {preventDestroy: true}参数阻止该形为。
数个事件也会在views上触发，详见 [Region 事件和回调](http://marionettejs.com/docs/v2.4.2/marionette.region.html#region-events-and-callbacks)

```JavaScript

    // Show the first view.
    //
    
    var myView = new MyView();
    MyApp.mainRegion.show(myView);

    // Replace the view with another. The
    // `destroy` method is called for you
    // 替换其他 view，会调用 `destroy` 方法
    var anotherView = new AnotherView();
    MyApp.mainRegion.show(anotherView);

    // Replace the view with another.
    // Prevent `destroy` from being called
    // 替换其他 view，阻止 `destroy` 调用
    var anotherView2 = new AnotherView();
    MyApp.mainRegion.show(anotherView2, { preventDestroy: true });
```

NOTE: When using preventDestroy: true you must be careful to cleanup your old views
manually to prevent memory leaks.

注：在使用 {preventDestroy:true} 时，你必须关注旧的view，手动释放，防止内存溢出。

## forceShow {强制显示}

If you re-call show with the same view, by default nothing will happen
because the view is already in the region. You can force the view to be re-shown
by passing in {forceShow: true} in the options parameter.

如果你重复调用同一个 view 的 `show` 方法，默认是不发生任何动作的，因为 view 在 region 中已经准备好了。
你可以传入 {forceShow: true} 配置参数，强制重新显示该view。（译注：刷新？）


```
    var myView = new MyView();
    MyApp.mainRegion.show(myView);

    // the second show call will re-show the view
    MyApp.mainRegion.show(myView, {forceShow: true});
```

## Emptying a region { 清空一个 region }

You can empty a region of its view and contents by invoking .empty() on the region instance.
If you would like to prevent the view currently shown in the region from being destroyed
you can pass {preventDestroy: true} to the empty method to prevent the default destroy behavior.
The empty method returns the region instance from the invocation of the method.

你可以在 region 的视图和内容中调用 region 实例的 `.empty()` 清空 region。
如果你想阻止当前视图在该 region 被销毁，你可以传入 `{preventDestroy:true}` 给 `empty` 方法阻止销毁的默认行为。
`empty` 方法返回调用该方法的 region 实例。


## onBeforeAttach & onAttach

Regions that are attached to the document when you execute show are special in that the
views that they show will also become attached to the document. These regions fire a pair of triggerMethods on all
of the views that are about to be attached – even the nested ones. This can cause a performance issue if you're
rendering hundreds or thousands of views at once.

当你执行 `show` 方法时，Regions 是附加到 `document` 上的。
这些 region 会触发绑定在所有 view 上的一对 `triggerMethods`(译注：onBeforeAttach 和 onAttach) —— 即使是嵌套的。
如果你要同时渲染成百上千的 view， 这将会出现性能问题。


If you think these events might be causing some lag in your app, you can selectively turn them off
with the triggerBeforeAttach and triggerAttach properties or show() options.

如果你觉得这些事件可能会导致性能滞后，你可以选择设置 `triggerBeforeAttach` 和 `triggerAttach` 属性，
或者通过 `show` 方法的参数来关闭他们。

```
    // No longer trigger attach
    // 不再触发附加事件
    myRegion.triggerAttach = false;
```

You can override this on a per-show basis by passing it in as an option to show.

你可以在覆写该属性的基本上，给 `show` 方法传入参数再来触发。

```
    // This region won't trigger beforeAttach...
    // 该 region 不再触发 beforeAttach
    myRegion.triggerBeforeAttach = false;

    // Unless we tell it to
    // 除非在调用的时候告诉他需要触发
    myRegion.show(myView, {triggerBeforeAttach: true});
```

Or you can leave the events on by default but disable them for a single show.
你也可以不阻止默认行为，但在单个 `show` 方法中禁用他们

```
    // This region will trigger attach events by default but not for this particular show.
    // 该 region 在默认情况下会触发附加事件，但在这个 show 中不会触发
    myRegion.show(myView, {triggerBeforeAttach: false, triggerAttach: false});
```

## Checking whether a region is showing a view {检查 region 是否已经显示了一个 view}

If you wish to check whether a region has a view, you can use the hasView
function. This will return a boolean value depending whether or not the region
is showing a view.

如果你希望检查一个 region 是有一 view，你可以使用 `hasView` 函数。
该函数会返回一个布尔值表示该 region 是否显示了一个 view。


## reset A Region  {重置 Region }

A region can be reset at any time. This destroys any existing view
being displayed, and deletes the cached el. The next time the
region shows a view, the region's el is queried from
the DOM.

一个 region 可以在任何时候被重置。这将会销毁所有已经存在并显示了的视图，并会删除缓存的 `el`。
该 region 下一次显示一个视图的时候，它的 `el` 会重新去 dom 中寻找。

```
    myRegion.reset();
```

This is useful when regions are re-used across view instances, and in unit testing.

在 region 实例跨视图复用（译注：大约就是一个 region 对应多个视图的时候）的和单元测试时，该方法非常有用。

## Set How View's el Is Attached {设置视图 el 的连接方式}

Override the region's attachHtml method to change how the view is attached
to the DOM. This method receives one parameter - the view to show.

重写 region 的 `attachHtml` 方法来改变视图连接DOM的方式。
该方法接受一个参数 —— 需要显示的 view

The default implementation of attachHtml is:

`attachHtml` 默认实现如下

```
    Marionette.Region.prototype.attachHtml = function(view){
      this.$el.empty().append(view.el);
    }
```

This replaces the contents of the region with the view's
el / content. You can override attachHtml for transition effects and more.

该方法用 view.el / content 替换到 region 的内容。
你可以覆盖该方法，添加一些过度效果或做更多的事件。

```
    Marionette.Region.prototype.attachHtml = function(view){
      this.$el.hide();
      this.$el.html(view.el);
      this.$el.slideDown("fast");
    }
```

It is also possible to define a custom render method for a single region by
extending from the Region class and including a custom attachHtml method.

还可以为一个从 Region 类扩展来的 region（可以包含一个自定义的 `attachHtml` 方法）， 单独定义一个自定义的 `render` 方法。


This example will make a view slide down from the top of the screen instead of just
appearing in place:

该例子的视图将会从屏幕顶端下滑，而不是直接出现在对应位置上。

```
    var ModalRegion = Marionette.Region.extend({
      attachHtml: function(view){
        // Some effect to show the view:
        // 为显示 view 添加一些特效
        this.$el.empty().append(view.el);
        this.$el.hide().slideDown('fast');
      }
    })

    MyApp.addRegions({
      mainRegion: '#main-region',
      modalRegion: {
        regionClass: ModalRegion,
        selector: '#modal-region'
      }
    })
```

## Attach Existing View {添加已经存在的 view}

There are some scenarios where it's desirable to attach an existing
view to a region , without rendering or showing the view, and
without replacing the HTML content of the region. For example, SEO and
accessibility often need HTML to be generated by the server, and progressive
enhancement of the HTML.

一些场景的 region 需要使用一个现有的 view 才能满足需要，不需要显示或者渲染 view，也不需要在 region 中替换 HTML 内容。
比如 SEO 常常需要在服务端生成 HTML, 或者一些渐进增强的项目的HTML（译注：直译，不明白这是说的啥）


There are two ways to accomplish this:
有两种方式去实现以上功能

* set the `currentView` in the region's constructor
  + 在 region 的构造器中设置 `currentView`。

  ```
      var myView = new MyView({
        el: $("#existing-view-stuff")
      });

      var region = new Marionette.Region({
        el: "#content",
        currentView: myView
      })
  ```
* call `attachView` on the region instance
  + 在 region 的实例中调用 `attachView`。

  ```
    MyApp.addRegions({
      someRegion: "#content"
    });

    var myView = new MyView({
      el: $("#existing-view-stuff")
    });

    MyApp.someRegion.attachView(myView);
  ```

## Region Events And Callbacks {网页上没有内容，不知道为什么...}

## Events raised during `show`: {show方法中的事件提升}

A region will raise a few events when showing and destroying views:

当一个 region 显示或销毁视图的时候，会提升几个事件。

> "before:show" / onBeforeShow - Called on the view instance after the view has been rendered, but before its been displayed.
  在 view 实例被渲染之后，显示之前调用

> "before:show" / onBeforeShow - Called on the region instance after the view has been rendered, but before its been displayed.
  在 region 实例被渲染之后，显示之前调用

> "show" / onShow - Called on the view instance when the view has been rendered and displayed.
  在 view 实例渲染和显示之后调用

> "show" / onShow - Called on the region instance when the view has been rendered and displayed.
  在 region 实例渲染和显示之后调用

> "before:swap" / onBeforeSwap - Called on the region instance before a new view is shown.
  NOTE: this will only be called when a view is being swapped, not when the region is empty.
  在 region 实例中一个新的视图显示之前。
  注：只会在 view 开始切换的时候调用，不会在 region 为空的时候调用。

> "before:swapOut" / onBeforeSwapOut - Called on the region instance before a new view swapped in.
    NOTE: this will only be called when a view is being swapped, not when the region is empty.
  在 region 实例切换一个新的视图之前调用。
  注：只会在视图切换的时候调用，region 为空的时候不调用。

> "swap" / onSwap - Called on the region instance when a new view is shown.
  NOTE: this will only be called when a view is being swapped, not when the region is empty.
  在 region 实例显示一个新的 view 的时候调用
  注：只会在在开始切换实例的时候调用，region 为空的时候不调用

> "swapOut" / onSwapOut - Called on the region instance when a new view swapped in to replace the currently shown view.
  NOTE: this will only be called when a view is being swapped, not when the region is empty.
  在 region 实例用一个新的 view 替换掉当前显示的 view 的时候触发
  注：仅在一个 view 开始切换的时候调用，region 为空的时候不调用。

> "before:empty" / onBeforeEmpty - Called on the region instance before the view has been emptied.
  在一个 region 实例的 view 补充清空之前调用

> "empty" / onEmpty - Called when the view has been emptied. These events can be used to run code when your region opens and destroys views.
  在一个 view 清空时调用。该事件可用于当打开或者销毁视图的时候返回代码（译注：我靠，什么代码？又不说。）。

```
    MyApp.mainRegion.on("before:show", function(view, region, options){
      // manipulate the `view` or do something extra
      // with the `region`
      // you also have access to the `options` that were passed to the Region.show call
    });

    MyApp.mainRegion.on("show", function(view, region, options){
      // manipulate the `view` or do something extra
      // with the `region`
      // you also have access to the `options` that were passed to the Region.show call
    });

    MyApp.mainRegion.on("before:swap", function(view, region, options){
      // manipulate the `view` or do something extra
      // with the `region`
      // you also have access to the `options` that were passed to the Region.show call
    });

    MyApp.mainRegion.on("swap", function(view, region, options){
      // manipulate the `view` or do something extra
      // with the `region`
      // you also have access to the `options` that were passed to the Region.show call
    });

    MyApp.mainRegion.on("before:swapOut", function(view, region, options){
      // manipulate the `view` or do something extra
      // with the `region`
      // you also have access to the `options` that were passed to the Region.show call
    });

    MyApp.mainRegion.on("swapOut", function(view, region, options){
      // manipulate the `view` or do something extra
      // with the `region`
      // you also have access to the `options` that were passed to the Region.show call
    });

    MyApp.mainRegion.on("empty", function(view, region, options){
      // manipulate the `view` or do something extra
      // with the `region`
      // you also have access to the `options` that were passed to the Region.show call
    });

    var MyRegion = Marionette.Region.extend({
      // ...

      onBeforeShow: function(view, region, options) {
        // the `view` has not been shown yet
      },

      onShow: function(view, region, options){
        // the `view` has been shown
      }
    });

    var MyView = Marionette.ItemView.extend({
      onBeforeShow: function(view, region, options) {
        // called before the `view` has been shown
      },
      onShow: function(view, region, options){
        // called when the `view` has been shown
      }
    });

    var MyRegion = Marionette.Region.extend({
      // ...

      onBeforeSwap: function(view, region, options) {
        // the `view` has not been swapped yet
      },

      onSwap: function(view, region, options){
        // the `view` has been swapped
      },

      onBeforeSwapOut: function(view, region, options) {
        // the `view` has not been swapped out yet
      },

      onSwapOut: function(view, region, options){
        // the `view` has been swapped out
      }
    });
```

## Custom Region Classes  {自定义 Region 类}

You can define a custom region by extending from Region. This allows you to create new functionality,
or provide a base set of functionality for your app.

你可以从 Region 扩展一个自定义的 region 类。允许你为你的应用创建一个新的功能函数或者提供一个基础设置的功能函数。

## Attaching Custom Region Classes {给 Region 类自定义附加}

Once you define a region class, you can attach the
new region class by specifying the region class as the value.
In this case, addRegions expects the constructor itself, not an instance.

一旦定义一个 region 类，你可以将其作为一个值，附加给指定的 region。
在下面的例子中，`addRegions` 接收 region 的构造器，而不是实例。


```
    var FooterRegion = Marionette.Region.extend({
      el: "#footer"
    });

    MyApp.addRegions({
      footerRegion: FooterRegion
    });
```

You can also specify a selector for the region by using
an object literal for the configuration.

你也可以通过一个对象字面量指定一个选择器作为 region 的配置

```
    var FooterRegion = Marionette.Region.extend({
      el: "#footer"
    });

    MyApp.addRegions({
      footerRegion: {
        selector: "#footer",
        regionClass: FooterRegion
      }
    });
```

Note that a region must have an element to attach itself to. If you
do not specify a selector when attaching the region instance to your
Application or LayoutView, the region must provide an `el` either in its
definition or constructor options.

注：region 必须要有一个element附加在上面。如果没有为你的 `Application` 或 `LayoutView` 上附加的 region 实例指定一个选择器，
该 region 必须在其提定义或构造函数上供一个 `el` 属性。


## Instantiate Your Own Region {实例化你自己的 Region }

There may be times when you want to add a region to your
application after your app is up and running. To do this, you'll
need to extend from `Region` as shown above and then use
that constructor function on your own:

有时候你可能需要在你的应用启动运行之后添加一个 region。
此种情况下，你需要自己从 `Region` 类上扩展，得到一个你自己的构造器。如下所示：

```
    var SomeRegion = Marionette.Region.extend({
      el: "#some-div",

      initialize: function(options){
        // your init code, here
      }
    });

    MyApp.someRegion = new SomeRegion();

    MyApp.someRegion.show(someView);
```

You can optionally add an initialize function to your Region
definition as shown in this example. It receives the options
that were passed to the constructor of the Region, similar to
a Backbone.View.

你可以在该示例中的自定义 `Region` 类随意的添加一个初始化函数（`initialize`），
他接受一个配置参数传给 `Region` 的构造函数，类似于 `Backbone.View`