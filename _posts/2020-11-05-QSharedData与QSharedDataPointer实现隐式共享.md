---
layout:     post
title:      QSharedData与QSharedDataPointer实现隐式共享
subtitle:   隐式共享
date:       2020-11-05
author:     zhaoguangnan
header-img: img/about_cpp_study.jpg
catalog: true
tags:
    - QT
---

## 什么是隐式共享:

### 1、一些理论
巧妙地隐式共享思想 - 浅拷贝、深拷贝
编个小程序来直观的理解隐式共享
修改前，大家都用同一块内存的数据
修改后，大家各用自己开辟的内存的数据
如何编写具有隐式共享特性的类？

## **巧妙地隐式共享思想 - 浅拷贝、深拷贝**

```cpp
QString str1 = "abcdefghijklmnopqrstuvwxyz";
QString str2 = str1;
```

在很久很久以前，没有隐式共享思想时，人们是这样操作上述代码的：“**给 str1 分配了一段内存，并用26个字母填满这段内存空间。给 str2 分配一段内存空间，然后开始复制数据到 str2 中**”。

后来有个聪明人想到了一个巧方法，叫隐式共享，他是这样操作上述代码的：“**str1 还是分配内存并赋值。str2 也开辟了内存空间，但是却不再把所有的数据都复制到 str2 内存空间中，仅仅是复制数据所在内存的地址，即指针**”。而这种复制操作叫“浅拷贝”。

哇，多么巧妙的思路，反正有了数据的指针，相当于我知道你这段数据在内存中的哪里，就可以随意访问了。你想想看，26个字母占用26个字节，指针才占多少字节啊，一般是4个字节。这复制一次是不是就节省了 26 - 4 = 22 字节的系统资源。这带来的好处就是，假如我这段数据很大很大，我执行复制操作的时候还是只复制指针4个字节，你说是不是很省资源？

对于 str2 中的数据，根据实际情况要么只读、要么修改。读的时候就直接按照复制的数据指针去挨个读出内存数据即可，但是如果要写的话就需要做些事情了。在修改前，str1 和 str2 的数据都在 str1 开辟的内存空间中，看上去是同一块内存片段。但我现在要修改 str2 的数据了诶，这要是一修改必然影响 str1。所以这个时候才会进行数据的全盘复制，即把 str1 的数据全部拷贝一份给了 str2，这种复制叫“深拷贝”。

隐式共享也称“写时复制（copy-on-write）”。总之一句话，隐式共享的思想就是：**修改前大家都用同一块内存的数据，修改时才会进行全数据拷贝操作**。

## **编个小程序来直观的理解隐式共享**

隐式共享理解起来还是很简单的，如果文字看不懂，我们编一个小程序来看看到底是怎么回事。

**修改前，大家都用同一块内存的数据**

[![BRv98U.png](https://s1.ax1x.com/2020/11/05/BRv98U.png)](https://imgchr.com/i/BRv98U)

代码片段：

```text
int main(int argc, char *argv[])
{
    QApplication a(argc, argv);

    QString s1 = "ABC";
    QString s2 = s1;

    qDebug() << s1.constData();
    qDebug() << s2.constData();
    
return a.exec();
}
```

看，两个 QString 都是使用同一片内存地址（0x1a5fa678）中的数据。

**修改后，大家各用自己开辟的内存的数据**

[![BRxPFP.png](https://s1.ax1x.com/2020/11/05/BRxPFP.png)](https://imgchr.com/i/BRxPFP)

代码片段：

```text
int main(int argc, char *argv[])
{
    QApplication a(argc, argv);

    QString s1 = "ABC";
    QString s2 = s1;
    s2[0] = 'B';

    qDebug() << s1.constData();
    qDebug() << s2.constData();

    return a.exec();
}
```

看，修改数据后，s2 从共享状态中脱离了出来自立山头，内部进行了深拷贝。其中 s1 的数据在内存中的 0x179ca058 位置，而 s2 的数据在内存中的 0x179ca098 位置。

## **如何编写具有隐式共享特性的类？**

以前我用纯 C++ 写过，但是当时考虑的很多，而且代码不好理解。Qt 有 QSharedData 和 QSharedDataPointer 类可以方便的实现隐式共享。这两个该类本身就实现了线程安全的引用计数，所以很多细节的东西不需要再去考虑了。

我们以 Employee 类为例，要实现该类的隐式共享，需要：

- 定义继承于 QSharedData 的 EmployeeData 类，用于包含所有的数据成员；
- 定义 Employee 类，包含 QSharedDataPointer<EmployeeData> 数据成员，有点类似 d 指针模式。

代码：

```text
#include <QSharedData>
#include <QString>

class EmployeeData : public QSharedData
{
public:
	EmployeeData() : id(-1) {}
	EmployeeData(const EmployeeData &other) : QSharedData(other), id(other.id), name(other.name) {}
	~EmployeeData();

	int id;
	QString name;
}

class Employee
{
public:
	Employee()
	{
		d = new EmployeeData;
	}
	Employee(int id, const QString &name)
	{
		d = new EmployeeData;
		setId(id);
		setName(name);
	}
	Employee(const Employee &other) : d(other.d) ()

	int id() const {return d->id;}
	void setId(int id) {d->id = id;}
        QString name() {return d->name;}
	void setName(const QString &name) {d->name = name;}
private:
	QSharedDataPointer<EmployeeData> d;
}
```

Employee 类对象的数据都从 d 指针访问。其中的写操作会自动调用 detach() 函数，这样共享数据对象的引用计数大于1会创建共享数据对象的副本。因为继承 QSharedData，所以内部会有个引用计数器。一般来说，实现隐式共享机制必须有默认构造函数、拷贝构造函数、析构函数三个。

用调试模式可以看到数据在修改后进行了深拷贝操作，如下：

[![BRxnwn.png](https://s1.ax1x.com/2020/11/05/BRxnwn.png)](https://imgchr.com/i/BRxnwn)

[![BRxlWT.png](https://s1.ax1x.com/2020/11/05/BRxlWT.png)](https://imgchr.com/i/BRxlWT)