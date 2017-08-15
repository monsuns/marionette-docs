## Marionette.ItemView

An ItemView is a view that represents a single item. That item may be a
Backbone.Model or may be a Backbone.Collection. Whichever it is though, it
will be treated as a single item.

一个 ItemView 代表着单一的项目视图。项目可能是一个 Backbone.Model，或者是一个 Backbone.Collection。
无论是谁，他都当作一个单一的项目处理。

ItemView extends directly from Marionette.View. Please see
the Marionette.View documentation
for more information on available features and functionality.

ItemView 直接从 Marionette.View 上扩展而来。详细信息参看 `Marionette.view` 文档，时面有许多可用的特性和功能说明。

Additionally, interactions with Marionette.Region
will provide features such as onShow callbacks, etc. Please see
the Region documentation for more information.

此外，与 Marionette.Region 交互提花了一些特性，比如 `onShow` 回调，以及一些其他的特性。
参看 Region 文档获取更多详细信息。

## ItemView render {ItemView 渲染}

Unlike Backbone Views, all Marionette views come with a powerful render method.
In fact, the primary differences between the views are the differences in their
render methods. It goes without saying that it is unwise to override the render
method of any Marionette view. Instead, you should use the onBeforeRender and onRender callbacks
to layer in additional functionality to the rendering of your view.

与 Backbone 的 Views 有些不同，所有的 Marionette views 都有一个强大的 `render` 方法。
事实上，二者的主要不同就在于该方法。因些，如果覆写任意 Marionette view 的 `render` 方法都是不明智的。
相反，你应该使用 [`onBeforeRender` 和 `onRender`](http://marionettejs.com/docs/v2.4.2/marionette.itemview.html#events-and-callback-methods) 回调在附加功能层渲染你的 view.

The `ItemView` defers to the `Marionette.Renderer` object to do the actual
rendering of the template.

`ItemView` 推迟了 `Marionette.Renderer` 对象渲染模板的时间。

The item view instance is passed as the third argument to the
`Renderer` object's `render` method, which is useful in custom
`Renderer` implementations.

`ItemView` 实例传入三个参数给 Render 对象的 `render` 方法。
在自定义实现 `Renderer` 很有用。

You should provide a `template` attribute on the item view, which
will be either a jQuery selector:

你可以在 `ItemView` 上提供一个 `template` 属性，也可以是一个 jQuery 选择器。

```
    var MyView = Marionette.ItemView.extend({
      template: "#some-template"
    });

    new MyView().render();
```

.. or a function taking a single argument: the object returned by ItemView.serializeData:

或者一个带有一个参数的函数，该参数是 [`ItemView.serializeData`](http://marionettejs.com/docs/v2.4.2/marionette.itemview.html#itemview-serializedata) 返回的对象。

```
    var my_template_html = '<div><%= args.name %></div>'
    var MyView = Marionette.ItemView.extend({
      template : function(serialized_model) {
        var name = serialized_model.name;
        return _.template(my_template_html)({
            name : name,
            some_custom_attribute : some_custom_key
        });
      }
    });

    new MyView().render();
```

Note that using a template function allows passing custom arguments into the .template function and allows for more control over how the .template function is called.

注：使用一个 template 函数允许传入自定义参数给 `_.template` 函数，允许在调用 `_.template` 函数时有更多的控制方式。

For more information on the `_.template` function see the Underscore docs.

更多信息参考 Underscore 文档中的 `_.template` 函数。

## Rendering A Collection In An ItemView {在 ItemView 中渲染一个 Collection}

While the most common way to render a Backbone.Collection is to use
a CollectionView or CompositeView, if you just need to render a
simple list that does not need a lot of interaction, it does not
always make sense to use these. A Backbone.Collection can be
rendered with a simple ItemView, using the templates to iterate
over an `items` array.

渲染一个 Backbone.Collection 最常用的方式是使用 `CollectionView` 或 `CompositeView`。
如果你只仅需要渲染一个简单的列表，不需要大量的交互，那么使用这些方式渲染就不合理。
Backbone.Collection 可以通过一个简单的 `ItemView` 来渲染，使用模板迭代一个 `items` 数组。

```
    <script id="some-template" type="text/html">
      <ul>
        <% _.each(items, function(item){ %>
        <li> <%= item.someAttribute %> </li>
        <% }); %>
      </ul>
    </script>
```

The important thing to note here, is the use of items as the
variable to iterate in the `_.each` call. This will always be the
name of the variable that contains your collection's items.

重要提示：使用 items 作为变量在 `_.each` 中迭代，需要保证这些变量都包含在 collections 的 items 中。
（译注：就是要保证这些属性值是item所有的。比如在`_.each`中调用 `item.someAttribute`，你总得保证 item 有这个属性吧，愚蠢的重要提示）

Then, from JavaScript, you can define and use an ItemView with this template, like this:

随后，在 JavaScript 中，你可以定义并使用一个带有该模板的 `ItemView`：

```
    var MyItemsView = Marionette.ItemView.extend({
      template: "#some-template"
    });

    var view = new MyItemsView({
      collection: someCollection
    });

    // show the view via a region or calling the .render method directly
    // 通过 region 或者直接调用 render 方法显示 view
```

Rendering this view will convert the `someCollection` collection in to
the `items` array for your template to use.

渲染该 view 需要转换 `someCollection` 该 `items` 数据所在的 collection，给 template 使用

For more information on when you would want to do this, and what options
you have for retrieving an individual item that was clicked or
otherwise interacted with, see the blog post on
[Getting The Model For A Clicked Element](http://lostechies.com/derickbailey/2011/10/11/backbone-js-getting-the-model-for-a-clicked-element/).


## Template-less ItemView

An `ItemView` can be attached to existing elements as well.
The primary benefit of this is to attach behavior and events to static content that has been rendered by your server (typically for SEO purposes).
To set up a template-less ItemView, your template attribute must be false.

`ItemView` 也可以附加到已经存在的元素上。
这样做的主要好处是将形为和事件转为静态化内容，在服务器上渲染（SEO友好目的为典型代表）。
建立一个 `template-less ItemView`，你的 `template` 属性值必须为 false

```
    <div id="my-element">
      <p>Hello World</p>
      <button class="my-button">Click Me</button>
    </div>
```

```
    var MyView = Marionette.ItemView.extend({
      el: '#my-element',

      template: false,

      ui: {
        paragraph: 'p',
        button: '.my-button'
      },

      events: {
        'click @ui.button': 'clickedButton'
      },

      clickedButton: function() {
        console.log('I clicked the button!');
      }
    });

    var view = new MyView();
    view.render();

    view.ui.paragraph.text();        // returns 'Hello World'
    view.ui.button.trigger('click'); // logs 'I clicked the button!'
```

Another use case is when you want to attach a Marionette.ItemView to a SVG graphic or canvas element,
to provide a uniform view layer interface to non-standard DOM nodes.
By not having a template this allows you to also use a view on pre-rendered DOM nodes,
such as complex graphic elements.

其他使用情况，当你想要附加一个 `Marionette.ItemView` 到 SVG 图形或 `canvas` 元素上的时候。
提供一个统一的视图层接口给非标准的节点。
但是现在还没有一个模板允许你可使用一个 view 在一个预渲染的 DOM 节点（比如一个复杂的图形元素）上。

## Events and Callback Methods {事件和回调方法}

There are several events and callback methods that are called for an ItemView.
These events and methods are triggered with the Marionette.triggerMethod function,
which triggers the event and a corresponding "on{EventName}" method.

这里有几个事件和回调方法可以在一个 ItemView 中调用。
这些事件和方法通过 `Marionette.triggerMethod` 函数触发。
这些事件通过统一的"on{EventName}"方法注册。

## "before:render" / onBeforeRender event {在Render之前调用}

Triggered before an ItemView is rendered.

在一个 `ItemView` 渲染之前触发。

```
    Marionette.ItemView.extend({
      onBeforeRender: function(){
        // set up final bits just before rendering the view's `el`
      }
    });
```

## "render" / onRender event {Render事件}

Triggered after the view has been rendered.
You can implement this in your view to provide custom code for
dealing with the view's `el` after it has been rendered.

在 view 渲染后触发。
你可以在你的视图中实现该方法，提供一个自定义代码的函数，处理 view 的 el 之后再渲染。

```
    Marionette.ItemView.extend({
      onRender: function(){
        // manipulate the `el` here. it's already
        // been rendered, and is full of the view's
        // HTML, ready to go.
      }
    });
```

## "before:destroy" / onBeforeDestroy event {销毁之前事件}

Triggered just prior to destroying the view, when the view's destroy() method has been called.

当视图的 `destroy` 方法被调用时，在 view 销毁之前被触发。

```
    Marionette.ItemView.extend({
      onBeforeDestroy: function(){
        // manipulate the `el` here. it's already
        // been rendered, and is full of the view's
        // HTML, ready to go.
      }
    });
```

## "destroy" / onDestroy event

Triggered just after the view has been destroyed.

view 被销毁之后触发

````
    Marionette.ItemView.extend({
      onDestroy: function(){
        // custom destroying and cleanup goes here
      }
    });
```

## ItemView serializeData {ItemView 序列化数据}

Item views will serialize a model or collection, by default, by
calling .toJSON on either the model or collection. If both a model
and collection are attached to an item view, the model will be used
as the data source. The results of the data serialization will be passed to the template
that is rendered.

默认情况下，ItemView 将会通过调用 `.toJSON` 序列化一个 model 或 collection。
如果 model 和 collection 同时附加到一个 ItemView 上，该 model 将会作为数据来源。
序列化结果的数据将会传给 template 用于渲染。

If the serialization is a model, the results are passed in directly:

如果序列化一个 model, 结果将直接传递(译注：注意 foo):

```
    var myModel = new MyModel({foo: "bar"});

    new MyItemView({
      template: "#myItemTemplate",
      model: myModel
    });

    MyItemView.render();
```

```
    <script id="myItemTemplate" type="template">
      Foo is: <%= foo %>
    </script>
```

If the serialization is a collection, the results are passed in as an items array:

如果序列化的是一个 collection，结果将会通过一个 items 数组传入(译注：模板中使用了_.each 函数)。

```
    var myCollection = new MyCollection([{foo: "bar"}, {foo: "baz"}]);

    new MyItemView({
      template: "#myCollectionTemplate",
      collection: myCollection
    });
    MyItemView.render();
```

```
    <script id="myCollectionTemplate" type="template">
      <% _.each(items, function(item){ %>
        Foo is: <%= foo %>
      <% }); %>
    </script>
```

If you need custom serialization for your data, you can provide a
serializeData method on your view. It must return a valid JSON
object, as if you had called .toJSON on a model or collection.

如果你需要一个为你的数据自定义一个序列化，你可以在你的 view 上提供一个 `serializeData` 方法。
它必须返回一个有效的 JSON 对象，就像你在一个 model 或 collection 上调用 `.toJSON` 方法一样。

```
    Marionette.ItemView.extend({
      serializeData: function(){
        return {
          "some attribute": "some value"
        }
      }
    });
```

## Organizing UI Elements {组织UI元素}

As documented in Marionette.View, you can specify a ui hash in your view that
maps UI elements by their jQuery selectors. This is especially useful if you access the
same UI element more than once in your view's code. Instead of
duplicating the selector, you can simply reference it by
this.ui.elementName:

你可以在你的 view 上指定一个 ui HashMap，通过 jQuery 选择器规划一个元素。
在你需要在你的 view 代码中不止一次地访问同一个元素，这将变得非常有效。
不用重复的使用选择器，你可以通过一个通过 `this.ui.elementName` 简单的引用它

You can also use the ui hash values from within events and trigger keys using the "@ui.elementName": syntax

你也可以从事件和触发器的键上使用"@ui.elementName"语法调用 ui hash 的值。

```
    Marionette.ItemView.extend({
      tagName: "tr",

      ui: {
        checkbox: "input[type=checkbox]"
      },

      onRender: function() {
        if (this.model.get('selected')) {
          this.ui.checkbox.addClass('checked');
        }
      }
    });
```

## modelEvents and collectionEvents {没啥好翻的，就是两个方法}

ItemViews can bind directly to model events and collection events
in a declarative manner:

ItemViews 可以通过声明的方式直接绑定到 model 和 collection 事件。

```
    Marionette.ItemView.extend({
      modelEvents: {
        "change": "modelChanged"
      },

      collectionEvents: {
        "add": "modelAdded"
      }
    });
```

For more information, see the Marionette.View documentation.

更多信息，参见[Marionette.View documentation](http://marionettejs.com/docs/v2.4.2/marionette.view.html#viewmodelevents-and-viewcollectionevents).
