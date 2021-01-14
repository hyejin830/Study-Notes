Dagger기반의 DI 라이브러리로 Annotation을 이용한 컴파일 타임 generated code로 의존성 주입을 구현

Hilt는 Dagger의 강력한 기능은 그대로 활용하면서 불필요한 annotation을 제거하여 사용하기 훨씬 쉽게 만든 라이브러리입니다.

-----

https://developer.android.com/training/dependency-injection/hilt-android?hl=ko

수동 종속 항목 삽입을 실행하는 상용구를 줄이는 Android용 종속 항목 삽입 라이브러리

모든 Android 클래스에 컨테이너를 제공하고 수명 주기를 자동으로 관리함으로써 애플리케이션에서 DI를 사용하는 표준 방법을 제공

## 종속 항목 추가

프로젝트의 루트 `build.gradle` 파일에 추가

```groovy
classpath 'com.google.dagger:hilt-android-gradle-plugin:2.28-alpha'
```

Gradle 플러그인을 적용,  `app/build.gradle` 파일에 다음 종속 항목을 추가

```groovy
apply plugin: 'dagger.hilt.android.plugin'
```

```groovy
dependencies {
    implementation "com.google.dagger:hilt-android:2.28-alpha"
    kapt "com.google.dagger:hilt-android-compiler:2.28-alpha"
}
```

Android 스튜디오 4.0 이상이 필요

## Hilt 애플리케이션 클래스

Hilt를 사용하는 모든 앱은 `@HiltAndroidApp`으로 주석이 지정된 `Application` 클래스를 포함해야 한다

`Application` 객체의 수명 주기에 연결되며 이와 관련한 종속 항목을 제공

## Android 클래스에 종속 항목 삽입

`Application` 클래스에 Hilt를 설정하고 애플리케이션 수준 구성요소를 사용할 수 있게 되면 Hilt는 `@AndroidEntryPoint` 주석이 있는 다른 Android 클래스에 종속 항목을 제공할 수 있다

Hilt는 현재 다음 Android 클래스를 지원

- `Application`(`@HiltAndroidApp`을 사용하여)
- `Activity`
- `Fragment`
- `View`
- `Service`
- `BroadcastReceiver`

구성요소에서 종속 항목을 가져오려면 다음과 같이 `@Inject` 주석을 사용하여 필드 삽입을 실행한다

```kotlin
@AndroidEntryPoint
class ExampleActivity : AppCompatActivity() {

  @Inject lateinit var analytics: AnalyticsAdapter
  ...
}
```

## Hilt 결합 정의

Hilt에 결합 정보를 제공하는 한 가지 방법은 *생성자 삽입*입니다.

`@Inject` 주석을 사용하여 클래스의 인스턴스를 제공하는 방법을 Hilt에 알려준다

```kotlin
class AnalyticsAdapter @Inject constructor(
  private val service: AnalyticsService
) { ... }
```

## Hilt 모듈

인터페이스를 생성자 삽입할 수 없습니다. 또한 외부 라이브러리의 클래스와 같이 소유하지 않은 유형도 생성자 삽입할 수 없습니다. 이럴 때는 *Hilt 모듈*을 사용하여 Hilt에 결합 정보를 제공할 수 있습니다.

이 모듈은 특정 유형의 인스턴스를 제공하는 방법을 Hilt에 알려줍니다. 

`@InstallIn` 주석을 지정하여 각 모듈을 사용하거나 설치할 Android 클래스를 Hilt에 알려야 합니다.

### @Binds를 사용하여 인터페이스 인스턴스 삽입

```
interface AnalyticsService {
  fun analyticsMethods()
}

// Constructor-injected, because Hilt needs to know how to
// provide instances of AnalyticsServiceImpl, too.
class AnalyticsServiceImpl @Inject constructor(
  ...
) : AnalyticsService { ... }

@Module
@InstallIn(ActivityComponent::class)
abstract class AnalyticsModule {

  @Binds
  abstract fun bindAnalyticsService(
    analyticsServiceImpl: AnalyticsServiceImpl
  ): AnalyticsService
}
```

Hilt가 AnalyticsModule의 종속 항목을 `ExampleActivity`에 삽입하기를 원하기 때문에 Hilt 모듈 `AnalyticsModule`에 `@InstallIn(ActivityComponent::class)` 주석을 지정합니다. 이 주석은 `AnalyticsModule`의 모든 종속 항목을 앱의 모든 활동에서 사용할 수 있음을 의미합니다.

### @Provides를 사용하여 인스턴스 삽입

클래스가 외부 라이브러리에서 제공되므로 클래스를 소유하지 않은 경우([Retrofit](https://square.github.io/retrofit/), [`OkHttpClient`](https://square.github.io/okhttp/) 또는 [Room 데이터베이스](https://developer.android.com/topic/libraries/architecture/room?hl=ko)와 같은 클래스) 또는 [빌더 패턴](https://en.wikipedia.org/wiki/Builder_pattern)으로 인스턴스를 생성해야 하는 경우에도 생성자 삽입이 불가능합니다

```
@Module
@InstallIn(ActivityComponent::class)
object AnalyticsModule {

  @Provides
  fun provideAnalyticsService(
    // Potential dependencies of this type
  ): AnalyticsService {
      return Retrofit.Builder()
               .baseUrl("https://example.com")
               .build()
               .create(AnalyticsService::class.java)
  }
}
```

---

