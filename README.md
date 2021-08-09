# scala-examples


## NonEmptyList implementation
```
  class NonEmptyList(head: Int, tail: Seq[Int]) extends Iterable[Int] {
    private val underlying: List[Int] = List(head) ++ tail
    override def iterator: Iterator[Int] = underlying.iterator
  }

  object NonEmptyList {
    def apply(head: Int) = new NonEmptyList(head, Nil)
    def apply(head: Int, tail: Int *): NonEmptyList = new NonEmptyList(head, tail)
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
