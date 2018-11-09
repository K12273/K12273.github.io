---
layout:     post
title:      Django的 Flask1-4章节介绍
subtitle:   Flask
date:       2018-11-01
author:     YD
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
    - flask
    - 一~四章节

---

> 翻译自django官方文档，地址为：[Models](

centos7.5 升级python版本到python3.6参考博客地址：https://www.cnblogs.com/tssc/p/7762998.html

flask github参考地址：https://github.com/luhuisicnu/The-Flask-Mega-Tutorial-zh

解决维护不同应用程序对应不同版本的问题，安装虚拟版本

- 创建项目目录

```python
mkdir microblog
cd microblog
```

- 创建虚拟环境 (python3x内置)

- 激活虚拟环境

  第二个参数为自定义虚拟环境名字（你创建的）

```python
python -m venv venv
source venv/bin/activate
```

- 开始安装flask命令,安装前升级下pip的版本

  对flask的安装情况进行验证。

```python
pip install --upgrade pip
pip install flask
>>> import flask
```

- 创建一个应用

```python
mkdir app
cd app
vi __init__.py
>> from flask import Flask
>> app = Flask(__name__)
>> from app import routes
```

​	<u>1. 导入Flask类</u>

​	<u>2. app对象为Flask类对象的实例</u>

​	<u>3. routes模块的导入是为了解决循环导入的问题,位置很重要，</u>

- microblog目录下创建microblog.py 

```python
from app import app#从app包导入app应用程序实例
```

​	<u>从app包导入app成员变量</u>

- 导入运行的环境变量

```python
export FLASK_APP=microblog.py
flask run#启动应用
```

==问题：本机不能访问服务器中flask的web服务器的解决办法==

```
原因：flask的app.run()方法运行服务器应用，默认是只能在本机访问的！！！如果需要在其他机器上访问，需要修改为：app.run(host='0.0.0.0')
from flask import Flask
app = Flask(__name__)
@app.route('/')
def hello_world():
    return 'Hello World!'
if __name__ == '__main__':
    app.debug = True
    app.run(host='0.0.0.0')
```

https://blog.csdn.net/San_South/article/details/80659919

- 创建网站的模板

  `E:\3-练习库\microblog\app\templates\index.html`

```python
# 在视图中写html模板
from app import app

@app.route('/')
@app.route('/index')
def index():
    #扩展为html输出
    user={'username':'Miguel'}
    return '''
    <html>
        <head>
            <title>主页-微博</title>
        </head>
        <body>
            <h1>Hello,'''+ user['username']+'''!</h1>
        </body>
    </html>
    '''
```

> 模板的进阶
>
> **与django不同的是，传递给前端不是字典**
>
> **使用前引入渲染模板方法：render_template**
>
> **模板部分与django是一致的，使用jinjia2语法**
>
> ```django
> #视图
> def index():
>     #扩展html输出
>     user={'username':'Miguel'}
>     return render_template('index.html',title='Home',user=user)
> #模板
> <html>
>     <head>
>         <title>{{ title }} - Microblog</title>
>     </head>
>     <body>
>         <h1>Hello, {{ user.username }}!</h1>
>     </body>
> </html>
> ```
>
> **模板中的常见语法总结** 
>
> ```python
> # 条件语句
> 
> {% if title %}
> ...
> {% else %}
> ...
> {% endif %}
> 
> # 循环语句
> {% for post in posts %}
> ...
> {{ post.author.username }}
> ...
> {% endfor %}
> 
> #模板的继承
> {% extends "base.html" %}
> {% block content %}
> ...
> {% endblock %}
> ```

- web表单的使用

**使用第三方插件flask-wtf**

```python
pip3.6 install flask-wtf
```

####　flask的配置 

**创建配置文件与配置项**

```python
#microblog\config.py
import os
class Config(object):
    SECRET_KEY =os.environ.get('SECRET_KEY') or 'you-will-never-guess'
```

**引入配置文件并使用** 

**导入配置模块**

**使用app.config.from_object读取配置**

**通过键值对的访问方式获取配置app.config['SECRET_KEY']**

```python
#microblog\app\__init__.py
from flask import Flask
from config import Config#导入配置模块


app = Flask(__name__)
app.config.from_object(Config)#使用app.config.from_object方法读取配置
# 通过app.config['SECRET_KEY']访问配置项
from app import routes
```

#### 用户登陆表单的实现

E:\3-练习库\microblog\app\forms.py

***从第三方控件导入flask_form表单***

***从wtforms导入表单中需要使用到的字段。***

***导入表单需要用到的验证器对象***

```python
from flask_wtf import FlaskForm #从第三方控件导入flask_form表单
from  wtforms import StringField,PasswordField,BooleanField,SubmitField #导入表单中需要使用到的字段。
from wtforms.validators import DataRequired #导入表单需要用到的验证

class LoginForm(FlaskForm):#定义类继承上面的第三方对象
    username=StringField('Username',validators=[DataRequired()])# 参数一 表单的别名
    password=PasswordField('Password',validators=[DataRequired()])# 参数二 DataRequired（）这是个验证器，只能验证表单是否为空
    remember_me=BooleanField('Remember Me')
    submit=SubmitField('Sign In')
```

***验证器有了之后引入到视图文件中进行使用***

***实例化表单对象并传递给模板***

```python
from app.forms import LoginForm
#视图函数
@app.route('/login')
def login():
    form=LoginForm()
    return render_template('login.html',title='Sign In',form=form)
```

E:\3-练习库\microblog\app\templates\login.html

**创建并使用模板**

==novalidate:属性规定当提交表单时不对其进行验证,如果使用该属性，则表单不会验证表单的输入==

==form.hidden_tag():生成隐藏字段，包括csrf-token，需要在配置中配置 SECRET_KEY==

==form.username.label:字段的标签==

==form.username(size=32):字段的表格，可以传入参数 如size方法 你也可以设置id与class==

```html
{% extends 'base.html' %}
{% block content %}
    <h1>Sign In</h1>
    <form action="." method="post" novalidate><!--这里的这个novalidate是什么意思？-->
        {{ form.hidden_tag() }}<!--生成隐藏字段，包括csrf-token，需要在配置中配置 SECRET_KEY-->
        <p>
            {{ form.username.label }}<br><!--字段的标签-->
            {{ form.username(size=32) }}<!--字段的表格，可以传入参数 如size方法 你也可以设置id与class-->
        </p>
        <p>
            {{ form.password.label }}<br>
            {{ form.password(size=32) }}
        </p>
        <p>{{ form.remember_me() }} {{ form.remember_me.label }}</p>
        <p>{{ form.submit() }}</p>
    </form>
{% endblock %}
```

**接收表单的数据** 

`E:\3-练习库\microblog\app\templates\login.html`

==路由装饰器中的methods参数。 它告诉Flask这个视图函数接受GET和POST请求==

==form.validate_on_submit():验证表单的数据是否有post提交，如果是get提交返回false==

==flash()方法：闪存方法，flash表示向用户显示消息，临时存储数据。闪现方法，使用get_flashed_messages()方法调用一次后就消失了。==

==redirect（）页面跳转函数==

==url_for() url函数，相当于django的url视图方法中的参数name，url_for()的参数是endpoint名称，也就是视图函数的名字==

```python
from flask import render_template,flash,redirect,url_for #引入渲染模板
@app.route('/login',methods=['GET','POST']) #只允许接收这两种提交的方法。
def login():
    form=LoginForm()
    if form.validate_on_submit():#验证表单的数据是否有post提交，如果是get提交返回false

        flash('Login requested for user {},remember_me={}'.format(form.username.data,form.remember_me.data))#flash表示向用户显示消息，临时存储数据。闪现方法，调用一次后遍消失了。
        return redirect(url_for('index'))#页面跳转方法
    return render_template('login.html',title='Sign In',form=form)
```

`E:\3-练习库\microblog\app\templates\base.html`

**模板中的数据展示**

==with模板 定义变量接收获取的数据==

==get_flashed_messages()获取flash方法存放的数据==

```html
<html>
    <head>
        <title>{{ title }} - Microblog</title>
    </head>
<div>
    Microblog:
    <a href="{{ url_for('index') }}">Home</a>
    <a href="{{ url_for('login') }}">Login</a>
</div>
    <hr/>
{% with messages =get_flashed_messages() %}  <!--获取flash函数存放的数据-->
    {% if messages %}
        <ul>
        {% for message in messages %}
            <li>{{ message }}</li>
        {% endfor %}
        </ul>
    {% endif %}
{% endwith %}
<body>
{% block content %}
{% endblock %}
</body>
</html>
```

**完善模板中的表单验证**

==form.username.errors 方法验证表单的错误返回值==

```python
{% for error in form.username.errors %}
    <span style="color:red;">[{{ error }}]</span>
{% endfor %}
{% for error in form.password.errors %}
    <span style="color:red;">[{{ error }}]</span>
{% endfor %}
```

#### 数据库操作

##### flask关于数据库操作的扩展包如下

**Flask-SQLAlchemy插件** 是个操作数据库的ORM包

**flask-migrate** 是数据库迁移插件

##### 安装

```python
pip3.6 install flask-sqlalchemy
pip3.6 install flask-migrate
```

##### 配置数据的存放环境

microblog\config.py

==从环境变量中获取数据库的配置，未获取到就定义一个新的数据库的存放位置==

```python
import os
#数据库的初始化配置如下，主要是定义数据库文件的存放位置
basedir=os.path.abspath(os.path.dirname(__file__)) #获取项目的根路径
class Config(object):#创建配置项
    SECRET_KEY =os.environ.get('SECRET_KEY') or 'you-will-never-guess'
    SQLALCHEMY_DATABASE_URI = os.environ.get('DATABASE_URL') or 'sqlite:///'+os.path.join(basedir,'app.db')
    SQLALCHEMY_TRACK_MODIFICATIONS =False #当数据库发生变更后，是否发送信号给应用
```

##### 实例化数据库对象与数据库迁移绑定

microblog\app\__init__.py

==实例化SQLAlchemy对象==

==实例化数据库迁移对象Migrate(),进行绑定==

```python
from flask_sqlalchemy import SQLAlchemy
from flask_migrate import Migrate
db = SQLAlchemy(app) #实例化数据库对象
migrate=Migrate(app,db)#注册迁移
from app import routes,models  #导入自己创建的模型模块
```

##### 编写数据库模型

**导入db对象**

**创建模型类User继承db.Model**

**创建字段,定义调试打印用户的实例长度**

**db.Column()方法创建字段，参数有很多**

**指定字段的类型:db.Integer,db.String,db.DateTime**

**指定字段的唯一性:unique=True**

**指定是否设置为索引:index=True**

**指定默认值：default=datetime.utcnow**

**设置外键:db.ForeignKey(user_id)**

**`__repr__`该方法用于在用户调试的时候打印用户的实例 相当于__str__方法**

**db.relationship()指定外键,参数有很多总结如下：**

```python
from app import db
from datetime import datetime
class User(db.Model):
    id=db.Column(db.Integer,primary_key=True)
    username = db.Column(db.String(64),index=True,unique=True)
    email = db.Column(db.String(120),index=True,unique=True)
    password_hash=db.Column(db.String(128))
    posts=db.relationship('Post',backref='author',lazy='dynamic')

    def __repr__(self):#该方法用于在用户调试的时候打印用户的实例 相当于__str__方法
        return '<User {}>'.format(self.username)
class Post(db.Model):
    id=db.Column(db.Integer,primary_key=True)
    body=db.Column(db.String(140))
    timestamp=db.Column(db.DateTime,index=True,default=datetime.utcnow)#这是传入函数本身，没有使用()
    user_id=db.Column(db.Integer,db.ForeignKey('user.id'))
    def __repr__(self):
        return '<Post {}>'.format(self.body)
```

##### 迁移数据库步骤

```python
# 建立存储库 
flask db init
# 迁移数据库 
flask db migrate -m "users table"
# 更改数据库 
flask db upgrade
# 回滚上次的迁移 
flask db downgrade
```

数据的添加

```python
>>> from app import db
>>> from app.models import User, Post
>>> u = User(username='john', email='john@example.com')
>>> db.session.add(u) #添加
>>> db.session.commit()#提交
```

```python
users = User.query.all()#查询所有用户数据
u = User.query.get(1)#根据id查询用户
```

关联数据的添加

```python
>>> u = User.query.get(1)#获取用户的对象实例
>>> p = Post(body='my first post!', author=u)#实例化post对象
>>> db.session.add(p)
>>> db.session.commit()
```

更多查考官方文档：http://flask-sqlalchemy.pocoo.org/2.3/

删除对象实例

```python
db.session.delete(u)
db.session.commit()
```







