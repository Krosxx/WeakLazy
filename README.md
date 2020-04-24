# WeakLazy
> WeakLazy for Kotlin.

Kotliner 应该都知道下面代码意义：

```kotlin
//这是一个懒初始化的变量
val value by lazy { //initializer }
```
但这个`lazy`存在一个问题：初始化后就无法自动回收，除非引用它的对象被回收。

想象一下：我们偶尔会弄一个全局懒初始化变量，这样都会一直保留在内存中。且无法手动 `value = null`

### 优点

1. 懒初始化
2. 在连续获取值时，不会频繁调用初始化块
3. 在程序gc后能及时释放内存。

### 演示

```kotlin
val finalWeakLazyDelegate = weakLazy {
    listOf(0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0)
}

val finalValue by finalWeakLazyDelegate

fun main() {
    println("hasValue: " + finalWeakLazyDelegate.hasValue)
    println(finalValue)
    println("hasValue: " + finalWeakLazyDelegate.hasValue)

    System.gc()    //or  finalWeakLazyDelegate.clearWeakValue()
    println("hasValue: " + finalWeakLazyDelegate.hasValue)
}
```
output:
```
hasValue: false
[0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0]
hasValue: true
hasValue: false
```

顺便说一下：`weakLazy` 初始化代码块可能多次执行，可以按需求来确定返回的值。

源码：[WeakLazy.kt](WeakLazy.kt)
