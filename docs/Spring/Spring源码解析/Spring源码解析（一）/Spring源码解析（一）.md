彻底读懂Spring（一）读源码，我们可以从第一行读起

> 在前面的文章中，我们已经完成了《[Spring官网阅读](https://blog.csdn.net/qq_41907991/article/details/105502255)》，有了上面的基础，那么源码的阅读也就不会太难了，从今天开始我们一步步走进Spring的源码。我们整个源码的解析将以下面这句代码为入口：
>
> ```java
> AnnotationConfigApplicationContext ac = new AnnotationConfigApplicationContext(Config.class);
> ```
>
> 下面就跟着我来看看，Spring第一行代码到底干了什么！
>
> **本系列文章对应`Spring版本为5.x`。**

上面这行代码就是执行了`AnnotationConfigApplicationContext`中的一个构造函数，其代码如下：

```java
public AnnotationConfigApplicationContext(Class<?>... annotatedClasses) {
    // 1.调用了AnnotationConfigApplicationContext的空参构造方法
    this();
    // 2.将配置类注册到容器中
    register(annotatedClasses);
	// 3.刷新容器    
    refresh();
}
```

# 找到第一行代码

本文的重点就是分析`AnnotationConfigApplicationContext`的空参构造，也就是文章标题所说的**第一行代码**。

其代码如下：

```java
public AnnotationConfigApplicationContext() {
    this.reader = new AnnotatedBeanDefinitionReader(this);
    this.scanner = new ClassPathBeanDefinitionScanner(this);
}
```

可以看到，仅仅是创建了两个对象而已，那么创建的这两个对象有什么用呢？

## AnnotatedBeanDefinitionReader是什么？

> 这个类的主要作用就是注册`BeanDefinition`，与之相类似的功能的类还有一个就是`ClassPathBeanDefinitionScanner`。它们最大的不同在于`AnnotatedBeanDefinitionReader`支持注册单个的`BeanDefinition`，而`ClassPathBeanDefinitionScanner`会一次注册所有扫描到的`BeanDefinition`。关于`ClassPathBeanDefinitionScanner`本文不专门做过多解释，只要能看懂这篇文章`ClassPathBeanDefinitionScanner`自然就懂了。
>
> 如果对于`BeanDefinition`不了解的推荐先阅读：
>
> [Spring官网阅读（四）BeanDefinition（上）](https://blog.csdn.net/qq_41907991/article/details/103589939)
>
> [Spring官网阅读（五）BeanDefinition（下）](https://blog.csdn.net/qq_41907991/article/details/103866987)

### AnnotatedBeanDefinitionReader源码解析

```java
// 这里只保留了这个类中的部分代码，其余不重要我都没有放出来
public class AnnotatedBeanDefinitionReader {
	
    // 上面我们已经说了，这个reader对象主要的工作就是注册BeanDefinition，那么将BeanDefinition注册到哪里去呢？所以它内部就保存了一个BeanDefinition的注册表。对应的就是我们代码中的AnnotationConfigApplicationContext
	private final BeanDefinitionRegistry registry;

    // 见名知意，Bean名称的生成器，生成BeanName
	private BeanNameGenerator beanNameGenerator = new AnnotationBeanNameGenerator();
	
    // 解析@Scope注解
	private ScopeMetadataResolver scopeMetadataResolver = new AnnotationScopeMetadataResolver();
	
    // 解析@Conditional注解
	private ConditionEvaluator conditionEvaluator;
	
    // 将解析指定的类成为BeanDefinition并注册到容器中
	public void registerBean(Class<?> annotatedClass, String name, Class<? extends Annotation>... qualifiers) {
		doRegisterBean(annotatedClass, null, name, qualifiers);
	}

    // 真正的执行注册的方法
    <T> void doRegisterBean(Class<T> annotatedClass, @Nullable Supplier<T> instanceSupplier, @Nullable String name,
                            @Nullable Class<? extends Annotation>[] qualifiers, BeanDefinitionCustomizer... definitionCustomizers) {
		
        // Spring在这里写死了，直接new了一个AnnotatedGenericBeanDefinition，也就是说通过reader对象注册的BeanDefinition都是AnnotatedGenericBeanDefinition。
        AnnotatedGenericBeanDefinition abd = new AnnotatedGenericBeanDefinition(annotatedClass);
       
        // 调用conditionEvaluator的shouldSkip方法
        // 判断当前的这个bd是否需要被注册
        if (this.conditionEvaluator.shouldSkip(abd.getMetadata())) {
            return;
        }
		// 在注册时可以提供一个instanceSupplier
        abd.setInstanceSupplier(instanceSupplier);
        
        // 解析@Scope注解，得到一个ScopeMetadata
        ScopeMetadata scopeMetadata = this.scopeMetadataResolver.resolveScopeMetadata(abd);
        
        // 将@Scope注解中的信息保存到bd中
        abd.setScope(scopeMetadata.getScopeName());
        
        // 调用beanNameGenerator生成beanName
        // 所谓的注册bd就是指定将bd放入到容器中的一个beanDefinitionMap中
        // 其中的key就是beanName,value就是解析class后得到的bd
        String beanName = (name != null ? name : this.beanNameGenerator.generateBeanName(abd, this.registry));
		
        // 这句代码将进一步解析class上的注解信息，Spring在创建这个abd的信息时候就已经将当前的class放入其中了，所有这行代码主要做的就是通过class对象获取到上面的注解（包括@Lazy，@Primary，@DependsOn注解等等），然后将得到注解中对应的配置信息并放入到bd中的属性中
        AnnotationConfigUtils.processCommonDefinitionAnnotations(abd);
        
        // 下面的这段不用过多关注，为了文章的完整性我这里还是说说我的理解
        // 正常的容器启动阶段qualifiers肯定等于null
        // 我能想到的不为空的方法就是直接在外部调用了register方法并且出入了qualifiers参数
        // 就是说我们手动直接注册了一个类，但是我们没有在类上添加@Lazy，@Primary注解，但是我们又希望能将其标记为Primary为true/LazyInit为true,这个时候就手动传入Primary.class跟Lazy.class即可。
        if (qualifiers != null) {
            for (Class<? extends Annotation> qualifier : qualifiers) {
                if (Primary.class == qualifier) {
                    abd.setPrimary(true);
                }
                else if (Lazy.class == qualifier) {
                    abd.setLazyInit(true);
                }
                else {
                    abd.addQualifier(new AutowireCandidateQualifier(qualifier));
                }
            }
        }
        
        // 我们注册时，我们可以传入一些回调方法，在解析得到bd后调用
        for (BeanDefinitionCustomizer customizer : definitionCustomizers) {
            customizer.customize(abd);
        }
		
        // bd中是没有beanName属性的，BeanDefinitionHolder中就是保存了beanName以及对应的BeanDefinition
        BeanDefinitionHolder definitionHolder = new BeanDefinitionHolder(abd, beanName);
        
        // 
        definitionHolder = AnnotationConfigUtils.applyScopedProxyMode(scopeMetadata, definitionHolder, this.registry);
        
        BeanDefinitionReaderUtils.registerBeanDefinition(definitionHolder, this.registry);
    }

}
```

