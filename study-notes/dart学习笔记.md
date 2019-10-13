# dart学习笔记

## 第1章 课程介绍及开发准备

### 语言介绍

- 可用于web和移动端开发（flutter）
- windows ：choco install dart-sdk
- linux: sudo apt-get install dart
- Mac: brew install dark --devel


## 第2章 数据类型

## 字符串
- 字符串类型的变量*num会重复num次
- \n在字符串中可以达到换行的效果
- '''内容'''，三个引号中的内容可支持换行显示

## List(数组)
dart中List即是数组类型，没有Array这个类型
- 创建不可变的List: var list = const[1,2,3];

### Map

- 创建Map:var language = {'first': 'Dart', 'sencond'" 'Java'};
- 创建不可变Map:var language = const { 'first' : 'Dart', 'second' : 'Java' };
- 构造创建: var language = new Map();

## 第3章 运算符

### 赋值运算符

- b ??= 10; 如果b未赋值时才把它赋值为10，如果已赋值则不操作；

### 条件表达式

- a ?? b; 如果a未赋值时返回b，如果a已赋值时返回a；

## 第4章 控制语句

### switch 语句
- switch 中使用continue 进行跳转执行

## 第5章 方法

### 定义
- dart function_declaration.dart 1 'Test' true; 传递参数进行启动执行
- 方法和参数类型都可以省略
- 方法默认返回null

### 可选参数
- 形式1：person(String name, {int age, String gender}){}: 这种可选参数传参基于key值
- 形式2：person(String name, [int age, String gender]){}: 这种可选参数传参基于顺序
- 正常参数必须在可选参数前面

## 第6章 面向对象编程-基础

### 类与对象

- new 可以省略
- dart 中方法不能被重载
- 私有方法和属性以下划线_开头

### 计算属性

	num get area => width * height;
		set area(value) {
			width = value / 20;
      	}
      	
### 构造方法
- 如果没有自定义构造方法，则会有个默认构造方法；
- 如果存在自定义构造方法，则默认构造方法无效；
- 可以以语法糖的形式直接赋值，形如：Person(this.name)
- 构造方法不能重载；
- 可以通过命名构造方法实现多个构造方法, 形如：Person.withName(this.name);

### 常量构造方法
- 常量对象的所有属性必要都是常量
- 常量运行速度更快

### 工厂构造方法
- 在构造方法前面增加factory即为工厂构造方法；
- 构造方法不能返回值，工厂构造方法可以返回值

### 初始化列表
- 初始化列表会在构造方法体执行之前执行
- 使用逗号分隔初始化表达式
- 初始化列表常用于设置final变量的值
- Person.withMap(Map map): gender = map['gender'];

### 静态成员
- 使用static 关键字来实现类级别的变量和函数
- 静态成员不能访问非静态成员，非静态成员可以访问静态成员
- 类中的常量需要使用static const声明

### 对象操作符
- 条件成员访问:?
- 类型转换:as
- 是否指定类型：is, is!
- 级联操作:..

### 对象call方法
- 如果类实现了call()方法，则该类的对象可以作为方法使用

## 第6章 面向对象编程-高级用法

### 继承中的构造方法
- 子类的构造方法默认会调用父类的无名无参的构造方法
- 如果父类没有无名无参的构造方法，则需要显式调用父类的构造方法
- 在构造方法参数后使用：显式调用父类构造方法，如：Student(String name) : super.withName(name);
- 构造方法执行顺序
	- 父类的构造方法在子类构造方法开始执行前执行
	- 如果有初始化列表，初始化列表会在父类构造方法之前执行

### 抽象类
- abstract 标识抽象类
- 抽象方法不需要abstract修饰，无实现
- 抽象类可以没有抽象方法
- 有抽象方法的类一定得声明为抽象类

### 接口
- 类和接口是统一的，类就是接口
- 每个类都隐式地定义了一个包含所有实例成员的接口
- 如果是复用已有类的实现，使用继承(extends)
- 如果只是使用已有类的外在行为，使用接口(implements) 

### Mixins
- 必须先有继承才能用mixins，class D extends A with B, C {}
- Mixins类似于多继承，是在多类继承中重用一个类代码的方式
- 作为Mixins的类不能有显式声明构造方法
- 作为Mixins的类只能继承自Object
- 使用关键字with连接一个或多个mixin

### 操作符覆写
- 覆写操作符要在类中定义

## 第8章 玫举 & 泛型

### 枚举
- 常用来替换常量
- index 从0开始，依次累加
- 不能指定原始值
- 不能添加方法

### 泛型
- dart中类型是可选的，可使用泛型限定类型
- 使用泛型能够有效的减少代码重复

