RecyclerView

### 주요 클래스

RecyclerView : 데이터에 해당하는 뷰가 포함된 ViewGroup이다. 뷰 자체이므로, 다른 UI 요소를 추가할 때 처럼 레이아웃에 RecyclerView를 추가하면 된다

ViewHolder : 목록의 각 개별 요소는 뷰 홀더 객체로 정의된다. 뷰 홀더가 생성되었을 때는 뷰 홀더에 연결된 데이터가 없다. 뷰 홀더가 생성된 후 RecyclerView가 뷰 홀더를 뷰 데이터에 바인딩한다. RecyclerView.ViewHolder을 확장하여 뷰 홀더를 정의 할 수 있다 

Adapter : RecyclerView는 뷰를 요청한 다음, 어댑터에서 메서드를 호출하여 뷰를 뷰의 데이터에 바인딩한다. RecyclerView.Adapter를 확장하여 어댑터를 정의할 수 있다

Layout Manager : 목록의 개별 요소를 정렬한다. RecyclerView 라이브러리에서 제공하는 레이아웃 관리자 중 하나를 사용하거나 레이아웃 관리자를 직접 정의할 수도 있다. 레이아웃 관리자는 모두 라이브러리의 LayoutManager 추상 클래스를 기반으로 한다

|                 | 내용                                               |
| --------------- | -------------------------------------------------- |
| ViewHolder      | ViewHolder 패턴 이용                               |
| Item Layout     | 가로/세로/지그재그 방향 모두 지원                  |
| Item Animation  | 아이템 애니메이션 처리                             |
| Adatper         | 데이터 제공하기 위한 구현 처리                     |
| Decoration      | 구분선 처리                                        |
| Click Detection | 개별 터치 이벤트 관리만 하고, 내장되어 있지는 않음 |

### 구현 단계

- 목록 또는 그리드의 모양을 결정해야 하고, 대개는 RecyclerView 라이브러리의 표준 레이아웃 관리자 중 하나를 사용할 수 있다
- 목록에 있는 각 요소의 모양과 동작 방식을 설계한다. 이 설계에 따라 ViewHolder 클래스를 확장한다. 사용 중인 ViewHolder 버전은 목록 항목에 필요한 모든 기능을 제공한다. 뷰 홀더는 View의 래퍼이고, 그 뷰는 RecyclerView로 관리된다
- 데이터를 ViewHolder 뷰와 연결하는 Adatper를 정의한다

### 레이아웃 계획

RecyclerView의 항목은 LayoutManager 클래스를 통해 정렬된다. 일반적으로 3가지 레이아웃 관리자를 제공한다

- LinearLayoutManager : 1차원 목록으로 정렬
- GridLayoutManager : 2차원 그리드로 정렬
- StaggeredGridLayoutManager : GridLayoutManager와 비슷하지만 행과 열이 동일한 높이 및 너비일 필요가 없으며 행 또는 열의 항목이 서로 오프셋 상태가 될 수 있다

### 어댑터 및 뷰 홀더 구현

레이아웃을 결정했으면 Adapter 및 ViewHolder 를 구현해야 한다. 이 두 클래스가 함께 작동하여 데이터 표시 방식을 정의한다. ViewHolder는 목록에 있는 개별 항목의 레이아웃을 포함하는 View의 래퍼이다. Adapter는 필요에 따라 ViewHolder 객체를 만들고 이러한 뷰에 데이터를 설정하기도 한다. 뷰에 데이터를 연결하는 프로세스를 바인딩이라고 한다.

어댑터를 정의할 때, 3가지 메서드를 재정의 해야 한다

- onCreateViewHolder() : RecyclerView는 ViewHolder를 새로 만들어야 할 때마다 이 메서드를 호출한다. 이 메서드는 ViewHolder와 그에 연결된 View를 생성하고 초기화하지만 뷰의 콘텐츠를 채우지는 않는다. ViewHolder가 아직 특정 데이터에 바인딩 된 상태가 아니기 때문이다
- onBindViewHolder() : RecyclerView는 ViewHolder를 데이터와 연결할 때 이 메서드를 호출한다. 이 메서드는 적절한 데이터를 가져와서 이 데이터를 사용하여 뷰 홀더의 레이아웃을 채운다. 
- getItemCount(): RecyclerView는 데이터 세트 크기를 가져올 때 이 메서드를 호출한다. 

```
class CustomAdapter(private val dataSet: Array<String>) :
        RecyclerView.Adapter<CustomAdapter.ViewHolder>() {

    /**
     * 커스텀 뷰 홀더를 사용하여 뷰의 타입의 참조를 제공한다
     */
    class ViewHolder(view: View) : RecyclerView.ViewHolder(view) {
        val textView: TextView

        init {
            // 뷰홀더의 뷰에 클릭 리스터를 정의
            textView = view.findViewById(R.id.textView)
        }
    }

    // 새로운 뷰를 생성 (레이아웃 매니저에서 호출)
    override fun onCreateViewHolder(viewGroup: ViewGroup, viewType: Int): ViewHolder {
        // 리스트 아이템의 UI를 정의한 뷰를 생성
        val view = LayoutInflater.from(viewGroup.context)
                .inflate(R.layout.text_row_item, viewGroup, false)

        return ViewHolder(view)
    }

    // 뷰의 컨텐츠를 대체한다 (레이아웃 매니저에서 호출)
    override fun onBindViewHolder(viewHolder: ViewHolder, position: Int) {

        // 이 포지션의 데이터 셋으로부터 항목을 가져오고, 뷰의 컨텐츠를 대체한다
        viewHolder.textView.text = dataSet[position]
    }

    // 데이터 셋의 사이즈를 리턴한다(레이아웃 매니저에서 호출)
    override fun getItemCount() = dataSet.size

}
```











https://github.com/android/views-widgets-samples/tree/main/RecyclerViewKotlin

1차 샘플

