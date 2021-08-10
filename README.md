# scala-examples


## NonEmptyList implementation
```
  class NonEmptyList[T](head: T, tail: Seq[T]) extends Iterable[T] {
    private val underlying: List[T] = List(head) ++ tail
    override def iterator: Iterator[T] = underlying.iterator
  }

  object NonEmptyList {
    def apply[T](head: T) = new NonEmptyList(head, Nil)
    def apply[T](head: T, tail: T *): NonEmptyList = new NonEmptyList(head, tail)
  }

  for (x <- NonEmptyList(1)) {
    println(x)
  }
```

## Limit parameter types

### With implicits
```
  sealed trait LimitedType[T]
  object LimitedType {
    implicit object LimitedInt extends LimitedType[String]
    implicit object LimitedLong extends LimitedType[Long]
  }

  def doSomething[T : LimitedType](t: T) = {}
  
  doSomething(1L)
  doSomething("string text")
  doSomething(1) // will not compile as there is no implicit wrapper
```

### With Contravariance

```
  trait Contra[-X]

  type Union[A, B] = {
    type Check[T] = Contra[Contra[T]] <:< Contra[Contra[A] with Contra[B]]
  }

  def doSomething[T : Union[Long, String]#Check](t: T) = {}

  doSomething(1L)
  doSomething("string text")
  doSomething(1) // contravariant can't prove Int. will not compile

```
