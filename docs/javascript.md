# 프론트엔드 인터뷰 - 자바스크립트

프론트엔드 인터뷰 핸드북 https://frontendinterviewhandbook.com/javascript-questions/ 을 보면서 한 문제씩 나름대로 공부하고 번역하고 있다. (원문의 번역은 https://bit.ly/3aFXhGH ) 

공부해보니, 결국은 참고자료를 읽어야만 제대로 이해할 수 있다.

## 이벤트 버블링을 이용한 딜리게이션(delegation, 위임)을 설명하세요.

HTML 컴포넌트는 click 따위의 이벤트를 발생시키곤 한다. 이 이벤트를 해당 컴포넌트에서 처리하지 않으면, 이벤트 버블링(event bubbling)에 의해 자동으로 상위 컴포넌트로 전달된다. 

이 특성을 이용해 하위 컴포넌트의 이벤트를 상위 컴포넌트에서 처리하는 것을 딜리게이션(위임)이라고 한다.

* 어떤 div 아래에 button이 여러개 있을 때, 각각의 버튼마다 핸들러를 만들지 않고, div 에 하나만 만들면 메모리 사용 공간을 덜 차지한다는 장점이 있다.
* 만약 ul 아래의 li 에서 발생하는 이벤트를 이런 식으로 처리한다면, li 들이 삭제되거나 추가되어도 핸들러를 해제하거나 새로 바인딩할 필요가 없어서 편리하다.

```
<ul id="parent-list">
	<li id="post-1">Item 1</li>
	<li id="post-2">Item 2</li>
	<li id="post-3">Item 3</li>
	<li id="post-4">Item 4</li>
	<li id="post-5">Item 5</li>
	<li id="post-6">Item 6</li>
</ul>

<script>
document.getElementById("parent-list").addEventListener("click", function(e) {
	// 클릭된 녀석이 e.target 에 들어있다.
	if(e.target && e.target.nodeName == "LI") {
		console.log("List item ", e.target.id.replace("post-", ""), " was clicked!");
	}
});
</script>
```
### 추가자료

- https://davidwalsh.name/event-delegate 
- https://stackoverflow.com/questions/1687296/what-is-dom-event-delegation


## 자바스크립트에서 `this` 에 대해 설명하시오.

기존 언어에서 일관성 있는 this 나 self 를 사용하던 것과는 달리, 자바스크립트에서는 this 가 상황에 따라 다른 객체를 가리킨다. 

0. 컴포넌트 자신


    아마도, 초기의 자바스크립트는 button 에 onclick 을 구현하기 위해 만들어졌을 것이다 (아니면, marque라던지). 이 경우 this 가 자연스럽게 button 을 가리키도록 구현하는 것이 자연스러웠을 것이다.

    ```
    <button id="1" onClick="reply_click(this.id)">B1</button>
    <button id="2" onClick="reply_click(this.id)">B2</button>
 
    <script>function reply_click(clicked_id) { alert(clicked_id); }</script>
    ```

    (원문에는 0번 용례가 없다. 하지만, 역사성을 설명하려면 필요하지 않을까.)


1. 함수를 호출할 때 new 가 사용되는 경우

    함수를 호출할 때 new 를 사용하면 function 내부의 this 는 새로 생성된 객체를 가리킨다.

    ```
    function ConstructorExample() {
      console.log(this);
      this.value = 10;
      console.log(this);
    }
  
    new ConstructorExample();
    // -> {}
    // -> { value: 10 }
    ```

2. apply, bind, call 을 사용하면

    그리고, 함수를 여기저기서 쓸 수 있게하려고 함수내부의 this 를 바꿔치는 기능을 제공한다.

    - bind 는 함수내의 this 가 특정한 객체를 가리키는 함수를 만들어준다.
    - call 은 첫번째 인자에 (함수내에서) this 가 가리킬 객체를 넘겨준다.
    - apply 는 call 하고 같은데, 뒤쪽에 인자들을 배열로 줘야한다.

    ```
    function fn() {
        console.log(this);
    }
    var obj = {
        value: 5
    };
    var boundFn = fn.bind(obj);
    boundFn();     // -> { value: 5 }
    fn.call(obj);  // -> { value: 5 }
    fn.apply(obj); // -> { value: 5 }
    ```

3. 객체내의 함수를 호출하면

함수내부에서 this 는 자기를 포함한 객체를 가리킨다. 

```
var obj = {
    value: 5,
    printThis: function() {
        console.log(this);
    }
};

obj.printThis(); // -> { value: 5, printThis: ƒ }
```

4. 그냥 함수를 호출한 경우에는

이때는 최상위 객체가 된다. 즉, Window 가 된다. 만약 `use strict` 를 사용했다면, `undefined` 가 된다. (node 에서 실행해보면 `Object` 라는 녀석이 나온다. 검색해보면 `global namespace object` 라고 나온다.)

적절해 보인다.

```
function fn() {
    console.log(this);
}
fn(); // -> Window {stop: ƒ, open: ƒ, alert: ƒ, ...}
```

5. 여러 상황이 한꺼번에 벌어지면

상위 규칙이 적용된다. 고 되어있는데, 아마도 1번이 제일 쎈것 같다. constructor 에 bind 해서 확인하면 될듯... 0번은 바꿀 방법이 아예 없을 것 같은데..?

6. 화살표 함수 `() => {}`

화살표 함수에서는 모든 규칙을 무시하고, 함수가 생성되는 시점의 this 로 고정된다. 즉, 함수 정의 바로 앞에서 `console.log(this)` 해보면 나오는 그녀석이 이 함수의 this 가 된다.


```
const obj = {
    value: 'abc',
    createArrowFn: function() {
        return () => console.log(this);
    }
};
const arrowFn = obj.createArrowFn();
arrowFn(); // -> { value: 'abc', createArrowFn: ƒ }
```

7. 점을 안찍으면 4번룰이 적용된다.

아래 예제가 좀 웃기는데, 원래 객체에 속해있던 메서드라고 해도, 새로운 이름으로 할당하면, 점을 안찍고 호출할 수 있고, 이 경우 this 는 Window 를 가리킨다.

```
var obj = {
    value: 'hi',
    printThis: function() {
        console.log(this);
    }
};
var print = obj.printThis;
obj.printThis(); // -> {value: "hi", printThis: ƒ}
print(); // -> Window {stop: ƒ, open: ƒ, alert: ƒ, ...}
```

8. 여러 규칙이 한꺼번에

```
var obj1 = {
    value: 'hi',
    print: function() {
        console.log(this);
    },
};
var obj2 = { value: 17 };
```

위와 같이 객체가 두개 있는 상황에서, 규칙 2, 3이 함께 있으면 2번이 적용된다.

`obj1.print.call(obj2); // -> { value: 17 }`

생성자와 점이 같이 있으면, 생성자쪽이 적용된다.

`new obj1.print(); // -> {}`

### 추가자료
- https://codeburst.io/the-simple-rules-to-this-in-javascript-35d97f31bde3 (너무 잘 설명되어있다. 사실 본문은 0번을 제외하면 이 링크를 공부한 결과이다.)
- https://stackoverflow.com/a/3127440/1751946

