Python快速入门
=======================

介绍
----

`语言排名 <http://www.tiobe.com/tiobe-index/>`__

查看Python语言设计哲学
----------------------

.. code:: python

    >>> import this
    The Zen of Python, by Tim Peters

    Beautiful is better than ugly.
    Explicit is better than implicit.
    Simple is better than complex.
    Complex is better than complicated.
    Flat is better than nested.
    Sparse is better than dense.
    Readability counts.
    Special cases aren't special enough to break the rules.
    Although practicality beats purity.
    Errors should never pass silently.
    Unless explicitly silenced.
    In the face of ambiguity, refuse the temptation to guess.
    There should be one-- and preferably only one --obvious way to do it.
    Although that way may not be obvious at first unless you're Dutch.
    Now is better than never.
    Although never is often better than *right* now.
    If the implementation is hard to explain, it's a bad idea.
    If the implementation is easy to explain, it may be a good idea.
    Namespaces are one honking great idea -- let's do more of those!

Python解释器
------------

::

    1. Cpython
    2. Jpython
    3. IronPython

简单入门
--------

Hello World
~~~~~~~~~~~

.. code:: python

    命令行输入python,回车
    # python 2.x
    print "Hello World!"
    # python 3.x
    print('Hello World')

用python执行

.. code:: python

    ➜  cat hello.py
    #/usr/bin/env python
    print('Hello World')
    ➜  python hello.py
    Hello World!

指定python解释器
~~~~~~~~~~~~~~~~

.. code:: python

    1. #!/usr/bin/python       ## 告诉shell使用/usr/bin/python执行
    2. #!/usr/bin/env python   ## 操作系统环境不同的情况下指定执行这个脚本用python来解释
    #!/usr/bin/env python3

执行python文件
~~~~~~~~~~~~~~

1. ``python hello.py``
2. ``chmod +x hello.py && ./hello.py``

指定字符编码
~~~~~~~~~~~~

1. ``# _*_ coding:utf-8 _*_``
2. ``# -*- coding:utf-8 -*-``
3. ``# coding:utf-8``

代码注释
--------

单行注释
~~~~~~~~

.. code:: python

    # 只需要在代码前面加上 '#' 号

多行注释
~~~~~~~~

多行注释用三个单引号或者三个双引号

.. code:: python

    """
    注释内容
    """

print 输出多行
~~~~~~~~~~~~~~

.. code:: python

    ➜  cat note.py
    #!/usr/bin/env python
    # _*_ coding:utf-8 _*_

    print("""
    My name is xxx
    I'm a python developer
    My blog is xxx
    Life is short,you need python.
    """)

执行结果

.. code:: python

    ➜  python note.py

    My name is xxx
    I'm a python developer
    My blog is xxx
    Life is short,you need python.

变量
----

命名规则

1. 变量只能包含数字、字母、下划线
2. 不能以数字开头
3. 变量名不能使用\ ``python``\ 内部的关键字

-  NAME 一般不大写，全大写用来代表常量
-  首字母大写常被用作类名

``python``\ 内部关键字

.. code:: python

    ['and', 'as', 'assert', 'break', 'class', 'continue', 'def', 'del', 'elif', 'else', 'except', 'exec', 'finally', 'for', 'from', 'global', 'if', 'import', 'in', 'is', 'lambda', 'not', 'or', 'pass', 'print', 'raise', 'return', 'try', 'while', 'with', 'yield']

``python``\ 中变量工作方式

1. 变量在它第一次赋值时创建;
2. 变量在表达式中使用时将被替换成它们所定义的值;
3. 变量在表达式中使用时必须已经被赋值，否则会报\ ``name 'xxx is not defined'``;
4. 变量像对象一样不需要在一开始进行声明.

动态类型模型
~~~~~~~~~~~~

.. code:: python

    >>> age = 21
    >>> age
    21
    >>> type(21)
    <type 'int'>

上述代码中age并没有指定数据类型，python在运行过程中已经决定了这个值时什么类型，而不用通过指定类型的方式。

垃圾收集
~~~~~~~~

在python基础中还有一个比较重要的概念就是垃圾回收机制

.. code:: shell

    >>> a = 1
    >>> b = a
    >>> id(a),id(b)
    (140426418868328, 140426418868328)

通过\ ``id()``\ 内置函数可以清楚地看到这两个变量指向同一块内存区域。

.. code:: python

    >>> name = "yjj"
    >>> name = "zt"
    >>> name
    'zt'

上述实例，可以理解\ ``垃圾回收机制``\ 是如何工作的

1. 创建一个变量\ ``name``\ ，值通过指针指向\ ``yjj``\ 的内存地址；
2. 如果yjj这个值之前没有在内存中创建，那么现在创建他，并让这个内存地址的引用数\ ``+1``\ ，此时等于\ ``1``\ ；
3. 然后对变量\ ``name``\ 重新赋值，让其指针指向\ ``zt``\ 的内存地址；
4. 那么此时\ ``yjj``\ 的值的引用数就变成\ ``0``\ ，当\ ``python``\ 检测到某个内存地址的引用数等于\ ``0``\ 时，就会把这个内存地址给删掉，从而释放内存；
5. 最后\ ``name``\ 的值的指针指向了\ ``zt``\ 的内存地址，所以\ ``name``\ 的值就是\ ``zt``

定义变量
~~~~~~~~

.. code:: python

    >>> name = "yjj"
    >>> print(name)
    yjj

基本的数据类型
--------------

数据类型初识

::

    1. 数字
        1. int(整型)
        2. long(长整型)
        3. float(浮点型)
        4. complex(复数)
    2. 布尔型(bool)
    3. 字符串(str)
    4. 列表(list)
    5. 元组(tuple)(不可变列表)
    6. 字典(dict)(无序)
    7. 集合(set)

数字
~~~~

整数类型定义的时候变量赋值\ ``直接使用数字``\ ，不要用双引号包起来

.. code:: python

    >>> age = 20
    >>> type(age)
    <class 'int'>
    >>> num = 2.2
    >>> type(num)
    <class 'float'>
    >>> c = 1j
    >>> type(c)
    <class 'complex'>

布尔值
~~~~~~

布尔值只有\ ``True（真）``\ ，\ ``False（假）``

.. code:: python

        >>> if True:
        ...  print("0")
        ... else:
        ...  print("1")
        ...
        0

    # 如果为真输出0，否则输出1

字符串
~~~~~~

定义字符串类型是需要用单引号或者双引号引起来的

.. code:: python

    >>> name = "yjj"
    >>> type(name)
    <type 'str'>

    >>> name = 'yjj'
    >>> print(name)
    yjj

列表
~~~~

创建列表

.. code:: python

    name_list = ['yang', 'six', 'liu']

或

.. code:: python

    name_list = list(['yang', 'six', 'liu'])

元组(不可变列表)
~~~~~~~~~~~~~~~~

创建元组

.. code:: python

    ages = (11, 22, 33, 44)

或

.. code:: python

    ages = tuple((11, 22, 33, 44))

字典(无序)
~~~~~~~~~~

创建字典

.. code:: python

    person = {"name": "yang", "age": "18"}

或者

.. code:: python

    person = dict({"name": "yang", "age": "18"})

流程控制
--------

if语句
~~~~~~

单条件
^^^^^^

.. code:: python

    ➜  cat num.py
    #!/usr/bin/env python
    # _*_ coding:utf-8 _*_

    num = 5

    if num > 1 :
      print("num大")
    else:
      print("num小")

    运行结果
    ➜  python num.py
    num大

多条件
^^^^^^

如果num变量大于5，那么就输出num大于5，如果num变量小于5，那么就输出num小于5，否则就输出num等于5

.. code:: python

    ➜  cat num2.py
    #!/usr/bin/env python
    # _*_ coding:utf-8 _*_

    num = 5
    if num > 5:
      print("num大于5")
    elif num < 5:
      print("num小于5")
    else:
      print("num等于5")

    结果
    ➜  python num2.py
    num等于5

while循环
~~~~~~~~~

定义一个变量count，默认为1，然后执行while循环，输出\ ``1~10``\ ，当count大于10,退出

.. code:: python

    ➜  cat while1.py
    #!/usr/bin/env python
    # _*_ coding:utf-8 _*_

    count = 1
    print("Start...")

    while count < 11:
      print("The count is: ", count)
      count += 1

    print("End...")

    ➜  python while1.py
    Start...
    The count is:  1
    The count is:  2
    The count is:  3
    The count is:  4
    The count is:  5
    The count is:  6
    The count is:  7
    The count is:  8
    The count is:  9
    The count is:  10
    End...

break
~~~~~

跳出当前循环体

.. code:: python

    ➜  cat break.py
    #!/usr/bin/env python
    # _*_ coding:utf-8 _*_

    count = 1

    print("Start...")

    while count < 5:
      if count == 3:
        break
      print("The count is: ", count)
      count += 1

    print("End...")

    ➜  python break.py
    Start...
    The count is:  1
    The count is:  2
    End...

continue
~~~~~~~~

跳出本次循环，继续下一次循环

.. code:: python

    ➜  cat continue.py
    #!/usr/bin/env python
    # _*_ coding:utf-8 _*_

    count = 1
    print("Start...")
    while count < 5:
      if count == 3:
        count += 1
        continue
      print("The count is: ", count)
      count += 1

    print("End...")

    ➜  python continue.py
    Start...
    The count is:  1
    The count is:  2
    The count is:  4
    End...

条件判断
~~~~~~~~

.. code:: python

    if 1 == 1:
    if 1 != 2:
    if 1 < 1:
    if 1 > 1:
    if 1 == 1 and 1 > 0:
    if 2 > 1 or 2 == 2:
    if True:
    if False:

交互式输入
~~~~~~~~~~

Python的交互式输入使用的是\ ``input()``\ 函数实现的，注意在\ ``Python2.7.x``\ 版本的时候可以使用\ ``raw_input()``\ 和\ ``input()``\ 函数，但是在\ ``Python3.5.x``\ 版本的时候就没有\ ``raw_input()``\ 函数了,只能使用\ ``input()``

例题：用户在执行脚本的时候，让他输入自己的名字，然后打印出来。

.. code:: python

    ➜  cat name.py
    #!/usr/bin/env python
    # _*_ coding:utf-8 _*_

    username = input("请输入你的名字: ")
    print("你的名字是：", username)

..

    注意: 默认所有输入都是字符串

.. code:: python

    age = int(input("age" "))
    # 强制字符串转换

练习
----

使用while循环输出1 2 3 4 5 6 8 9 10
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. code:: python

    #!/usr/bin/env python3
    # _*_ coding:utf-8 _*_

    print("Start...")
    count = 1
    while count < 11:
        print(count)
        count += 1
    print("End...")

    执行结果
    Start...
    1
    2
    3
    4
    5
    6
    7
    8
    9
    10
    End...

求1-100的所有数的和
~~~~~~~~~~~~~~~~~~~

思路：定义两个变量，分别是\ ``count``\ 和\ ``num``\ ，利用while语句循环输出\ ``1-100``\ ，然后每次就让\ ``count + num``\ ，这样循环一百次之后相加的结果就是1到100的和了。

代码

.. code:: python

    #!/usr/bin/env python3
    # _*_ coding:utf-8 _*_

    count = 1
    num = 0
    while count <= 100:
        num += count
        count += 1
    print(num)

输出结果

.. code:: python

    5050

输出 1-100 内的所有奇数
~~~~~~~~~~~~~~~~~~~~~~~

思路：
利用%整数相除的余，如果余数是1那么当前的count就是奇数，如果余0，那么当前的count就是偶数。

代码

.. code:: python

    #!/usr/bin/env python3
    # _*_ coding:utf-8 _*_

    count = 1
    while count <= 100:
        if count % 2 == 1:
            print(count)
        count += 1

输出 1-100 内的所有偶数
~~~~~~~~~~~~~~~~~~~~~~~

代码

.. code:: python

    #!/usr/bin/env python3
    # _*_ coding:utf-8 _*_

    count = 1
    while count <= 100:
        if count % 2 == 0:
            print(count)
        count += 1

求1-2+3-4+5 … 99的所有数的和
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. code:: python

    #!/usr/bin/env python3
    # _*_ coding:utf-8 _*_

    count = 1
    while count < 100:
        if count == 1:
            num = count
        elif count % 2 == 1:
            num = num + count
        elif count %2 == 0:
            num = num - count
        count += 1
    print(num)

结果

::

    50

其他方法:

.. code:: python

    li = [ x for x in range(1,100,2)] + [ -y for y in range(2,100,2)]
    print(sum(li))

    ...

用户登陆
~~~~~~~~

需求：写一个脚本，用户执行脚本的时候提示输入用户名和密码，如果用户名或者密码连续三次输入错误则退出，如果输入正确则显示登陆成功，然后退出。

.. code:: python

    #!/usr/bin/env python3
    # _*_ coding:utf-8 _*_

    import getpass

    # username yang
    # password 111111

    count = 3
    while count > 0:
        username = input("username: ").strip()
        password = getpass.getpass("password: ")
        if username == "yang" and password == "111111":
            print("\033[34mWelcome %s \033[0m" % username)
            break
        count -= 1
        print("\033[31mYou have {} times\033[0m".format(count))

账号或密码连续三次输入错误则退出程序，并且每次提醒用户剩余多少次登陆的机会。

其他知识
--------

bytes类型
~~~~~~~~~

三元运算
~~~~~~~~

.. code:: python

    result = 值1 if 条件 else 值2

如果条件为真: result = 值1

如果条件为假: result = 值2

进制
~~~~

-  二进制,01
-  八进制,01234567
-  十进制,0123456789
-  十六进制,0123456789ABCDEF

一切皆对象
~~~~~~~~~~

对于python,一切事物都是对象,对象基于类创建
