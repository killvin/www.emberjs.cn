英文原文: [http://emberjs.com/guides/routing/defining-your-routes/](http://emberjs.com/guides/routing/defining-your-routes/)

## 定义路由（Defining Your Route）

When your application starts, the router is responsible for displaying
templates, loading data, and otherwise setting up application state.
It does so by matching the current URL to the _routes_ that you've
defined.

当启动你的应用时，路由器会负责展示模板，载入数据，以及设置应用状态等任务。
这些都是通过将当前的URL与你定义的_路由_进行匹配来实现的。

```js
App.Router.map(function() {
  this.route("about", { path: "/about" });
  this.route("favorites", { path: "/favs" });
});
```

Now, when the user visits `/about`, Ember.js will render the `about` 
template. Visiting `/favs` will render the `favorites` template.

现在当用户访问`/about`时，Ember.js就会渲染`about`的模板。访问`/favs`将渲染`favorites`的模板。

<aside>
**Heads up!** You get a few routes for free: the `ApplicationRoute` and
the `IndexRoute` (corresponding to the `/` path).
[See below](#toc_initial-routes) for more details.

**注意！**Ember会自动创建一些路由：`ApplicationRoute`、`IndexRoute`（响应`/`路径）。详细的内容[如下所示](#toc_initial-routes)。
</aside>

Note that you can leave off the path if it is the same as the route
name. In this case, the following is equivalent to the above example:

提示：如果路径（path）的名字跟路由（route）的名字是一样的话，你可以不用写上路径。
所以下面的示例跟上面的是相同的。

```js
App.Router.map(function() {
  this.route("about");
  this.route("favorites", { path: "/favs" });
});
```

Inside your templates, you can use `{{link-to}}` to navigate between
routes, using the name that you provided to the `route` method (or, in
the case of `/`, the name `index`).

在模板里面，你可以用`{{link-to}}`来导向路由，这需要用到你在`route`方法中定义的名字
（对于`/`来说，名字就是`index`）。

```handlebars
{{#link-to 'index'}}<img class="logo">{{/link-to}}

<nav>
  {{#link-to 'about'}}About{{/link-to}}
  {{#link-to 'favorites'}}Favorites{{/link-to}}
</nav>
```

The `{{link-to}}` helper will also add an `active` class to the link that
points to the currently active route.

`{{link-to}}`助手会在链接上面加上`active`的类名（class）来指出当前活跃的路由。

You can customize the behavior of a route by creating an `Ember.Route`
subclass. For example, to customize what happens when your user visits
`/`, create an `App.IndexRoute`:

你也可以通过创建一个`Ember.Route`的子类来对路由的行为进行自定义。例如，创建
`App.IndexRoute`类来定义当用户访问`/`时会发生什么。

```javascript
App.IndexRoute = Ember.Route.extend({
  setupController: function(controller) {
    // Set the IndexController's `title`
    controller.set('title', "My App");
  }
});
```

The `IndexController` is the starting context for the `index` template.
Now that you've set `title`, you can use it in the template:

`IndexController`是`index`模板初始的上下文环境。如果你已经设置了`title`，
那么你可以在模板里面使用它。

```handlebars
<!-- get the title from the IndexController -->
<h1>{{title}}</h1>
```

(If you don't explicitly define an `App.IndexController`, Ember.js will
automatically generate one for you.)

（如果你没有显式的声明`IndexController`，`Ember.js`会自动生成一个。）

Ember.js automatically figures out the names of the routes and controllers based on
the name you pass to `this.route`.

`Ember.js`会自动地根据你在`this.route`设置的名字来找出对应的路由与控制器。

<table>
  <thead>
  <tr>
    <th>URL</th>
    <th>Route Name</th>
    <th>Controller</th>
    <th>Route</th>
    <th>Template</th>
  </tr>
  </thead>
  <tr>
    <td><code>/</code></td>
    <td><code>index</code></td>
    <td><code>IndexController</code></td>
    <td><code>IndexRoute</code></td>
    <td><code>index</code></td>
  </tr>
  <tr>
    <td><code>/about</code></td>
    <td><code>about</code></td>
    <td><code>AboutController</code></td>
    <td><code>AboutRoute</code></td>
    <td><code>about</code></td>
  </tr>
  <tr>
    <td><code>/favs</code></td>
    <td><code>favorites</code></td>
    <td><code>FavoritesController</code></td>
    <td><code>FavoritesRoute</code></td>
    <td><code>favorites</code></td>
  </tr>
</table>

### 资源（Resources）

You can define groups of routes that work with a resource:

你可以为一个资源定义一系列的路由：

```javascript
App.Router.map(function() {
  this.resource('posts', { path: '/posts' }, function() {
    this.route('new');
  });
});
```

As with `this.route`, you can leave off the path if it's the same as the
name of the route, so the following router is equivalent:

跟`this.route`一样，如果路径名称跟路由名称相同，你可以忽略路径，所以下面的路由器跟上面是等效的：

```javascript
App.Router.map(function() {
  this.resource('posts', function() {
    this.route('new');
  });
});
```

This router creates three routes:
这个路由器创建了三个路由：

<table>
  <thead>
  <tr>
    <th>URL</th>
    <th>Route Name</th>
    <th>Controller</th>
    <th>Route</th>
    <th>Template</th>
  </tr>
  </thead>
  <tr>
    <td><code>/</code></td>
    <td><code>index</code></td>
    <td><code>IndexController</code></td>
    <td><code>IndexRoute</code></td>
    <td><code>index</code></td>
  </tr>
  <tr>
    <td>N/A</td>
    <td><code>posts</code><sup>1</sup></td>
    <td><code>PostsController</code></td>
    <td><code>PostsRoute</code></td>
    <td><code>posts</code></td>
  </tr>
  <tr>
    <td><code>/posts</code></td>
    <td><code>posts.index</code></code></td>
    <td><code>PostsController</code><br>↳<code>PostsIndexController</code></td>
    <td><code>PostsRoute</code><br>↳<code>PostsIndexRoute</code></td>
    <td><code>posts</code><br>↳<code>posts/index</code></td>
  </tr>
  <tr>
    <td><code>/posts/new</code></td>
    <td><code>posts.new</code></td>
    <td><code>PostsController</code><br>↳<code>PostsNewController</code></td>
    <td><code>PostsRoute</code><br>↳<code>PostsNewRoute</code></td>
    <td><code>posts</code><br>↳<code>posts/new</code></td>
  </tr>
</table>

<small><sup>1</sup> Transitioning to `posts` or creating a link to
`posts` is equivalent to transitioning to `posts.index` or linking to
`posts.index`</small>

<small><sup>1</sup> 跳转到`posts`或者链接到`posts`，等效于跳转到`posts.index`或链接到`posts.index`。</small>

NOTE: If you define a resource using `this.resource` and **do not** supply
a function, then the implicit `resource.index` route is **not** created. In
that case, `/resource` will only use the `ResourceRoute`, `ResourceController`,
and `resource` template.


注意：如果你通过`this.resource`定义了一个资源，但是*没有*提供一个函数作为参数，
那么隐式的`resource.index`是*不会*被创建的。在这种情况下，`/resource`只会用到
`ResourceRoute`，`RescourceController`和`resource`模板。

Routes nested under a resource take the name of the resource plus their
name as their route name. If you want to transition to a route (either
via `transitionTo` or `{{#link-to}}`), make sure to use the full route
name (`posts.new`, not `new`).

一个资源下的嵌套路由的名字会是资源名加上路由名。如果你想跳转到一个路由（用`transitionTo`或
`{{#link-to}}`），请确保使用了完整的路由名（如：`post.new`，而不是`new`）。

Visiting `/` renders the `index` template, as you would expect.

正如你期望的一样，访问`/`会渲染`index`模板。

Visiting `/posts` is slightly different. It will first render the
`posts` template. Then, it will render the `posts/index` template into the
`posts` template's outlet.

访问`/posts`会有点不同。它会先渲染`posts`模板，然后再渲染`posts/index`模板到
`post`模板的出口（`outlet`）上。

Finally, visiting `/posts/new` will first render the `posts` template,
then render the `posts/new` template into its outlet.

最后，访问`/posts/new`会先渲染`posts`模板，然后渲染`posts/new`模板到它的出口上。

NOTE: You should use `this.resource` for URLs that represent a **noun**,
and `this.route` for URLs that represent **adjectives** or **verbs**
modifying those nouns. For example, in the code sample above, when
specifying URLs for posts (a noun), the route was defined with
`this.resource('posts')`. However, when defining the `new` action
(a verb), the route was defined with `this.route('new')`.

注意：你应该使用this.resource来定义一个URL中的**名词**字段，而对于用来改变名词字段的**形容词**或**动词**字段 ，使用this.route来定义。例如，在上例中的代码，当指定`posts`（名词）的URL时，路由被定义为`this.resource('posts')`。然而，当定义`new`操作（动词）时，那么路由被定义为`this.route('new')`。

### 动态段（Dynamic Segments）

One of the responsibilities of a resource's route handler is to convert a URL
into a model.

在路由处理器的众多职责里，其中有一个就是转换URL并将其传入模型（`model`）中。

For example, if we have the resource `this.resource('posts')`, our
route handler might look like this:

例如，如果我们有一个资源`this.resource('posts')`，那么我们的路由处理器看起来可能像这样：

```js
App.PostsRoute = Ember.Route.extend({
  model: function() {
    return this.store.find('post');
  }
});
```

The `posts` template will then receive a list of all available posts as
its context.

`posts`模板将会接收到一张所有可用的posts清单并将它们当做是上下文环境。

Because `/posts` represents a fixed model, we don't need any
additional information to know what to use.  However, if we want a route
to represent a single post, we would not want to have to hardcode every
possible post into the router.

由于`/posts`映射到一个特定的模型上，所以我们不需要其他额外的信息就可以运行。然而，如果我们想要路由映射到某个post上，我们可不想通过在路由器中写死每一个可能的post来实现。

Enter _dynamic segments_.

探究_动态段_

A dynamic segment is a portion of a URL that starts with a `:` and is
followed by an identifier.

一个动态段是URL的一部分，由一个`:`起始，后面加上一个标示符组成。

```js
App.Router.map(function() {
  this.resource('posts');
  this.resource('post', { path: '/post/:post_id' });
});

App.PostRoute = Ember.Route.extend({
  model: function(params) {
    return this.store.find('post', params.post_id);
  }
});
```

Because this pattern is so common, the above `model` hook is the
default behavior.

由于这种模式很常用，所以上面的模型（`model`）钩子函数就是默认的行为。

For example, if the dynamic segment is `:post_id`, Ember.js is smart
enough to know that it should use the model `App.Post` (with the ID
provided in the URL). Specifically, unless you override `model`, the route will
return `this.store.find('post', params.post_id)` automatically.

例如，如果动态段是`:post_id`，`ember.js`会智能地使用`App.post`（加上`URL`提供的`ID`)。
特别地，如果你没有重写了模型（`model`），路由将会自动地返回`this.store.find('post', params.post_id)`。

Not coincidentally, this is exactly what Ember Data expects. So if you
use the Ember router with Ember Data, your dynamic segments will work
as expected out of the box.

这不是巧合，而是`Ember Data`所想要的。所以如果你使用`Ember`路由和`Ember Data`，
你的动态段将会以默认的方式工作。

If your model does not use the `id` property in the URL, you should
define a serialize method on your route:

如果模型没有在URL中使用`id`属性，那么应该在路由中定义一个序列化方法：

```javascript
App.Router.map(function() {
  this.resource('post', {path: '/posts/:post_slug'});
});

App.PostRoute = Ember.Route.extend({
  model: function(params) {
    // the server returns `{ slug: 'foo-post' }`
    return jQuery.getJSON("/posts/" + params.post_slug);
  },

  serialize: function(model) {
    // this will make the URL `/posts/foo-post`
    return { post_slug: model.get('slug') };
  }
});
```

The default `serialize` method inserts the model's `id` into the route's
dynamic segment (in this case, `:post_id`).

缺省的`serialize`方法将模型的`id`插入到路由的动态段中（上述的`:post_id`)。

### （嵌套资源）Nested Resources

You cannot nest routes, but you can nest resources:

你不能嵌套路由，但是你可以嵌套资源：

```javascript
App.Router.map(function() {
  this.resource('post', { path: '/post/:post_id' }, function() {
    this.route('edit');
    this.resource('comments', function() {
      this.route('new');
    });
  });
});
```

This router creates five routes:

这个路由器创建了五个路由：

<div style="overflow: auto">
  <table>
    <thead>
    <tr>
      <th>URL</th>
      <th>Route Name</th>
      <th>Controller</th>
      <th>Route</th>
      <th>Template</th>
    </tr>
    </thead>
    <tr>
      <td><code>/</code></td>
      <td><code>index</code></td>
      <td><code>App.IndexController</code></td>
      <td><code>App.IndexRoute</code></td>
      <td><code>index</code></td>
    </tr>
    <tr>
      <td>N/A</td>
      <td><code>post</code></td>
      <td><code>App.PostController</code></td>
      <td><code>App.PostRoute</code></td>
      <td><code>post</code></td>
    </tr>
    <tr>
      <td><code>/post/:post_id<sup>2</sup></code></td>
      <td><code>post.index</code></td>
      <td><code>App.PostIndexController</code></td>
      <td><code>App.PostIndexRoute</code></td>
      <td><code>post/index</code></td>
    </tr>
    <tr>
      <td><code>/post/:post_id/edit</code></td>
      <td><code>post.edit</code></td>
      <td><code>App.PostEditController</code></td>
      <td><code>App.PostEditRoute</code></td>
      <td><code>post/edit</code></td>
    </tr>
    <tr>
      <td>N/A</td>
      <td><code>comments</code></td>
      <td><code>App.CommentsController</code></td>
      <td><code>App.CommentsRoute</code></td>
      <td><code>comments</code></td>
    </tr>
    <tr>
      <td><code>/post/:post_id/comments</code></td>
      <td><code>comments.index</code></td>
      <td><code>App.CommentsIndexController</code></td>
      <td><code>App.CommentsIndexRoute</code></td>
      <td><code>comments/index</code></td>
    </tr>
    <tr>
      <td><code>/post/:post_id/comments/new</code></td>
      <td><code>comments.new</code></td>
      <td><code>App.CommentsNewController</code></td>
      <td><code>App.CommentsNewRoute</code></td>
      <td><code>comments/new</code></td>
    </tr>
  </table>
</div>


<small><sup>2</sup> `:post_id` is the post's id.  For a post with id = 1, the route will be:
`/post/1`</small>

<small><sup>2</sup> `:post_id`就是post的id。例如一个post的id是1，那么路由就是`/post/1`</small>

The `comments` template will be rendered in the `post` outlet.
All templates under `comments` (`comments/index` and `comments/new`) will be rendered in the `comments` outlet.

`comments`模板会被渲染进`post`的出口。
所有在`comments`下的模板（`comments/index` 和 `comments/new`）都会被渲染进`comments`出口。

You are also able to create deeply nested resources in order to preserve
the namespace on your routes:

为了保护路由的命名空间，可以添加深层嵌套的资源：

```javascript
App.Router.map(function() {
  this.resource('foo', function() {
    this.resource('foo.bar', { path: '/bar' }, function() {
      this.route('baz'); // This will be foo.bar.baz
    });
  });
});
```

This router creates the following routes:

上面定义的路由器会创建如下路由：

<div style="overflow: auto">
  <table>
    <thead>
    <tr>
      <th>URL</th>
      <th>路由名称 (Route Name)</th>
      <th>控制器 (Controller)</th>
      <th>路由 (Route)</th>
      <th>模板 (Template)</th>
    </tr>
    </thead>
    <tr>
      <td><code>/</code></td>
      <td><code>index</code></td>
      <td><code>App.IndexController</code></td>
      <td><code>App.IndexRoute</code></td>
      <td><code>index</code></td>
    </tr>
    <tr>
      <td><code>/foo</code></td>
      <td><code>foo.index</code></td>
      <td><code>App.FooIndexController</code></td>
      <td><code>App.FooIndexRoute</code></td>
      <td><code>foo/index</code></td>
    </tr>
    <tr>
      <td><code>/foo/bar</code></td>
      <td><code>foo.bar.index</code></td>
      <td><code>App.FooBarIndexController</code></td>
      <td><code>App.FooBarIndexRoute</code></td>
      <td><code>foo/bar/index</code></td>
    </tr>
    <tr>
      <td><code>/foo/bar/baz</code></td>
      <td><code>foo.bar.baz</code></td>
      <td><code>App.FooBarBazController</code></td>
      <td><code>App.FooBarBazRoute</code></td>
      <td><code>foo/bar/baz</code></td>
    </tr>
  </table>
</div>

### Initial routes

### 初始路由

A few routes are immediately available within your application:  

一些路由在应用创建后便存在：

  - `App.ApplicationRoute` is entered when your app first boots up. It
  renders the `application` template.  

  - 应用启动时，首先进入`App.ApplicationRoute`，它将渲染`application`模板。

  - `App.IndexRoute` is the default route, and will render the `index`
  template when the user visits `/` (unless `/` has been overridden by your own custom route).  

  - `App.IndexRoute`是默认路由，当用户访问`/`时，将渲染`index`模板（除非`/`被自定义的路由覆盖）。

Remember, these routes are part of every application, so you don't need
to specify them in `App.Router.map`.

请记住，这些路由是每个应用的一部分，因此不需要在`App.Router.map`中指定。

### Wildcard / globbing routes

### 通配符路由

You can define wildcard routes that will match mutliple routes. This could be used, for example,
if you'd like a catchall route which is useful when the user enters an incorrect URL not managed
by your app.

可以定义通配符路由来匹配多个路由。这种方法很有用，比如如果想获取用户进入应用的错误路由的时候。

```javascript
App.Router.map(function() {
  this.route('catchall', {path: '/*wildcard'});
});
```

Like all routes with a dynamic segment, you must provide a context when using a `{{link-to}}`
or `transitionTo` to programatically enter this route.

通配符路由与动态路由一样，在使用`{{link-to}}或者`transitionTo`来进入这个路由的时候，需要提供一个上下文。

```javascript
App.ApplicationRoute = Ember.Route.extend({
  actions: {
    error: function () {
      this.transitionTo('catchall', "application-error");
    }
  }
});
```

With this code, if an error bubbles up to the Application route, your application will enter
the `catchall` route and display `/application-error` in the URL.

在上述代码中，如果一个错误冒泡到应用路由，那么应用将进入`catchall`路由，并在URL中显示`/application-error`。
