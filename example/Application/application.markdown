The Application is a container for the rest of your code. It is recommended
that every Marionette app have at least one instance of Application.

`Application` 是一个代码容器，建议每一个 `Marionette` 应该至少有一个由 `Application` 实例。

By creating an Application you get three important things:
创建一个 `Application` 有三个重要点需要注意：

A start method to kick off your application.
This allows you an opportunity to do things that may need to occur before, say, you
begin routing. An example would be making an AJAX call to request data that your app
needs before starting.

有一个 `start` 方法用于启动你的应用。
该方法允许你在应用启动前做一些事情，比如你的应用在开始之前，需要执行一个 `AJAX` 请求以获得数据。

A namespace to keep things off of the window.
If you are not using a module loader like ES6 modules, CommonJS, or AMD, then
you can use the Application to store your Javascript objects. And if you are
using one of those module systems, then you can still attach things to the
application to aid in debugging.

需要一个命名空间，用于持有对象。
如果你没有使用 `ES6 Modules`， `CommonJS`, `AMD` 这类的模块加载器，
你可以使用 `Application` 保存你的 JavaScript 对象。
如果你使用了模块加载系统，你依然可以将数据绑定在应用上，以协同调式。

Integration with the Marionette Inspector. The Marionette Inspector is a fantastic tool
that makes it easy to understand and debug your application. Using the Application Class
will automatically hook up your application to that extension.

集成了 `Marionette Inspector`。它是一个非常有用的工具，可以使用你的应用更易理解，调式也更方便。
使用 `Application` 类将自动将你的应用关联到其他扩展（译注：大约是指 Backbone 的方法和属性）。

Note that the Application is undergoing many changes to become more lightweight. While it
still includes many more features beyond what has been listed here, such as a Radio Channel and Regions,
these features are now deprecated. Refer to the relevant sections below to learn what to use
instead of these deprecated features.

注：`Application` 经历多次版本迭代之后，变得越来越轻量。
它仍然包含了许多特性，比如 `Radio Channel` `Regions`。
这些特性目前不再赞成使用，参考下面相关章节学习替代这些特性的方法。

## Getting Started {入门指南}

A common pattern in Backbone apps is the following:

在 Backbone 中，app 通常为如下模式

```
    var app = {};
```

Two notable examples of this pattern are [DocumentCloud's source](https://github.com/documentcloud/documentcloud/blob/master/public/javascripts/application.js#L3)
and [Backbone Boilerplate](https://github.com/backbone-boilerplate/backbone-boilerplate/blob/master/app/app.js#L1-L6).
DocumentCloud is notable because it is the codebase that Backbone was abstracted from. If such a thing as a quintessential Backbone application
existed, then that app would certainly be a candidate. Backbone Boilerplate is notable as perhaps the most popular library
for bootstrapping a Backbone application. Do note that in the Backbone Boilerplate code the exported object is implicit.

该模式有两个著名的例子 [DocumentCloud 上的资源](https://github.com/documentcloud/documentcloud/blob/master/public/javascripts/application.js#L3)
和 [Backbone 样板](https://github.com/backbone-boilerplate/backbone-boilerplate/blob/master/app/app.js#L1-L6)

DocumentCloud（译注：这货好像是一个ruby的库） 出名是因为 Backbone 就是基于他的设计而来。
【译注：后面好难翻译，和代码无关，不翻了】

The pattern of creating a Javascript object is so popular because it provides you with a location to
put the pieces of your application. For instance, attaching a Router to this object is common practice.

这种用一个 Javascript 对象创建一个app是非常流行的，原因在于他给你的应用提供了一个存储位置。
比如，给这个对象添加一个 `Router` 是常见的做法。


Using a raw Javascript object is great, but Marionette provides a light wrapper for a plain Javascript object, which is the
Application. One benefit to using the Application is that it comes with a start method. This can be used to accomplish
tasks before the rest of your application begins. Let's take a quick look at an example:

使用一个未加工的 Javascript 对象是非常好的，但是 Marionette 提供了一个 `Application` 为 Javascript 对象提供了一个包容器。
使用 `Application` 的好处之一在于它带有 `start` 方法。该方法可以在 application 开始之前实现一些任务。下面快速看一个例子：

```
    // Create our Application
    // 创建应用
    var app = new Mn.Application();

    // Start history when our application is ready
    // 当应用准备好时，开始 history 监控
    app.on('start', function() {
      Backbone.history.start();
    });

    // Load some initial data, and then start our application
    // 加载一些初始化数据，之后启动应用
    loadInitialData().then(app.start);
```

In the simple example above, we could have just as easily started history after our initial data had loaded.
This pattern becomes more useful as the startup phase of your application becomes more complex.

在上面这个简单的例子，我们可以很容易的在数据初始化之前调用 history 。
当你的应用在启动阶段需要做一些复杂的准备的时候，这种创建应用的方式将会变得非常有用。


## Initialize {初始化}

Like other objects in Backbone and Marionette, Applications have an initialize method.
It is called immediately after the Application has been instantiated, and is invoked with
the same arguments that the constructor received.

与 Backbone 和 Marionette 其他对象一样， Application 对象也有一个 `initialize` 方法。
在 Application 实例化的时候立即调用，接收的参数与构造函数的一样（译注：也就是传给构造函数的参数会传给该方法）。

```
    var app = Marionette.Application.extend({
      initialize: function(options) {
        console.log('My container:', options.container);
      }
    });

    // Although applications will not do anything
    // with a `container` option out-of-the-box, you
    // could build an Application Class that does use
    // such an option.
    var app = new app({container: '#app'});
```

## Application Events {Application 事件}

The Application object raises a few events during its lifecycle, using the Marionette.triggerMethod function.
These events can be used to do additional processing of your application.
For example, you may want to pre-process some data just before initialization happens.
Or you may want to wait until your entire application is initialized to start Backbone.history.

在 Application 的生命周期内，Application 对象会使用 `Marionette.triggerMethod` 函数提升一些事件。
这些事件可以为你的应用附加一些处理程序。
例如：你需要在初始化之前预处理一些数据，或者你可能想要在你的应用初始化之后开始 `Backbone.history`

The events that are currently triggered, are:

这些事件在以下情况被触发：


"before:start" / onBeforeStart: fired just before the Application starts and before the initializers are executed.

在 Application 启动、初始化之前

"start" / onStart: fires after the Application has started and after the initializers have been executed.

Application 启动、初始化之后

```
    MyApp.on("before:start", function(options){
      options.moreData = "Yo dawg, I heard you like options so I put some options in your options!";
    });

    MyApp.on("start", function(options){
      if (Backbone.history){
        Backbone.history.start();
      }
    });
```

The options parameter is passed through the start method of the application object (see below).

该配置参数传给 Application 对象的 `start` 方法（见下文）


## Starting An Application {启动一个 Application}

Once you have your application configured, you can kick everything off by calling: MyApp.start(options).

一旦你的应用配置好，你可以通过调用 `MyApp.start(options)` 踢掉一切。

This function takes a single optional parameter. This parameter will be passed
to each of your initializer functions, as well as the initialize events. This
allows you to provide extra configuration for various parts of your app throughout the
initialization sequence.

该函数接受一个可选参数，该参数将会传递给每一个初始化函数和初始化事件。
这允许在你的应用初始化序列中不同的部份提供额外的配置。

```
    var options = {
        something: "some value",
        another: "#some-selector"
    };

    MyApp.start(options);
```

## Application Regions

Warning: deprecated
This feature is deprecated. Instead of using the Application as the root of your view tree,
you should use a Layout View. To scope your Layout View to the entire document,
you could set its el to 'body'. This might look something like the following:

警告：已弃用。

该特性已经弃用。你应该用 `Layout View`，替代在视图树的根上创建Application。如果要将整个 `document`
做为你的 `Layout View`，你可以设置它的 `el` 属性为 `body`。如下所示：

```
    var RootView = Marionette.LayoutView.extend({
      el: 'body'
    });
```

Later, you can attach an instance of the RootView to your Application instance.
稍后，你可以附加一个 RootView 的实例到你的 Application 的实例上。

```
    app.rootView = new RootView();
```

Application instances have an API that allow you to manage Regions.
These Regions are typically the means through which your views become attached to the document.

Application 的实例有一个允许你管理 Regions 的接口。
这些 Regions 的代表性的功能就是将你的视图添加到 `document`。

You can create Regions through the `addRegions` method by passing in an object
literal or a function that returns an object literal.

你可以通过传递一个对象字面量或者一个返回一个对象字面量的函数，给 `addRegions` 方法，创建 Regions

There are three syntax forms for adding a region to an application object.

这里有三种语法用于添加一个 region 到 application 对象上。

* ## jQuery Selector { jQuery 选择器}
 > The first is to specify a jQuery selector as the value of the region
   definition. This will create an instance of a Marionette.Region directly,
   and assign it to the selector:

 > 第一种方案是在 region 定义时指定一个 jQuery 选择器。这将会立即创建一个 `Marionette.Region`
   实例,并分配给该选择器

 ```
     MyApp.addRegions({
       someRegion: "#some-div",
       anotherRegion: "#another-div"
     });
 ```

* ## Custom Region Class {自定义 Region 类}

 > The second is to specify a custom region class, where the region class has
   already specified a selector:

 > 第二种方法：通过指定一个自定义的 region 类，该类需要已经指定一个选择器。

 ```
    var MyCustomRegion = Marionette.Region.extend({
      el: "#foo"
    });

    MyApp.addRegions(function() {
      return {
        someRegion: MyCustomRegion
      };
    });
 ```

* Custom Region Class And Selector {自定义 Region 类和选择器}

 > The third method is to specify a custom region class, and a jQuery selector
   for this region instance, using an object literal:

 > 第三种方式：为一个 region 实例用一个对象字面量的方式，指定自定义的 region 类和一个 jQuery 选择器

 ```
   var MyCustomRegion = Marionette.Region.extend({});

   MyApp.addRegions({

     someRegion: {
       selector: "#foo",
       regionClass: MyCustomRegion
     },

     anotherRegion: {
       selector: "#bar",
       regionClass: MyCustomRegion
     }

   });
 ```

## Region Options {Region 的配置}

You can also specify regions per Application instance.

你也可以为每个 Application 实例指定 Region

```
    new Marionette.Application({
      regions: {
        fooRegion: '#foo-region'
      }
    });
```

## Overriding the default RegionManager {覆写默认的 RegionManager}

If you need the RegionManager's class chosen dynamically, specify getRegionManager:

如果你需要动态选择 RegionManager 类，可以指定 `getRegionManager`

```
    Marionette.Application.extend({
    // ...

    getRegionManager: function() {
        // custom logic
        return new MyRegionManager();
    }
```

This can be useful if you want to attach Application's regions to your own instance of `RegionManager`.

如果你想连接你自己的的 `RegionManager` 实例到你的应用上的 region 的时候，该方法会变得很有用。（译注：我不知道这种场景是什么样的情况）


## Get Region By Name {使用名字获取 Region }

A region can be retrieved by name, using the getRegion method:
使用 `getRegion` 方法，可以通过名字找到一个 region。

```
    var app = new Marionette.Application();
    app.addRegions({ r1: "#region1" });

    var myRegion = app.getRegion('r1');
```
Regions are also attached directly to the Application instance, but this is not recommended usage.

Region 也可以直接立即附加到 Application 的实例，【但是这种方法不推荐使用】。

## Removing Regions {移除 Regions }

Regions can also be removed with the removeRegion method, passing in
the name of the region to remove as a string value:

Regions 可以通过 `removeRegion` 方法移除，传入 region 的 name (是一个String类型的值)即可。

```
    MyApp.removeRegion('someRegion');
```
Removing a region will properly empty it before removing it from the application object.

移除一个 region 之前，将会从 application 对象上清空它。

For more information on regions, see the region documentation Also, the API that Applications use to
manage regions comes from the RegionManager Class, which is documented over here.

更多关于 regions 的信息，请看 region（译注：查看regions.markdown） 文档。 Application 使用 RegionManager 类中的方法管理 regions 的文档都在这里。


## Application.mergeOptions

Merge keys from the options object directly onto the Application instance.

Application 初始化的时候，将立即合并配置参数对象上的键

```
    var MyApp = Marionette.Application.extend({
      initialize: function(options) {
        this.mergeOptions(options, ['myOption']);

        console.log('The option is:', this.myOption);
      }
    })
```

More information at mergeOptions
更多`mergeOptions`的信息，[参见](http://marionettejs.com/docs/v2.4.2/marionette.functions.html#marionettemergeoptions)


## Application.getOption

Retrieve an object's attribute either directly from the object, or from the object's this.options, with this.options taking precedence.

直接从 Application 对象上取回对象的属性，或者从对象的 `this.options`取回，这取决于 `this.options` 上定义的优先级（译注：怎么定义，俺还没看到）。

More information getOption

更多 `getOption` 的信息，[参见](http://marionettejs.com/docs/v2.4.2/marionette.functions.html#marionettegetoption)


## Adding Initializers {添加初始化设置}

Warning: deprecated

This feature is deprecated, and is scheduled to be removed in version 3 of Marionette. Instead
of Initializers, you should use events to manage start-up logic. The start event is an ideal
substitute for Initializers.

警告：已弃用。
该特性已经弃用。计划在 Marionette 3 版中删除该特性。你可以使用事件来管理逻辑替代 `Initializers`。
`start` 事件是一个理想的替代方案。

If you were relying on the deferred nature of Initializers in your app, you should instead
use Promises. This might look something like the following:

如果在你的 app 中需要依赖 `initializers` 的 `deferred` 特性。你可以使用 `Promises` 替代。
类似于下面的例子：

```
    doAsyncThings().then(app.start);
```

Your application needs to do useful things, like displaying content in your
regions, starting up your routers, and more. To accomplish these tasks and
ensure that your Application is fully configured, you can add initializer
callbacks to the application.

你的应用需要做一些必须的事情，比如在 regions 中显示内容、启动你的路由监控等，完成这些任务或者确认你的应用已经完整配置好了。
你可以为该就用添加 `initializer` 回调

```
    MyApp.addInitializer(function(options){
      // do useful stuff here
      var myView = new MyView({
        model: options.someModel
      });
      MyApp.mainRegion.show(myView);
    });

    MyApp.addInitializer(function(options){
      new MyAppRouter();
      Backbone.history.start();
    });
```

These callbacks will be executed when you start your application,
and are bound to the application object as the context for
the callback. In other words, this is the MyApp object inside
of the initializer function.

在你启动你的应用的时候，这些回调将会被执行，并且会将这些回调作用域绑定到应用对象的上下文。
换言之，`initializer` 函数会成为 `MyApp` 对象的内部成员。

The options argument is passed from the start method (see below).

该配置参数通过 `start` 方法传入。

Initializer callbacks are guaranteed to run, no matter when you
add them to the app object. If you add them before the app is
started, they will run when the start method is called. If you
add them after the app is started, they will run immediately.

`Initializer` 回调函数保证会被执行，无论你在何时将他们添加到 app 对象上。
如果在 app 启动之前添加，则会在 `start` 方法调用的时候调用他们。
如果在 app 启动之后添加，则会立即执行。


## The Application Channel {Application 通道}

Warning: deprecated

This feature is deprecated, and is scheduled to be removed in the next major release of Marionette.
Instead of accessing Channels through the Application, you should use the Wreqr (or Radio) API.
By default the application's channel is named 'global'. To access this channel, you can use
the following code, depending on whether you're using Wreqr or Radio:

警告：已弃用。

该特性已被弃用，计划在下一个主要发布版中删除。
通过 Application 访问通道，你可以使用 `Wreqr` 或 `Radio` API替代。
默认情况下，通道用 'global' 命名。你可以依赖 `Wreqr` 或 `Radio` 访问通道。如下代码所示：

```
    // Wreqr
    var globalCh = Backbone.Wreqr.radio.channel('global');

    // Radio
    var globalCh = Backbone.Radio.channel('global');
```

Marionette Applications come with a messaging system to facilitate communications within your app.
Marionette 应用程序通过[消息传送系统](http://en.wikipedia.org/wiki/Message_passing)使你的应用更容易的通信。

The messaging system on the Application is the radio channel from Backbone.Wreqr, which is actually comprised of three distinct systems.
Application 的消息传送系统是来自于 Backbone.Wreqr 的 `radio channel`，实际上由三个不同的系统组成。

Marionette Applications default to the 'global' channel, but the channel can be configured.
Marionette 应用默认为 'global' 通道，但是该通道也是可以配置的。

```
    var MyApp = new Marionette.Application({ channelName: 'appChannel' });
```

This section will give a brief overview of the systems; for a more in-depth look you are encouraged to read
the Backbone.Wreqr documentation.

该部份只是给出一些该系统的摘要，如果想更深入的了解，鼓励你阅读 [Backbone.Wreqr 文档](https://github.com/marionettejs/backbone.wreqr)

## Event Aggregator {事件聚合器}

The Event Aggregator is available through the vent property. vent is convenient for passively sharing information between
pieces of your application as events occur.

事件聚合器可以通过 `vent` 属性来使用。`vent` 是在应用部件和事件之前传输被动信息的快捷方式。

```
    var MyApp = new Marionette.Application();

    // Alert the user on the 'minutePassed' event
    MyApp.vent.on("minutePassed", function(someData){
      alert("Received", someData);
    });

    // This will emit an event with the value of window.someData every minute
    window.setInterval(function() {
      MyApp.vent.trigger("minutePassed", window.someData);
    }, 1000 * 60);
```

## Request Response {请求、响应}

Request Response is a means for any component to request information from another component without being tightly coupled.
An instance of Request Response is available on the Application as the reqres property.

请求响应是一个意味着对于任何组件从另一个组件请求信息不需要紧密耦合。
可以通过 Application 的 `reqres` 属性来使用一个响应请求实例。

```
    var MyApp = new Marionette.Application();

    // Set up a handler to return a todoList based on type
    // 设置一个句柄用于返回 todoList 基于的类型
    MyApp.reqres.setHandler("todoList", function(type){
      return this.todoLists[type];
    });

    // Make the request to get the grocery list
    // 使用 request 获得购物清单
    var groceryList = MyApp.reqres.request("todoList", "groceries");

    // The request method can also be accessed directly from the application object
    // request 方法也能通过 application 对象直接访问
    var groceryList = MyApp.request("todoList", "groceries");
```

## Commands {命令集}

Commands are used to make any component tell another component to perform an action without a direct reference to it.
A Commands instance is available under the `commands` property of the Application.

Commands 是用于任意组件不通过直接引用，告诉其他组件执行一个行为。（译注：大约就是说可以不用指定到某个部份，再调用某个部份的方法。
这样说来，commands应该是一个app的全局对象，app下所有的组成部份都可以直接访问）。
可以通过 Application 下的 `commands` 属性使用一个 Commands 实例。

Note that the callback of a command is not meant to return a value.
注：回调的 command 不是为了一返回一个值

```
    var MyApp = new Marionette.Application();

    MyApp.model = new Backbone.Model();

    // Set up the handler to call fetch on the model
    MyApp.commands.setHandler("fetchData", function(reset){
      MyApp.model.fetch({reset: reset});
    });

    // Order that the data be fetched
    MyApp.commands.execute("fetchData", true);

    // The execute function is also available directly from the application
    MyApp.execute("fetchData", true);
```

## Accessing the Application Channel {访问应用程序通道}

To access this application channel from other objects within your app you are encouraged to get a handle of the systems
through the Wreqr API instead of the Application instance itself.

从另一个对象上访问 application 通道，鼓励通过 `Wreqr` 接口取得一个 handle 替代直接使用 Application 实例本身。

```
    // Assuming that we're in some class within your app,
    // and that we are using the default 'global' channel
    // it is preferable to access the channel like this:
    var globalCh = Backbone.Wreqr.radio.channel('global');
    globalCh.vent;

    // This is discouraged because it assumes the name of your application
    window.app.vent;
```


