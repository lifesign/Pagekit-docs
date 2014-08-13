# ��ͼ����Ӧ

<p class="uk-article-lead">��Ⱦ��ͼ���߷����Զ������Ӧ.</p>

�ڴ���������, ���������صĽ����һ����Ⱦ���ҳ��. ��Ȼ��Ҳ���Է���json����,��ת��һ����ҳ����ߴ���ҳ. ��Щ������ͨ����ͼ����Ӧ���������.

## ��Ⱦ��ͼ

ͨ��ʹ�� `@Response` ע����ָ��Ҫ����Ⱦ���ļ��� ͬʱ����������������Ҫ����Ⱦ����ͼ����������. ע�� `head.title`��һ�������ֵ���ڸ�ҳ��� `<title>`��ֵ

��Ⱦ `<extensions>/hello/views/index.razr`:

```php
/**
 * @Response("hello/view.razr")
 */
public function viewAction($id=1)
{
    return ['head.title' => __('View article'), 'id' => $id];
}
```

��Ҳ�����ֶ���ͨ��`View`��������Ⱦһ��ģ���ļ�. �����ǵ����붯̬�ļ���ĳ����ͼ ������ɽ��������.

```php
public function anotherViewAction()
{
    $view = 'extension://hello/views/view.razr';
    $data = ['head.title' => __('View article'), 'id' => 1];
    return $this['view']->render($view, $data);
}
```

ģ���ļ����﷨ʹ�õ���[Razrģ������](https://github.com/pagekit/razr).

```HTML
<!-- extensions/hello/views/index.razr -->

<h1>You are viewing article number @id</h1>
<p>
   ...
</p>

```

### ��ͼ����

Ĭ�������,��ͼ���ڲ����г��ֵ�,ͨ�����ɶ�Ӧ�����ⶨ���. �����Ϊʲô��������Ӳ�ֻ�ǲ�������,����һ��������html���.
�����Ҫ��ֹ���������,��������� `layout` ����Ϊ `false`.

| ����                       | ʾ��                                      |
|------------------------------|----------------------------------------------|
| ʹ��ע�͵���ʽ                    | `@Response("hello/index.razr", layout=false)`|
| ʹ�� `view` ����                | `$this['view']->setLayout(false);`           |

�����ṩһ��ָ���Ĳ��� `$this['view']->setLayout('hello/theme.razr');`

��ͼ�ļ� `<extensions>/hello/views/theme.razr`:

```HTML
<h1>This is the main theme</h1>

<section>
    @section('content')
</section>
```

## ģ��
ģ���ļ�ͨ��pagekit�Դ���[����](https://github.com/pagekit/razr)ģ������,�ṩ�˺��������Լ����̿���.

### ·������

ÿһ��·�ɶ���һ����Ӧ������,��������ʹ�þ����ĳ��·������̬��������. ����ڵ���ģ��������ͬ����Ч.

```HTML
<a href="@url.route('@hello/default/view')">View all articles</a>
<a href="@url.route('@hello/default/view', ['id' => 23])">View article 23</a>
```

��Ҳ����ʹ�� `@url.to` ���ӵ���Դ�ļ�,����ͼƬ���������ļ�.

```HTML
<img src="@url.to('extensions/hello/extension.svg')" alt="Extension icon" />
```

### ���ػ��ַ���

�����Ҫ��һ���ַ������ʻ�, ��`@trans`������������.

```
@trans('Save')
```

Translateable pluralisation.

```
@transchoice('{0} No posts found|{1} %posts% post found|]1,Inf[ %posts% posts found', users|length, ['%posts%' => posts|length])
```

����i18n(���ʻ�)�μ�[����](translation.md).

## ��Ӧ

### ��ת

ʹ�� `redirect($url, $parameters = [], $status = 302, $headers = [])` ��������ת

```php
function redirectAction()
{
    return $this['response']->redirect('@hello/greet/name', ['name' => 'Someone']);
}
```

�����Ŀ������̳��� `Pagekit\Framework\Controller\Controller`, Ҳ����ֱ�ӵ��� `redirect` ����������ת.

```php
public function redirectAction()
{
    return $this->redirect('@hello/greet/name', ['name' => 'Someone']);
}
```

### ����json

����ͨ��ע�������÷���json����

```php
/*
 *@Response("json")
 */
public function jsonAction()
{
    return ['error' => true, 'message' => 'There is nothing here. Move along.'];
}
```

��Ȼ��Ҳ����ʹ��`response`�������ֶ�����json����.

```php
public function jsonAction()
{
    $data = ['error' => true, 'message' => 'There is nothing here. Move along.'];
    return $this['response']->json($data);
}

```


### �Զ�����Ӧ�ʹ���ҳ

�����ʹ�� `create($content = '', $status = 200, $headers = [])` ���Զ����κ�HTTP��Ӧ��Ϣ.

```php
function forbiddenAction()
{
    return $this['response']->create('Permission denied.', 401);
}
```

### ����

�����ʹ�� `download($file, $name = null, $headers = [])` ������һ���ļ�����������.��������� `Content-Disposition: attachment` ��ô��ǿ���ڴ󲿷ֵ�������ϵ���һ�����Ϊ�ĵ���.

```php
public function downloadAction()
{
    return $this['response']->download('extensions/hello/extension.svg');
}
```
