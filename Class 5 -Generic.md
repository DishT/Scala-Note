# Class 5 -Generic
## 舉了一個Queue的例子：

```scala
class Queue[T]private{
    private val queue: List[T]
    }{
    
    def enqueue(x:T) = new Queue[T](queue : +x)
    
    def dequeue:(T,Queue[T])={
        val x :: queuel = queue
        (x, new Queue(queue1))
    }
    
    def isEmpty : Boolean = queue.isEmpty
    override def toString: String = {
    s*Queue$(queue.toString.drop(4))
    }
    
    }

```

```scala
object Queue{
    def empty[T]: Queue[T] = new Queue(Nil)
    def apply[T](xs: T*):Queue[T] = new Queue(xs.toList)
}
```

```scala
object QueueTest extend App{
    val q: Queue[Int] = Queue.apply(1,2,3,4)
    println(q.dequeue)
}
```
老師提問：

這裡的效能如何呢？
不是很好。

要如何提升這裡的效能？
如何提升enqeue的效能？
    Ans: two-list
## 問題：
**要如何提升這裡的效能？**
**如何提升enqeue的效能？**
    Ans: two-list
用兩個list來做，
```scala
class Queue[T]private{
    private val queue: List[T]
    private val trailing: List[T]
    }{
    
    def enqueue(x:T) = new Queue[T](leading, x :: trailing)
```
    加一個新的方法: 確認他是不是空的，
```scala
    def mirror:(List[T], List[T]) = {
    if (!leading.isEmpty)(leading, trailing)
    else(trailing.reverse, Nil)
    }
    
    
```
    改成兩個
```scala
    def dequeue:(T,Queue[T])={
        val (x :: leading, trailing) = mirror
        (x, new Queue(leading1, trailing))
    }
```
    確認兩者皆為空
```scala
    def isEmpty : Boolean = trailing.isEmpty && leading.isEmpty
```
    
```scala
    override def toString: String = {
    s*Queue$(queue.toString.drop(4))
    }
    
    }

```


```scala
object Queue{
    def empty[T]: Queue[T] = new Queue(Nil)
    def apply[T](xs: T*):Queue[T] = new Queue(xs.toList)
}
```



```scala
object QueueTest extend App{
    val q: Queue[Int]:Queue[T] = new Queue(Nil,Nil)
    val q: Queue[Int] = Queue.apply(1,2,3,4)
    println(q.dequeue)
}
```
改成

## 回到Generic
老師用了mirror做例子，宣告一個y，compiler出錯了！為什麼？
```scala
    def mirror:(List[T], List[T]) = {
        val y = new T[]
        if (!leading.isEmpty)(leading, trailing)
        else(trailing.reverse, Nil)
    }
```
這裡出現問題了
因為： 無法再generic type的method裡面宣告generic type 變數。
    C++ 裡面，就不用
**黑板說明**
          Any
    /                 \
AnyVal      AnyRef    
/          \
Int      Double

這裡出現了什麼問題？？
How to bridge the diffence type of ?
Generic in Java

```scala
int in the heap, 
```
    
    加了@specialized(Int)，這樣可以提升效能，same code in different type.
```scala
class Queue[@specialized(Int) T]private{
    private val queue: List[T]
    private val trailing: List[T]
    }{
    
    def enqueue(x:T) = new Queue[T](leading, x :: trailing)
```
## 看看Generic跟Subtyping的關係

**黑板：**
```scala
Apple <: Fruit
Queue [Apple] < Queue[Fruit]
val q = Queue(new Apple)
Queue[Apple]
val q1 : Queue[Fruit] = q

```
這裡會不會有問題？
Apple is subtype of fruit//

**回到code**
```scala
object QueueTest extend App{
    val q: Queue[Int] = Queue.empty
    val q1 = q.enqueue(1).enqueue(2).enqueue(3).enqueue(4)
    
    val qAny:Queue[Any] = q1
    println(q.dequeue)
}
```

Scala並不知道Apple 跟Fruit的關係
所以要特別告訴Scala他們有關係，並且叫做[Covarience](https://twitter.github.io/scala_school/zh_cn/type-basics.html)
Ans: 宣告 +T
```scala
class Queue[+T]private{
    private val queue: List[T]
    private val trailing: List[T]
    }{
    
    def enqueue(x:T) = new Queue[T](leading, x :: trailing)
```
這裡又出現問題了，x不太好，
## **Subtype Counter varience**
```scala
Apple <: Fruit
Queue [Apple] >: Queue[Fruit]
```
> Covarince vs Counter-variance

新的code例子：注意：要用+T, 因為upper bound
**1. Covarience : + T**
```scala
class Call[+T](private val content:T){
    def read: T = content
    
    /*def write (x:T): Unit = {
        content = x
    }*/
}
object CallTest{
    val c = new Call("Hello")
    val o: Call[AnyRef] = c
    //o.write(new AnyRef)
    println(o.read)
    c.read.chartAt(0)
}
```
問題在哪？

**黑板**

T >> c:Call >> T

> 

**2. Counter-variance : -T**
```scala
class Call[-T](private val content:T){
    def read: T = content
    
    def write (x:T): Unit = {
        content = x
    }
}
object CallTest{
    val c = new Call(new AnyRef)
    val c1: Call[String] = c
    //o.write(new AnyRef)
    c1.write("Hello")
}
```

# 下半堂課：