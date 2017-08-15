## Marionette.LayoutView

A LayoutView is a hybrid of an ItemView and a collection of Region objects. They
are ideal for rendering application layouts with multiple sub-regions
managed by specified region managers.

LayoutView 是由 ItemView 和 Region 对象的 collection 混合而成。
他们非常适合在指定的 region managers 下的多个子 region 的布局。

A layoutView can also act as a composite-view to aggregate multiple
views and sub-application areas of the screen allowing applications to
attach multiple region managers to dynamically rendered HTML.

layoutView 也可以做为集合了多个视图和子应用程序在屏幕上的区域的聚合视图（composite-view），
允许应用附加多个 region 动态绚染 HTML。

You can create complex views by nesting layoutView managers within Regions.

可以通过 Regions 嵌套 LayoutView 创建复杂视图。

For a more in-depth discussion on LayoutViews, see the blog post
[Manage Layouts And Nested Views With Marionette](http://lostechies.com/derickbailey/2012/03/22/managing-layouts-and-nested-views-with-backbone-marionette/)

更深入的讨论 `LayoutViews`，参见博客
[Marionette 管理布局和嵌套视图](http://lostechies.com/derickbailey/2012/03/22/managing-layouts-and-nested-views-with-backbone-marionette/)

Please see
[the Marionette.ItemView documentation](http://marionettejs.com/docs/v2.4.2/marionette.itemview.html)
for more information on available features and functionality.

参考 [Marionette.ItemView 文档] 查看更多可用特性和功能的详细信息

Additionally, interactions with Marionette.Region
will provide features such as onShow callbacks, etc. Please see
[the Region documentation](http://marionettejs.com/docs/v2.4.2/marionette.region.html) for more information.

此外，基于 Marionette.Region 的交互将会提供一些新的特性，比如 `onShow` 回调。
更详细信息见 [Region 文档](http://marionettejs.com/docs/v2.4.2/marionette.region.html)


## Basic Usage {基本用法}

The `LayoutView` extends directly from `ItemView` and adds the ability
to specify `regions` which become `Region` instances that are attached
to the layoutView.

`LayoutView` 直接从 `ItemView` 扩展而来，并添加了指定的 `region` 实例成为其功能属性。

```
    <script id="layout-view-template" type="text/template">
      <section>
        <navigation id="menu">...</navigation>
        <article id="content">...</article>
      </section>
    </script>
```

```
    var AppLayoutView = Marionette.LayoutView.extend({
      template: "#layout-view-template",

      regions: {
        menu: "#menu",
        content: "#content"
      }
    });

    var layoutView = new AppLayoutView();
    layoutView.render();
```

Once you've rendered the layoutView, you now have direct access
to all of the specified regions as region managers.

一旦你绚染了 layoutView，你可以使用 region manager 直接访问 layoutView 中指定的 region

```
    layoutView.getRegion('menu').show(new MenuView());

    layoutView.getRegion('content').show(new MainContentView());
```

There are also helpful shortcuts for more concise syntax.

也提供了有用的简捷语法

```
    layoutView.showChildView('menu', new MenuView());

    layoutView.showChildView('content', new MainContentView());
```

## Region Options {Region 配置}

A LayoutView can take a regions hash that allows you to specify regions per LayoutView instance.
`LayoutView` 可以设置一个 regions 的散列，你可以通过它来为每一个 `LayoutView` 实例指定 regions

```
    new Marionette.LayoutView({
     regions: {
       "cat": ".doge",
       "wow": {
         selector: ".such",
         regionClass: Coin
       }
     }
    })
```

## LayoutView childEvents {LayoutView 子事件}

You can specify a childEvents hash or method which allows you to capture all
bubbling childEvents without having to manually set bindings.

你可以通过一个 childEvents 散列或方法，捕获所有的 childEvents 冒泡事件，而不用手动去绑定。

The keys of the hash can either be a function or a string
that is the name of a method on the layout view.

散列的键可以是一个函数或者一个该 layoutView 的函数名的字符串。

The function is called in the context of the view. The first parameter is
the child view, which emitted the event, the remainder are the arguments
associated with the event.

该函数运行作用域为该视图。第一个参数为子视图，引发的事件中，其余的参数为该事件相关的数据。

```
    // childEvents can be specified as a hash...
    // childEvents 可以通过一个hash来指定
    var MyLayoutView = Marionette.LayoutView.extend({

      // This callback will be called whenever a child is rendered or emits a `render` event
      childEvents: {
        render: function(childView) {
          console.log("a childView has been rendered");
        }
      }
    });

    // ...or as a function that returns a hash.
    // 可也以通过一个函数，返回一个hash来指定
    var MyLayoutView = Marionette.LayoutView.extend({

      childEvents: function() {
        return {
          render: this.onChildRender
        }
      },

      onChildRender: function(childView) {
      }
    });
```

This also works for custom events that you might fire on your child views.

也可以通过可能在子视图中触发的自定义事件来达到以上的目的。

```
    // The child view fires a custom event, `show:message`
    // 子视图触发一个自定义事件 showMessage
    // 在showMessage中调用 'show:message'
    var ChildView = new Marionette.ItemView.extend({
        events: {
          'click .button': 'showMessage'
        },

        showMessage: function (e) {
          console.log('The button was clicked.');
          this.triggerMethod('show:message', msg);
        }
    });

    // The parent uses childEvents to catch that custom event on the child view
    // 父对象使用 childEvents 捕获子视图的自定义事件
    var ParentView = new Marionette.LayoutView.extend({
        childEvents: {
          'show:message': function (childView, msg) {
            console.log('The show:message event bubbled up to the parent.');
          }
        },

        // Alternatively we can use the trigger notation with childview: as the
        // prefix

        onChildviewShowMessage: function (childView, msg) {
          console.log('The show:message event bubbled up to the parent.');
        }
    });
```

译注：以上案例，俺不明白，子视图是什么鬼？待翻了 ItemView 之后再回头看看。（!!todo）

## Specifying Regions As A Function {使用一个函数指定 Regions}

Regions can be specified on a LayoutView using a function that returns
an object with the region definitions. The returned object follow the
same rules for defining a region, as outlined above.

`LayoutView` 上的 regions 可以通过一个函数来指定，该函数返回一个定义了 region 的对象。
返回的对象根据如上所述的标准规则定义一个 region。

```
    Marionette.LayoutView.extend({
      // ...

      regions: function(options){
        return {
          fooRegion: "#foo-element"
        };
      },

      // ...
    });
```

Note that the function receives the view's `options` arguments that
were passed in to the view's constructor. `this.options` is not yet
available when the regions are first initialized,
so the options must be accessed through this parameter.

注：该函数按收的视图的配置参数是通过视图的构造器传入。`this.options` 在第一次初始化 region 的时候还不可用。
所以该配置参数必须通过这个参数(译注：options)来访问。


## Overriding the default RegionManager {覆写默认 RegionManager}

If you need the RegionManager's class chosen dynamically, specify getRegionManager:
如果你需要随心所欲的设置 RegionManager 类，可以通过指定 getRegionManager 方法。

```
    Marionette.LayoutView.extend({
      // ...

      getRegionManager: function() {
        // custom logic
        // 自定义逻辑

        return new MyRegionManager();
      }
```
This can be useful if you want to attach LayoutView's regions to your own instance of `RegionManager`
在你需要给 `LayoutView` 的 region 添加你自己的 `RegionManager` 实例的时候，这变得会有用。


## Region Availability {Region可用状态}

Any defined regions within a layoutView will be available to the
View or any calling code immediately after instantiating the
View. This allows a View to be attached to an existing
DOM element in an HTML page, without the need to call a render
method or anything else, to create the regions.

任何在 layoutView 中定义的 region，在初始化视图之后，该 view 或其他代码都可以立即调用。
这允许一个视图不用调用 `render` 方法或其他动作，直接将页面上已经存在的DOM元素附加给 regions。

However, a region will only be able to populate itself if the
View has access to the elements specified within the region
definitions. That is, if your view has not yet rendered, your
regions may not be able to find the element that you've
specified for them to manage. In that scenario, using the
region will result in no changes to the DOM.

然而，如果一个视图通过指定的 region 访问元素，该视图的 region 只能够填充自己。（译注：我草，这是啥意思？翻译可能有误，求大神解惑）。
这意味着，如果你的视图还没有渲染，regions 可能还不能在你指定的 manage 中去查找元素。
在这种情况下，使用 region，DOM将不会变化。

## Re-Rendering A LayoutView {重新渲染 LayoutView}

A layoutView can be rendered as many times as needed, but renders
after the first one behave differently than the initial render.

一个 layoutView 可以在需要的时候多次渲染，但是之后的渲染和初始渲染在表现上有所不同。

The first time a layoutView is rendered, nothing special happens. It just
delegates to the `ItemView` prototype to do the render. After the
first render has happened, though, the render function is modified to
account for re-rendering with regions in the layoutView.

第一次渲染 layoutView时，如果没有指定其他形为，只会委派到 `ItemView` 的原型上执行渲染。（译注：代理到原型？是指将render的作用域指向原型么）。

After the first render, all subsequent renders will force every
region to be emptied by calling the `empty` method on them. This will
force every view in the region, and sub-views if any, to be destroyed
as well. Once the regions are emptied, the regions will also be
reset so that they are no longer referencing the element of the previous
layoutView render.

在首次渲染之后，随后的所有渲染形为，都将调用每一个 region 的 `empty` 方法强制清空它们。
这会强制清空 region 中的每一个视图，如果有子视图，也会一起清除，同样也会销毁他们（译注：清除和销毁是不一样的，所以此处特别提了出来）。
一旦 region 被清空，region 将会重置，这就意味着 region 不再引用上一个 layoutView 的元素进行渲染。（译注：这是表示所有的DOM需要重新从页面上查找么？）

Then after the layoutView is finished re-rendering itself,
showing a view in the layoutView's regions will cause the regions to attach
themselves to the new elements in the layoutView.

在 layoutView 完成重新渲染自身形为之后，会在其 region 中显示一个视图。
这会导致 region 将其自身添加到 layoutView 中新的元素上。


## Avoid Re-Rendering The Entire LayoutView {避免渲染全部LayoutView}

There are times when re-rendering the entire layoutView is necessary. However,
due to the behavior described above, this can cause a large amount of
work to be needed in order to fully restore the layoutView and all of the
views that the layoutView is displaying.

重新渲染整个layoutView在有的时候是必要的。
然而，由于上述的行为，在全面恢复 layoutView 和显示 layoutView 所有视图的时候，会导致大量的工作触发。

Therefore, it is suggested that you avoid re-rendering the entire
layoutView unless absolutely necessary. Instead, if you are binding the
layoutView's template to a model and need to update portions of the layoutView,
you should listen to the model's "change" events and only update the
necessary DOM elements.


因此,建议你避免重新呈现整个 layoutView 除非绝对必要。
相反，如果你将 layoutView 的模版绑定到了一个 model上，且只需要更新部份内容，
你应该监听这些 model 的 'change' 事件,只更新必要的DOM元素。

## Nested LayoutViews And Views {嵌套的 LayoutViews 和 Views}

Since the LayoutView extends directly from ItemView, it
has all of the core functionality of an item view. This includes
the methods necessary to be shown within an existing region manager.

因为 `LayoutView` 是直接从 `ItemView` 扩展而来，所以他拥有 `ItemView` 的所有核心功能。
其中包含了使用 region manager 显示视图的必要的一些方法。

In the following example, we will use the Application's Regions
as the base of a deeply nested view structure.

在下面的例子中，我们将会使用 Application 的 Regions 建立一个基础的深度嵌套视图结构。

```
    // Create an Application
    // 创建一个Application

    var MyApp = new Marionette.Application();

    // Add a region
    // 添加一个region
    MyApp.addRegions({
      main: "main"
    });

    // Create a new LayoutView
    // 创建一个新的 layoutView

    var layoutView = new Marionette.LayoutView({
      // This option removes the layoutView from
      // the DOM before destroying the children
      // preventing repaints as each option is removed.
      // However, it makes it difficult to do close animations
      // for a child view (false by default)
      //
      // 该配置在销毁子元素之前，将layoutView从DOM上移除，阻止子元素重绘。
      // 但是，这将会使关闭子视图的动画效果变得困难(译注：依然不明白啥意思)
      // 默认为false
      destroyImmediate: true
    });

    // Lastly, show the LayoutView in the App's mainRegion
    // 最后，通过 App的 mainRegion 显示 layoutView
    MyApp.getRegion('main').show(layoutView);
```

You can nest LayoutViews as deeply as you want. This provides for a well organized,
nested view structure.

你可以随意创建多层嵌套，下面提供了一个优雅的嵌套视图结构。


For example, to nest 3 layouts:

三层嵌套。

```
    var layout1 = new Layout1();
    var layout2 = new Layout2();
    var layout3 = new Layout3();

    MyApp.getRegion('main').show(layout1);

    layout1.showChildView('region1', layout2);
    layout2.showChildView('region2', layout3);
```

## Efficient Nested View Structures {高效的嵌套视图结构}

The above example works great, but it causes three separate paints: one for each layout that's being
shown. Marionette provides a simple mechanism to infinitely nest views in a single paint: just render all
of the children in the onBeforeShow callback.

上面的例子能很好的工作，但它会导致三个独立的绘制:
每个布局的一个视图就会显示一次（译注：重绘），
Marionette 为多层嵌套视图提供了一个简单的机制，在一个单一的绘制中，只在 `onBeforeShow` 回调中渲染所有的子元素。

```
    var ParentLayout = Marionette.LayoutView.extend({
      onBeforeShow: function() {
        this.showChildView('header', new HeaderView());
        this.showChildView('footer', new FooterView());
      }
    });

    myRegion.show(new ParentLayout());
```
In this example, the doubly-nested view structure will be rendered in a single paint.

在这一个例子中，两层嵌套视图结果只在一次重绘中渲染。

This system is recursive, so it works for any deeply nested structure. The child views
you show can render their own child views within their onBeforeShow callbacks!

该系统是一个递归函数实现，所以他能用于任意深度的嵌套结构。你可以将子元素视图全部在 `onBeforeShow` 回调中渲染。

## Use of the attach event {使用附加事件}

Often times you need to know when your views in the view tree have been attached to the `document`,
like when using certain jQuery plugins. The `attach` event, and associated `onAttach` callback, are perfect for this
use case. Start with a Region that's a child of the `document` and show any LayoutView in it: every view in the tree
(including the parent LayoutView) will have the `attach` event triggered on it when they have been
attached to the `document`.

通常的，你需要知道你的视图树上的视图已经并添加到 `document` 对象上了。
就好像你使用某一个 jQuery 插件一样。`attach` 事件和关联的 `onAttach` 回调，可以为这个需求提供完善的支持。
在一个是 `document` 子元素的 region 上显示任意 LayoutView 时，视图树上的每一个视图（包含父LayoutView）添加到 `document` 上时，将会有一个 `attach` 事件被触发。

Note that inefficient tree rendering will cause the `attach` event to be fired multiple times.
This situation can occur if you render the children views after the parent has been rendered, such as using
`onShow` to render children. As a rule of thumb, most of the time you'll want to render any nested views in
the `onBeforeShow` callback.

注：低效树（译注：大概是嵌套视图）在渲染时可能会导致多次触发 `attach` 事件。
这种情况发生于你选择先渲染父视图再渲染子视图的情况。
诸如使用 `onShow` 渲染子视图。一般来说，绝大多数时间你都应该在 `onBeforeShow` 回调中渲染嵌套视图。

## Destroying A LayoutView {销毁LayoutView}

When you are finished with a layoutView, you can call the
`destroy` method on it. This will ensure that all of the region managers
within the layoutView are destroyed correctly, which in turn
ensures all of the views shown within the regions are destroyed correctly.

当你不再需要一个 layoutView 时，你可以调用其上的 `destroy` 方法。这将确保 layoutView 中所有的 region managers 都能够有保障地销毁。
反过来说，确保 regions 中的所有显示的视图有保障地被销毁。

If you are showing a layoutView within a parent region manager, replacing
the layoutView with another view or another layoutView will destroy the current
one, the same it will destroy a view.

如果你想在一个父 region manager 中显示一个 layoutView，用另一个视图或者 layoutView 替换掉该 layoutView，
当前的 layoutView 将会被销毁，同样也会销毁一个视图。（译注：有点绕，不明白）

All of this ensures that layoutViews and the views that they contain are cleaned up correctly.

这将确保 layoutView 包含的 layoutViews 和 views 都被正常的清理干净。

When calling destroy on a layoutView, the layoutView will be returned. This can be useful for chaining.

当调用 layoutView 的 destroy 方法时，layoutView 将会被返回，以利于链式调用。


## Custom Region Class {自定义 Region 类}

If you have the need to replace the Region with a region class of
your own implementation, you can specify an alternate class to use
with the regionClass property of the LayoutView.

如果你想用你自己实现的 region 代替 Region 类，你可以通过 LayoutView 的 `regionClass` 属性指定一个代替类。

```
    var MyLayoutView = Marionette.LayoutView.extend({
      regionClass: SomeCustomRegion
    });
```

You can also specify custom Region classes for each region:

也可以为每一个 region 指定一个自定义的 Region 类

```
    var AppLayoutView = Marionette.LayoutView.extend({
      template: "#layout-view-template",

      regionClass: SomeDefaultCustomRegion,

      regions: {
        menu: {
          selector: "#menu",
          regionClass: CustomRegionClassReference
        },
        content: {
          selector: "#content",
          regionClass: CustomRegionClass2Reference
        }
      }
    });
```

## Adding And Removing Regions {添加和删除Region}

Regions can be added and removed as needed, in a
LayoutView instance. Use the following methods:

在 LayoutView 中 Regions 可以使用如下方法在需要的时候添加或删除：

`addRegion`

`addRegions`

`removeRegion`

`addRegion:`

```
    var layoutView = new MyLayoutView();

    // ...

    layoutView.addRegion("foo", "#foo");
    layoutView.getRegion('foo').show(new someView());
```

addRegions:

```
    var layoutView = new MyLayoutView();

    // ...

    // Object literal
    layoutView.addRegions({
      foo: "#foo",
      bar: "#bar"
    });

    // Or, function that returns an object literal
    // 或者一个返回对象字面的函数
    layoutView.addRegions(function() {
      return {
        baz: "#baz",
        quux: "#quux"
      };
    });
```
removeRegions:

```
    var layoutView = new MyLayoutView();

    // ...

    layoutView.removeRegion("foo");
```
Any region can be removed, whether it was defined
in the regions attribute of the region definition,
or added later.

任何 region 都是可以被删除的，无论他是在定义 region 时通过 regions 的属性定义，
还是后面添加上的。

For more information on using these methods, see
the `regionManager` documentation.

更多使用这些方法的信息，regionManager 文档（后续再翻译）

## Region Naming {Region 命名}

A LayoutViews' Regions are attached directly to the LayoutView instance with the name of the region
as the key and the region itself as the value. Because of this, you need to be careful
to avoid conflicts with existing properties on the LayoutView when you name your Region.

layoutView 的 regions 是使用 region 的 key 直接附加到 layoutView 的实例上的，region 自身是其值(译注：key:value)。
因此，在命名一个 region 的时候，要注意与 layoutView 上已经存在的属性名相冲突。

The prototype chain of LayoutViews is:

LayoutView 属性的链为：

Backbone.View > Marionette.View > Marionette.ItemView > Marionette.LayoutView

Consequently, every property on each of those Classes must be avoided as Region names.
The most common issue people run into is trying to name their Region "attributes".
Be aware that you are not able to do this.

因此，给 region 命名的时候，需要避免以上的所有类的属性名。
最常见的问题就是试图用 "attributes" 做为 region 的名字。
请注意，你不能这样做。

The following is an abbreviated list of other names that can't be used as Region names.
For a more complete list refer to the API documentation for each Class on the prototype chain:

下来有一个简短的列表，这些不能用来做为 region 的名字。
更完整的清单请查阅API文档的每个类原型链

attributes
constructor
regionClass
render
destroy
addRegion
addRegions
removeRegion

Note: this is a known issue that is flagged for being fixed in v2
注：这是一个已知的问题，在 v2 中标记。

