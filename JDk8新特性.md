#Jdk8 新特性
##1.函数式接口	
###函数式接口:
 1. 有且只有一个抽象方法的接口,称之为函数式接口
 2. 当然接口中可以包含其他的方法(默认,静态,私有)
>@FunctionalInterface注解
>作用:可以检测接口是否是一个函数式接口
>
	是:编译成功
	否:编译失败(接口中没有抽象方法抽象方法的个数多余1个)

	lambda表达式可以被当做匿名内部类的"语法糖",但二者原理上是不同的
	lambda编译后不会有新的class 文件产生,而匿名内部类有新文件产生,因此lambda的效率比匿名内部类高

##函数式编程
###Lambda的延迟执行 
	有些场景的代码执行后,结果不一定会被使用,从而造成性能浪费。而Lambda表达式是延迟执行的,这正好可以作为解决方案,提升性能。
	用到的时候才会执行Lambda中的方法
	
	1. lambda表达式 作为参数
  

##2.stream 流
	List.stream().filter(name-> name.startWith("wang"))
				 .filter(name->name.length ==3)
				.forEach(name->System.out.println(name))
 这里的filter、map、skip都是在对函数模型进行操作,集合元素并没有真正被处理。只有当终结方法count执行的时候,整个模型才会按照指定策略执行操作。而这得益于Lambda的延迟执行特性。		 	
###Stream流中的常用方法
1. filter:用于对Stream流中的数据进行过滤</br>
Stream<T> filter(Predicate<? super T> predicate);</br>
filter方法的参数Predicate是一个函数式接口，所以可以传递Lambda表达式，对数据进行过滤
Predicate中的抽象方法:</br>
boolean test(T t);

2. map :
 如果需要将流中的元素**映射**到另一个流中，可以使用map方法.
<R> Stream<R> map(Function<? super T, ? extends R> mapper);
该接口需要一个Function函数式接口参数， 可以将当前流中的类型数据转换为另一种R类型的流。
Function中的抽象方法:
R apply(T t);

3. count:统计个数</br>
	long count();</br>
count 方法是个终结方法,返回值是个long 类型的整数 所以不能继续调用stream流中的其他方法

4. limit方法可以对流进行截取，只取用前n个。</br>
方法签名:
Stream<T> limit(long maxSize);
参数是一个long型，**如果集合当前长度大于参数则进行截取;否则不进行操作**</br>
limit方法是一个延迟方法,只是对流中的元素进行截取,返回的是一个新的流,所以可以继续调用Stream流中的其他方法

5. skip:用于跳过元素
如果希望跳过前几个元素，可以使用skip方法获取一个截取之后的新流:
Stream<T> skip(long n);
如果流的当前长度大于n,则跳过前n个;否则将会得到一个长度为0的空流。

6. concat: 组合方法(静态方法),用于把流组合到一起
如果有两个流，希望合并成为一个流，那么可以使用Stream接口的静态方法concat
static <T> Stream<T> concat(Stream<? extends T> a, Stream<? extends T> b)
	
	例如:
	Stream<String> concat = Stream.concat(stream1, stream2);

##获取流
java.util.stream.stream<T>是java8新加入的最常用的流接口。( 这并不是一个函数式接口。)
获取一个流非常简单,有以下几种常用的方式:

- 所有的Collection 集合都可以通过stream默认方法获取流;
- Stream接口的静态方法of可以获取数组对应的流。
 
###特点:
stream流属于管道流。只能被消费(使用)1次 </br>
第一个Stream流调用完毕方法，数据就会流转到下一个Stream上 </br>
而这时第一个Stream流已经使用完毕就会关闭了 </br>
所以第一个Stream流就不能再调用方法了 </br>

#3.方法引用
 