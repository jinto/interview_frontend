프론트엔드 인터뷰 핸드북(https://frontendinterviewhandbook.com/javascript-questions/)을 보면서 한 문제씩 나름대로 공부하고 번역한다. 추가자료도 읽어본다.


# 이벤트 딜리게이션(delegation, 위임)은 무엇인가.

HTML 컴포넌트는 click 따위의 이벤트를 발생시키곤 한다. 이 이벤트를 해당 컴포넌트에서 처리하지 않고, 상위 컴포넌트 - 즉, 컨테이너 - 에서 처리하기도 한다. 이것을 딜리게이션이라고 한다.

그저 해당 컴포넌트에 이벤트 리스너를 만들지 않으면, 이벤트 버블링(event bubbling) 특성으로 인해 상위 컴포넌트의 이벤트 리스너로 전달된다.

* 어떤 div 아래에 button이 여러개 있을 때, 각각의 버튼마다 핸들러를 만들지 않고, div 에만 만들어서 처리하면 메모리 사용 공간이 줄어듭니다.
* 만약 ul 아래의 li 에서 발생하는 이벤트를 받는 상황이라면, li 들이 삭제되거나 추가되어도 핸들러를 해제하거나 새로 바인딩할 필요가 없어서 편리합니다.

추가자료) 
- https://davidwalsh.name/event-delegate
- https://stackoverflow.com/questions/1687296/what-is-dom-event-delegation
- 이것들도 번역하면 좋겠는데, 너무 길다.
