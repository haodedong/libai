# Lambda 表达式
## JAVA8是2014年推出，至今已有4年的时间，其中最大的改变就是Lambda表达式和Collection的Stream接口，极大的改变了我们的编程习惯，本篇会从Lambda表达式开始说起。
### Lambda表达式是什么？
Lambda表达式的意思就是 **没有函数名的函数**
它可以接受一些参数，然后会执行一些操作，
就是酱。
它的结构是 这样

![](http://pqjvqtwoe.bkt.clouddn.com/libai/59B5B1B2-D905-4321-86C3-8D30BF12BA46.png)

一共分为3部分，**()**   ,**->**,**{}**
如上图所示，（）中存放变量，{} 中存放执行的步骤，中间加上 —> 就ok
下面 搞一个例子来说明一下：

```java
	public class Test {
	    public static void main(String[] args) {
		List<String> list = new ArrayList<>();
		list.forEach((String str) -> {
		    System.out.println(str);//步骤
		    //doSomething
		});
	    }
	}
```
						


这是遍历一个list。
具体是怎么遍历的，暂且不提，先搞明白Lambda表达式。
就跟第一个图 所描述的那样：
（）中存放了一个String类型的参数，
{}	中进行一些操作。
中间加上一个 —>
就是这样。
如果（）中只有一个参数，那么可以省略小括号。如果{}中只有一行带脉，那么{}也是可以省略的。而且基本上我都不写参数的类型，可以可以的，那么下面的这段代码


```java
	public class Test {
	    public static void main(String[] args) {
		List<String> list = new ArrayList<>();
		list.forEach(str -> {
		    System.out.println(str);//步骤
		});
	    }
	}
```


可以简写成
```java
	public class ThreadTest {
    public static void main(String[] args) {
        List<String> list = new ArrayList<>();
        list.forEach(str ->
            System.out.println(str)//步骤
        );
    }
}
```



如果lamdba表达式 仅仅调用了一个方法 ，那么还可以简写
```java
	public class ThreadTest {
	    public static void main(String[] args) {
		List<String> list = new ArrayList<>();
		list.forEach(
			System.out::println//步骤
		);
	    }
	}
```



**::**
在java中表示**方法引用**，如果lambda表达式就一句话，执行了一个方法。
那么 可以使用 **类名/类型名::方法名**
或者 **对象::方面名** 来简写
但是要注意 此时不能写方法名的小括号

### ok,那么Lambda表达式可以在哪里使用呢？
Lambda表达式可以在任何地方使用嘛？
并不是
有关它的使用，我们需要提到一个新的名词，**函数式接口**
这也是Java8的新特性之一。
但是它**说白了也就是一个接口**
**函数式接口**
首先是一个接口，然后它只有一个public abstract 的方法，但是可以有其他的别的声明的方法。如下
```java
	public interface FunctionInterface {
	    public abstract void doSomething();
	}
```


由于接口的方法默认就是public abstatic ，所以可以不写public abstract，这个接口也是函数式接口。

JAVA8 中已经添加了许多这种函数式接口
具体可见 java.util.function
他们都只有一个public abstract 的方法。

如图1.4遍历使用了forEach（）方法，那么我们来看看forEach这个方法长什么样子？
```java
	   default void forEach(Consumer<? super T> action) {
		Objects.requireNonNull(action);// action 若为空，抛异常
		for (T t : this) {
		    action.accept(t);
		}
	    }
```


咦，入参是一个接口Consumer<? super T> action（一个函数式接口），也就是说咱们给这个函数式接口传入了一个Lambda表达式。
那么 我们继续来看看Consummer是怎么定义的
```java
	@FunctionalInterface
	public interface Consumer<T> {

	    /**
	     * Performs this operation on the given argument.
	     *
	     * @param t the input argument
	     */
	    void accept(T t);

	    default Consumer<T> andThen(Consumer<? super T> after) {
		Objects.requireNonNull(after);
		return (T t) -> { accept(t); after.accept(t); };
	    }
	}
```


它符合函数式接口的定义，只有一个public abstatic 的方法 accept。另一个方法andThen，并不是public abstatic 的。
再来看图1.7
没毛病！
首先判断action不能为空
然后遍历List 执行action的accept方法
那么重点来了！！！！
怎么就调用了accept这个方法了呢，我并没有重写这个方法啊，看来，Lambda表达式相当于重载了这个方法，返回了这个接口的实现类，就像匿名内部类一样。来来来，我用匿名内部类写一下
```java
	class ThreadTest {
	    public static void main(String[] args) {
		List<String> list = new ArrayList<>();
		list.forEach(
			new Consumer<String>() {
			    @Override
			    public void accept(String s) {
				System.out.println(s);
			    }
			}
		);
	    }
	}
``````

啊呀呀！ 果然如此！！！
但是，像是像，编译的时候，并没有真正的生成了一个类！！！

### 为什么要用Lambda表达式呢？
很明显，首先lambda表达式简化了代码的编写，不必为了一个方法，造一个对象。使用方法引用更能减少代码的编写。
其次，本篇开始所说的Stream流更是完美的和Lambda表达式融为一起，让我们更方便的对Collection进行操作。
