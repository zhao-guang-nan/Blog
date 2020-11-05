---
layout:     post
title:      QPointer与QSharedPointer与QWeakPointer与QScopedPointer的使用方法
subtitle:   QT四个指针的使用
date:       2020-11-05
author:     zhaoguangnan
header-img: img/about_cpp_study.jpg
catalog: true
tags:
    - QT
---

## QPointer:
​	QPointer can only point to QObject instances. It will be automatically set to nullptr if the pointed to object is destroyed. It is a weak pointer specialized for QObject.

Consider this fragment:

```c++
QObject *obj = new QObject;
QPointer<QObject> pObj(obj);
delete obj;
Q_ASSERT(pObj.isNull()); // pObj will be nullptr now
```

## QSharedPointer
A reference-counted pointer. The actual object will only be deleted, when all shared pointers are destroyed. Equivalent to std::shared_ptr.

```c++
int *pI = new int;
QSharedPointer<int> pI1(pI);
QSharedPointer<int> pI2 = pI1;
pI1.clear();
// pI2 is still pointing to pI, so it is not deleted
pI2.clear();
```


// No shared pointers anymore, pI is deleted
Note that as long as there is a shared pointer, the object is not deleted!

## QWeakPointer:
Can hold a weak reference to a shared pointer. It will not prevent the object from being destroyed, and is simply reset. Equivalent to std::weak_ptr, where lock is equivalent to toStrongRef.

```c++
int *pI = new int;
QSharedPointer<int> pI1(pI);
QWeakPointer<int> pI2 = pI1;
pI1.clear();
```

// No shared pointers anymore, pI is deleted

```c++
//
// To use the shared pointer, we must "lock" it for use:
QSharedPointer<int> pI2_locked = pI2.toStrongRef();
Q_ASSERT(pI2_locked.isNull());
```

This can be used if you need access to an object that is controlled by another module.

This can be used if you need access to an object that is controlled by another module.

To use a weak pointer, you must convert it to a QSharedPointer. You should never base a decision on the weak pointer being valid. You can only use data() or isNull() to determine that the pointer is null.

Generally, to use a weak pointer, you must convert it to a shared pointer since such an operation ensures that the object will survive for as long as you are using it. This is equivalent to "locking" the object for access and is the only correct way of using the object pointed to by a weak pointer.

## QScopedPointer:
This is just a helper class that will delete the referenced object when the pointer goes out of scope. Thus, binds a dynamically allocated object to a variable scope.

You can use this for RAII semantics for locals, e.g.:

```c++
MyClass *foo() {
    QScopedPointer<MyClass> myItem(new MyClass);
    // Some logic
    if (some condition) {
        return nullptr; // myItem will be deleted here
    }
    return myItem.take(); // Release item from scoped pointer and return it
}
```


The item will also be deleted in case of an exception

Another use case can be member variables of an object. Then you don't need to write a destructor for those:

```c++
class MyClass {
public:
    MyClass() : myPtr(new int) {}
private:
    QScopedPointer<int> myPtr; // Will be deleted automatically when containing object is deleted
}
```





