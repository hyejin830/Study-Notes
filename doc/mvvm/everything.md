### ViewModel vs AAC ViewModel

ViewModel : MVVM의 ViewModel은 마이크로소프트에서 처음 발표했는데, 모델과 뷰 사이의 데이터를 관리하고 바인딩 해주는 역할을 한다

AAC(Android Architecture Component)의 ViewModel : 액티비티의 생명주기에 분리시켜
ViewModel Scope의 생명주기를 따릅니다. Activity 재생성, Rotation 같은 Configuration 변경에 영향을 받지 않습니다. 

ViewModelProvider.Factory : helper 객체

안드로이드의 생명주기에 분리되어 ViewModel의 Scope를 따르기 때문에 화면 회전에도 데이터가 날라가지 않고 유지 

-ViewModel() , AndroidViewModel() 차이점 - Application의 유무, 이것을 상속받으면 메모리 누수 발생가능. 일반적으로 developer에서 ViewModel() 쓰도록 권장하고 있다.

https://yoon-dailylife.tistory.com/14

### provide vs bind

provide : inject 어노테이션 x, 모든 파라미터 선언

bind : inject 어노테이션, 

