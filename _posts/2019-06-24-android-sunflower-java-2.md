---
layout: post
title: Android Sunflower + Dagger2
category: Android
tabs: [android, android jetpack, dagger]
bigimg: [/assets/images/2019-06-24/dagger1.jpeg, /assets/images/2019-06-24/dagger2.jpg]
gh-repo: leno23/sunflower-java
# no-post-nav: true
---

&#160; &#160; &#160; &#160;这篇文章主要介绍Sunflower项目加入`Dagger2`依赖注入框架的过程，也是我对Dagger框架学习和理解加深的一个过程。

&#160; &#160; &#160; &#160;这里就不再对Dagger中`@Component`, `@Subcomponent`, `@Module`, `@Binds`, `@Provides`, `@Inject`, `@Singleton`等注解的作用和用法做过多的介绍了，大家可以参考[官方文档](https://dagger.dev/)及大量的第三方博客。  
&#160; &#160; &#160; &#160;主要介绍一下使用Dagger2过程中几次改进及对应的理解。

Version | Description
 ------ | -------
[v1.3.0](https://github.com/leno23/sunflowr-by-java/releases/tag/v1.3.0)  | improve performance and a elegant way to inject params like intent of activity
[v1.2.0](https://github.com/leno23/sunflowr-by-java/releases/tag/v1.2.0)  | another simple and better way to inject `ViewModel` 
[v1.1.0](https://github.com/leno23/sunflowr-by-java/releases/tag/v1.1.0)  | using dagger2 as di, add LongClickListener to delete plant
[v1.0.0](https://github.com/leno23/sunflowr-by-java/releases/tag/v1.0.0)  | android architecture in Java

## v1.1.0版本

&#160; &#160; &#160; &#160;[v1.1.0](https://github.com/leno23/sunflowr-by-java/releases/tag/v1.1.0)版本是参照Google官方示例[GithubBrowserSample](https://github.com/googlesamples/android-architecture-components/tree/master/GithubBrowserSample)写的。按照其中的DI注入流程从Kotlin改写为Java。  
&#160; &#160; &#160; &#160;在Kotlin改写成Java的过程中也遇到了不少问题。比如`@IntoMap`，`@MapKey`即`Multibindings`问题，查了很多博客，都没有说清楚。最后又去仔细查看[官方文档](https://dagger.dev/multibindings)，最终解决了问题。所以一定要对官方文档足够重视！不要图快，跳过官方文档，只看一些第三方的快速集成的方法。在看其他第三方资料之前，最好先把官方文档认真读一遍，会减少很多疑惑，变相的节省宝贵的时间。  
&#160; &#160; &#160; &#160;如果你是从项目一开始就用Dagger作为依赖注入框架，在掌握了基本使用方法后，一般不会出什么问题；如果你像我一样把之前的项目改成使用Dagger依赖注入，那一定要认真仔细的检查是否所有的依赖都注入`@Inject`了，是否所有的`Module`都加入了`Component`等等。  
&#160; &#160; &#160; &#160;我就是因为忘记把`ViewModleModule`加入到`Component`中，导致一直报错。我以为是我把Kotlin转成Java过程中出了问题，所以查了大量的资料。不过虽然浪费了不少时间，但也加深了我对Dagger的理解以及Kotlin和Java之间的异同。也催生了v1.2.0版本。  
&#160; &#160; &#160; &#160;我们先来看看v1.1.0版本是怎么做的。  

主要代码片段：  
[ViewModelFactory](https://github.com/leno23/sunflowr-by-java/blob/v1.1.0/app/src/main/java/com/cym/sunflower/viewmodels/ViewModelProviderFactory.java):
```java
@Singleton
public class ViewModelProviderFactory extends ViewModelProvider.NewInstanceFactory {
    Map<Class<? extends ViewModel>, Provider<ViewModel>> creators;
    @Inject
    public ViewModelProviderFactory(Map<Class<? extends ViewModel>, Provider<ViewModel>> creators) {
        this.creators = creators;
    }
    @NonNull
    @Override
    public <T extends ViewModel> T create(@NonNull Class<T> modelClass) {
        Provider<ViewModel> creator = creators.get(modelClass);
        if (creator == null) {
            for (Map.Entry<Class<? extends ViewModel>, Provider<ViewModel>> entry : creators.entrySet()) {
                if (modelClass.isAssignableFrom(entry.getKey())) {
                    creator = entry.getValue();
                    break;
                }
            }
        }
        if (creator == null) {
            throw new IllegalArgumentException("unknown model class " + modelClass);
        }
        return (T) creator.get();
    }
}
```

[ViewModelKey](https://github.com/leno23/sunflowr-by-java/blob/v1.1.0/app/src/main/java/com/cym/sunflower/di/ViewModelKey.java):
```java
@MapKey
@interface ViewModelKey {
    Class<? extends ViewModel> value();
}
```

[ViewModelModule](https://github.com/leno23/sunflowr-by-java/blob/v1.1.0/app/src/main/java/com/cym/sunflower/di/ViewModelModule.java):
```java
@Module
abstract class ViewModelModule {
    @Binds
    @IntoMap
    @ViewModelKey(GardenPlantingListViewModel.class)
    abstract ViewModel bindGardenPlantingListViewModel(GardenPlantingListViewModel gardenPlantingListViewModel);
    @Binds
    @IntoMap
    @ViewModelKey(PlantDetailViewModel.class)
    abstract ViewModel bindPlantDetailViewModel(PlantDetailViewModel plantDetailViewModel);
    @Binds
    @IntoMap
    @ViewModelKey(PlantListViewModel.class)
    abstract ViewModel bindPlantListViewModel(PlantListViewModel plantListViewModel);
    @Binds
    abstract ViewModelProvider.Factory bindViewModelFactory(ViewModelProviderFactory factory);
}
```

## v1.2.0版本

&#160; &#160; &#160; &#160;上面提到的，在查阅资料的过程中，看到了一种ViewModel简洁的注入方式，[Android ViewModel injections revisited](https://azabost.com/android-viewmodel-injections-revisited/)。所以就按照这样的方法重新写了ViewModel的Factory。这就是v1.2.0的由来。

主要代码片段：  
[ViewModelFactory](https://github.com/leno23/sunflowr-by-java/blob/v1.2.0/app/src/main/java/com/cym/sunflower/viewmodels/ViewModelSimpleFactory.java):
```java
@Singleton
public class ViewModelSimpleFactory<VM extends ViewModel> extends ViewModelProvider.NewInstanceFactory {
    private Lazy<VM> viewModel;
    @Inject
    public ViewModelSimpleFactory(Lazy<VM> viewModel) {
        this.viewModel = viewModel;
    }
    @NonNull
    @Override
    public <T extends ViewModel> T create(@NonNull Class<T> modelClass) {
        return (T) viewModel.get();
    }
}
```

[ViewModel](https://github.com/leno23/sunflower-java/blob/v1.2.0/app/src/main/java/com/cym/sunflower/viewmodels/GardenPlantingListViewModel.java):
```java
public class GardenPlantingListViewModel extends ViewModel {
    
    @Inject
    public GardenPlantingListViewModel(GardenPlantingRepository gardenPlantingRepository) {
    }
}
```
&#160; &#160; &#160; &#160;这样就好了，是不是比v1.1.0中的方法简洁了很多。剩下的你只需要在Activity或Fragment中注入使用就好了。  

[Activity or Fragment](https://github.com/leno23/sunflowr-by-java/blob/v1.2.0/app/src/main/java/com/cym/sunflower/ui/GardenFragment.java):
```java
public class GardenFragment extends Fragment implements Injectable {
    @Inject
    public ViewModelSimpleFactory<GardenPlantingListViewModel> factory;
    @Override
    public View onCreateView(LayoutInflater inflater, ViewGroup container, Bundle savedInstanceState) {
        //...
        GardenPlantingListViewModel viewModel = ViewModelProviders.of(this, factory).get(GardenPlantingListViewModel.class);
        //...
    }
}
```

## v1.3.0版本

&#160; &#160; &#160; &#160;上面完成了基本的依赖注入，但有一种情况，参数是动态变化的。比如说依赖于Activity或Fragment传递的`intent`，即每次都是不同的。这种情况如何实现呢？  
&#160; &#160; &#160; &#160;在[v1.1.0](https://github.com/leno23/sunflowr-by-java/releases/tag/v1.1.0)和[v1.2.0](https://github.com/leno23/sunflowr-by-java/releases/tag/v1.2.0)版本中，采用了折中的办法。就是先不注入这个参数，在拿到这个实例后，通过`setter`进行设置。如：  
[PlantDetailViewModel](https://github.com/leno23/sunflower-java/blob/v1.2.0/app/src/main/java/com/cym/sunflower/viewmodels/PlantDetailViewModel.java):
```java
public class PlantDetailViewModel extends ViewModel {
    PlantRepository plantRepository;
    private GardenPlantingRepository gardenPlantingRepository;
    private String plantId;
    @Inject
    public PlantDetailViewModel(PlantRepository plantRepository, GardenPlantingRepository gardenPlantingRepository) {
        this.plantRepository = plantRepository;
        this.gardenPlantingRepository = gardenPlantingRepository;
    }

    public void setPlantId(String plantId) {
        this.plantId = plantId;
    }
}
```
[PlantDetailFragment](https://github.com/leno23/sunflower-java/blob/v1.2.0/app/src/main/java/com/cym/sunflower/ui/PlantDetailFragment.java):
```java
public class PlantDetailFragment extends Fragment implements Injectable {

    @Inject
    public ViewModelSimpleFactory<PlantDetailViewModel> factory;
    @Override
    public View onCreateView(LayoutInflater inflater, ViewGroup container, Bundle savedInstanceState) {
        String plantId = PlantDetailFragmentArgs.fromBundle(getArguments()).getPlantId();

        PlantDetailViewModel plantDetailViewModel = ViewModelProviders.of(this, factory).get(PlantDetailViewModel.class);
        plantDetailViewModel.setPlantId(plantId);
    }
}
```

&#160; &#160; &#160; &#160;这种方式不太让我满意，所以我又查找资料。找到了两种方式：  
1. 创建带构造函数的`module`  
    比如我们构建一个PlantDetailModule：
    ```java
    @Module
    public class PlantDetailModule(){
        String plantId;
        public PlantDetailModule(String plantId) {
            this.plantId = plantId;
        }

        @Provides
        PlantDetailViewModel providePlantDetailViewModel(PlantRepository plantRepository, GardenPlantingRepository gardenPlantingRepository) {
            return new PlantDetailViewModel(plantRepository, gardenPlantingRepository, plantId)
        }
    }
    ```
    PlantDetailViewModel:
    ```java
    public class PlantDetailViewModel extends ViewModel {
        PlantRepository plantRepository;
        private GardenPlantingRepository gardenPlantingRepository;
        private String plantId;
        
        public PlantDetailViewModel(PlantRepository plantRepository, GardenPlantingRepository gardenPlantingRepository, String plantId) {
            this.plantRepository = plantRepository;
            this.gardenPlantingRepository = gardenPlantingRepository;
            this.plantId = plantId;
        }
    }
    ```
    Activity:
    ```java
    public class MainActivity extends AppCompatActivity {
 
        @Inject
        PlantDetailViewModel viewModel;
        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            //...
            DaggerAppComponent.builder()
                    .plantDetailModule(new PlantDetailModule("apple"))
                    .build()
                    .inject(this);
            //...
        }
    }
    ```
    &#160; &#160; &#160; &#160;这种方式显然很复杂，而且也不符合Dagger的最佳使用方式：`module`最好是无状态的，static的。详细可查看另一篇文章[Dagger2 Guidelines](https://leno23.github.io/android/2019/06/27/dagger-guidelines.html)。
2. 使用[AssistedInject库](https://github.com/square/AssistedInject)  
    &#160; &#160; &#160; &#160;AssistedInject是square开发的配合Dagger使用的一个依赖注入库。好消息是Dagger在未来也会集成这个库。  
    使用方法：  
    首先，添加依赖:  
    ```java
    compileOnly 'com.squareup.inject:assisted-inject-annotations-dagger2:0.4.0'
    annotationProcessor 'com.squareup.inject:assisted-inject-processor-dagger2:0.4.0'
    ```

    第二，使用注解`@AssistedInject`代替`@Inject`, 为变量前添加`@Assisted`注解, 使用`@AssistedInject.Factory`注解添加一个Factory接口.  
    [PlantDetailViewModel](https://github.com/leno23/sunflower-java/blob/v1.3.0/app/src/main/java/com/cym/sunflower/viewmodels/PlantDetailViewModel.java):
    ```java
    public class PlantDetailViewModel extends ViewModel {
        @AssistedInject
        public PlantDetailViewModel(PlantRepository plantRepository, GardenPlantingRepository gardenPlantingRepository,@Assisted String plantId) {
        }
        @AssistedInject.Factory
        public interface Factory {
            PlantDetailViewModel create(String plantId);
        }
    }
    ```
    第三, 创建一个`module`或者加入一个已经存在的module，使得上面创建Factory接口加入graph中。  
    [GardenActivityModule](https://github.com/leno23/sunflower-java/blob/v1.3.0/app/src/main/java/com/cym/sunflower/di/GardenActivityModule.java):
    ```java
    @AssistedModule
    @Module(includes = {AssistedInject_GardenActivityModule.class})
    abstract class GardenActivityModule {
        //...
    }
    ```
    第三，在[Activity or Fragement](https://github.com/leno23/sunflower-java/blob/v1.3.0/app/src/main/java/com/cym/sunflower/ui/PlantDetailFragment.java):
    ```java
    public class PlantDetailFragment extends Fragment implements Injectable {
        @Inject
        public PlantDetailViewModel.Factory factory2;
        @Override
        public View onCreateView(LayoutInflater inflater, ViewGroup container, Bundle savedInstanceState) {
            String plantId = PlantDetailFragmentArgs.fromBundle(getArguments()).getPlantId();
            PlantDetailViewModel plantDetailViewModel = factory2.create(plantId);
        }
    }
    ```
    &#160; &#160; &#160; &#160;其中，第一和第三步只需要做一次。也就是说除了第一次，其他注入你只需要写一个Foctory接口。所以这还算是一个比较优雅的解决方案。

&#160; &#160; &#160; &#160;但是，这个对一般的带变量参数的注入是个完美的解决方案。但对`ViewModel`来说，它应该由`ViewModelProvider.Factory`提供。我暂时还想不出比较好的解决方法。如果你有更好的方案，欢迎你联系我。



&#160; &#160; &#160; &#160;欢迎Star&Follow！