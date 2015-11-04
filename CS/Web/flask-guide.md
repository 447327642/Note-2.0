# Flask Study Note

## 基本介绍

+ 保持核心简单且易扩展
+ 简单的任务应该保持简单，它们的实现不应是大量代码的堆叠
+ web 开发是危险的，注意数据安全
+ 依赖两个外部库
    * Werkzeug 一个WSGI(在 web 应用和多种服务器之间开发和部署的标准 Python 接口)
    * Jinja2 渲染模板

## 如何开始一个项目

### 步骤 0 ：创建文件夹

在开始之前需要为应用创建下列文件夹:

```
/flaskr
    /static
    /templates
```

flaskr 文件夹不是一个 Python 包，只是一个用来存放我们文件的地方。我们将把以后 要用到的数据库模式和主模块放在这个文件夹中。 static 文件夹中的文件是用于供 应用用户通过 HTTP 访问的文件，主要是 CSS 和 javascript 文件。 Flask 将会在 templates 文件夹中搜索 Jinja2 模板，所有在教程中的模板都放在 templates 文件夹中。

### 步骤 1 ：数据库模式

首先我们要创建数据库模式。本应用只需要使用一张表，并且由于我们使用 SQLite ， 所以这一步非常简单。把以下内容保存为 schema.sql 文件并放在我们上一步创建的 flaskr 文件夹中就行了：

```
drop table if exists entries;
create table entries (
  id integer primary key autoincrement,
  title text not null,
  text text not null
);
```

这个模式只有一张名为 entries 的表，表中的字段为 id 、 title 和 text 。 id 是主键，是自增整数型字段，另外两个字段是非空的字符串型字段。

### 步骤 2 ：应用构建代码

现在我们已经准备好了数据库模式了，下面来创建应用模块。我们把模块命名为 flaskr.py ，并放在 flaskr 文件夹中。为了方便初学者学习，我们把库的导入与 相关配置放在了一起。对于小型应用来说，可以把配置直接放在模块中。但是更加清晰的 方案是把配置放在一个独立的 .ini 或 .py 文件中，并在模块中导入配置的值。

在 flaskr.py 文件中:

```
# all the imports
import sqlite3
from flask import Flask, request, session, g, redirect, url_for, \
     abort, render_template, flash

# configuration
DATABASE = '/tmp/flaskr.db'
DEBUG = True
SECRET_KEY = 'development key'
USERNAME = 'admin'
PASSWORD = 'default'
```

接着创建真正的应用，并用同一文件中的配置来初始化，在 flaskr.py 文件中:

```
# create our little application :)
app = Flask(__name__)
app.config.from_object(__name__)
```

from_object() 会查看给定的对象（如果该对象是一个字符串就会 直接导入它），搜索对象中所有变量名均为大字字母的变量。在我们的应用中，已经将配 置写在前面了。你可以把这些配置放到一个独立的文件中。

通常，从一个配置文件中导入配置是比较好的做法，我们使用 from_envvar() 来完成这个工作，把上面的 from_object() 一行替换为:

	app.config.from_envvar('FLASKR_SETTINGS', silent=True)

这样做就可以设置一个 FLASKR_SETTINGS 的环境变量来指定一个配置文件，并 根据该文件来重载缺省的配置。 silent 开关的作用是告诉 Flask 如果没有这个环境变量 不要报错。

secret_key （密钥）用于保持客户端会话安全，请谨慎地选择密钥，并尽可能的使它 复杂而且不容易被猜到。 DEBUG 标志用于开关交互调试器。因为调试模式允许用户执行 服务器上的代码，所以 永远不要在生产环境中打开调试模式 ！

我们还添加了一个方便连接指定数据库的方法。这个方法可以用于在请求时打开连接，也 可以用于 Python 交互终端或代码中。以后会派上用场。

```
def connect_db():
    return sqlite3.connect(app.config['DATABASE'])
```

最后，在文件末尾添加以单机方式启动服务器的代码:

```
if __name__ == '__main__':
    app.run()
```

到此为止，我们可以顺利运行应用了。输入以下命令开始运行:

	python flaskr.py

你会看到服务器已经运行的信息，其中包含应用访问地址。

因为我们还没创建视图，所以当你在浏览器中访问这个地址时，会得到一个 404 页面未 找到错误。很快我们就会谈到视图，但我们先要弄好数据库。

### 步骤 3 ：创建数据库

如前所述 Flaskr 是一个数据库驱动的应用，更准确地说是一个关系型数据库驱动的 应用。关系型数据库需要一个数据库模式来定义如何储存信息，因此必须在第一次运行 服务器前创建数据库模式。

使用 sqlite3 命令通过管道导入 schema.sql 创建模式:

	sqlite3 /tmp/flaskr.db < schema.sql

上述方法的不足之处是需要额外的 sqlite3 命令，但这个命令不是每个系统都有的。而且还必须提供数据库的路径，容易出错。因此更好的方法是在应用中添加一个数据库初始化 函数。

添加的方法是：首先从 contextlib 库中导入 contextlib.closing() 函数，即在 flaskr.py 文件的导入部分添加如下内容:

from contextlib import closing

接下来，可以创建一个用来初始化数据库的 init_db 函数，其中我们使用了先前创建的 connect_db 函数。把这个初始化函数放在 flaskr.py 文件中的`connect_db` 函数 下面:

```python
def init_db():
    with closing(connect_db()) as db:
        with app.open_resource('schema.sql', mode='r') as f:
            db.cursor().executescript(f.read())
        db.commit()
```

closing() 帮助函数允许我们在 with 代码块保持数据库连接 打开。应用对象的 open_resource() 方法支持也支持这个功能， 可以在 with 代码块中直接使用。这个函数打开一个位于来源位置（你的 flaskr 文件夹）的文件并允许你读取文件的内容。这里我们用于在数据库连接上执行 代码。

当我们连接到数据库时，我们得到一个提供指针的连接对象（本例中的 db ）。这个 指针有一个方法可以执行完整的代码。最后我们提供要做的修改。 SQLite 3 和其他 事务型数据库只有在显式提交时才会真正提交。

现在可以创建数据库了。打开 Python shell ，导入，调用函数:

```
>>> from flaskr import init_db
>>> init_db()
```

### 步骤 4 ：请求数据库连接

现在我们已经学会如何打开并在代码中使用数据库连接，但是如何优雅地在请求时使用它 呢？我们会在每一个函数中用到数据库连接，因此有必要在请求之前初始化连接，并在 请求之后关闭连接。

Flask 中可以使用 before_request() 、 after_request() 和 teardown_request() 装饰器达到这个目的:

```python
@app.before_request
def before_request():
    g.db = connect_db()

@app.teardown_request
def teardown_request(exception):
    db = getattr(g, 'db', None)
    if db is not None:
        db.close()
    g.db.close()
```

使用 before_request() 装饰的函数会在请求之前调用，且不传递 参数。使用 after_request() 装饰的函数会在请求之后调用，且 传递发送给客户端响应对象。它们必须传递响应对象，所以在出错的情况下就不会执行。 因此我们就要用到 teardown_request() 装饰器了。这个装饰器下 的函数在响应对象构建后被调用。它们不允许修改请求，并且它们的返回值被忽略。如果 请求过程中出错，那么这个错误会传递给每个函数；否则传递 None 。

我们把数据库连接保存在 Flask 提供的特殊的 g 对象中。这个对象与 每一个请求是一一对应的，并且只在函数内部有效。不要在其它对象中储存类似信息， 因为在多线程环境下无效。这个特殊的 g 对象会在后台神奇的工作，保 证系统正常运行。

若想更好地处理这种资源，请参阅 在 Flask 中使用 SQLite 3 。

下面请阅读 步骤 5 ：视图函数.

Hint

我该把这些代码放在哪里？

如果你按教程一步一步读下来，那么可能会疑惑应该把这个步骤和以后的代码放在哪里？比较有条理的做法是把这些模块级别的函数放在一起，并把新的 before_request 和 teardown_request 函数放在前文的 init_db 函数 下面（即按照教程的顺序放置）。

### 步骤 5 ：视图函数

现在数据库连接弄好了，接着开始写视图函数。我们共需要四个视图函数：

#### 显示条目

这个视图显示所有数据库中的条目。它绑定应用的根地址，并从数据库中读取 title 和 text 字段。 id 最大的记录（最新的条目）在最上面。从指针返回的记录集是一个包含 select 语句查询结果的元组。对于教程应用这样的小应用，做到这样就已经够好了。但是 你可能想要把结果转换为字典，具体做法参见 简化查询 中的例子。

这个视图会把条目作为字典传递给 show_entries.html 模板，并返回渲染结果:

```python
@app.route('/')
def show_entries():
    cur = g.db.execute('select title, text from entries order by id desc')
    entries = [dict(title=row[0], text=row[1]) for row in cur.fetchall()]
    return render_template('show_entries.html', entries=entries)
```

#### 添加一个新条目

这个视图可以让一个登录后的用户添加一个新条目。本视图只响应 POST 请求，真正的 表单显示在 show_entries 页面中。如果一切顺利，我们会 flash() 一个消息给下一个请求并重定向回到 show_entries 页面:

```python
@app.route('/add', methods=['POST'])
def add_entry():
    if not session.get('logged_in'):
        abort(401)
    g.db.execute('insert into entries (title, text) values (?, ?)',
                 [request.form['title'], request.form['text']])
    g.db.commit()
    flash('New entry was successfully posted')
    return redirect(url_for('show_entries'))
```

注意，我们在本视图中检查了用户是否已经登录（即检查会话中是否有 logged_in 键，且 对应的值是否为 True ）。

安全性建议

请像示例代码一样确保在构建 SQL 语句时使用问号。否则当你使用字符串构建 SQL 时 容易遭到 SQL 注入攻击。更多内容参见 在 Flask 中使用 SQLite 3 。

#### 登录和注销

这些函数用于用户登录和注销。登录视图根据配置中的用户名和密码验证用户并在会话中 设置 logged_in 键值。如果用户通过验证，键值设为 True ，那么用户会被重定向到 show_entries 页面。另外闪现一个信息，告诉用户已登录成功。如果出现错误，模板会 提示错误信息，并让用户重新登录:

```python
@app.route('/login', methods=['GET', 'POST'])
def login():
    error = None
    if request.method == 'POST':
        if request.form['username'] != app.config['USERNAME']:
            error = 'Invalid username'
        elif request.form['password'] != app.config['PASSWORD']:
            error = 'Invalid password'
        else:
            session['logged_in'] = True
            flash('You were logged in')
            return redirect(url_for('show_entries'))
    return render_template('login.html', error=error)
```

登出视图则正好相反，把键值从会话中删除。在这里我们使用了一个小技巧：如果你使用 字典的 pop() 方法并且传递了第二个参数（键的缺省值），那么当字典中有 这个键时就会删除这个键，否则什么也不做。这样做的好处是我们不用检查用户是否已经 登录了。

```python
@app.route('/logout')
def logout():
    session.pop('logged_in', None)
    flash('You were logged out')
    return redirect(url_for('show_entries'))
```

### 步骤 6 ：模板

现在开始写模板。如果我们现在访问 URL ，那么会得到一个 Flask 无法找到模板文件的 异常。 Flask 使用 Jinja2 模板语法并默认开启自动转义。也就是说除非用 Markup 标记一个值或在模板中使用 |safe 过滤器，否则 Jinja2 会把如 < 或 > 之类的特殊字符转义为与其 XML 等价字符。

我们还使用了模板继承以保存所有页面的布局统一。

请把以下模板放在 templates 文件夹中：

#### layout.html

这个模板包含 HTML 骨架、头部和一个登录链接（如果用户已登录则变为一个注销链接 ）。如果有闪现信息，那么还会显示闪现信息。 {% block body %} 块会被子模板中 同名（ body ）的块替换。

session 字典在模板中也可以使用。你可以使用它来检验用户是否已经 登录。注意，在 Jinja 中可以访问对象或字典的不存在的属性和成员。如例子中的 'logged_in' 键不存在时代码仍然能正常运行：

```html
<!doctype html>
<title>Flaskr</title>
<link rel=stylesheet type=text/css href="{{ url_for('static', filename='style.css') }}">
<div class=page>
  <h1>Flaskr</h1>
  <div class=metanav>
  {% if not session.logged_in %}
    <a href="{{ url_for('login') }}">log in</a>
  {% else %}
    <a href="{{ url_for('logout') }}">log out</a>
  {% endif %}
  </div>
  {% for message in get_flashed_messages() %}
    <div class=flash>{{ message }}</div>
  {% endfor %}
  {% block body %}{% endblock %}
</div>
```

#### show_entries.html

这个模板扩展了上述的 layout.html 模板，用于显示信息。注意， for 遍历了我们 通过 render_template() 函数传递的所有信息。模板还告诉表单使用 POST 作为 HTTP 方法向 add_entry 函数提交数据：

```html
{% extends "layout.html" %}
{% block body %}
  {% if session.logged_in %}
    <form action="{{ url_for('add_entry') }}" method=post class=add-entry>
      <dl>
        <dt>Title:
        <dd><input type=text size=30 name=title>
        <dt>Text:
        <dd><textarea name=text rows=5 cols=40></textarea>
        <dd><input type=submit value=Share>
      </dl>
    </form>
  {% endif %}
  <ul class=entries>
  {% for entry in entries %}
    <li><h2>{{ entry.title }}</h2>{{ entry.text|safe }}
  {% else %}
    <li><em>Unbelievable.  No entries here so far</em>
  {% endfor %}
  </ul>
{% endblock %}
```

#### login.html

最后是简单显示用户登录表单的登录模板：

```html
{% extends "layout.html" %}
{% block body %}
  <h2>Login</h2>
  {% if error %}<p class=error><strong>Error:</strong> {{ error }}{% endif %}
  <form action="{{ url_for('login') }}" method=post>
    <dl>
      <dt>Username:
      <dd><input type=text name=username>
      <dt>Password:
      <dd><input type=password name=password>
      <dd><input type=submit value=Login>
    </dl>
  </form>
{% endblock %}
```

### 步骤 7 ：添加样式

现在万事俱备，只剩给应用添加一些样式了。只要把以下内容保存为 static 文件夹中 的 style.css 文件就行了：

```css
body            { font-family: sans-serif; background: #eee; }
a, h1, h2       { color: #377ba8; }
h1, h2          { font-family: 'Georgia', serif; margin: 0; }
h1              { border-bottom: 2px solid #eee; }
h2              { font-size: 1.2em; }

.page           { margin: 2em auto; width: 35em; border: 5px solid #ccc;
                  padding: 0.8em; background: white; }
.entries        { list-style: none; margin: 0; padding: 0; }
.entries li     { margin: 0.8em 1.2em; }
.entries li h2  { margin-left: -1em; }
.add-entry      { font-size: 0.9em; border-bottom: 1px solid #ccc; }
.add-entry dl   { font-weight: bold; }
.metanav        { text-align: right; font-size: 0.8em; padding: 0.3em;
                  margin-bottom: 1em; background: #fafafa; }
.flash          { background: #cee5F5; padding: 0.5em;
                  border: 1px solid #aacbe2; }
.error          { background: #f0d6d6; padding: 0.5em; }
```

### 测试 Flask 引用

未经测试的小猫，肯定不是一只好猫。

这句话的出处不详（译者注：这句是译者献给小猫的），也不一定完全正确，但是基本上 是正确的。未经测试的应用难于改进现有的代码，因此其开发者会越改进越抓狂。反之， 经过自动测试的代码可以安全的改进，并且如果可以测试过程中立即发现错误。

Flask 提供的测试渠道是公开 Werkzeug 的 Client ，为你 处理本地环境。你可以结合这个渠道使用你喜欢的测试工具。本文使用的测试工具是随着 Python 一起安装好的 unittest 包。

### 测试骨架
为了测试应用，我们添加了一个新的模块 (flaskr_tests.py) 并创建了如下测试骨架:

```python
import os
import flaskr
import unittest
import tempfile

class FlaskrTestCase(unittest.TestCase):

    def setUp(self):
        self.db_fd, flaskr.app.config['DATABASE'] = tempfile.mkstemp()
        flaskr.app.config['TESTING'] = True
        self.app = flaskr.app.test_client()
        flaskr.init_db()

    def tearDown(self):
        os.close(self.db_fd)
        os.unlink(flaskr.app.config['DATABASE'])

if __name__ == '__main__':
    unittest.main()
```

setUp() 方法中会创建一个新的测试客户端并初始化一个新的 数据库。在每个独立的测试函数运行前都会调用这个方法。 tearDown() 方法的功能是在测试结束后关闭文件，并在文件 系统中删除数据库文件。另外在设置中 TESTING 标志开启的，这意味着在请求时关闭 错误捕捉，以便于在执行测试请求时得到更好的错误报告。

测试客户端会给我们提供一个简单的应用接口。我们可以通过这个接口向应用发送测试 请求。客户端还可以追踪 cookies 。

因为 SQLite3 是基于文件系统的，所以我们可以方便地使用临时文件模块来创建一个临时 数据库并初始化它。 mkstemp() 函数返回两个东西：一个低级别的文件 句柄和一个随机文件名。这个文件名后面将作为我们的数据库名称。我们必须把句柄保存 到 db_fd 中，以便于以后用 os.close() 函数来关闭文件。

如果现在进行测试，那么会输出以下内容:

```
$ python flaskr_tests.py

----------------------------------------------------------------------
Ran 0 tests in 0.000s

OK
```

### 第一个测试

现在开始测试应用的功能。当我们访问应用的根 URL （ / ）时应该显示 “ No entries here so far ”。我们新增了一个新的测试方法来测试这个功能:

```python
class FlaskrTestCase(unittest.TestCase):

    def setUp(self):
        self.db_fd, flaskr.app.config['DATABASE'] = tempfile.mkstemp()
        self.app = flaskr.app.test_client()
        flaskr.init_db()

    def tearDown(self):
        os.close(self.db_fd)
        os.unlink(flaskr.app.config['DATABASE'])

    def test_empty_db(self):
        rv = self.app.get('/')
        assert 'No entries here so far' in rv.data
```

注意，我们的调试函数都是以 test 开头的。这样 unittest 就会自动识别这些 是用于测试的函数并运行它们。

通过使用 self.app.get ，可以向应用的指定 URL 发送 HTTP GET 请求，其返回的是 一个 ~flask.Flask.response_class 对象。我们可以使用 data 属性来检查应用的返回值（字符串 类型）。在本例中，我们检查输出是否包含 'No entries here so far' 。

再次运行测试，会看到通过了一个测试:

```
$ python flaskr_tests.py
.
----------------------------------------------------------------------
Ran 1 test in 0.034s

OK
```

### 登录和注销

我们应用的主要功能必须登录以后才能使用，因此必须测试应用的登录和注销。测试的 方法是使用规定的数据（用户名和密码）向应用发出登录和注销的请求。因为登录和注销 后会重定向到别的页面，因此必须告诉客户端使用 follow_redirects 追踪重定向。

在 FlaskrTestCase 类中添加以下两个方法:

```python
def login(self, username, password):
    return self.app.post('/login', data=dict(
        username=username,
        password=password
    ), follow_redirects=True)

def logout(self):
    return self.app.get('/logout', follow_redirects=True)
```

现在可以方便地测试登录成功、登录失败和注销功能了。下面为新增的测试代码:

```python
def test_login_logout(self):
    rv = self.login('admin', 'default')
    assert 'You were logged in' in rv.data
    rv = self.logout()
    assert 'You were logged out' in rv.data
    rv = self.login('adminx', 'default')
    assert 'Invalid username' in rv.data
    rv = self.login('admin', 'defaultx')
    assert 'Invalid password' in rv.data
```

### 测试增加条目功能

我们还要测试增加条目功能。添加以下测试代码:

```python
def test_messages(self):
    self.login('admin', 'default')
    rv = self.app.post('/add', data=dict(
        title='<Hello>',
        text='<strong>HTML</strong> allowed here'
    ), follow_redirects=True)
    assert 'No entries here so far' not in rv.data
    assert '&lt;Hello&gt;' in rv.data
    assert '<strong>HTML</strong> allowed here' in rv.data
```

这里我们检查了博客内容中允许使用 HTML ，但标题不可以。应用设计思路就是这样的。

运行测试，现在通过了三个测试:

```
$ python flaskr_tests.py
...
----------------------------------------------------------------------
Ran 3 tests in 0.332s

OK
```

关于更复杂的 HTTP 头部和状态码测试参见 MiniTwit 示例 。这个示例的源代码中 包含了更大的测试套件。

### 其他测试技巧

除了使用上述测试客户端外，还可以在 with 语句中使用 test_request_context() 方法来临时激活一个请求环境。在这个 环境中可以像以视图函数中一样操作 request 、g 和 session 对象。示例:

```python
app = flask.Flask(__name__)

with app.test_request_context('/?name=Peter'):
    assert flask.request.path == '/'
    assert flask.request.args['name'] == 'Peter'
```

其他与环境绑定的对象也可以这样使用。

如果你必须使用不同的配置来测试应用，而且没有什么好的测试方法，那么可以考虑使用 应用工厂（参见 应用工厂 ）。

注意，在测试请求环境中 before_request() 函数和 after_request() 函数不会被自动调用。但是当调试请求环境离开 with 块时会执行 teardown_request() 函数。如果需要 before_request() 函数和正常情况下一样被调用，那么你必须调用 preprocess_request()

```python
app = flask.Flask(__name__)

with app.test_request_context('/?name=Peter'):
    app.preprocess_request()
    ...
```

在这函数中可以打开数据库连接或者根据应用需要打开其他类似东西。

如果想调用 after_request() 函数，那么必须调用 process_response() ，并把响应对象传递给它:

```python
app = flask.Flask(__name__)

with app.test_request_context('/?name=Peter'):
    resp = Response('...')
    resp = app.process_response(resp)
    ...
```

这个例子中的情况基本没有用处，因为在这种情况下可以直接开始使用测试客户端。

### 伪造资源和环境

通常情况下，我们会把用户认证信息和数据库连接储存到应用环境或者 flask.g 对象中，并在第一次使用前准备好，然后在断开时删除。假设应用中 得到当前用户的代码如下:

```python
def get_user():
    user = getattr(g, 'user', None)
    if user is None:
        user = fetch_current_user_from_database()
        g.user = user
    return user
```

在测试时可以很很方便地重载用户而不用改动代码。可以先象下面这样钩接 flask.appcontext_pushed 信号:

```python
from contextlib import contextmanager
from flask import appcontext_pushed

@contextmanager
def user_set(app, user):
    def handler(sender, **kwargs):
        g.user = user
    with appcontext_pushed.connected_to(handler, app):
        yield
然后使用:

from flask import json, jsonify

@app.route('/users/me')
def users_me():
    return jsonify(username=g.user.username)

with user_set(app, my_user):
    with app.test_client() as c:
        resp = c.get('/users/me')
        data = json.loads(resp.data)
        self.assert_equal(data['username'], my_user.username)
```

### 保持环境

有时候这种情形是有用的：触发一个常规请求，但是保持环境以便于做一点额外 的事情。 在 Flask 0.4 之后可以在 with 语句中使用 test_client() 来 实现:

```python
app = flask.Flask(__name__)

with app.test_client() as c:
    rv = c.get('/?tequila=42')
    assert request.args['tequila'] == '42'
```

如果你在没有 with 的情况下使用 test_client() ，那么 assert 会出错失败。因为无法在请求之外访问 request 。

### 访问和修改会话

有时候在测试客户端中访问和修改会话是非常有用的。通常有两方法。如果你想测试会话中 的键和值是否正确，你可以使用 flask.session:

```python
with app.test_client() as c:
    rv = c.get('/')
    assert flask.session['foo'] == 42
```

但是这个方法无法修改会话或在请求发出前访问会话。自 Flask 0.8 开始，我们提供了 “会话处理”，用打开测试环境中会话和修改会话，最后保存会话。处理后的会话独立于 后端实际使用的会话:

```python
with app.test_client() as c:
    with c.session_transaction() as sess:
        sess['a_key'] = 'a value'

    # 运行到这里时，会话已被保存
```


注意在这种情况下必须使用 sess 对象来代替 flask.session 代理。 sess 对象本身可以提供相同的接口。

---

## 安装

+ 使用 `virutalenv` 可以有效保证环境的互不影响
+ `sudo pip install virualenv`
+ 利用 `activate-wkk.sh` 激活环境
+ `pip install Flask`

## 快速入门

一个最小的 Flask 应用如下:

```python
from flask import Flask
app = Flask(__name__)

@app.route('/')
def hello_world():
    return 'Hello World!'

if __name__ == '__main__':
    app.run()
```

把它保存为 hello.py 或其他类似名称并用你的 Python 解释器运行这个文件。请不要 使用 flask.py 作为应用名称，这会与 Flask 本身发生冲突。

```
$ python hello.py
 * Running on http://127.0.0.1:5000/
```

现在，在浏览器中打开 http://127.0.0.1:5000/ ，就 可以看到问候页面了

那么，这些代码是什么意思呢？

1. 首先我们导入了 Flask 类。这个类的实例将会成为我们的 WSGI 应用。
2. 接着我们创建了这个类的实例。第一个参数是应用模块或者包的名称。如果你使用一个 单一模块（就像本例），那么应当使用 `__name__ `，因为名称会根据这个模块是按 应用方式使用还是作为一个模块导入而发生变化（可能是 `__main__` ，也可能是 实际导入的名称）。这个参数是必需的，这样 Flask 就可以知道在哪里找到模板和 静态文件等东西。更多内容详见 Flask 文档。
3. 然后我们使用 route() 装饰器来告诉 Flask 触发函数的 URL 。
4. 函数名称可用于生成相关联的 URL ，并返回需要在用户浏览器中显示的信息。
5. 最后，使用 run() 函数来运行本地服务器和我们的应用。 `if __name__ == '__main__':` 确保服务器只会在使用 Python 解释器运行代码的情况下运行，而不会在作为模块导入时运行。

按 control-C 可以停止服务器。

+ 如果要同网络的可以访问 `app.run(host='0.0.0.0`)
+ 上面方法每次修改代码都需要手动重启开发服务器，在调试模式下 Flask 可以自动重新载入

方法一：

    app.debug = True
    app.run()

方法二：

    app.run(debug = True)

### 路由

`route()` 装饰器把一个函数绑定到对应的 URL 上，如果想要添加变量部分，需要标记为 `<variable_name>` 或者可以用 `<converter:variable_name>` 来指定一个转换器：

    @app.route('/user/<username>')
    def show_user_profile(username):
        return 'User %s' % username

    @app.route('/post/<int:post_id>')
    def show_post(post_id):
        return 'Post %d' % post_id

现有的转换器

+ 默认为 string
+ int 接受整数
+ float 接受整数或浮点数
+ path 接受斜线

### 唯一的 URL / 重定向行为

Flask 的 URL 规则都是基于 Werkzeug 的路由模块的。其背后的理念是保证漂亮的 外观和唯一的 URL 。这个理念来自于 Apache 和更早期的服务器。

假设有如下两条规则:

```python
@app.route('/projects/')
def projects():
    return 'The project page'

@app.route('/about')
def about():
    return 'The about page'
```
  
它们看上去很相近，不同之处在于 URL 定义 中尾部的斜杠。第一个例子中 prjects 的 URL 是中规中举的，尾部有一个斜杠，看起来就如同一个文件夹。访问 一个没有斜杠结尾的 URL 时 Flask 会自动进行重定向，帮你在尾部加上一个斜杠。

但是在第二个例子中， URL 没有尾部斜杠，因此其行为表现与一个文件类似。如果 访问这个 URL 时添加了尾部斜杠就会得到一个 404 错误。

为什么这样做？因为这样可以在省略末尾斜杠时仍能继续相关的 URL 。这种重定向 行为与 Apache 和其他服务器一致。同时， URL 仍保持唯一，帮助搜索引擎不重复 索引同一页面。

### URL 构建

如果可以匹配 URL ，那么 Flask 也可以生成 URL 吗？当然可以。 url_for() 函数就是用于构建指定函数的 URL 的。它把函数名称作为 第一个参数，其余参数对应 URL 中的变量。未知变量将添加到 URL 中作为查询参数。 例如：

```python
>>> from flask import Flask, url_for
>>> app = Flask(__name__)
>>> @app.route('/')
... def index(): pass
...
>>> @app.route('/login')
... def login(): pass
...
>>> @app.route('/user/<username>')
... def profile(username): pass
...
>>> with app.test_request_context():
...  print url_for('index')
...  print url_for('login')
...  print url_for('login', next='/')
...  print url_for('profile', username='John Doe')
...
/
/login
/login?next=/
/user/John%20Doe
```

（例子中还使用下文要讲到的 test_request_context() 方法。这个 方法的作用是告诉 Flask 我们正在处理一个请求，而实际上也许我们正处在交互 Python shell 之中，并没有真正的请求。详见下面的 本地环境 ）。

为什么不在把 URL 写死在模板中，反而要动态构建？有三个很好的理由：

反向解析通常比硬编码 URL 更直观。同时，更重要的是你可以只在一个地方改变 URL ，而不用到处乱找。
URL 创建会为你处理特殊字符的转义和 Unicode 数据，不用你操心。
如果你的应用是放在 URL 根路径之外的地方（如在 /myapplication 中，不在 / 中）， url_for() 会为你妥善处理。

### HTTP 方法

HTTP （ web 应用使用的协议）) 协议中有访问 URL 的不同方法。缺省情况下，一个路由 只回应 GET 请求，但是可以通过 methods 参数使用不同方法。例如:

```python
@app.route('/login', methods=['GET','POST'])
def login():
   if request.method == 'POST':
       do_the_login()
   else:
       show_the_login_form()
```

### 静态文件 

动态的 web 应用也需要静态文件，一般是 CSS 和 JavaScript 文件。理想情况下你的 服务器已经配置好了为你的提供静态文件的服务。在开发过程中， Flask 也能做好这个 工作。只要在你的包或模块旁边创建一个名为 static 的文件夹就行了。静态文件位于 应用的 /static 中。

使用选定的 'static' 端点就可以生成相应的 URL :

	url_for('static', filename='style.css')

这个静态文件在文件系统中的位置应该是 static/style.css 。

### 模板渲染 

在 Python 内部生成 HTML 不好玩，且相当笨拙。因为你必须自己负责 HTML 转义，以 确保应用的安全。因此， Flask 自动为你配置的 Jinja2 模板引擎。

使用 render_template() 方法可以渲染模板，你只要提供模板名称和需要 作为参数传递给模板的变量就行了。下面是一个简单的模板渲染例子:

```python
from flask import render_template

@app.route('/hello/')
@app.route('/hello/<name>')
def hello(name=None):
    return render_template('hello.html', name=name)
```

Flask 会在 templates 文件夹内寻找模板。因此，如果你的应用是一个模块，那么模板 文件夹应该在模块旁边；如果是一个包，那么就应该在包里面：

情形 1: 一个模块:

```
/application.py
/templates
    /hello.html
```

情形 2: 一个包:

```
/application
    /__init__.py
    /templates
        /hello.html
```

你可以充分使用 Jinja2 模板引擎的威力。更多内容，详见官方 Jinja2 模板文档 。

模板举例：

```
<!doctype html>
<title>Hello from Flask</title>
{% if name %}
  <h1>Hello {{ name }}!</h1>
{% else %}
  <h1>Hello World!</h1>
{% endif %}
```

在模板内部你也可以访问 request 、session 和 g [1] 对象，以及 get_flashed_messages() 函数。

模板在继承使用的情况下尤其有用，其工作原理 模板继承 方案 文档。简单的说，模板继承可以使每个页面的特定元素（如页头，导航，页尾）保持 一致。

### 请求对象

请求对象在 API 一节中有详细说明这里不细谈（参见 request ）。 这里简略地谈一下最常见的操作。首先，你必须从 flask 模块导入请求对象:

	from flask import request

通过使用 method 属性可以操作当前请求方法，通过使用 form 属性处理表单数据。以下是使用两个属性的例子:

```python
@app.route('/login', methods=['POST', 'GET'])
def login():
    error = None
    if request.method == 'POST':
        if valid_login(request.form['username'],
                       request.form['password']):
            return log_the_user_in(request.form['username'])
        else:
            error = 'Invalid username/password'
    # 如果请求访求是 GET 或验证未通过就会执行下面的代码
    return render_template('login.html', error=error)
```

当 form 属性中不存在这个键时会发生什么？会引发一个 KeyError 。如果你不 像捕捉一个标准错误一样捕捉 KeyError ，那么会显示一个 HTTP 400 Bad Request 错误页面。因此，多数情况下你不必处理这个问题。

要操作 URL （如 ?key=value ）中提交的参数可以使用 args 属性:

	searchword = request.args.get('key', '')

用户可能会改变 URL 导致出现一个 400 请求出错页面，这样降低了用户友好度。因此， 我们推荐使用 get 或通过捕捉 KeyError 来访问 URL 参数。

完整的请求对象方法和属性参见 request 文档。

### 文件上传

用 Flask 处理文件上传很容易，只要确保不要忘记在你的 HTML 表单中设置 enctype="multipart/form-data" 属性就可以了。否则浏览器将不会传送你的文件。

已上传的文件被储存在内存或文件系统的临时位置。你可以通过请求对象 files 属性来访问上传的文件。每个上传的文件都储存在这个 字典型属性中。这个属性基本和标准 Python file 对象一样，另外多出一个 用于把上传文件保存到服务器的文件系统中的 save() 方法。下例展示其如何运作:

```python
from flask import request

@app.route('/upload', methods=['GET', 'POST'])
def upload_file():
    if request.method == 'POST':
        f = request.files['the_file']
        f.save('/var/www/uploads/uploaded_file.txt')
    ...
```

如果想要知道文件上传之前其在客户端系统中的名称，可以使用 filename 属性。但是请牢记这个值是 可以伪造的，永远不要信任这个值。如果想要把客户端的文件名作为服务器上的文件名， 可以通过 Werkzeug 提供的 secure_filename() 函数:

```python
from flask import request
from werkzeug import secure_filename

@app.route('/upload', methods=['GET', 'POST'])
def upload_file():
    if request.method == 'POST':
        f = request.files['the_file']
        f.save('/var/www/uploads/' + secure_filename(f.filename))
    ...
```

更好的例子参见 上传文件 方案。

### Cookies

要访问 cookies ，可以使用 cookies 属性。可以使用请求对象 的 set_cookie 方法来设置 cookies 。请求对象的 cookies 属性是一个包含了客户端传输的所有 cookies 的字典。 在 Flask 中，如果能够使用 会话 ，那么就不要直接使用 cookies ，因为 会话比较安全一些。

读取 cookies:

```python
from flask import request

@app.route('/')
def index():
    username = request.cookies.get('username')
    # 使用 cookies.get(key) 来代替 cookies[key] ，
    # 以避免当 cookie 不存在时引发 KeyError 。
```

储存 cookies:

```python
from flask import make_response

@app.route('/')
def index():
    resp = make_response(render_template(...))
    resp.set_cookie('username', 'the username')
    return resp
```

注意， cookies 设置在响应对象上。通常只是从视图函数返回字符串， Flask 会把它们 转换为响应对象。如果你想显式地转换，那么可以使用 make_response() 函数，然后再修改它。

使用 延迟的请求回调 方案可以在没有响应对象的情况下设置一个 cookie 。

同时可以参见 关于响应 。

### 重定向和错误

使用 redirect() 函数可以重定向。使用 abort() 可以更早 退出请求，并返回错误代码:

```python
from flask import abort, redirect, url_for

@app.route('/')
def index():
    return redirect(url_for('login'))

@app.route('/login')
def login():
    abort(401)
    this_is_never_executed()
```

上例实际上是没有意义的，它让一个用户从索引页重定向到一个无法访问的页面（401 表示禁止访问）。但是上例可以说明重定向和出错跳出是如何工作的。

缺省情况下每种出错代码都会对应显示一个黑白的出错页面。使用 errorhandler() 装饰器可以定制出错页面:

```python
from flask import render_template

@app.errorhandler(404)
def page_not_found(error):
    return render_template('page_not_found.html'), 404
```

注意 render_template() 后面的 404 ，这表示页面对就的出错代码是 404 ，即页面不存在。缺省情况下 200 表示一切正常。

### 关于响应

视图函数的返回值会自动转换为一个响应对象。如果返回值是一个字符串，那么会被转换 为一个包含作为响应体的字符串、一个 200 OK 出错代码 和一个 text/html MIME 类型的响应对象。以下是转换的规则：

1. 如果视图要返回的是一个响应对象，那么就直接返回它。
2. 如果要返回的是一个字符串，那么根据这个字符串和缺省参数生成一个用于返回的 响应对象。
3. 如果要返回的是一个元组，那么元组中的项目可以提供额外的信息。元组中必须至少 包含一个项目，且项目应当由 (response, status, headers) 组成。 status 的值会重载状态代码， headers 是一个由额外头部值组成的列表或字典。
4. 如果以上都不是，那么 Flask 会假定返回值是一个有效的 WSGI 应用并把它转换为 一个响应对象。

如果想要在视图内部掌控响应对象的结果，那么可以使用 make_response() 函数。

设想有如下视图：

```python
@app.errorhandler(404)
def not_found(error):
    return render_template('error.html'), 404
```

可以使用 make_response() 包裹返回表达式，获得响应对象，并对该对象 进行修改，然后再返回：

```python
@app.errorhandler(404)
def not_found(error):
    resp = make_response(render_template('error.html'), 404)
    resp.headers['X-Something'] = 'A value'
    return resp
```

### 会话

除了请求对象之外还有一种称为 session 的对象，允许你在不同请求 之间储存信息。这个对象相当于用密钥签名加密的 cookie ，即用户可以查看你的 cookie ，但是如果没有密钥就无法修改它。

使用会话之前你必须设置一个密钥。举例说明:

```python
from flask import Flask, session, redirect, url_for, escape, request

app = Flask(__name__)

@app.route('/')
def index():
    if 'username' in session:
        return 'Logged in as %s' % escape(session['username'])
    return 'You are not logged in'

@app.route('/login', methods=['GET', 'POST'])
def login():
    if request.method == 'POST':
        session['username'] = request.form['username']
        return redirect(url_for('index'))
    return '''
        <form action="" method="post">
            <p><input type=text name=username>
            <p><input type=submit value=Login>
        </form>
    '''

@app.route('/logout')
def logout():
    # 如果会话中有用户名就删除它。
    session.pop('username', None)
    return redirect(url_for('index'))

# 设置密钥，复杂一点：
app.secret_key = 'A0Zr98j/3yX R~XHH!jmN]LWX/,?RT'
```

这里用到的 escape() 是用来转义的。如果不使用模板引擎就可以像上例 一样使用这个函数来转义。

### 日志

有时候可能会遇到数据出错需要纠正的情况。例如因为用户篡改了数据或客户端代码出错 而导致一个客户端代码向服务器发送了明显错误的 HTTP 请求。多数时候在类似情况下 返回 400 Bad Request 就没事了，但也有不会返回的时候，而代码还得继续运行 下去。

这时候就需要使用日志来记录这些不正常的东西了。自从 Flask 0.3 后就已经为你配置好 了一个日志工具。

以下是一些日志调用示例:

```
app.logger.debug('A value for debugging')
app.logger.warning('A warning occurred (%d apples)', 42)
app.logger.error('An error occurred')
```

logger 是一个标准的 Python Logger 类， 更多信息详见官方的 logging 文档 。



