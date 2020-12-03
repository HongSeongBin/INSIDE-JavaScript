#### 클로저의 활용

```javascript
function HelloFunc(){
    this.greeting = "hello";
}
HelloFunc.prototype.call = function(func){
    func ? func(this.greeting) : this.func(this.greeting);
}
var userFunc = function(greeting){
    console.log(greeting);
}
var objHello = new HelloFunc();
objHello.func = userFunc;
objHello.call();
```
하아... 이 코드를 이해하는데 너무 오래 걸렸네... 잘봐. objHello는 greeting 이라는 프로퍼티를 가지고 있는 객체로 처음 만들어지지. 그리고 이 녀석에게 func이라는 프로퍼티를 만들어주는데 이 친구는 userFunc이라는 함수를 참조하지.

그리고 가장 이해하기 어려웠던 call 함수를 보자. call 함수는 HelloFunc 객체의 프로토타입 객체의 프로퍼티로 정의 되어있고 함수야. 그렇기에 objHello.call()로 호출할 수 있어. 그 이유는 스코프를 배웠으니 알겠찌.

그렇게 해서 call 메소드를 호출하면 this 바인딩에서 메서드의 바인딩은 그 메서드를 호출한 객체로 this가 바인딩 되자나. 즉, objHello라는 객체로 바인딩 되고 call을 호출하는 부분을 보면 알 수 있듯이 인자가 없어.

인자가 없을 경우 자바스크립트에서는 undefined로 넘겨준다고 했찌? 그럼 this.func(this.greeting) 메소드가 작동하게 될 것이고 objHello 객체의 func 프로퍼티는 userFunc을 참조하고 있으니 userFunc함수에 this.greeting 즉 "hello"를 넘긴 결과를 출력하게 되는거야!!

<br/>



#### 함수의 캡슐화

: 그럼 이 클로저라는게 어디에 쓰이는지 한번 보자. 만약 너가 "i am xxx, i live in xxx, i'm xx years old"라는 문장을 출력하는데 이 x 부분들을 사용자에게 인자로 입력을 받으려고 해.

당연히 가장 먼저 떠오르는 것은 저 것들을 템플릿으로 만들어서 사용하는거지?? 하지만 자바스크립트는 {}단위로 스코프를 구분하는 것도 아니고 템플릿을 만들면 전역변수로 만들어 질 것이고 이는 외부에서 접근이 가능하게 되어져.

그렇게되면 다른 함수에서 해당 템플릿으로 이용하는 배열에 쉽게 접근하여 값을 바꿀 수 있고, 실수로 같은 이름의 변수를 만들어 버그를 유발할 수 있어. 

-> 클로저를 사용하면 해결할 수 있어.

```javascript
var getCompletedStr = (function(){
  var buffAr = [
    'i am ',
    '',
    '. i live in ',
    '',
    '. i\'m ',
    '',
    ' years old.',
  ];
  
  return (function(name,city,age){
    buffAr[1] = name;
    buffAr[3] = city;
    buffAr[5] = age;
    
    return buffAr.join('');
  });
})();

var str = getCompletedStr('Sungbin','Daegu',25);
console.log(str);
```
이런식으로 말야!! 클로저를 활용해서 buffAr을 추가적인 스코프에 넣고 사용하면 위의 문제가 해결되지. 이 경우 getCompletedStr에 익명의 함수를 즉시 실행시켜 반환되는 함수를 할당한 것이고 이 반환되는 함수가 클로저가 되어 자유변수 buffAr을 스코프 체인에서 참조할 수 있게 되는거지

<br/>



#### 클로저를 활용할 때 주의사항

1. 클로저의 프로퍼티값이 쓰기 가능하므로 그 값이 여러 번 호출로 항상 변할 수 있음에 유의해야 해. (자유변수의 값을 변화시킬 수 있으니 잘 사용해라 이말이야!)
2. 하나의 클로저가 여러 함수 객체의 스코프 체인에 들어가 있는 경우도 있어.
3. 루프 안에서 클로저를 활용할 때는 주의해.

<br/>



## Chapter06 객체지향 프로그래밍


 
