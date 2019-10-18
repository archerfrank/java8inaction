# Charpter 1

## Stream processing
1. Sequential processing:
```java 
import static java.util.stream.Collectors.toList;
List<Apple> heavyApples =
    inventory.stream().filter((Apple a) -> a.getWeight() > 150)
                      .collect(toList());
```

2. Parallel processing:

```java 
import static java.util.stream.Collectors.toList;
List<Apple> heavyApples =
    inventory.parallelStream().filter((Apple a) -> a.getWeight() > 150)
                              .collect(toList());
```

## Passing code to methods with behavior parameterization

```java
File[] hiddenFiles = new File(".").listFiles(File::isHidden);
```

Functions are first-class values; remember how methods can be passed as functional values and how anonymous functions (lambdas)            are written.

## Parallelism and shared mutable data

Multicore processors aren’t fully served by existing Java programming practice

## DEFAULT METHODS

The Java 8 solution is to break the last link—an interface can now contain method signatures for which an implementing class         doesn’t provide an implementation! So who implements them? The missing method bodies are given as part of the interface (hence         default implementations) rather than in the implementing class.                  
This provides a way for an interface designer to enlarge an interface beyond those methods that were originally planned—without         breaking existing code. Java 8 uses the new default keyword in the interface specification to achieve this.

For example, in Java 8 you can now call the sort method directly on a List. This is made possible with the following default method in the Java 8 List interface, which calls the static method Collections.sort:

```java
default void sort(Comparator<? super E> c) {
    Collections.sort(this, c);
}
```

# Chapter 2
                                     
## Behavior parameterization   

We call this a predicate (that is, a function that returns a boolean)    

```java
    public static List<Apple> filter(List<Apple> inventory, ApplePredicate p){
		List<Apple> result = new ArrayList<>();
		for(Apple apple : inventory){
			if(p.test(apple)){
				result.add(apple);
			}
		}
		return result;
	}       

	interface ApplePredicate{
		public boolean test(Apple a);
	}

	static class AppleWeightPredicate implements ApplePredicate{
		public boolean test(Apple apple){
			return apple.getWeight() > 150; 
		}
	}
	static class AppleColorPredicate implements ApplePredicate{
		public boolean test(Apple apple){
			return "green".equals(apple.getColor());
		}
	}

	static class AppleRedAndHeavyPredicate implements ApplePredicate{
		public boolean test(Apple apple){
			return "red".equals(apple.getColor()) 
					&& apple.getWeight() > 150; 
		}
	}
```                                
## Anonymous classes       
Anonymous classes are often used in the context of GUI applications to create event-handler objects

```java
button.setOnAction(new EventHandler<ActionEvent>() {    public void handle(ActionEvent event) {        System.out.println("Woooo a click!!");    }});
```

Ideally we’d like to encourage programmers to use the behavior parameterization pattern, because as you’ve just seen, it makes         your code more adaptive to requirement changes. 


Preview of lambda expressions    
```java
List<Apple> result =  filterApples(inventory, (Apple apple) -> "red".equals(apple.getColor()));
```                                   
## Real-world examples: Comparator, Runnable, and GUI

1. Sorting with a Comparator

```java
// java.util.Comparator
public interface Comparator<T> {
    public int compare(T o1, T o2);
}

inventory.sort(new Comparator<Apple>() {
     public int compare(Apple a1, Apple a2){
          return a1.getWeight().compareTo(a2.getWeight());
     }
});

inventory.sort(
  (Apple a1, Apple a2) -> a1.getWeight().compareTo(a2.getWeight()));

```

2. Executing a block of code with Runnable

```java
Thread t = new Thread(() -> System.out.println("Hello world"));
```

3. GUI event handling
```java
button.setOnAction((ActionEvent event) -> label.setText("Sent!!"));
```

# Chapter 3

## Lambdas in a nutshell

A lambda expression can be understood as a concise representation of an anonymous function that can be passed around: it doesn’t have a name,         but it has a list of parameters, a body, a return type, and also possibly a list of exceptions that can be thrown. That’s         one big definition; let’s break it down:                                          
* Anonymous— We say anonymous because it doesn’t have an explicit name like a method would normally have: less to write and think about!                                       
* Function— We say function because a lambda isn’t associated with a particular class like a method is. But like a method, a lambda has a list of parameters,            a body, a return type, and a possible list of exceptions that can be thrown.                                       
* Passed around— A lambda expression can be passed as argument to a method or stored in a variable.                                       
* Concise— You don’t need to write a lot of boilerplate like you do for anonymous classes.

The lambda we just showed you has three parts  

![](imgs/03fig01_alt.jpg)

* A list of parameters— In this case it mirrors the parameters of the compare method of a Comparator—two Apples.                                       
* An arrow— The arrow -> separates the list of parameters from the body of the lambda.                                       
* The body of the lambda— Compare two Apples using their weights. The expression is considered the lambda’s return value.

The basic syntax of a lambda is either

```java
(parameters) -> expression
```
or

```java
(parameters) -> { statements; }
```
| Use case  | Examples of lambdas                     | 
|-----------|--------------------------------------------------------| 
| A boolean expression  | A boolean expression	(List<String> list) -> list.isEmpty() | 
| Creating objects | () -> new Apple(10)                  | 
| Consuming from an object | (Apple a) -> {System.out.printlna.getWeight());}              | | Select/extract from an object |  (String s) -> s.length()           | 
| Combine two values | (int a, int b) -> a * b               | 
| Compare two objects | (Apple a1, Apple a2) -> a1.getWeight().compareTo(a2.getWeight()) | 
## Where and how to use lambdas   
* Functional interface

A functional interface is an interface that specifies exactly one abstract method.


| Functional interface  | Function descriptor                     | Primitive pecializations|
|-----------|--------------------------------------------------------| --------------------------------------------------------| 
| Predicate<T>  | T -> boolean |IntPredicate, LongPredicate, DoublePredicate |
| Consumer<T> | T -> void                  | IntConsumer, LongConsumer, DoubleConsumer|
| Function<T, R> | T -> R              | IntFunction<R>, IntToDoubleFunction, IntToLongFunction, LongFunction<R>, LongToDoubleFunction, LongToIntFunction, DoubleFunction<R>, ToIntFunction<T>, ToDoubleFunction<T>, ToLongFunction<T>|
| Supplier<T> | () -> T                  | BooleanSupplier, IntSupplier, LongSupplier, DoubleSupplier|
| UnaryOperator<T> | T -> T                 | IntUnaryOperator, LongUnaryOperator, DoubleUnaryOperator|
| BinaryOperator<T>|(T, T) -> T|IntBinaryOperator, LongBinaryOperator, DoubleBinaryOperator|
| BiPredicate<L, R> | BiPredicate<L, R>	(L, R) -> boolean               | |
| BiConsumer<T, U>| (T, U) -> void                 | ObjIntConsumer<T>, ObjLongConsumer<T>, ObjDoubleConsumer<T>|
| BiFunction<T, U, R> | (T, U) -> R                | ToIntBiFunction<T, U>, ToLongBiFunction<T, U>, ToDoubleBiFunction<T, U>|


## The execute around pattern   

1. define one functional interface and use it as parameter
2. pass Lamdar expression.
3. in this example, we could also use Function<T, R> as parameter, then we don't need to define functional interface.

```java
	public static void main(String ...args) throws IOException{

        // method we want to refactor to make more flexible
        String result = processFileLimited();
        System.out.println(result);

        System.out.println("---");
// handle one line once.
		String oneLine = processFile((BufferedReader b) -> b.readLine());
		System.out.println(oneLine);
// handle two lines one time
		String twoLines = processFile((BufferedReader b) -> b.readLine() + b.readLine());
		System.out.println(twoLines);

	}

    public static String processFileLimited() throws IOException {
        try (BufferedReader br =
                     new BufferedReader(new FileReader("lambdasinaction/chap3/data.txt"))) {
            return br.readLine();
        }
    }


	public static String processFile(BufferedReaderProcessor p) throws IOException {
		try(BufferedReader br = new BufferedReader(new FileReader("lambdasinaction/chap3/data.txt"))){
			return p.process(br);
		}

	}

	public interface BufferedReaderProcessor{
		public String process(BufferedReader b) throws IOException;

	}
```

## Functional interfaces, type inference    
* Predicate
```java
@FunctionalInterface
public interface Predicate<T>{
    boolean test(T t);
}

public static <T> List<T> filter(List<T> list, Predicate<T> p) {
    List<T> results = new ArrayList<>();
    for(T s: list){
        if(p.test(s)){
            results.add(s);
        }
    }
    return results;
}

Predicate<String> nonEmptyStringPredicate = (String s) -> !s.isEmpty();
List<String> nonEmpty = filter(listOfStrings, nonEmptyStringPredicate);
```

* Consumer

The java.util.function.Consumer<T> interface defines an abstract method named **accept** that takes an object of generic type T and returns no result (void).

* Function

The java.util.function.Function<T, R> interface defines an abstract method named **apply** that takes an object of generic type T as input and returns an object of generic type R. 

As a result, in Java there’s a mechanism to convert a primitive type into a corresponding reference type. This mechanism         is called _boxing_. The opposite approach (that is, converting a reference type into a corresponding primitive type) is called _unboxing_.

Java 8 brings a specialized version of the functional interfaces we described earlier in order to avoid autoboxing operations         when the inputs or outputs are primitives. 

| Use case  | Example of lambda              | Matching functional interface|
|-----------|--------------------------------------------------------| --------------------------------------------------------| 
| A boolean expression  | (List<String> list) -> list.isEmpty() |Predicate<List<String>>|
|Creating objects  | () -> new Apple(10) |Supplier<Apple>|
| Consuming from an object | (Apple a) -> System.out.println(a.getWeight()) |Consumer<Apple>|
| Select/extract from an object  | (String s) -> s.length() |Function<String, Integer> or ToIntFunction<String>|
| Combine two values  | (int a, int b) -> a * b |IntBinaryOperator|
| Compare two objects  |  objects	(Apple a1, Apple a2) -> a1.getWeight().compareTo (a2.getWeight()) |Comparator<Apple> or BiFunction<Apple, Apple, Integer> or ToIntBiFunction<Apple, Apple>|



If a lambda has a statement expression as its body, it’s compatible with a function descriptor that returns void (provided the parameter list is compatible too). 

```java
// Predicate has a boolean return
Predicate<String> p = s -> list.add(s);
// Consumer has a void return
Consumer<String> b = s -> list.add(s);
```

* Using local variables

Lambdas are allowed         to capture (that is, to reference in their bodies) instance variables and static variables without restrictions. But local         variables have to be explicitly declared final or are effectively final. In other words, lambda expressions can capture local variables that are assigned to them only once. 

## Method references  

Apple::getWeight is a method reference to the method getWeight defined in the Apple class. Remember that no brackets are needed because you’re not actually calling the method. The method reference is shorthand for the lambda expression (Apple a) -> a.getWeight().

| Lambda  | Examples of lambdas                     | 
|-----------|--------------------------------------------------------| 
|(Apple a) -> a.getWeight()|	Apple::getWeight|
|() -> Thread.currentThread().dumpStack()|	Thread.currentThread()::dumpStack|
|(str, i) -> str.substring(i)|	String::substring|
|(String s) -> System.out.println(s)|	System.out::println|


There are three main kinds of method references:


1.  A method reference to a static method (for example, the method parseInt of Integer, written Integer::parseInt)

2.  A method reference to an instance method of an arbitrary type (for example, the method length of a String, written String::length)

3.  A method reference to an instance method of an existing object (for example, suppose you have a local variable expensiveTransaction that holds an object of type Transaction, which supports an instance method getValue; you can write expensiveTransaction::getValue)

![](imgs/03fig05_alt.jpg)

```java
List<String> str = Arrays.asList("a","b","A","B");
str.sort((s1, s2) -> s1.compareToIgnoreCase(s2));

List<String> str = Arrays.asList("a","b","A","B");
str.sort(String::compareToIgnoreCase);
```

In practice

```java
    import static java.util.Comparator.comparing;

        inventory.sort((a1, a2) -> a1.getWeight().compareTo(a2.getWeight()));
        System.out.println(inventory);
        
        // reshuffling things a little
        inventory.set(1, new Apple(10, "red"));
        
        // 4
        // [Apple{color='red', weight=10}, Apple{color='red', weight=20}, Apple{color='green', weight=155}]
        inventory.sort(comparing(Apple::getWeight));
        System.out.println(inventory);       
```

## Composing lambdas

Functional interfaces such as Comparator, Predicate, and Function have several default methods that can be used to combine lambda expressions

* Composing Comparators
```java
Comparator<Apple> c = Comparator.comparing(Apple::getWeight);
//reverse order
inventory.sort(comparing(Apple::getWeight).reversed());
// second comparator
inventory.sort(comparing(Apple::getWeight).reversed().thenComparing(Apple::getCountry));
```
* Composing Predicates

The Predicate interface includes three methods that let you reuse an existing Predicate to create more complicated ones: **negate**, **and**, and **or**.

Note that the precedence of methods and and or is managed from left to right using their positions in the chain. So a.or(b).and(c) can be seen as (a || b) && c.

* Composing Functions

The Function interface comes with two default methods for this, **andThen** and **compose**, which both return an instance of Function.

The method andThen returns a function that first applies a given function to an input and then applies another function to the result of that application.

f.andThen(g) - >  g(f(x))
f.compose(g) - > f(g(x))

```java
public class Letter {
	public static String addHeader(String text) {
		return "From Raoul, Mario and Alan: " + text;
	}

	public static String addFooter(String text) {
		return text + " Kind regards";
	}

	public static String checkSpelling(String text) {
		return text.replaceAll("labda", "lambda");
	}
}

// first pipeline
Function<String, String> addHeader = Letter::addHeader;
Function<String, String> transformationPipeline  = addHeader.andThen(Letter::checkSpelling)             .andThen(Letter::addFooter);

//second pipeline
Function<String, String> addHeader = Letter::addHeader;Function<String, String> transformationPipeline  = addHeader.andThen(Letter::addFooter);

```

# Introducing streams

Streams consume from a data-providing source such as collections, arrays, or I/O resources. Note that generating a stream            from an ordered collection preserves the ordering. The elements of a stream coming from a list will have the same order as            the list.

* Pipelining— Many stream operations return a stream themselves, allowing operations to be chained and form a larger pipeline. This enables            certain optimizations that we explain in the next chapter, such as laziness and short-circuiting. A pipeline of operations can be viewed as a database-like query on the data source.                 

* Internal iteration— In contrast to collections, which are iterated explicitly using an iterator, stream operations do the iteration behind the            scenes for you. Using an internal iteration, the processing         of items could be transparently done in parallel or in a different order that may be more optimized

Collect as an operation that takes as an argument various recipes for accumulating the elements of a stream into a summary result.

So keep in mind that you can consume a stream **only once**!

## STREAM OPERATIONS

You can see two groups of operations:     

1. filter, map, and limit can be connected together to form a pipeline. 
2. collect causes the pipeline to be **executed** and closes it.

Intermediate operations such as filter or sorted return another stream as the return type. This allows the operations to be connected to form a query. What’s important is         that intermediate operations **don’t perform any processing** until a terminal operation is invoked on the stream pipeline—they’re         lazy. 

Terminal operations produce a result from a stream pipeline. A result is any nonstream value such as a List, an Integer, or even void. 


**Operation**|**Type**|**Return type**|**Argument of the operation**|**Function descriptor**
:-----:|:-----:|:-----:|:-----:|:-----:
filter|Intermediate|Stream<T>|Predicate<T>|T -> boolean
map|Intermediate|Stream<R>|Function<T, R>|T -> R
limit|Intermediate|Stream<T>| | 
sorted|Intermediate|Stream<T>|Comparator<T>|(T, T) -> int
distinct|Intermediate|Stream<T>| | 


Terminal operation.

* For each  
Consumes each element from a stream and applies a lambda to each of them. The operation returns void.
* Count  
Returns the number of elements in a stream. The operation returns a long
* collect  
Reduces the stream to create a collection such as a List, a Map, or even an Integer. See chapter 6 for more detail.

# Chapter 5. Working with streams

## FILTERING AND SLICING

```java
        List<Dish> vegetarianMenu =
            menu.stream()
                .filter(Dish::isVegetarian)
                .collect(toList());

        vegetarianMenu.forEach(System.out::println);

        // Filtering unique elements
        List<Integer> numbers = Arrays.asList(1, 2, 1, 3, 3, 2, 4);
        numbers.stream()
               .filter(i -> i % 2 == 0)
               .distinct()
               .forEach(System.out::println);
        // Truncating a stream
        List<Dish> dishesLimit3 =
            menu.stream()
                .filter(d -> d.getCalories() > 300)
                .limit(3)
                .collect(toList());

        dishesLimit3.forEach(System.out::println);

        // Skipping elements
        List<Dish> dishesSkip2 =
            menu.stream()
                .filter(d -> d.getCalories() > 300)
                .skip(2)
                .collect(toList());

        dishesSkip2.forEach(System.out::println);

```

## MAPPING


There’s a method called Arrays.stream()that takes an array and produces a stream. This is always used in the flatMap.

the flatMap method lets you replace each value of a stream with another stream and then concatenates all the generated streams into a         single stream.

```java
import static java.util.stream.Collectors.toList;
// map
        List<String> dishNames = menu.stream()
                                     .map(Dish::getName)
                                     .collect(toList());
        System.out.println(dishNames);

        // map
        List<String> words = Arrays.asList("Hello", "World");
        List<Integer> wordLengths = words.stream()
                                         .map(String::length)
                                         .collect(toList());
        System.out.println(wordLengths);

        // flatMap
        words.stream()
                 .flatMap((String line) -> Arrays.stream(line.split("")))
                 .distinct()
                 .forEach(System.out::println);

        // flatMap, this could be used like database join.
        List<Integer> numbers1 = Arrays.asList(1,2,3,4,5);
        List<Integer> numbers2 = Arrays.asList(6,7,8);
        List<int[]> pairs =
                        numbers1.stream()
                                .flatMap((Integer i) -> numbers2.stream()
                                                       .map((Integer j) -> new int[]{i, j})
                                 )
                                .filter(pair -> (pair[0] + pair[1]) % 3 == 0)
                                .collect(toList());
        pairs.forEach(pair -> System.out.println("(" + pair[0] + ", " + pair[1] + ")"));
```

##  FINDING AND MATCHING

These three operations, anyMatch, allMatch, and noneMatch, make use of what we call short-circuiting

The findAny method returns an arbitrary element of the current stream. It can be used in conjunction with other stream operations. 
```java

public class Finding{

    public static void main(String...args){
        if(isVegetarianFriendlyMenu()){
            System.out.println("Vegetarian friendly");
        }

        System.out.println(isHealthyMenu());
        System.out.println(isHealthyMenu2());
        
        Optional<Dish> dish = findVegetarianDish();
        dish.ifPresent(d -> System.out.println(d.getName()));
    }
    
    private static boolean isVegetarianFriendlyMenu(){
        return menu.stream().anyMatch(Dish::isVegetarian);
    }
    
    private static boolean isHealthyMenu(){
        return menu.stream().allMatch(d -> d.getCalories() < 1000);
    }
    
    private static boolean isHealthyMenu2(){
        return menu.stream().noneMatch(d -> d.getCalories() >= 1000);
    }
    
    private static Optional<Dish> findVegetarianDish(){
        return menu.stream().filter(Dish::isVegetarian).findAny();
    }
    
}

```

You may wonder why we have both **findFirst** and **findAny**. The answer is parallelism. Finding the first element is more constraining in parallel. If you don’t care about which element            is returned, use findAny because it’s less constraining when using parallel streams.

## REDUCING