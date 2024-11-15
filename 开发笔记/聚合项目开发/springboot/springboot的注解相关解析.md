## 自定义注解用法

> 在Spring Boot中，我们可以通过自定义注解来实现各种自定义功能，例如验证请求参数、权限控制等。下面是自定义注解的基本用法：

 **创建注解类**

首先，我们需要创建一个注解类，通过@interface关键字来定义注解类，例如：

```java
@Target(ElementType.METHOD)
@Retention(RetentionPolicy.RUNTIME)
public @interface CustomAnnotation {
    String value() default "";
}

```
在上面的例子中，我们定义了一个名为CustomAnnotation的注解，它有一个属性value，默认值为空字符串。注解类的注释中，使用了@Target和@Retention元注解来指定注解适用的目标和生命周期。

**在需要使用注解的地方标记**

然后，我们可以在需要使用这个注解的地方进行标记。例如，我们可以在某个Controller的方法上使用CustomAnnotation注解：

```java
@RestController
public class MyController {
    
    @GetMapping("/")
    @CustomAnnotation("hello")
    public String helloWorld() {
        return "Hello World!";
    }
}

```

在上面的例子中，我们在helloWorld()方法上使用了@CustomAnnotation("hello")注解，其中的参数值为"hello"。

**处理注解**

最后，我们需要编写代码来处理这个注解。可以通过反射来获取注解的属性值，例如：

```java
@Aspect
@Component
public class CustomAspect {
    
    @Around("@annotation(com.example.demo.CustomAnnotation)")
    public Object handleCustomAnnotation(ProceedingJoinPoint joinPoint) throws Throwable {
        MethodSignature signature = (MethodSignature) joinPoint.getSignature();
        CustomAnnotation annotation = signature.getMethod().getAnnotation(CustomAnnotation.class);
        String value = annotation.value();
        // 处理注解
        return joinPoint.proceed();
    }
}

```
在上面的例子中，我们使用Spring AOP框架，在handleCustomAnnotation()方法中处理CustomAnnotation注解。

通过@Around("@annotation(com.example.demo.CustomAnnotation)")指定切点为带有CustomAnnotation注解的方法。

然后，使用反射获取注解的属性值，即value属性的值，并进行处理。

以上是自定义注解的基本用法，可以根据需求进一步扩展和优化。

## 注解方法里使用其他service 的引用方式

> 例子如下：
> 可以使用 @ 来引入service类使用

```java
@PreAuthorize("@ss.hasPermission('merchant:platform:record:page')")
```

## 注解方法里使用有string的参数，如何引入常量使用呢？

> 例子如下：
> 业务中代码有使用d