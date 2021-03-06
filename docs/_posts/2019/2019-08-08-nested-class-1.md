---
class: post-template
navigation: True
title: 第4期：定义class的骚姿势——从嵌套类到lambda（上）
date: '2019-8-8 20:25:23'
tags:
- Java与Linux学习周刊
- Java
---

我们几乎每天都在定义class，除了熟练的新建一个`.java`文件，写上`public class MyClass{}`之外，还会碰到一些看起来很奇怪的类定义？没错，它们就是：**嵌套类、内部类、局部类、匿名类、lambda等**，下面我们就各个击破吧！


<!--more-->

# 什么是嵌套类

Java Tutorial里给出的定义是：

> The Java programming language allows you to define a class **within another class.** Such a class is called a nested class.

非常简单，如果一个类**定义在了另外一个类内部**，就是嵌套类（Netsted Class），比如像这样，`NestedClass`就是一个嵌套类。

```java
class OuterClass {
    class NestedClass {
    }
}
```

一句 *within another class*，说来轻松，但问题来了：

1. 所谓定义在内部，**那什么是“内部”呢**？
2. 为什么要定义在内部，**与外部有什么样的关系**？

这些，都是我们研究嵌套类时需要考虑的。

# 嵌套类的分类

在研究嵌套类之前，首先弄明白嵌套类有哪些类型。根据 Java Tutorial 的介绍，嵌套类，首先根据是**否用static修饰**，分为两大类：**静态嵌套类（static nested classes）**和**内部类（inner class）**。

>  **Terminology:**Nested classes are divided into two categories: static and non-static. Nested classes that are declared`static`are called*static nested classes*. Non-static nested classes are called *inner classes*.

进一步，**内部类又可以分为：局部类、匿名类、lambda表达式**。下面是一个例子，囊括了所有种类的嵌套类：

```java
// file: AllNestedClass.java
package com.imshuai.javalinux.w4;
public class AllNestedClass {
	// 静态嵌套类
	static class StaticNestedClass {
	}
	// 内部类
	class InnerClass {
	}
	public void useLocalClass() {
		// 局部类
		class LocalClass{
		}
	}
	public void useAnonymousClass(){
		// 匿名类
		Runnable runable =  new Runnable() {
			@Override
			public void run() {
				System.out.print("Hello, Anonymous");
			}
		};
	}
	public void useLambda() {
		// Lambda表达式
		Runnable runable = ()-> System.out.print("Hello, Lambda");
	}
}

// 同一个Java文件可定义多个top-level的非public class
class NonPublic{	
}
```

它们的关系可整理如下图所示：

![](/attachment/w4/img/Nested_Class.png)

内容不少，我们首先从相对简单的“静态嵌套类”开始。

# 静态嵌套类

静态嵌套类的基本形式是：1. 定义为外部类的一个成员。2. 通过static修饰。比如下面的例子：

```java
class OuterClass {
    static class StaticNestedClass {
    }
}
```

而且，静态嵌套类，可以像一般的类成员一样，使用**public**、**protected**、**private**等修饰。

## 无法直接访问外部类实例成员
和静态方法或静态变量一样，静态嵌套类是**类成员**，因此静态外部类**不能直接访问外部类的实例方法或变量**，这一点很像静态方法，比如：

```java
package com.imshuai.javalinux.w4.staticnested;
public class OuterClass {
	public String outerName = "outerName";
	public static class StaticNetstedClass{
		public void print() {
			/*
			 *  编译错误: Cannot make a static reference
			 *  to the non-static field outerName
			 */
			System.out.println(outerName);
		}
	}
}
```

但静态嵌套类可以通过**创建外部类的实例**访问外部类的实例变量：

```java
package com.imshuai.javalinux.w4.staticnested;
public class OuterClass {
	public String outerName = "outerName";
	public static class StaticNetstedClass{
		public void print() {
			// 通过实例访问，与普通类无异
			System.out.println(new OuterClass().outerName);
		}
	}
}
```

同时，静态嵌套类还是有**嵌套类的优待**的：**可以访问外部类实例的private成员**，比如上面的`outerName`修改为`private`，`StaticNetstedClass.print()`一样可以访问：

```java
package com.imshuai.javalinux.w4.staticnested;
public class OuterClass {

	private String outerName = "outerName";
	public static class StaticNetstedClass{
		public void print() {
			// 通过实例访问，可以访问private变量
			System.out.println(new OuterClass().outerName);
		}
	}
}
```

## 静态嵌套类的引用方式

嵌套类在引用时，需要通过外部类实现。下面是在外部引用和实例化静态类的例子：

```java
// 无法直接判断OuterClass是报名还是外部类
import com.imshuai.javalinux.w4.staticnested.OuterClass.StaticNetstedClass;
public class Main {
	public static void main(String[] args) {
		StaticNetstedClass snc = new StaticNetstedClass();
		snc.print();
	}
}
```

从上面的用法看，静态嵌套类其实与普通的类并无太大区别，甚至看起来，包裹它的外部类就像它的包名而已。`StaticNetstedClass`看起来好像包名就是：`com.imshuai.javalinux.w4.staticnested.OuterClass`

不过，更清晰的表示是带上`OuterClass.`作为自己的前缀，就像这样：

```java
package com.imshuai.javalinux.w4;
import com.imshuai.javalinux.w4.staticnested.OuterClass;
public class Main {
	public static void main(String[] args) {
		// 带上OuterClass，表明是自己是嵌套类
		OuterClass.StaticNetstedClass snc = new OuterClass.StaticNetstedClass();
		snc.print();
	}
}
```

## 静态嵌套类的访问权限

我们知道一个普通类一般只能被声明为`public`或`package`访问权限。而在外部类的管理下，静态嵌套类的访问权限，完全由外部类说的算。这就带来了一个有意思的结果：**静态嵌套类可以是private、protected、public或package的**。比如下面，`StaticNetstedClass`声明为`protected`，因此只能由`OuterClass`自己或其子类声明和使用：

```java
public class OuterClass {
	private String outerName = "outerName";
	// 只有OuterClass的子类可以声明和使用
	protected static class StaticNetstedClass{
		public void print() {
			System.out.println(new OuterClass().outerName);
		}
	}
}
```

## 关于接口

由于接口**本质上是静态的**，所以如果在一个类内部定义接口，虽然没有使用static，依然是static的，可以当做静态嵌套类，而不是内部类。

**静态嵌套类小结：**
1. 和静态方法类似，静态嵌套类不能**直接访问**外部类的实例变量或方法，但可以直接访问外部类的static变量或方法。
2. 静态嵌套类具有所有嵌套类的优待：可以**访问外部类的private成员**。
3. 对于静态嵌套类，外部类就像是充当一个**特殊的“包名”**，将静态嵌套类**封印**在自己的内部，灵活的对外提供private、protected、public以及package访问控制。

# 内部类

相比静态嵌套类，内部类（或者说非静态嵌套类）更复杂，变化也更多。内部类根据其scope可以分为：

1. 一般内部类（声明为外部类的实例成员）
2. 局部类（声明为方法的局部成员）
3. 匿名类（没有名字，用完即焚）
4. lambda表达式（Java 8提供的匿名类）

下面逐一介绍。

## 内部类的实例化

我们先研究一般内部类，即被定义为外部类非static成员的内部类。其基本形式是：

```java
public class OuterClass {
	public class InnerClass{
	}
}
```

由于**内部类总是与一个实例关联**，因此**内部类无法单独实例化**。若要实例化内部类，需先实例化一个外部类，然后通过外部类的实例才能实例化内部类，这样**保证了内部类实例总是绑定了外部类实例**。下面举例说明。

我先定义过一个简单的外部类和内部类：

```java
package com.imshuai.javalinux.w4.inner;
public class OuterClass {
	public class InnerClass{
	}
}
```

下面是实例化的步骤：

```java
package com.imshuai.javalinux.w4;
import com.imshuai.javalinux.w4.inner.OuterClass;
public class Main {
	public static void main(String[] args) {
        // 实例化外部类
		OuterClass outerObject = new OuterClass();
        // 通过.new实例化内部类
		OuterClass.InnerClass innerObject = outerObject.new InnerClass();
	}
}
```

最后一句，`outerObject.new InnerClass()`，**关键字`new`带一个前缀**，用来说明绑定的外部实例。

如果硬改成像嵌套静态类那样直接实例化，编译会有如下报错：

```java
/* 编译报错:
- No enclosing instance of type OuterClass is accessible. 
Must qualify the allocation with an enclosing instance of type
OuterClass (e.g. x.new A() where x is an instance of OuterClass).
*/
OuterClass.InnerClass innerObject = new OuterClass.InnerClass();
```

## 内部类的static限制

内部类不能定义和声明static成员。Java Tutorial给出的原因是，内部类总是和一个实例关联，所以不能定义static：
>  because an inner class is associated with an instance, it cannot define any static members itself.

我觉得很牵强，其实Oracle的Java Tutorial并没有说清楚。不过我觉得这可能只是人为限制，因为**我们可以让内部类通过继承一个含有static成员或方法的父类突破这一限制**（如下例），既然可以通过继承调用静态方法，为什么不能直接包含静态方法呢？

```java
package com.imshuai.javalinux.w4.inner;
class StaticParent{
    // 父类加一个static方法
	public static void print() {
		System.out.println("Static parent");
	}
}
class Outer {
    // 内部类继承父类
	class Inner extends StaticParent{
	}
}

public class InnerClassStatic{
	public static void main(String[] args) {
       // 调用父类的static方法，
       // 看起来就像调用自己的static方法一样
		Outer.Inner.print();
	}
}
```

但仍存在一个例外是，**内部类可以定义常量。所谓常量即static final修饰的原始数据类型或String**，它们都是可以在编译期计算出来，又编译器并做代码替换。比如下面是可行的：

```java
package com.imshuai.javalinux.w4.inner;
public class StaticFinalConstantMember {
	class Inner{
		// static final constant is allowed.
		public static final int AGE = 3;	
	}
	public static void main(String[] args) {
		System.out.println(StaticFinalConstantMember.Inner.AGE);
	}
}
```

## 内部类可访问外部类的实例变量或方法

和实例变量或实例方法一样，内部类与外部类的实例相关联，并且可以**直接访问外部类的实例变量或实例方法**。

```java
package com.imshuai.javalinux.w4.inner;
public class OuterClass {
	private String name = "Jack";
	public class InnerClass{
		public void printName() {
			System.out.println(name);
		}
		public void setName(String newName) {
			name = newName;
		}
	}
}
```

## 变量定义覆盖（shadowing）

内部类如果定义了和外部类一样的变量名，则在内部类中也会出现变量名覆盖现象（shadowing），如果需要访问外部类的变量，则**需要通过外部类的类名进行区分**，比如下面的例子：

```java
package com.imshuai.javalinux.w4.inner;
public class ShadowTest {
	public int x = 0;

    class FirstLevel {
        public int x = 1;
        void methodInFirstLevel(int x) {
            System.out.println("x = " + x);
            System.out.println("this.x = " + this.x);
            System.out.println("ShadowTest.this.x = " + ShadowTest.this.x);
        }
    }

    public static void main(String[] args) {
        ShadowTest st = new ShadowTest();
        ShadowTest.FirstLevel fl = st.new FirstLevel();
        fl.methodInFirstLevel(23);
    }
}
```

方法参数、内部类以及外部类都有相同变量名`x`。此时直接写`x`代表最近的定义，即方法参数；`this`指代内部类；而外部类的`x`，需要增加类名`ShadowTest`再加上`this`。

所以，**类名+this+变量名的形式才是实例成员的完全体**，即便没有冲突你可以这样写，就是太啰罢了：
```java
public class ThisTest {
	
	private String name;
	public void print() {
		System.out.println(ThisTest.this.name);
	}
}
```

# 内部类之局部类

除了上面将内部类定义在class内，还可以**定义在代码段里（比如方法体内），称作局部类（Local Class）**。下面代码中`PhoneNumber`就是一个局部类的例子，它定义在了`validatePhoneNumber`的方法体内：

```java
package com.imshuai.javalinux.w4.lcoal;
public class LocalClassExample {
	private static String regularExpression = "[^0-9]";
	public static void validatePhoneNumber(String phoneNumber1, String phoneNumber2) {
		final int numberLength = 10;
		// 局部类，包含业务逻辑
		class PhoneNumber{
			String formattedPhoneNumber = null;
			public PhoneNumber(String phoneNumber) {
				String currentNumber = phoneNumber.replaceAll(regularExpression, "");
				if(currentNumber.length() == numberLength) {
					formattedPhoneNumber = currentNumber;
				}else {
					formattedPhoneNumber = "INVALID";
				}
			} // end of PhoneNumber

			public String getNumber() {
				return formattedPhoneNumber;
			}
		} // end of validatePhoneNumber()
		
		// 实例化局部类
		PhoneNumber myNumber1 = new PhoneNumber(phoneNumber1);
		PhoneNumber myNumber2 = new PhoneNumber(phoneNumber2);
		
		System.out.println("First number is " + myNumber1.getNumber());
		System.out.println("Second number is " + myNumber2.getNumber());
	}
	
	public static void main(String[] args) {
		validatePhoneNumber("123-456-7890", "456-7890");
	}
}
```

上面的代码，局部类`PhoneNumber`，封装了对电话号码的格式验证的逻辑。那为什么使用局部类呢？想象一下，如果验证电话号码的逻辑不用局部类，可以怎么实现？最常规的办法就是将逻辑抽象成一个private方法供`validatePhoneNumber`调用，比如这样：

```java
package com.imshuai.javalinux.w4.lcoal;

public class LocalClassExampleOtherwise {
	private static String regularExpression = "[^0-9]";

	public static void validatePhoneNumber(String phoneNumber1, String phoneNumber2) {
		// 实例化局部类
		String myNumber1 = getFormatedNumber(phoneNumber1);
		String myNumber2 = getFormatedNumber(phoneNumber2);

		System.out.println("First number is " + myNumber1);
		System.out.println("Second number is " + myNumber2);
	}

	private static String getFormatedNumber(String phoneNumber) {
		final int numberLength = 10;
		String formattedPhoneNumber = null;
		String currentNumber = phoneNumber.replaceAll(regularExpression, "");
		if(currentNumber.length() == numberLength) {
			formattedPhoneNumber = currentNumber;
		}else {
			formattedPhoneNumber = "INVALID";
		}
		return formattedPhoneNumber;
	}
	
	public static void main(String[] args) {
		validatePhoneNumber("123-456-7890", "456-7890");
	}
}
```

相比而言：

1. **private方法扩大了代码的scope**，本来只是`validatePhoneNumber`内部的逻辑，现在变成了整个class的逻辑。
2. 如果对电话号码增加更多的逻辑，可能会**产生更多的private方法**，而这一切都却可以在local class里轻易扩展的。
3. **局部类天然的可以访问方法的局部变量**，如果抽象成private方法，需要传递参数。

因此，局部类相当于**封装了一段需要在方法内复用的代码，这段代码只在当前方法可以使用，是一种比private方法更严格的做法**。

但我觉得，如果**外部类逻辑本身比较纯粹和简单**，使用局部类意义并不大。因为这个外部类的存在本身就是为了封装这段逻辑，何必再搞一层？而且class内部的private方法是完全在掌控之内的。

## 局部类访问局部变量

**局部类相比一般内部类的一个优势是，可以直接访问局部变量**，包括方法的参数、方法体内定义的局部变量。比如上面的例子，局部类`PhoneNumber`访问了局部变量`numberLength`。

注意上例中，`numberLength`被声明为`final`，否则可能无法编译通过，**这是局部类访问局部变量的final限制**。但从JDK 8开始，局部变量也可以不是final的，但仍然要像final一样对待，即不能修改局部变量（包括外部方法本身），称作为**等效final（effectively final）**。

Java为什么要这样限制局部类呢？这是因为**局部类的生命周期可能在外部方法之外**。比如下面的例子：

我们先定义一个接口：

```java
public interface TestInterface{
	public void internalTestFunction();
}
```

然后在一个方法内定义局部类，实现上述接口，通过方法将局部类的实例返回，并在main函数访问：

```java
public class VariableCapture {
	public static TestInterface testFunction () {
		int value = 3;
		class LocalClsss implements TestInterface{
			@Override
			public void internalTestFunction() {
				System.out.println(value);
			}
		}
		return new LocalClsss();
	}	
	public static void main(String []args) {
        TestInterface testObject = testFunction();  
        testObject.internalTestFunction(); 
    }
}
```

方法`testFunction`的局部变量`value`在`testFunction`执行完毕后，就应该销毁了。但后续的`internalTestFunction`方法是局部类的方法，还会继续执行。为了保证局部类对`value`的正常访问，局部类**实际上是复制了一份局部变量**，这种被局部类访问问的变量，在Java里称作**俘获变量（captured variable）**。**Java为了保证两份拷贝的一致性，所以需要保证俘获变量是final或等效final的**。

## 局部类访问类成员

局部类和内部类一样，可以访问所在外部类的成员。但要注意的是，**局部类如果在static方法内定义，则不能访问外部类的实例成员**。这一点也很容用理解，因为static方法并不依赖于实例，所以局部类并没有对应的实例可绑定。

## 局部类的static限制

方法内是不可能存在static修饰符的。所以，局部类肯定不是static的。而接口本质上是static的，所以**不存在所谓局部接口**，即不能在方法内定义接口。

除此之外，局部类和内部类一样，局部类也不能声明和定义static成员（除非是static final修饰的常量）。

**局部类小结：**
1. 局部类也是一种内部类，只不过“**更内部**”，从class成员内部，变成了方法的内部。
2. 局部类除了能像一般内部类一样访问外部类的实例变量和方法外，**还可以访问方法的局部变量，但访问的局部变量必须是final或等效final的**。
3. 局部类访问外部类的变量，受包含包含方法修饰符的影响。如果包含方法是static的，则局部类只能访问外部类的static成员；如果包含方法是非static的，则不受此限制。
4.** 局部类的scope仅存在代码段的局部，外部无法使用**。
5. 和内部类一样，**局部类一般不能定义或声明static成员**。


# 总结

本文主要介绍了嵌套类的主要类型。根据是否static可以大体分为静态嵌套类和内部类；而内部类又可以分为：一般内部类、局部类、匿名类和lambda表达式，它们的关系如下：


![](/attachment/w4/img/Nested_Class.png)


然后分别介绍了静态嵌套类、内部类、局部类的定义、使用特点和限制。

**其中，匿名类和lambda表达式相关，且lambda表达式更为复杂，留作下周单独介绍。**下期同时还会对嵌套类做进一步探讨，比如：

1. 嵌套类编译后的class文件。
2. JDK中是如何使用嵌套类的。
3. 与嵌套类关系不太大，但看其来很类似的非public类（即同一个java文件定义多个平行的class）

# 参考
1. [Nested Classes (The Java™ Tutorials > Learning the Java Language  > Classes and Objects)](https://docs.oracle.com/javase/tutorial/java/javaOO/nested.html)
2. [Variable Capture in Java](http://www.devcodenote.com/2015/04/variable-capture-in-java.html)
3. [java - Why can't we have static method in a (non-static) inner class - Stack Overflow](https://stackoverflow.com/questions/975134/why-cant-we-have-static-method-in-a-non-static-inner-class)

> 本文转自我的知乎专栏[《Java与Linux学习周刊》](https://zhuanlan.zhihu.com/java-linux)的[《第4期：定义class的骚姿势——从嵌套类到lambda（上）》](https://zhuanlan.zhihu.com/p/77270861)。
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTEwMDc5NjQ4NTEsLTEzMTM0OTA3MjEsMT
E1MzQ0MjQxLDExNzUxNTkxMzVdfQ==
-->
