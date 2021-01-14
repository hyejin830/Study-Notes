https://codelabs.developers.google.com/codelabs/android-hilt/?hl=ko#1

## 4. Hilt in your Application

앱의 생명주기에 붙어 있는 컨테이너를 연결하기 위해, Application 클래스에 @HiltAndroidApp 달아야 한다

@HiltAndroidApp은 종속성 주입을 사용할 수있는 애플리케이션의 기본 클래스를 포함하여 Hilt의 코드 생성을 발생시킨다. 애플리케이션 컨테이너는 앱의 상위 컨테이너이며, 이는 다른 컨테이너가 제공하는 종속성에 액세스 할 수 있음을 의미한다

```
@HiltAndroidApp
class LogApplication : Application() {
    ...
}
```

## 5. Field injection with Hilt

LogsFragment에서 Hilt 를 사용 하려면  @AndroidEntryPoint 어노테이션을 달아야한다

```
@AndroidEntryPoint
class LogsFragment : Fragment() {
    ...
}
```

Android 클래스에 @AndroidEntryPoint하면 Android 클래스 수명주기를 따르는 종속성 컨테이너가 생성된다

주입할 필드에 hilt @inject 어노테이션을 사용하여 서로 다른 타입의 인스턴스에 주입할 수 있다 

```
@AndroidEntryPoint
class LogsFragment : Fragment() {
    // filed injeciton
    @Inject lateinit var logger: LoggerLocalDataSource
    @Inject lateinit var dateFormatter: DateFormatter

    ...
}
```

Hilt에게 한 유형의 인스턴스를 제공하는 방법을 알리려면 주입하려는 클래스의 생성자에 @Inject 주석을 추가해야 한다

```
class DateFormatter @Inject constructor() { ... }
```

## 6. Scoping instances to containers

어노티에션을 사용하여 인스턴스범위를 컨테이너로 지정 가능하다

인스턴스의 범위를 애플리케이션 컨테이너로 지정하는 어노테이션은 @Singleton 이다

이 어노테이션은 항상 동일한 인스턴스를 제공한다 

```
@Singleton
class LoggerLocalDataSource @Inject constructor(private val logDao: LogDao) {
    ...
}
```

LoggerLcoalDataBase에는 생성자인 LogDao의 인스턴스가 필요하다. 하지만 LogDao는 인터페이스이기 때문에 생성자에 어노테이션을 달 수 없다. 그렇다면 이 유형의 인스턴스 제공은 어떻게 할까?

## 7. Hilt modules

* 힐트는 인스턴스를 제공하는 방법에 대해 바인딩이라고도 부른다

모듈은 Hilt에 바인딩을 추가하는 데 사용된다

 Hilt 모듈에는 프로젝트에 포함되지 않은 인터페이스 또는 클래스와 같이 생성자 주입이 불가능한 유형에 대한 바인딩을 포함해야 한다

Hilt 모듈은 @Module 과 @InstllIn 을 단 클래스이다

@Module : 모듈임을 알린다

@InstallIn : Hilt 컴포넌트로 지정함으로써, 바인딩을 사용할 수 있는 컨테이너로 알린다

##### 모듈 생성

```
package com.example.android.hilt.di

@InstallIn(ApplicationComponent::class)
@Module
object DatabaseModule {

}
```

LoggerLocalDataBaseSource는 어플리케이션 컨테이너의 범위이기 때문에, LogDao 바인딩은 어플리케이션 컨테이너에서 가능하게 하는 것이 필요하다

ApplicationCompnent.class처럼 관련있는 Hilt 컴포넌트의 클래스를 전달하고 @InstallIn 어노테이션을 사용함으로써 요구조건을 구체화한다

##### @Provides로 인스턴스 제공

```
@Module
object DatabaseModule {

    @Provides
    fun provideLogDao(database: AppDatabase): LogDao {
        return database.logDao()
    }
}
```

위 코드는 LogDao의 인스턴스를 제공할 때, database.logDao() 가 실행되어 진다 

또한 AppDatabase는 전이 종속성이 있어, 이 타입으로 인스턴스를 제공하는 방법을 알려줘야 한다

```
@Module
object DatabaseModule {

    @Provides
    @Singleton
    fun provideDatabase(@ApplicationContext appContext: Context): AppDatabase {
        return Room.databaseBuilder(
            appContext,
            AppDatabase::class.java,
            "logging.db"
        ).build()
    }

    @Provides
    fun provideLogDao(database: AppDatabase): LogDao {
        return database.logDao()
    }
}
```

