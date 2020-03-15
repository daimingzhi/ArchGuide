你真的了解泛型吗？从java的Type到Spring的ResolvableType

>关于泛型的基本知识在本文中不会过多提及，本文主要解决的是如何处理泛型，以及java中Type接口下对泛型的一套处理机制，进而分析Spring中的ResolvableType。

# 必须知道的事

在阅读本文前，我下面两个问题大家必须知道

## 为什么会出现Type？

> Type体系的出现主要是为了解决泛型的一系列问题。大家在后文中会发现，Type的不同子类，分别解决了泛型的不同问题。

## <span id = "use">申明泛型与使用泛型的区别？</span>

我们往往会在方法或者类上申明泛型，就像下面这样

```java
public class Main<T, K> {
    
    private Map<T,?> map;
    
    public <U, V> void test01(Map<U, V> map) {

    }
    
    public void test02(Map<T, K> map) {

    }
}
```

在上面的例子中，类上标注的`T,K`是对泛型的申明，而在字段`map`以及`test02`方法中标注的`T,K`是对类上标注的`T,K`的使用。同样，就方法`test01`而言，在返回值前的`<U, V>`是对方法泛型的申明，而参数中的`Map<U, V>`是对泛型的使用。

我们再看一个例子

```java
//报错can't resolve symbol 'A',can't resolve symbol 'B'
class three<C> extends two<A, B> {
}
// 正常编译
class three<A,B,C> extends two<A, B> {
}

class two<A,B> {
}
```

在上面的例子中，第一个类会编译失败，这是因为`extends two<A, B>`是在使用具体的泛型类，那么使用的泛型类必须要明确具体类型。而`three<A,B,C> extends two<A, B>`之所以能够编译通过，是因为`extends two<A, B>`所使用的`A,B`在前面已经申明过了，也就是说类型已经被确定下来了。

我们可以对申明泛型与使用泛型做一个简单的总结：

1. 申明泛型都是在方法以及类上进行申明
2. 使用泛型主要在子类的定义，方法参数的定义，以及父类定义上。
3. 使用的泛型必须要先进行申明

在了解了这些内容后，我们现在进入正文。

# Type

## 简介

Type是Java 编程语言中所有类型的公共高级接口（官方解释），也就是Java中所有类型的“爹”；其中，“所有类型”的描述尤为值得关注。它并不是我们平常工作中经常使用的 int、String、List、Map等数据类型，而是从Java语言角度来说，对基本类型、引用类型向上的抽象；

Type体系中类型的包括：原始类型(Class)、参数化类型(ParameterizedType)、数组类型(GenericArrayType)、类型变量(TypeVariable)、基本类型(Class);

原始类型，不仅仅包含我们平常所指的类，还包括枚举、数组、注解等；

参数化类型，就是我们平常所用到的泛型List<String>、Map<String,Integer>这种；

数组类型，并不是我们工作中所使用的数组String[] 、byte[]，而是带有泛型的数组，即T[] ；

基本类型，也就是我们所说的java的基本类型，即int,float,double等

**Type体系的出现主要是为了解决泛型的一系列问题。**

## 接口定义

```java
public interface Type {
   // 返回这个类型的名称
    default String getTypeName() {
        return toString();
    }
}
```

可以看到`Type`接口内只定义了一个方法，这个方法会返回该类型的名称

## UML类图

![官网实例化](image/2020031301.png)

在上面的图中对于Class我相信大家都已经很了解了。我们主要对其余四个子接口进行测试分析

#### ParameterizedType

##### 简介

参数化类型，也就是我们所说的泛型。像List<String>就是一个参数化类型，但是List并不是，因为没有使用泛型。

##### 接口定义

```java
public interface ParameterizedType extends Type {
	// 对于一个参数化类型而言，必定是带有泛型的，所有这里是为了获取到其中的泛型的具体类型，也就是<>中的内容
    // 返回一个数组是因为，有时候会定义多个泛型，比如Map<String,String>
    Type[] getActualTypeArguments();

	// 获取原始类型，这里不带泛型，就是class
    Type getRawType();

	// 获取这个类所在类的类型，这里可能比较拗口，举个例子，假如当前这个ParameterizedType的类型为
    // O<T>.I<U>，那么调用这个方法所返回的就是一个O<T>类型
    Type getOwnerType();
}
```

##### 使用示例

```java
public class Main extends OwnerTypeDemo<String> {

    private List<String> stringList;

    private Map<String, String> stringStringMap;

    private Map.Entry<String, ?> entry;

    private OwnerTypeDemo<String>.Test<String> testOwnerType;

    private List list;

    private Map map;

    public void test(List<String> stringList, List list) {

    }

    public static void main(String[] args) {
        Class<Main> mainClass = Main.class;
        Field[] fields = mainClass.getDeclaredFields();
        for (Field field : fields) {
            Type genericType = field.getGenericType();
            String typeName = genericType.getTypeName();
            String name = field.getName();
            if (genericType instanceof ParameterizedType) {
                System.out.println(name + "是一个参数化类型,类型名称为：" + typeName);
                ParameterizedType parameterizedType = (ParameterizedType) genericType;
                Type[] actualTypeArguments = parameterizedType.getActualTypeArguments();
                System.out.println(name + "的actualTypeArguments：" + Arrays.toString(actualTypeArguments));
                Type ownerType = parameterizedType.getOwnerType();
                System.out.println(name + "的ownerType：" + ownerType);
                Type rawType = parameterizedType.getRawType();
                System.out.println(name + "的rawType：" + rawType);
            } else {
                System.out.println(name + "不是一个参数化类型,类型名称为：" + typeName);
            }
        }
        System.out.println("===================开始测试方法中的参数=========================");
        Method[] declaredMethods = mainClass.getDeclaredMethods();
        for (Method declaredMethod : declaredMethods) {
            String methodName = declaredMethod.getName();
            Type[] genericParameterTypes = declaredMethod.getGenericParameterTypes();
            for (int i = 0; i < genericParameterTypes.length; i++) {
                Type parameterType = genericParameterTypes[i];
                String typeName = parameterType.getTypeName();
                System.out.println("打印" + methodName + "方法的参数，" + "第" + (i + 1) + "个参数为：" + parameterType);
                if (parameterType instanceof ParameterizedType) {
                    System.out.println("第" + (i + 1) + "个参数是一个参数化类型, 类型名称为 ： " + typeName);
                } else {
                    System.out.println("第" + (i + 1) + "个参数不是一个参数化类型, 类型名称为 ： " + typeName);
                }
            }
        }
        System.out.println("===================开始测试父类中的泛型=========================");
        // 获取带有泛型的父类
        Type genericSuperclass = mainClass.getGenericSuperclass();
        if (genericSuperclass instanceof ParameterizedType) {
            System.out.println("父类是一个参数化类型，类型名称为：" + genericSuperclass.getTypeName());
        }

    }
}

class OwnerTypeDemo<T> {
    class Test<T> {

    }
}

```

程序会做如下输出：

```java
stringList是一个参数化类型,类型名称为：java.util.List<java.lang.String>
stringList的actualTypeArguments：[class java.lang.String]
stringList的ownerType：null
stringList的rawType：interface java.util.List
stringStringMap是一个参数化类型,类型名称为：java.util.Map<java.lang.String, java.lang.String>
stringStringMap的actualTypeArguments：[class java.lang.String, class java.lang.String]
stringStringMap的ownerType：null
stringStringMap的rawType：interface java.util.Map
entry是一个参数化类型,类型名称为：java.util.Map$Entry<java.lang.String, ?>
entry的actualTypeArguments：[class java.lang.String, ?]
entry的ownerType：interface java.util.Map
entry的rawType：interface java.util.Map$Entry
testOwnerType是一个参数化类型,类型名称为：main.java.OwnerTypeDemo<java.lang.String>$Test<java.lang.String>
testOwnerType的actualTypeArguments：[class java.lang.String]
testOwnerType的ownerType：main.java.OwnerTypeDemo<java.lang.String>
testOwnerType的rawType：class main.java.OwnerTypeDemo$Test
list不是一个参数化类型,类型名称为：java.util.List
map不是一个参数化类型,类型名称为：java.util.Map
===================开始测试方法中的参数=========================
打印main方法的参数，第1个参数为：class [Ljava.lang.String;
第1个参数不是一个参数化类型, 类型名称为 ： java.lang.String[]
打印test方法的参数，第1个参数为：java.util.List<java.lang.String>
第1个参数是一个参数化类型, 类型名称为 ： java.util.List<java.lang.String>
打印test方法的参数，第2个参数为：interface java.util.List
第2个参数不是一个参数化类型, 类型名称为 ： java.util.List
===================开始测试父类中的泛型=========================
父类是一个参数化类型，类型名称为：main.java.OwnerTypeDemo<java.lang.String>
```

------

可以看到，通过`ParameterizedType`可以让我们在[使用泛型](#use)时获取到具体的泛型类型，但是现在问题来了，如何获取到[申明泛型](#use)时的具体类型呢？

这种情况下，依赖`ParameterizedType`是没有办法处理的。这个时候就需要用到`Type`的另外一个子类`TypeVariable`了。

#### TypeVariable

##### 简介

类型变量，或者也可以叫泛型变量。主要让我们获取在方法以及类上申明的泛型，用于处理[泛型的申明](#use)

##### 接口定义

```java
public interface TypeVariable<D extends GenericDeclaration> extends Type, AnnotatedElement {
    // 获取泛型的边界
    Type[] getBounds();
	// 获取申明所在的具体对象
    D getGenericDeclaration();
	// 获取具体类型变量的名称
    String getName();
	// 
    AnnotatedType[] getAnnotatedBounds();
}
```

可以看到，`TypeVariable`本身也使用了泛型，并且泛型的上界为`GenericDeclaration`。在了解`TypeVariable`之前，有必要先对`GenericDeclaration`做一个简单的说明。`GenericDeclaration`这个接口主要限定了哪些地方可以定义`TypeVariable`，换言之，也就是定义了哪些地方可以申明泛型。这个接口只有3个实现类（忽略`Executable`抽象类）。如下：

![官网实例化](image/2020031501.png)

从这里我们也能看到，我们只能在方法（包括普通方法跟构造方法）以及类上申明泛型。

##### 使用示例











#### GenericArrayType

##### 简介

##### 接口定义

##### 使用示例





------

了解了ParameterizedType跟TypeVariable之后，接着我们思考一个问题，我们在定义泛型时，经常会使用来通配符，形如下面这种形式`{? extends Number}`，这个时候即使我们获取到`? extends Number`也没有办法做进一步的处理。这个时候就要用到我们接下来要介绍的这个接口了，请往下看

















#### WildcardType

##### 简介

专门用来处理泛型中的通配符，需要注意的是，WildcardType并不是JAVA所有类型中的一种，表示的仅仅是类似 `{? extends T}`、`{? super K}`这样的通配符表达式。

##### 接口定义

```java
public interface WildcardType extends Type {
	
    // 获取泛型上界
    Type[] getUpperBounds();
	
    // 获取泛型下界
    Type[] getLowerBounds();

}
```

上面这两个方法之所以会返回数组是因为，还有形如`{<T, K extends Main & Type>}`这样的通配符表达式。

##### 使用示例



# ResolvableType

# 总结