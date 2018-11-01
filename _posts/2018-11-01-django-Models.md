---
layout:     post
title:      Django的Models介绍
subtitle:   Models
date:       2018-11-01
author:     YD
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
    - Django
    - Models
---

> 手势在用户交互中有着举足轻重的作用，这篇文字简单的介绍了iOS中的手势，并通过手势对控件进行变形处

> 翻译自django官方文档，地址为：[Models](https://docs.djangoproject.com/en/2.1/topics/db/models/)

### django的模型知识

1. 简介

   一般而言，每个模型都映射到单个数据表。模型是单一明确的，关于你的数据的信息来源。它包含重要的字段和你所存储数据的行为。一般而言，每个模型映射到一个数据库当中的一张数据表。

2. 基础

   每个模型都是一个python类，这个类继承了父类`django.db.models.Model` 

   定义的每一个模型中的属性都代表一个数据库中的字段

   使用django的model，实际上django给了你一个自动生成数据库访问的接口（API）[点击查询](https://docs.djangoproject.com/en/2.1/topics/db/queries/) 

3. 简单实例

   以下的示例定义个一个`Person` 模型，模型中含有   `first_name`   ,``last_name`  

   ```python
   from django.db import models
   class Person(models.Model):
       first_name = models.CharField(max_length=30)
       last_name = models.CharField(max_length=30)
   ```

   `first_name` 与`last_name`  是模型的字段，每一个字段具体来说，就是类的属性，并且每个属性映射到一个数据库的表列（也就是数据库表中的字段）

   上面的`Person` 模型将会创建如下一个数据表：

   ```python
   CREATE TABLE myapp_person (
       "id" serial NOT NULL PRIMARY KEY,
       "first_name" varchar(30) NOT NULL,
       "last_name" varchar(30) NOT NULL
   )
   ```

   一些技术笔记:

   - 在数据库中，生成的表名为 `myapp_person` 多了一个应用名前缀 `myapp_` 它是自动从一些模型元数据中派生出来的`metadata ` ,但是能够被覆盖，查看更多细节[点这里](https://docs.djangoproject.com/en/2.1/ref/models/options/#table-names) 
   - 数据库中的id字段是被自动添加的，但是这个行为能够被覆盖改写，看[自动主键字段](https://docs.djangoproject.com/en/2.1/topics/db/models/#automatic-primary-key-fields) 
   - 在上面的列子中，`CREATE TABLE` 创建表的sql语句使用的是`PostgreSQL` 数据库语句的参数形式，但是，值得注意的是，django会根据你的django的`setting.py` 配置，量身定制不同数据库配置的sql形式。

   4. 使用models

      一旦你定义了你的模型，你需要告诉django你需要去使用这些模型。怎么做呢？编辑你的`settings.py` 文件，并且修改这个 `INSTALLED_APPS` 配置，去添加包含`models.py` 文件的模块名子(app_name)

      例如,如果你定义的应用模型是在`myapp` 下面的 `models.py` 文件当中， `INSTALLED_APPS`  应当这样配置：

      ```python
      INSTALLED_APPS = [
          #...
          'myapp',
          #...
      ]
      ```

      当你配置了新的应用到 `INSTALLED_APPS` 一定要运行 `manage.py migrate` ,可以选择首次使用 `manage.py makemigrations` 迁移他们。

   5. 字段

      模型中最重要的部分，并且是模型中唯一需要的一部分，是django定义的数据库字段列表，字段具体为类的属性，但是一定要注意，不要选择字段名字跟你的模型API的关键字冲突，如 `clean` ,`save` ,`delete` 列如：

      ```python
      from django.db import models
      
      class Musician(models.Model):
          first_name = models.CharField(max_length=50)
          last_name = models.CharField(max_length=50)
          instrument = models.CharField(max_length=100)
      
      class Album(models.Model):
          artist = models.ForeignKey(Musician, on_delete=models.CASCADE)
          name = models.CharField(max_length=100)
          release_date = models.DateField()
          num_stars = models.IntegerField()
      ```

   6. 字段的类型

      在你模型里面的每个字段应该是适合该字段类的实例，[点击参考](https://docs.djangoproject.com/en/2.1/ref/models/fields/#django.db.models.Field) django会使用这个字段的类型去判断一些事情

      - `column type`  使用告诉数据库，去存储哪种类型的数据（e.g.`INTEGER`, `VARCHAR`, `TEXT`）
      - 这个默认的HTML 控件，当需要呈现一个表单字段的时候去使用（e.g. `<input type="text">`, `<select>`） 
      - 一般涉及较少表单验证的需求，如被使用在django的后台和一些自动生成表单中

      django附带有很多的字段类型，你能够找到完整的列表[在这里](https://docs.djangoproject.com/en/2.1/ref/models/fields/#model-field-types) 当然，如何django内置的字段类型不奏效的话，你也可以[参考这里](https://docs.djangoproject.com/en/2.1/ref/models/fields/#model-field-types) 自定义字段。

   7. 字段的参数

      每个字段都有特定的一些参数，（参考文档 [模型字段参考](https://docs.djangoproject.com/en/2.1/ref/models/fields/#model-field-types)）列如`CharField` 字段与他的子类都需要一个`max_length` 参数，去指定`varchar` 数据库字段的大小，以便于用来存储数据。

      对所有的字段来说，有一些公共的参数是需要的，这些参数对字段来说是可选的，这些公共字段的解说[参考这里](https://docs.djangoproject.com/en/2.1/ref/models/fields/#common-model-field-options) 

      下面这里对经常使用的一些参数进行了总结。

      `null` : 默认为`False`,如果为`True` ,django将会存储空值作为`NULL` 值的形式展现

      `blank`: 如果为`True ` 这个字段是被允许是空白的，默认是`False`  

      > 请注意:
      >
      > 这个字段是与`null` 字段不同的，`null` 纯粹是与数据库相关的（database-related），然而，`blank` 是与验证相关的（validation-related），比如说，这个`blank=True`,表单验证将会允许键入一个空值，`blank=False`,这个表单必须要填写值。

      `choices` :一个可迭代的双层元祖，使用为这个字段，提供可选择的元素范围，如果使用了这个参数，默认的表单控件将会成为一个选择框，而不是一个标准的文本字段，并且，你只能从被给的选择中做出选择。

      > 列如:
      >
      > ```python
      > YEAR_IN_SCHOOL_CHOICES = (
      >     ('FR', 'Freshman'),
      >     ('SO', 'Sophomore'),
      >     ('JR', 'Junior'),
      >     ('SR', 'Senior'),
      >     ('GR', 'Graduate'),
      > )
      > ```
      >
      > 每个元祖中的第一个元素是将要存储在数据库中的值，第二个元素是通过表单控件展示出来给用户看的值。
      >
      > 下面给了一个模型实例，为这个字段所陈列的值，使用了`choices` 参数，可以通过 `get_F00_display()` 方法获取到这些陈列的值。[参考链接](https://docs.djangoproject.com/en/2.1/ref/models/instances/#django.db.models.Model.get_FOO_display) 
      >
      > ```python
      > from django.db import models
      > 
      > class Person(models.Model):
      >     SHIRT_SIZES = ( 		#在模型的内部定义了一个二元元祖
      >         ('S', 'Small'),
      >         ('M', 'Medium'),
      >         ('L', 'Large'),
      >     )
      >     name = models.CharField(max_length=60)
      >     shirt_size = models.CharField(max_length=1, choices=SHIRT_SIZES)
      >     #属性的使用
      > ```
      >
      > ```python
      > >>> p = Person(name="Fred Flintstone", shirt_size="L")#获取实例对象
      > >>> p.save()
      > >>> p.shirt_size#获取存储在数据库中的值
      > 'L'
      > >>> p.get_shirt_size_display()#获取form表单展示的值
      > 'Large'
      > ```

      `default` : 这个字段的默认值，它可能是一个值或者一个可调用的对象，如果默认是一个可调用的对象，每次被访问的时候，它将会创建一个新的对象。

      `help_text` : 使用表单控件展示额外的帮助文本，对于一个文件来说，这是有用的，虽然你的字段没有被用在form表单上。

      `primary_key` 如果设置为`True` ,这个字段就是这个模型的主键

      如果你的模型的任何字段没有指定 `primary_key=True`，django将会自动的添加 `IntegerField` 去约束主键，这样，你不需要刻意设置主键在你的字段当中的任何一个上，除非你想要覆盖默认的主键，想要了解更多的话，[点击这里](https://docs.djangoproject.com/en/2.1/topics/db/models/#automatic-primary-key-fields)  

      这个主键字段是可读的，如果你改变了现有对象的主键值，并且保存操作之后，一个新对象将会被创建与原有的对象一起并列。

      > ```python
      > from django.db import models
      > class Fruit(models.Model):
      >     name = models.CharField(max_length=100, primary_key=True)#将name设置为主键
      > ```
      >
      > ```python
      > >>> fruit = Fruit.objects.create(name='Apple')
      > >>> fruit.name = 'Pear'
      > >>> fruit.save()
      > >>> Fruit.objects.values_list('name', flat=True)
      > <QuerySet ['Apple', 'Pear']>#修改主键后，新生成一个对象
      > ```

      `unique` 如果设置为`True` 这个字段的值在整个数据表中是唯一的。

4. 自动主键字段

   默认情况下，django给每个模型下面的字段

   ```python
   id = models.AutoField(primary_key=True)
   ```

   这是一个自增的主键

   如果你想要指定一个自定义主键，只要在你的字段上指定 `primary_key=True` ,如果django看到你已经明确设置了主键，django不会添加自增id字段啦。

   每个模型都需要明确一个字段设置`primary_key=True` (要么明确声明，要么自动添加)

5. 详细的字段名

   每个字段类型，除了`ForeignKey`,`ManyToManyField`,`OneToOneField`之外，接收一个可选的第一位置参数---一个详细的名字，如果这个详细的字段名字没有被给的话，django将会自动的使用字段的属性名字来创建它，通过间隔来转化强调。

   列如:设置这个详细名字为`"person's first name"`:

   ```python
   first_name = models.CharField("person's first name", max_length=30)#使用空格来强调
   
   #在下面的这个例子中，这个详细的名字为字段名字
   first_name = models.CharField(max_length=30)
   ```

   如果是`ForeignKey`,`ManyToManyField`,`OneToOneField` 这三个字段类型的话，需要第一个参数是一个模型类，因此，详细的字段说明需要使用`verbose_name` 关键字参数。示例如下:

   ```python
   poll = models.ForeignKey(
       Poll,#模型类
       on_delete=models.CASCADE,
       verbose_name="the related poll",#关键字verbose_name
   )
   sites = models.ManyToManyField(Site, verbose_name="list of sites")
   place = models.OneToOneField(
       Place,
       on_delete=models.CASCADE,
       verbose_name="related place",
   )
   ```

   约定习俗并不需要给`verbose_name`的首字母大写，但是django会在需要的时候自动的给他首字母大写。

6. 关系（Relationships）

   很明显地，关系型数据库的魅力(power,不知道翻译成什么比较好)就在于表与表的相互关系。django提供了几种方式去定义这三种最普遍的数据库关系类型： `many-to-one`, `many-to-many` and `one-to-one`  

   - **多对一关系**

     定义多对一的关系，使用`django.db.models.ForeignKey`，[点解参考这里](https://docs.djangoproject.com/en/2.1/ref/models/fields/#django.db.models.ForeignKey)  可以使用它就想任何字段类型一样：引入它作为模型的类属性。

     `ForeignKey` 必须要一个位置参数：与之相关的类模型（是个模型名字）

     例如，如果一个Car模型`Manufacturer` (制造商，厂商)字段，那就是说，一个`Manufacturer` 可以制造多亮车，但是每一辆车只能有一个制造商，，使用下面的定义

     ```python
     from django.db import models
     
     class Manufacturer(models.Model):
         # ...
         pass
     
     class Car(models.Model):
         manufacturer = models.ForeignKey(Manufacturer, on_delete=models.CASCADE)
         # ...#给一对多的多添加外键
     ```

     你也可以创建一个[递归关系](https://docs.djangoproject.com/en/2.1/ref/models/fields/#recursive-relationships) （一个对象自身有一个多对一的关系）和[没有定义的关系关系模型](https://docs.djangoproject.com/en/2.1/ref/models/fields/#lazy-relationships)，具体查看[模型字段参考](https://docs.djangoproject.com/en/2.1/ref/models/fields/#ref-foreignkey) 

     外键的字段名字（如`manufacturer` 在上面的示例中）最好是模型的名字，小写，只是被建议，但不是必须的，当然，你也可以把这儿字段命名成你想要命名的，例如

     ```python
     class Car(models.Model):
         company_that_makes_it = models.ForeignKey(
             Manufacturer,
             on_delete=models.CASCADE,
         )
         # ...
     ```

     | 另见参考                                                     |
     | ------------------------------------------------------------ |
     | `ForeignKey` 字段接收大量的额外参数，这些参数被解释在[模型字段参考中](https://docs.djangoproject.com/en/2.1/ref/models/fields/#foreign-key-arguments) ，这些选项帮助定义这个关系应该怎么工作，所有的参数都是可选的。 |
     | 有关访问backwards-related 对象，参考下面的[关系后台实例](https://docs.djangoproject.com/en/2.1/topics/db/queries/#backwards-related-objects) |
     | 对示例代码，看[多对一关系模型示例](https://docs.djangoproject.com/en/2.1/topics/db/examples/many_to_one/) |

   - **多对多关系** 

     为了定义多对多的关系，使用`ManyToManyField`,你能够使用它就想别的字段类型一样，通过引入它作为模型的类属性。

     `ManyToManyField` 需要一个位置参数，就是相关类模型

     例如，一个Pizza(披萨饼)有多个Topping(装饰配料)对象，那就是说，一个装饰配料能够在多个披萨上面，每个披萨上面有多个装饰配料，以下给出示例代码

     ```python
     from django.db import models
     
     class Topping(models.Model):
         # ...
         pass
     
     class Pizza(models.Model):
         # ...
         toppings = models.ManyToManyField(Topping)
     ```

     你也可以创建一个[递归关系](https://docs.djangoproject.com/en/2.1/ref/models/fields/#recursive-relationships) （一个对象自身有多对多的关系）和[没有定义的关系关系模型](https://docs.djangoproject.com/en/2.1/ref/models/fields/#lazy-relationships)，具体查看[模型字段参考](https://docs.djangoproject.com/en/2.1/ref/models/fields/#ref-foreignkey) 

     建议多对多的字段的命名应该是一个复数，来描述这个相关模型对象的集合。

     相关模型中的哪个模型去选择使用多对多的字段，是没有关系的，（你给上面的Topping使用多对多的字段也是可以的）

     一般而言，多对多的实例应该放在将被编辑的模型对象里面，什么意思呢？比如在上面的实例当中，一般我们会描述，topping是在披萨上面，（而不是Topping有一个多对多字段的披萨），因为它是更加自然=的想到，一个披萨有许多的toppings，而不是一个topping是在许多的披萨上面，上面的实例的设置的方式，这个披萨表单会让用户选择toppings。

     简而言之一句话，配料是放在披萨上面的，并且是用户去选择多种披萨的配料，所以将多对多的字段放在了披萨模型对象当中。

     ==更多多对多实例参考链接==[点击这里](https://docs.djangoproject.com/en/2.1/topics/db/examples/many_to_many/) 

     多对多字段也接收许多额外的参数，跟多对一的相似，参考上面链接。

     **关于多对多关系的额外字段** 

     当你只是简单的处理多对多的关系，比如披萨与配料的混合匹配，一个标准的多对多字段是你所需要的（ManyToManyField）,然而，有时候你也许需要把数据与2个模型关系进行关联。

     比如，这个情况，一个应用程序要去追踪音乐家属于哪个音乐组里面，在一个人与组（组里面是会员）之间就存在多对多的关系，因此你可能使用多对多的字段去表示这种关系，然而，组里面的会员有很多详细信息你可能想要去收集，比如这个人加入这个组的日期等等、

     针对这种情况，django允许你去指定哪个模型去掌控多对多的关系，然后你也可以在中间模型当中设置额外的字段，这个中间件模型是跟多对多字段有关系的，多对多字段是通过`Through`关键字参数指定到中间模型上的。

     比如音乐家的例子，代码如下，

     ```python
     from django.db import models
     
     class Person(models.Model):
         name = models.CharField(max_length=128)
     
         def __str__(self):
             return self.name
     
     class Group(models.Model):
         name = models.CharField(max_length=128)
         members = models.ManyToManyField(Person, through='Membership')
         #与中间模型进行关联
         # through 关键字
         def __str__(self):
             return self.name
     
     class Membership(models.Model):#中间模型，用来收集组内成员 的信息
         person = models.ForeignKey(Person, on_delete=models.CASCADE)#①
         group = models.ForeignKey(Group, on_delete=models.CASCADE)#②
         date_joined = models.DateField()
         invite_reason = models.CharField(max_length=64)
     ```

     但你设置了中间模型之后，你要明确的指出涉及到多对多关系模型的外键，显示的声明这2个模型的关系。如①与②。

     中间模型也是有一些限制条件的：

     - 你的中间模型必须包含有且只有一个外键到这个源模型上（在我们的例子中，就是这个Group模型，Group就是这个中间模型的源模型）或者你必须显示的指定外键，django将会使用`ManyToManyField.through_fields` 来用在这个关系上。如果你有多于不只一个外键并且`through_fields` 没有被指定，一个异常将会被抛出，这个相似的限制也适用于目标模型的外键上（如示例中的Person模型）
     - 对于一个模型，通过中间模型本身就是一个多对多的关系模型，同一个模型被允许有2个外键，但是他们将被视为多对多关系的 2（不同）边，如果有多于2个外键through，你也必须指定`through_fields` （同上面的实例），否则抛出错误异常。
     - 当从一个模型自身利用一个中间模型定义一个多对多的关系，你必须使用`symmetrical=False`  [详情参考这里](https://docs.djangoproject.com/en/2.1/ref/models/fields/#manytomany-arguments)

     既然你已经设置你的多对多字段去使用你的中间模型（如上面的Membership）。接下里，你就准备去开始创建一些多对多的关系吧（如何操作中间模型数据)，你要实现这个要通过创建中间模型的实例。

     ```python
     >>> ringo = Person.objects.create(name="Ringo Starr")
     >>> paul = Person.objects.create(name="Paul McCartney")
     >>> beatles = Group.objects.create(name="The Beatles")
     >>> m1 = Membership(person=ringo, group=beatles,
     ...     date_joined=date(1962, 8, 16),
     ...     invite_reason="Needed a new drummer.")#创建中间模型的实例之前 创建关系模型对象
     >>> m1.save()
     >>> beatles.members.all()#通过组去获取人这个对象
     <QuerySet [<Person: Ringo Starr>]>
     >>> ringo.group_set.all()#通过人来反向查询组 获取对应的组对象
     <QuerySet [<Group: The Beatles>]>
     >>> m2 = Membership.objects.create(person=paul, group=beatles,
     ...     date_joined=date(1960, 8, 1),
     ...     invite_reason="Wanted to form a band.")
     >>> beatles.members.all()
     <QuerySet [<Person: Ringo Starr>, <Person: Paul McCartney>]>
     ```

     **注意** 不想一般的多对多字段，你不能使用add(),create(),或者set()去创建这个关系。

     ```python
     >>> # The following statements will not work
     >>> beatles.members.add(john)
     >>> beatles.members.create(name="George Harrison")
     >>> beatles.members.set([john, paul, ringo, george])
     ```

     为什么呢？你不单单创建了介于Person与一个Group之间的关系，你需要指定所有的细节信息给这个Membership模型，这简单的`add`,`create`和`assignment` 调用不能提供一个方式去指定额外的细节，因此，这些方法不能被使用俩操作多对多关系的中间模型，实现这种关系类型的唯一方式就是去创建中间模型的实例。

     `remove()` 不能被使用也是因为这个原因，例如，按照惯例，由中间模型创建的数据表不能够强制唯一在这个（模型1，模型2）对上，一个`remove()`方法的调用不能够提供足够的信息以至于哪个中间模型实例应该被删除。

     ```python
     >>> Membership.objects.create(person=ringo, group=beatles,
     ...     date_joined=date(1968, 9, 4),
     ...     invite_reason="You've been gone for a month and we miss you.")
     >>> beatles.members.all()
     <QuerySet [<Person: Ringo Starr>, <Person: Paul McCartney>, <Person: Ringo Starr>]>
     >>> # This will not work because it cannot tell which membership to remove
     >>> beatles.members.remove(ringo)
     ```

     但是，这个`clear()` 方法能够被用来移除所有的多对多关系

     ```python
     >>> # Beatles have broken up
     >>> beatles.members.clear()
     >>> # 注意，这个方法删除的是中间模型的对象
     >>> Membership.objects.all()
     <QuerySet []>
     ```

     一旦你通过中间模型实例建立了多对多关系，你能够执行查询，就想一般的多对多关系，你能够使用多对多关系模型的属性去查询。

     ```python
     # Find all the groups with a member whose name starts with 'Paul'
     >>> Group.objects.filter(members__name__startswith='Paul')#学习这种方法。
     <QuerySet [<Group: The Beatles>]>
     ```

     当你使用中间模型的时候，你也能够查询他的属性

     ```python
     # Find all the members of the Beatles that joined after 1 Jan 1961
     >>> Person.objects.filter(
     ...     group__name='The Beatles',
     ...     membership__date_joined__gt=date(1961,1,1))
     <QuerySet [<Person: Ringo Starr]>
     ```

     如果你需要获取一个成员信息，你也许可以直接查询中间模型

     ```python
     >>> ringos_membership = Membership.objects.get(group=beatles, person=ringo)
     >>> ringos_membership.date_joined
     datetime.date(1962, 8, 16)
     >>> ringos_membership.invite_reason
     'Needed a new drummer.'
     ```

     获取同样信息的另外一种方法就是通过查询 [多对多反向关系](https://docs.djangoproject.com/en/2.1/topics/db/queries/#m2m-reverse-relationships)  从一个Person对象中

     ```python
     >>> ringos_membership = ringo.membership_set.get(group=beatles)
     >>> ringos_membership.date_joined
     datetime.date(1962, 8, 16)
     >>> ringos_membership.invite_reason
     'Needed a new drummer.'
     ```

   - **一对一字段** 

     一对一字段也是需要个位置参数，是与之相关的模型类

     例如，如果你正在建一个数据库叫"place",你将会建非常标准的材料在这个数据库里面，诸如地址、电话号码等等，然后你要在这个"place"上面再建造一个"``Restaurant` " 数据库，在`Restaurant`  你不想重复和再复制同样的字段的话，你能够使用  `Restaurant`  去一对一关联  `Place` (因为一个饭店是一个地方，实际上，为了处理这个情况，你通常会使用继承，这里涉及到一个隐式的一对一关系) 

     就`ForeignKey` 说来，一个递归关系能够被定义，未定义的参数模型也能被使用，参上。

     ==查看一对一模型更多的实例== [点解链接](https://docs.djangoproject.com/en/2.1/topics/db/examples/one_to_one/) 

     `OneToOneField` 字段也接收一个可选的参数`parent_link` [点解链接](https://docs.djangoproject.com/en/2.1/ref/models/fields/#django.db.models.OneToOneField.parent_link) 

     一对一字段类用来自动生成一个模型的主键，这不再是准确的的（尽管你能够在主键里面人为的传递参数），在一个模型中，有可能有多个一对一类型的字段。

   - 模型传递文件（Models across files）

     让一个模型与另外一个app应用联系是完全可以的，为了能够实现这个，需要在文件的顶部导入这个相关模型，然后，就参考别的模型类的使用方法去使用就可以了。

     ```python
     from django.db import models
     from geography.models import ZipCode	#引入应用的模型类
     
     class Restaurant(models.Model):
         # ...
         zip_code = models.ForeignKey(
             ZipCode,
             on_delete=models.SET_NULL,
             blank=True,
             null=True,
         )
     ```

   - 字段命名的限制

     django的模型字段命名有2个限制

     1. 一个模型字段名字不能是django的保留字，这样容易导致python参数错误。

        ```python
        class Example(models.Model):
            pass = models.IntegerField() # 'pass' is a reserved word!
        ```

     2. 模型字段中不能有2个及以上的下划线，因为django的查询是依据下划线查找的，例如

        ```python
        class Example(models.Model):
            foo__bar = models.IntegerField() # 'foo__bar' has two underscores!
        ```

     这些限制不影响你对模型的使用，因为你的字段名字不是必须同你的数据库字段名匹配。[详情看链接](https://docs.djangoproject.com/en/2.1/ref/models/fields/#django.db.models.Field.db_column) 

     SQL保留字，比如`join` `where` `select` 是被允许作为模型的字段名字的，因为django避开了所有的数据库表名字与字段名字，在每个潜在的sql查询语句上，他使用了特别的数据库引擎的引用参数。 

   - 自定义字段类型。

     如果已经存在的模型字段跟你的使用目的不匹配的，或者如果你希望利用较少的公共数据列表类型，你是可以创造你自己的字段类的，创建自己的字段，[参考地址](https://docs.djangoproject.com/en/2.1/howto/custom-model-fields/) 

   - Meta 参数

     通过使用内部类Meta来给你的模型添加原数据

     ```python
     from django.db import models
     
     class Ox(models.Model):
         horn_length = models.IntegerField()
     
         class Meta:#内部类
             ordering = ["horn_length"]#定义按照什么来排序
             verbose_name_plural = "oxen"#定义详细的说明
     ```

     **模型元数据** 可以使除了字段以外的其他东西，诸如：排序选项([ordering](https://docs.djangoproject.com/en/2.1/ref/models/options/#django.db.models.Options.ordering));数据表名字([db_table](https://docs.djangoproject.com/en/2.1/ref/models/options/#django.db.models.Options.db_table)),或者人类易理解的单复数名字（[verbose_name](https://docs.djangoproject.com/en/2.1/ref/models/options/#django.db.models.Options.verbose_name) 和 [verbose_name_plural](https://docs.djangoproject.com/en/2.1/ref/models/options/#django.db.models.Options.verbose_name_plural)）这些都是可选的，不是必须参数，而且，添加Meta类完全是可选的。

     一个完整的Meta选项的参数列表能够[从这里找到](https://docs.djangoproject.com/en/2.1/ref/models/options/) 

   - **模型属性** 

     `objects` 模型最重要的属性之一是 `Manager` [点解了解](https://docs.djangoproject.com/en/2.1/topics/db/managers/#django.db.models.Manager) ，他是数据库查询操作到django模型的接口，并且被用来从数据库[检索实例](https://docs.djangoproject.com/en/2.1/topics/db/queries/#retrieving-objects) ，Manager是被定义的，这个默认的名字是`objects`（[参考](https://docs.djangoproject.com/en/2.1/ref/models/class/#django.db.models.Model.objects)）,Manager是唯一可直接进入到模型类中，而不是模型的实例中。

<hr>

​	

- 模型的方法

  定义自定义一个模型的方法来添加自定义“行级”功能对象。然而`Manager` 方法旨在做 “table-wide” 的东西，模型方法应该模型实例上起作用。[参考](https://docs.djangoproject.com/en/2.1/topics/db/managers/#django.db.models.Manager) 

  这是使业务逻辑保持在同一个地方上有价值的技术-模型

  例如，这个模型有一些定制的方法：

  ```python
  from django.db import models
  
  class Person(models.Model):
      first_name = models.CharField(max_length=50)
      last_name = models.CharField(max_length=50)
      birth_date = models.DateField()
  
      def baby_boomer_status(self):
          "Returns the person's baby-boomer status."
          import datetime
          if self.birth_date < datetime.date(1945, 8, 1):
              return "Pre-boomer"
          elif self.birth_date < datetime.date(1965, 1, 1):
              return "Baby boomer"
          else:
              return "Post-boomer"
  
      @property
      def full_name(self):
          "Returns the person's full name."
          return '%s %s' % (self.first_name, self.last_name)
  ```

  这个最后一个方法在这个例子中是一个[属性](https://docs.djangoproject.com/en/2.1/glossary/#term-property) 

  这个[模型实例参考](https://docs.djangoproject.com/en/2.1/ref/models/instances/) 有一个完整的已给的每个模型方法的列表，你能够重写他们中的大部分，[看重写模型方法的参考](https://docs.djangoproject.com/en/2.1/topics/db/models/#overriding-predefined-model-methods) ，在下面，有一些你经常定义重写的方法。

  `__str__()` python的魔术方法，返回任何对象的字符窜表示方法，一个模型实例当需要被强制显示成一个字符窜表示的时候，python和django都会使用这个魔术方法。最显著的，当你要在交互模式下列出一个对象或者在后台当中，你就会使用这个方噶

  你总是想要定义这个方法，这个方法的默认不是非常有用的。

  `get_absolute_url()` 

  这个方法是告诉django怎么为一个对象去计算这个url，django在他的后台接口中使用这个方法，并且在任何需要的时候给一个对象算出一个url地址

  一个对象当需要一个url的时候，应该标识定义这个方法。

- **重写预定义模型方法** 

  另外一组模型方法，他们封装了一大群数据库的行为，你想要去定制，尤其是你经常想要去修改方法`save()` 和`delete()` 工作方式。

  你可以随意的重写这些方法，并且更改他们的表现

  一个经典的重写内置方法的使用方式是，如果你想让一些东西被触发，无论什么时候你保存一个对象，例如，（看`save()`方法的参数部分[点击这里](https://docs.djangoproject.com/en/2.1/ref/models/instances/#django.db.models.Model.save)） 

  ```python
  from django.db import models
  
  class Blog(models.Model):
      name = models.CharField(max_length=100)
      tagline = models.TextField()
  
      def save(self, *args, **kwargs):#重写save()方法
          do_something()
          super().save(*args, **kwargs)  # Call the "real" save() method.
          do_something_else()
  ```

  你也能够阻止保存该方法

  ```python
  from django.db import models
  
  class Blog(models.Model):
      name = models.CharField(max_length=100)
      tagline = models.TextField()
  
      def save(self, *args, **kwargs):
          if self.name == "Yoko Ono's blog":
              return # Yoko shall never have her own blog!
          else:
              super().save(*args, **kwargs)  # Call the "real" save() method.
  ```

  这是非常重要的去记住调用父类的方法，如super().save(*args,**kwargs)这个事情，为了确保这个对象仍然能够保存到数据库当中，如果你忘记调用父类的方法，这个默认的行为不将会发生，数据库不会有任何改变。

  同样重要的是，你通过参数传递给模型方法，这是`*args` 与`**kwargs`所要做的，django将会不时的扩展内置方法的功能，增加一些新的参数，如果使用`*args`,`**kwargs` 在你的方法定义中，你要保证你的代码能够自动的支持这些被添加的新的参数。

  注意：请注意这个`delete()` 方法对于一个对象来说是非必须调用的，[当批量使用一个查询集合删除对象](https://docs.djangoproject.com/en/2.1/topics/db/queries/#topics-db-queries-delete) 或者由于[级联删除](https://docs.djangoproject.com/en/2.1/ref/models/fields/#django.db.models.ForeignKey.on_delete) 去保障定制删除得以执行，你能够使用 [pre_delete](https://docs.djangoproject.com/en/2.1/ref/signals/#django.db.models.signals.pre_delete) 或者 [post_delete](https://docs.djangoproject.com/en/2.1/ref/signals/#django.db.models.signals.post_delete) 信号

  但是不幸的是，没有创建或者更新对象的批量解决方法，因为灭幼`save()`,`pre_save()`和`post_save` 方法被调用。

- 执行定制的sql

  另外一种常见的模式是在模型方法和模块级方法中书写定制的sql声明，更多使用行sql的详细细节，[请查看行sql的文档](https://docs.djangoproject.com/en/2.1/topics/db/sql/) 

- 模块的继承

  在django中的模块继承的工作方法原理与python 中的一般类继承的方式是几乎相同的，但是基础仍然是遵循基础部分，那就是意味这这个基础类应当继承`django.db.models.Model` 

  你要做的唯一决定是是否你想要父类模型用他们自己的权利（就是使用他们自己的数据表）。或者如果这个模型父类只是公共信息的拥有者，那么这样的话只能通过子类模型可见。

  在django中有三种风格的继承方式。

  1. 经常，你只想要父类模型去持有信息，你不想要给每个继承他的子类去输入这个信息，这个类不将会被孤立使用，因此，随后就跟着[抽象基类](https://docs.djangoproject.com/en/2.1/topics/db/models/#abstract-base-classes) 
  2. 如果你正在使一个已经存在的模型成为子类（也许完成来自于另外一个应用），并且每个模型都拥有他自己的数据表，[多表继承](https://docs.djangoproject.com/en/2.1/topics/db/models/#multi-table-inheritance)是个很好的方式。
  3. 最后，如果你只是想要去修改一个模型的python级别的行为，不使用任何方式去修改模型字段，你能够使用[代理模型](https://docs.djangoproject.com/en/2.1/topics/db/models/#proxy-models) 

- 抽象基类

  当你想要把一些公共的信息放到许多别的模型中的时候，抽象基类是非常有用的，你写你的基类，并且设置`abstract=True` 在你的Meta内部类中，这个模型不会被用于创建数据表，相反，当被当成一个基类使用的话，这个抽象基类的字段被添加到继承他的子模型类当中了。

  给出示例：

  ```python
  from django.db import models
  
  class CommonInfo(models.Model):
      name = models.CharField(max_length=100)
      age = models.PositiveIntegerField()
  
      class Meta:#抽象类
          abstract = True
  class Student(CommonInfo):#继承上面的抽象基类
      home_group = models.CharField(max_length=5)
  ```

  这个学生模型将会有三个字段name,age,和home_group,这个CommonInfo模型不能够被使用作为一个一般的django模型，因为他是一个抽象基类，它不能生成一个数据表或者有一个manager,并且不能被实例化或者直接被保存。

  从抽象基类中继承的字段能够被重写，通过使用另一个字段或者值，或者使用`None`去移除。

  对于许多的使用，这类的模型继承将会明确你想要的是什么，他提供了一种方式，就是在python级别提取出公共信息，然后，子类模型仍然只在数据库级别创建一个数据表。

- Meta继承

  当一个抽象基类被创建之后，django会让任何一个你声明的Meta内部类在基类中作为一个属性可以获取，如果一个子类没有声明它自己的Meta类，它将会继承父亲的Meta类，如果这个子类想要扩展父类的Meta类，它能够继承它，例如：

  ```python
  from django.db import models
  
  class CommonInfo(models.Model):
      # ...
      class Meta:
          abstract = True
          ordering = ['name']
  
  class Student(CommonInfo):
      # ...
      class Meta(CommonInfo.Meta):#继承父类的Meta类
          db_table = 'student_info'
  ```

  django给抽象基类Meta类做过一个调整：在正式使用Meta属性之前，它会设置`abstract=False`,这个意思是抽象基类的子类不会自动变成抽象基类。当然，你也能够使一个抽象基类继承另外一个抽象基类，你要记住每次都要设置`abstract=True` 

  一些属性的设置对抽象基类来说是没有意义的，例如，`db_table` 的意思是所有的子类（哪些子类呢？没有指定他们自己的Meta属性的）将会使用相同的数据表，几乎可以确信不是你想要的。

- 小心使用`related_name` 和`related_query_name` 属性

  如果你正在 使用 `related_name` 或者 `related_query_name`   在一个`ForeignKey` 或者`ManyToManyField` 你必须指定唯一的反向名字并且查询字段的名称。在一个抽象基类里面就会造成一个问题，因为在这个抽象基类里面的字段被引用到每个子类里面，每次都会使用完成相同的值（包括`related_name` 和 `related_query_name`） 

  [related_name](https://docs.djangoproject.com/en/2.1/ref/models/fields/#django.db.models.ForeignKey.related_name)  [related_query_name](https://docs.djangoproject.com/en/2.1/ref/models/fields/#django.db.models.ForeignKey.related_query_name) 

  为了优化处理这个问题，但你将要使用`related_name` 或者`related_query_name` 在一个抽象基类里面（只在这个类里面的时候）， 部分值应该包含`%（app_label）s` 和`%(class)s`.

  > `%(class)s` 由子类类名的小写名字替换
  >
  > `%(app_label)s` 应当是应用子类的小写名字去替换，每个已安装的应用程序的名字应该是独一无二的并且在每个应用里面的模块类名字也必须是独一无二的，所以生成的名字最终是不一样的。

  例如，下面所给的`common/models.py` 

  ```python
  from django.db import models
  
  class Base(models.Model):
      m2m = models.ManyToManyField(
          OtherModel,
          related_name="%(app_label)s_%(class)s_related",
          related_query_name="%(app_label)s_%(class)ss",
      )
  
      class Meta:
          abstract = True
  
  class ChildA(Base):
      pass
  
  class ChildB(Base):
      pass
  ```

  另外一个应用`rare/models.py` 

  ```python
  from common.models import Base #导入另一个应用的模型并别继承
  
  class ChildB(Base):
      pass
  ```

  解释说明:

  这个`common.ChildA.m2m` 字段的反向名字将会是`common_childa_related` ,并且反向查询的部分就是`common_childas` `common.ChildB.m2m`字段的反向名字就是`common_childb_related` ,并且反向查询名字就是`common_childbs` 

  最后，字段 `rare.ChildB.m2m`反向查询的名字就会是`rare_childb_related ` 并且这个反向查询名字将会是 `rare_childbs`  这是有你怎样使用`'%(class)s'` and `'%(app_label)s'` 部分去构建你的related name与related query name，但是如果你忘记使用他，django在进行数据库迁移的时候就会抛出错误。

  如果你没有给抽象基类的字段指定一个`related_name` 属性，这个默认的反向名字将会是子类名字后面跟着`_set` ,就想你一般直接去子类里面去声明了一个字段，例如，在上面的代码当中，如果这个`related_name` 属性是遗漏了，这个m2m字段的反向名字在ChildA的情形下将会是`childa_set` ，并且`childb_set`  将会是`ChildB` 的字段情况.

- 多表继承

  第二种模型继承是在层次结构里的每个模型都是一个模型的本身，每个模型都是跟它自己的数据库表相对应的，并且能够单独地被查询和被创建，这个继承关系介绍链接介于子类模型与每个它的父类模型之间（通过自动创建OnrToOneField）例如：

  ```python
  from django.db import models
  
  class Place(models.Model):
      name = models.CharField(max_length=50)
      address = models.CharField(max_length=80)
  
  class Restaurant(Place):#继承上面的模型
      serves_hot_dogs = models.BooleanField(default=False)
      serves_pizza = models.BooleanField(default=False)
  ```

  `Place`  模型的字段也是能够被模型Restaurant所获取到的，尽管这个数据是在一个不同的数据表里面，因此有2种可能：

  ```python
  >>> Place.objects.filter(name="Bob's Cafe")
  >>> Restaurant.objects.filter(name="Bob's Cafe")
  ```

  如果你有一个`Place` 也是一个Restaurant，你能够获取到这个`Place`  对象与`Restaurant`  对象，通过使用小写字母的模型名字。

  ```python
  >>> p = Place.objects.get(id=12)
  # If p is a Restaurant object, this will give the child class:
  >>> p.restaurant
  <Restaurant: ...>
  ```

  然而，如果p对象在上面的实例中不是一个`Restaurant` （它已经被直接创建作为一个Place对象或者是一些别的父类的），参考 `p.restaurant` 将会抛出一个异常。

  这个自动创建饭店的`OneToOneField` 关联到`Place`  就像这个 

  ```python
  place_ptr = models.OneToOneField(
      Place, on_delete=models.CASCADE,
      parent_link=True,#设置参数
  )
  ```

  你能够覆盖这个字段通过使用你自己的`OneToOneField` 使用属性`parent_link=True` 在模型Restaurant

- Meta与多表继承

  在多表继承的情况下，一个子类继承父类的Meta类是没有意义的，所有的Meta选项已经被应用到父类里面，然后再应用一次正常情况下将会出现冲突行为（这个与抽象基类形成对比，抽象基类没有自身的权利）

  因此，一个子类模型不能获取父类的Meta类。但是在一些限制情况下，子类也能继承父类的一些行为： 如果这个子类没有指定一个`ordering` 属性或者`get_latest_by` 属性，它将会继承他父类的。

  如果这个父类有一个ordering属性并且你不想要子类去有一些天生的ordering属性，你能够显示的禁用它:

  ```python
  class ChildModel(ParentModel):
      # ...
      class Meta:
          # Remove parent's ordering effect
          ordering = []
  ```

- 继承与反向关系

  因为多表继承隐式的使用了一个`ontToOneField`去关联子类与父类，它是有可能从父类向子类移动，按照上面的例子。然而，这个使用到了默认的`related_name` 值给`ForeignKey` 和多对多的关系。如果你正在在一个父类模型的子类上使用这些关系，你必须指定`relatede_name` 属性给每个字段，如果你忘记了，django将会抛出一个参数错误。

  例如，再次使用上面的Place类，让我们创建另外一个有多对多关系的子类。

  ```python
  class Supplier(Place):
      customers = models.ManyToManyField(Place)
  ```

  结果会导致如下错误：i 

  ```python
  Reverse query name for 'Supplier.customers' clashes with reverse query
  name for 'Supplier.place_ptr'.
  
  HINT: Add or change a related_name argument to the definition for
  'Supplier.customers' or 'Supplier.place_ptr'.
  ```

  紧跟着 添加一个`related_name` 给这个`customers` 模型将会解决这个错误：

  ```python
  models.ManyToManyField(Place, related_name='provider')
  ```

- 指定父类的关联字段

  如上所示，django将会自动的创建一个`OneToOneField` 关联你的子类回到任何非抽象的父类模型上面去，如果你想要控制你的属性名关联回父类，你能够创建你自己的`OneToOneField` 并且设置`parent_link=True` 去表明你的字段是链回父类了。

- 代理模型

  当使用多表继承的时候，对于每个模型的子类来说，一个新的数据表被创建，这个通常是希望出现的行为，因为子类需要一个地方去存储一些多余的数据字段（这些字段不存在与基类中），有些时候，然而，你就是想要改变python的一个模型表现，也许，是要改变默认的manager，或者添加一些新的方法。

  这个就是代理模型继承：给源模型创建一个代理，你能够创造，删除，并且更新这个代理模型的实例，并且所有的数据将会被保存仿佛你正在使用源（非代理）模型，这个区别在于你能够修改代理模型中的默认排序或者默认管理，而不会改变源模型的数据。

  代理模型被声明为一个看似普通的模型，你告诉django他是代理模型，通过给内部类Meta设置proxy属性为True。

  例如，加入你想要给一个person模型添加一个方法，你能够这样做：

  ```python
  from django.db import models
  
  class Person(models.Model):
      first_name = models.CharField(max_length=30)
      last_name = models.CharField(max_length=30)
  
  class MyPerson(Person): #子类
      class Meta:
          proxy = True #设置为代理模型，
  
      def do_something(self):
          # ...
          pass
  ```

  `MyPerson`  类和他的父类一样`Person` 类一样，操作同样的数据表，特别的，任何一个Person的实例将会通过MyPerson模型类可获得，反之亦然。

  ```Python
  >>> p = Person.objects.create(first_name="foobar")
  >>> MyPerson.objects.get(first_name="foobar")
  <MyPerson: foobar>
  ```

  在一个模型中，你也可以使用一个代理模型去定义一个不同的默认排序。你也许不想要按照父类模型去排序，但是，当你使用代理的时候，有规律的按照`last_name` 属性排序是很容易实现的，

  ```python
  class OrderedPerson(Person):
      class Meta:
          ordering = ["last_name"]
          proxy = True
  ```

  现在正常的Person查询将会是没有顺序的，并且，`OrderedPerson`  查询将会按照last_name去排序。

  代理模型继承Meta属性，跟常规模型一样的方式

- #### QuerySet`s 也能返回被请求的模型

  无论何时你查询Person对象都是没有办法让django返回一个MyPerson的对象。对于一个Person对象的查询集合将会返回这些对象的类型。代理对象的整个点是依赖源Person的代码将会使用这些，而且你自己的代码能够使用你引入的扩展（无论怎样没有别的代码依赖），这不是用你自己创造的方式去到处取代Person模型。

- 基类的限制

  一个代理模型必须明确的继承一个非抽象模型类，你也不能从多个非抽象模型中继承他，因为这个代理模型不能够提供一些在不同的数据表中介于行之间的连接，一个代理模型能够继承任意数量的抽象类，假如他们不会定义任何模型字段。一个代理模型也能够继承任意数量的代理模型，共同享有公共非抽象父类。

- 代理模型管理

  如果你没有在一个代理模型中指定任何一个模型管理，它继承他父类的模型管理。如果你定义了一个管理，它将会成为默认的，尽管一些定义在父类上的管理将仍然是可获取的。

  继续我们上面的例子，你能够改变默认的管理，当你使用查询Person模型的时候像这个，

  ```python
  from django.db import models
  
  class NewManager(models.Manager):
      # ...
      pass
  
  class MyPerson(Person):
      objects = NewManager()
  
      class Meta:
          proxy = True
  ```

  如果你想要添加新的管理给这个代理，不替代已经存在的默认管理，你能够在定制管理文档中使用[技术描述](https://docs.djangoproject.com/en/2.1/topics/db/managers/#custom-managers-and-inheritance) 创建一个基础类，包含一个新的管理并且继承主要的基类。

  ```python
  # Create an abstract class for the new manager.
  class ExtraManagers(models.Model):
      secondary = NewManager()
  
      class Meta:
          abstract = True
  
  class MyPerson(Person, ExtraManagers):
      class Meta:
          proxy = True
  ```

  你也许不会经常这样做，但是，当你要做的的时候，这个是可能的。

- 代理继承与非管理模型之间的区别

  代理模型的继承看起来跟创建非管理模型相当相似，在一个模型的Meta类使用`managed` 属性。

  随着你设置了Meta.db_table 你能够创建一个非托管的模型去遮盖一个现有的模型并且添加python方法。然而，当你做一些改变的时候，为了去保持2端复制同步的话将会是非常重复与脆弱的。

  另一方面，代理模型用来明确的表现像是一个模型，他们总是跟父类模型保持同步，因为因为他们直接继承他们的字段和managers

  一般的规则是：

  如果你正在映射一个已经存在的模型或者数据表，并且不想要所有的原数据表列，使用`Meta.managed=False` 这个选项是用来给数据视图和数据表进行建模，不在django的控制之下。

  如果你想要修改一个模型的python行为，但是要源字段保持相同，使用`Meta.proxy=True` 

  这样设置以便于代理模型是明确的复制源模型的存储结构，当数据被保存的时候。

- 多继承

  正如python的子类，django模型继承多个父类模型也是可以的。请记住一般的python应用名字解析规则。

  一个特别的名字出现在第一个基类里面(e.g Meta)将会是被使用的这一个，例如，这个意思是如果多个父类含有一个Meta类，只有第一个父类将会被用到，并且所有别的将会被忽略。

  一般而言，你不需要继承多父类，这个有用的情况是最小类：给每个类添加额外的字段或者方法去继承这个最小类。尽可能保持你的继承层次简介明了，以便于你不需要努力去找出特别的信息源。

  请注意：继承一个共有id主键字段的多模型将会抛出错误，为了合适的使用多继承，你需要显示的使用`AutoField` 在基类模型中。

  ```python
  class Article(models.Model):
      article_id = models.AutoField(primary_key=True)
      ...
  
  class Book(models.Model):
      book_id = models.AutoField(primary_key=True)
      ...
  
  class BookReview(Book, Article):
      pass
  ```

  或者我们使用共同的祖先去持有`AutoField`,[参考](https://docs.djangoproject.com/en/2.1/ref/models/fields/#django.db.models.AutoField) ，这就需要从父类模型到共同祖先之间需要使用`OneToOneField` 去避免字段之间的冲突（自动生成与被继承之间的）。

  ```python
  class Piece(models.Model):
      pass
  
  class Article(Piece):
      article_piece = models.OneToOneField(Piece, on_delete=models.CASCADE, parent_link=True)
      ...
  
  class Book(Piece):
      book_piece = models.OneToOneField(Piece, on_delete=models.CASCADE, parent_link=True)
      ...
  
  class BookReview(Book, Article):
      pass
  ```

- 不允许隐藏字段名

  在一般的python类继承中，对于子类来说，这是允许覆盖任何一个来自父类的属性，在django中，对于模型字段是不被允许的。如果一个非抽象模型的基类有一个字段调用`author` 你不能创建另外一个模型字段，或者定义一个属性调用 `author` 在任何一个继承那个基类的子类中。

  这个限制不适用与继承一个抽象模型类的模型字段中，比如字段也许会被另外一个字段或者值覆盖，或者通过设置`field_name = None`被移除。

  警告!

  模型管理从抽象基类中被继承过来。覆盖了一个被引用的继承字段，可能会造成微妙的一些bugs。查看 [custom managers and model inheritance](https://docs.djangoproject.com/en/2.1/topics/db/managers/#custom-managers-and-inheritance) 

  注释：

  在模型中的一些字段会定义额外的一些属性。例如，一个 [`ForeignKey`](https://docs.djangoproject.com/en/2.1/ref/models/fields/#django.db.models.ForeignKey)  定义额外的属性，用_id添加到字段名字当中。`related_name`  与 `related_query_name` 在外键模型中也是一样的。

  这些额外的属性不能够被覆盖除非这个定义的字段能够被改变或者被移除，以便于它不再定义额外的属性。

  在一个父类模型中覆盖字段会导致在区域的困难，比如初始化一个新的实例（去指定哪个字段应该被初始化在Model.__init__）和序列化。有一些特点，一般的python类继承不必用完全相同的方式处理，因为，介于django模型继承与python类继承之间的区别不是专断的，随意的。

  这个限制只是适用于是[字段](https://docs.djangoproject.com/en/2.1/ref/models/fields/#django.db.models.Field)实例的属性。如果你想的话，正常的python属性能够被覆盖。它也仅用于python能够理解的属性名，如果你人工的指定数据库列表名，在多表继承中，你能够有相同的列名字同时出现在子类模型和一个祖先模型中（他们是列名出现在不同的数据表中），

  如果你覆盖任何一个祖先模型中的任何模型字段，django就会抛出一个字段错误异常。

- 在一个包里面的组织模型

  [`manage.py startapp`](https://docs.djangoproject.com/en/2.1/ref/django-admin/#django-admin-startapp) 会创建一个个和应用结构，包括一个models.py 文件。如果你有许多的模型，单独用一个文件去组织他们也许是相当有用的，

  要做这个，需要创建一个models包。移除models.py并且创建一个`myapp/models/`  目录，带着一个__init__.py 文件和存储你模型的文件。 你必须要用 `__init__.py` 文件导入这些模型

  例如，如果你有organic.py和synthetic.py在这个模型目录里面。

  ```python
  # myapp/models/__init__.py
  from .organic import Person
  from .synthetic import Robot
  ```

  要明确的导入每个模型而不是使用`from .models import *`  语句，这个有点就是不会使你的空间凌乱，使代码更加可读，并且使代码分析工具可用。

  **另见参阅** 

  模型参考 [The Models Reference](https://docs.djangoproject.com/en/2.1/ref/models/) 

  覆盖了所有模型相关的API 包括fields, related objects, and `QuerySet`
