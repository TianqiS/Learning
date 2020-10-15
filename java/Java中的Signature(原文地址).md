### Java中的Signature([原文地址](https://www.thoughtco.com/method-signature-2034235))

简单来讲就是通过相同的函数名称以及不同的参数列表来实现对方法的重载

In [Java](https://www.thoughtco.com/what-is-java-2034117), a method signature is part of the method declaration. It's the combination of the method name and the [parameter](https://www.thoughtco.com/parameter-2034268) list.

The reason for the emphasis on just the method name and parameter list is because of [overloading](https://www.thoughtco.com/overloading-2034261). It's the ability to write methods that have the same name but accept different parameters. The Java compiler is able to discern the difference between the methods through their method signatures.

## Method Signature Examples

```java
public void setMapReference(int xCoordinate, int yCoordinate)
{
//method code
}
```

The method signature in the above example is *setMapReference(int, int).* In other words, it's the method name and the parameter list of two integers.

```java
public void setMapReference(Point position)
{
//method code
}
```

The Java compiler will let us add another method like the above example because its method signature is different, *setMapReference(Point)* in this case.

```java
public double calculateAnswer(double wingSpan, int numberOfEngines, double length, double grossTons) 
{
   //method code
}
```

In our last example of a Java method signature, if you follow the same rules as the first two examples, you can see that the method signature here is *calculateAnswer(double, int, double, double)*.