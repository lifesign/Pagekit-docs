# 视图与响应

<p class="uk-article-lead">渲染视图或者发送自定义的响应.</p>

在大多数情况下, 控制器返回的结果是一个渲染后的页面. 当然你也可以返回json数据,跳转到一个新页面或者错误页. 这些都可以通过视图与响应服务来完成.

## 渲染视图

通过使用 `@Response` 注释来指明要被渲染的文件， 同时控制器方法返回需要被渲染到视图的数组数据. 注意 `head.title`是一个特殊的值用于给页面的 `<title>`赋值

渲染 `<extensions>/hello/views/index.razr`:

```php
/**
 * @Response("hello/view.razr")
 */
public function viewAction($id=1)
{
    return ['head.title' => __('View article'), 'id' => $id];
}
```

你也可以手动的通过`View`服务来渲染一个模板文件. 尤其是当你想动态的加载某个视图 这个技巧将会很有用.

```php
public function anotherViewAction()
{
    $view = 'extension://hello/views/view.razr';
    $data = ['head.title' => __('View article'), 'id' => 1];
    return $this['view']->render($view, $data);
}
```

模板文件的语法使用的是[Razr模板引擎](https://github.com/pagekit/razr).

```HTML
<!-- extensions/hello/views/index.razr -->

<h1>You are viewing article number @id</h1>
<p>
   ...
</p>

```

### 视图布局

默认情况下,视图是在布局中呈现的,通常是由对应的主题定义的. 这就是为什么上面的例子不只是产生两行,而是一个完整的html输出.
如果想要禁止这样的输出,你可以设置 `layout` 参数为 `false`.

| 方法                       | 示例                                      |
|------------------------------|----------------------------------------------|
| 使用注释的形式                    | `@Response("hello/index.razr", layout=false)`|
| 使用 `view` 服务                | `$this['view']->setLayout(false);`           |

或者提供一个指定的布局 `$this['view']->setLayout('hello/theme.razr');`

视图文件 `<extensions>/hello/views/theme.razr`:

```HTML
<h1>This is the main theme</h1>

<section>
    @section('content')
</section>
```

## 模板
模板文件通过pagekit自带的[刀锋](https://github.com/pagekit/razr)模板引擎,提供了函数功能以及流程控制.

### 路由链接

每一个路由都有一个对应的名字,因此你可以使用具体的某个路由来动态生成链接. 这点在刀锋模板引擎中同样有效.

```HTML
<a href="@url.route('@hello/default/view')">View all articles</a>
<a href="@url.route('@hello/default/view', ['id' => 23])">View article 23</a>
```

你也可以使用 `@url.to` 链接到资源文件,比如图片或者其他文件.

```HTML
<img src="@url.to('extensions/hello/extension.svg')" alt="Extension icon" />
```

### 本地化字符串

如果想要让一个字符串国际化, 用`@trans`包裹起来即可.

```
@trans('Save')
```

Translateable pluralisation.

```
@transchoice('{0} No posts found|{1} %posts% post found|]1,Inf[ %posts% posts found', users|length, ['%posts%' => posts|length])
```

更多i18n(国际化)参见[翻译](translation.md).

## 响应

### 跳转

使用 `redirect($url, $parameters = [], $status = 302, $headers = [])` 来进行跳转

```php
function redirectAction()
{
    return $this['response']->redirect('@hello/greet/name', ['name' => 'Someone']);
}
```

如果你的控制器继承了 `Pagekit\Framework\Controller\Controller`, 也可以直接调用 `redirect` 方法进行跳转.

```php
public function redirectAction()
{
    return $this->redirect('@hello/greet/name', ['name' => 'Someone']);
}
```

### 返回json

可以通过注释来配置返回json数据

```php
/*
 *@Response("json")
 */
public function jsonAction()
{
    return ['error' => true, 'message' => 'There is nothing here. Move along.'];
}
```

当然你也可以使用`response`服务来手动返回json数据.

```php
public function jsonAction()
{
    $data = ['error' => true, 'message' => 'There is nothing here. Move along.'];
    return $this['response']->json($data);
}

```


### 自定义响应和错误页

你可以使用 `create($content = '', $status = 200, $headers = [])` 来自定义任何HTTP响应信息.

```php
function forbiddenAction()
{
    return $this['response']->create('Permission denied.', 401);
}
```

### 下载

你可以使用 `download($file, $name = null, $headers = [])` 来生成一个文件的下载链接.如果设置了 `Content-Disposition: attachment` 那么会强制在大部分的浏览器上弹出一个另存为的弹框.

```php
public function downloadAction()
{
    return $this['response']->download('extensions/hello/extension.svg');
}
```
