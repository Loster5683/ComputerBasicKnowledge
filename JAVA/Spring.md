# Spring

###  IoC 概述

#### IoC：Inverse of Control（控制反转）

- 读作**“反转控制”**，更好理解，不是什么技术，而是一种设计思想，就是将原本在程序中手动创建对象的控制权，交由Spring框架来管理。
- **正控：**若要使用某个对象，需要自己去负责对象的创建
- **反控：**若要使用某个对象，只需要从 Spring 容器中获取需要使用的对象，不关心对象的创建过程，也就是把创建对象的控制权反转给了Spring框架
- **好莱坞法则：**Don’t call me ,I’ll call you

**控制反转是一种通过描述（在 Java 中可以是 XML 或者注解）并通过第三方（Spring）去产生或获取特定对象的方式。**

### Spring IoC 容器

Spring 会提供 **IoC 容器**来管理和容纳我们所开发的各种各样的 Bean，并且我们可以从中获取各种发布在 Spring IoC 容器里的 Bean，并且**通过描述**可以得到它。

Spring IoC 容器的设计主要是基于以下两个接口：

- **BeanFactory**
- **ApplicationContext**

其中 ApplicationContext 是 BeanFactory 的子接口之一，换句话说：**BeanFactory 是 Spring IoC 容器所定义的最底层接口，**而 ApplicationContext 是其最高级接口之一，并对 BeanFactory 功能做了许多的扩展，所以在**绝大部分的工作场景下**，都会使用 ApplicationContext 作为 Spring IoC 容器。

#### BeanFactory 和 ApplicationContext 的区别：

- **BeanFactory：**是Spring中最底层的接口，只提供了最简单的IoC功能,负责配置，创建和管理bean。
  在应用中，一般不使用 BeanFactory，而推荐使用ApplicationContext（应用上下文），原因如下。
- **ApplicationContext：**
  1.继承了 BeanFactory，拥有了基本的 IoC 功能；
  2.除此之外，ApplicationContext 还提供了以下功能：
  ① 支持国际化；
  ② 支持消息机制；
  ③ 支持统一的资源加载；
  ④ 支持AOP功能；

### Spring IoC 的容器的初始化和依赖注入

虽然 Spring IoC 容器的生成十分的复杂，但是大体了解一下 Spring IoC 初始化的过程还是必要的。这对于理解 Spring 的一系列行为是很有帮助的。

**注意：**Bean 的定义和初始化在 Spring IoC 容器是两大步骤，它是先定义，然后初始化和依赖注入的。

- **Bean 的定义分为 3 步：**
  1.Resource 定位
  Spring IoC 容器先根据开发者的配置，进行资源的定位，在 Spring 的开发中，通过 XML 或者注解都是十分常见的方式，定位的内容是由开发者提供的。
  2.BeanDefinition 的载入
  这个时候只是将 Resource 定位到的信息，保存到 Bean 定义（BeanDefinition）中，此时并不会创建 Bean 的实例
  3.BeanDefinition 的注册
  这个过程就是将 BeanDefinition 的信息发布到 Spring IoC 容器中
  **注意：**此时仍然没有对应的 Bean 的实例。

做完了以上 3 步，Bean 就在 Spring IoC 容器中被定义了，而没有被初始化，更没有完成依赖注入，也就是没有注入其配置的资源给 Bean，那么它还不能完全使用。

对于初始化和依赖注入，Spring Bean 还有一个配置选项——**【lazy-init】**，其含义就是**是否初始化 Spring Bean**。在没有任何配置的情况下，它的默认值为 default，实际值为 false，也就是 **Spring IoC 默认会自动初始化 Bean**。如果将其设置为 true，那么只有当我们使用 Spring IoC 容器的 getBean 方法获取它时，它才会进行 Bean 的初始化，完成依赖注入。

[Spring IoC 详解](https://www.cnblogs.com/wmyskxz/p/8824597.html)

### 装配 Spring Bean 

大部分场景下，我们都会使用 ApplicationContext 的具体实现类，因为对应的 Spring IoC 容器功能相对强大。

而在 Spring 中提供了 3 种方法进行配置：

- 在 XML 文件中显式配置
- 在 Java 的接口和类中实现配置
- 隐式 Bean 的发现机制和自动装配原则

#### 方式选择的原则

在现实的工作中，这 3 种方式都会被用到，并且在学习和工作之中常常混合使用，所以这里给出一些关于这 3 种优先级的建议：

1.**最优先：通过隐式 Bean 的发现机制和自动装配的原则。**
基于约定由于配置的原则，这种方式应该是最优先的

- **好处：**减少程序开发者的决定权，简单又不失灵活。

2.**其次：Java 接口和类中配置实现配置**
在没有办法使用自动装配原则的情况下应该优先考虑此类方法

- **好处：**避免 XML 配置的泛滥，也更为容易。
- **典型场景：**一个父类有多个子类，比如学生类有两个子类，一个男学生类和女学生类，通过 IoC 容器初始化一个学生类，容器将无法知道使用哪个子类去初始化，这个时候可以使用 Java 的注解配置去指定。

3.**最后：XML 方式配置**
在上述方法都无法使用的情况下，那么也只能选择 XML 配置的方式。

- **好处：**简单易懂（当然，特别是对于初学者）
- **典型场景：**当使用第三方类的时候，有些类并不是我们开发的，我们无法修改里面的代码，这个时候就通过 XML 的方式配置使用了。

#### 通过注解装配 Bean

