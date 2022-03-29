---
layout: post
title: Dagger2 Guidelines
category: Android
tabs: [android, dagger]
bigimg: [/assets/images/2019-06-24/dagger1.jpeg, /assets/images/2019-06-24/dagger2.jpg]
gh-repo: leno23/sunflower-java
# no-post-nav: true
---

&#160; &#160; &#160; &#160;这篇文章指导如何优化和高效利用Dagger2。

&#160; &#160; &#160; &#160;本文适合对Dagger2有一定经验，知道Dagger2的基本使用方法的读者。如果你对Dagger2还不熟悉，可以查看[官方文档](https://dagger.dev/)，或者参考我的上一篇文章[Android Sunflower + Dagger2](https://leno23.github.io/android/2019/06/24/android-sunflower-java-2.html)结合我的Demo项目[sunflower-java](https://github.com/leno23/sunflower-java/tree/master)一起学习。  
&#160; &#160; &#160; &#160;这篇文章主要参考[Keeping the Daggers Sharp](https://medium.com/square-corner-blog/keeping-the-daggers-sharp-%EF%B8%8F-230b3191c3f)以及[Dagger 2 on Android: The Official Guidelines](https://proandroiddev.com/dagger-2-on-android-the-official-guidelines-you-should-be-following-2607fd6c002e)。

## Constructor Injection

&#160; &#160; &#160; &#160;优先选择构造器注入(Constructor Injection)，而不是字段注入(Field Injection)。(Field不知道如何翻译，域？字段？**/(ㄒoㄒ)/~~**)
```java
// BAD
class CardConverter {
    @Inject
    PublicKeyManager publicKeyManager;
    public CardConverter() {}
}

// GOOD
class CardConverter {
    private final PublicKeyManager publicKeyManager;
    @Inject
    public CardConverter(PublicKeyManager publicKeyManager) {
        this.publicKeyManager = publicKeyManager;
    }
}
```
Kotlin消除了构造器注入的构造器模板：
```kotlin
class CardConverter @Inject constructor(
    private val publicKeyManager: PublicKeyManager
)
```
&#160; &#160; &#160; &#160;当然，对系统构造生成的对象，我们仍需使用字段注入。  
例如Activity：
```java
public class MainActivity extends Activity {
    @Inject
    PublicKeyManager publicKeyManager;
}
```

## Scoping

&#160; &#160; &#160; &#160;[Scoping is expensive](https://youtu.be/PBrhRvhF00k?t=1273)，使用@Scope是会消耗很多资源的。每个`@Singleton`或者自定义的`@Scope`都要通过Double check双重检查锁实现。所以除非必要，应该尽量减少使用@Scope。  
&#160; &#160; &#160; &#160;当需要集中访问一个具有可变状态的类时，单例很有用。
```java
// GOOD
@Singleton
public class BadgeCounter {
    public final Observable<Integer> badgeCount;
    @Inject
    public BadgeCounter(...) {
        badgeCount = ...
    }  
}
```
&#160; &#160; &#160; &#160;如果一个类，没有可变状态量，其实就没有必要使用@Singleton。而且Dagger中有个特殊的Scope注解`@Reusable`，参考[官方文档](https://dagger.dev/api/latest/dagger/Reusable.html)——有时您希望限制实例化@Inject构造的类或调用@Provides方法的次数，但不需要保证在任何特定组件或子组件的生命周期内使用完全相同的实例，可以使用@Reusable。这在Android等环境中很有用，因为分配可能很昂贵。比如，`Gson`我认为很适合用@Reusable。
```java
@Module
public class AppModule {
    @Reusable
    @Provides
    static Gson provideGson() {
        return new Gson();
    }
}
```
&#160; &#160; &#160; &#160;但有一种情况，当你初始化一个第三方库，虽然你不需要传入任何状态变量，但如果它内部在维护一个或几个变量，那么也应该使用`@Singleton`而不是`@Reusable`。比如[Stack Overflow response by Jeff Bowman](https://stackoverflow.com/questions/39136042/dagger-reusable-scope-vs-singleton/39154297#39154297)中讨论的问题，`OkHttpClient`内部有线程池等变量，所以应该声明为`@Singleton`。  
&#160; &#160; &#160; &#160;另一种情况，如果我们创建实例成本高昂，或者要避免重复创建和丢弃实例，则应该使用`@Singleton`。

## 优先使用@Inject而不是@Provides

&#160; &#160; &#160; &#160;在你可以控制的类中，优先使用Constructor Injection。当你使用`@Inject`注解Constructor时，当前类会自动加入Dagger的Graph中，而不用再次在Module中用`@Provides`声明。而且直接在当前类中声明，也更容易让人理解代码的意图。`@Provides`是给你创建那些控制不了的第三方库的实例而使用的。
```java
// BAD
@Module
class ToastModule {
    @Provides RealToastFactory realToastFactory(Application context) {
        return new RealToastFactory(context);
    }
}

// GOOD
public class RealToastFactory {
    private Application context;
    @Inject
    public RealToastFactory(Application context) {
        this.context = context;
    }  
}
```

## 优先使用static @Provides 方法

&#160; &#160; &#160; &#160;Dagger `@Provides`方法可以是静态的。  
```java
@Module
class ToastModule {
    @Provides
    static ToastFactory toastFactory(RealToastFactory factory) {
        return factory;
    }
}
```
&#160; &#160; &#160; &#160;如果@Provides方法是静态的，生成的代码可以直接调用方法，而不必创建模块实例。该方法调用可以由编译器内联。
```java
public final class DaggerAppComponent extends AppComponent {
  // ...
    @Override
    public ToastFactory toastFactory() {
        return ToastModule.toastFactory(realToastFactoryProvider.get())
    }
}
```
&#160; &#160; &#160; &#160;一个静态方法不会有太大变化，但如果所有绑定都是静态的，那么性能会有相当大的提升。  
&#160; &#160; &#160; &#160;如果你把module设为`abstract`抽象类，那所有的方法必须是`static`或`abstract`。如果不是，编译时会报错。  
&#160; &#160; &#160; &#160;如果像上一篇文章提到的，因为你的模块有状态而做不到abstract或者static，你应该重新审视修改它。就行官网提到的：
>Warning: it’s discouraged for modules to have state; this can lead to unpredictable behavior. Moreover, module instances in general are rarely useful. We have considered removing them.  --[Dagger Core Semantics](https://dagger.dev/semantics/)  

&#160; &#160; &#160; &#160;在Kotlin中它变得棘手，因为你不能像Java一样在类中使用静态方法。一种解决方案是：把module类设置成`object`；使用`@JvmStatic`注解声明provide方法：
```java 
@Module
object YourModule {
    @JvmStatic
    @Provides
    fun provideThatDependency() = ThatDependency()
}
```

## 优先使用@Binds而不是@Provides

&#160; &#160; &#160; &#160;当你将一种类型映射到另一种类型时，使用`@Binds`而不是`@Provides`。
```java
@Module
abstract class BookPresenterModule {
    @Binds
    abstract BookPresenter bindBookPresenter(BookPresenterImpl bookPresenter);
}
```
&#160; &#160; &#160; &#160;该方法必须是抽象的。它不会被调用;生成的代码将知道直接使用该实现。

## 避免在interface绑定中使用@Singleton

>Statefulness is an implementation detail

```java
@Module
abstract class ToastModule {
    // BAD, remove @Singleton
    @Singleton
    @Binds
    abstract ToastFactory toastFactory(RealToastFactory factory);
}
```
&#160; &#160; &#160; &#160;只有实现才知道他们是否需要确保集中访问可变状态。  
&#160; &#160; &#160; &#160;将实现绑定到接口时，不应该有任何作用域注释。

## Application context

&#160; &#160; &#160; &#160;设置Dagger的第一步是都是将应用程序上下文设为公开依赖项。对于Android应用来说，就是其Application context。  
1. 最早的实现方式：
    ```java
    @Singleton
    @Component(modules = {ApplicationModule.class, YourModule.class, ThatOtherModule.class})
    interface ApplicationComponent


    @Module
    class ApplicationModule {
        private Context applicationContext;
        public ApplicationModule(private Context applicationContext) {
            this.applicationContext = applicationContext;
        }
        @Provides
        Context provideApplicationContext(){
            return applicationContext;
        }
    }

    class YourApplication extends Application() {
        DaggerApplicationComponent.builder()
            .applicationModule(ApplicationModule(applicationContext))
            .build();
    }
    ```
    &#160; &#160; &#160; &#160;我们创建一个接收应用程序上下文作为构造函数参数的模块，然后创建一个公开它的提供方法。这很好用，但是就像上文说的，module有了状态，我们不能再使用静态`@Provides`方法了。

2. 为了解决这个问题，在[Dagger 2.9](https://github.com/google/dagger/releases/tag/dagger-2.9)中，加入了`@BindsInstance`注解。我们能够在组件（或子组件）构建器中注释方法，以便我们可以轻松地绑定在图形外部构造的实例。
    ```java
    @Singleton
    @Component(modules = {ApplicationModule.class, YourModule.class, ThatOtherModule.class})
    interface ApplicationComponent{
        @Component.Builder
        interface Builder {
            @BindsInstance
            Builder applicationContext(Context applicationContext);
            ApplicationComponent build();
        }
    }


    class YourApplication extends Application() {
        DaggerApplicationComponent
            .builder()
            .applicationContext(applicationContext)
            .build();
    }
    ```
    &#160; &#160; &#160; &#160;虽说还是比较繁琐，但它不再需要一个有状态模块。

3. 之后，在[Dagger 2.22](https://github.com/google/dagger/releases/tag/dagger-2.22)引入了`@Component.Factory`注解，即component factory来代替component builder。工厂只有一个方法，我们可以注释它的参数。
    ```java
    @Component(modules = ...)
    interface ApplicationComponent {

        @Component.Factory
        interface Factory {
            ApplicationComponent create(@BindsInstance Context applicationContext);
        }
        //...
    }

    class YourApplication extends Application() {
        DaggerApplicationComponent
            .factory()
            .create(applicationContext);
    }
    ```
    &#160; &#160; &#160; &#160;它不仅简化了编码。而且这种改变带来了编译时的安全性：之前，如果我们有多个builder方法，就有可能忘记调用其中一个并且它仍然可以编译。现在，我们只有一个方法，每当我们调用它时，我们必须提供每个参数，因此不可能再忘记为组件提供强制依赖。