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
<br/>
#### 클래스 기반 vs 프로토타입 기반

: 클래스 기반의 언어는 클래스로 객체의 기본적인 형태와 기능을 정의하고, 생성자로 인스턴스를 만들어서 사용할 수 있어. 클래스에 정의된 메서드로 여러가지 기능을 수행할 수 있는거지. 이런 유형의 언어는 모든 인스턴스가 클래스에 정의된 대로 같은 구조이고 보통 런타임에 바꿀수 없어.

반면에 프로토타입 기반의 언어는 객체의 자료구조, 메서드 등을 동적으로 바꿀 수 있어.

-> 그렇기에 장단점이 명확해져. 정확성,안정성,예측성 관점에서는 클래스 기반 언어가 조금 더 좋으나 동적으로 자유롭게 객체의 구조와 동작 방식을 바꿀 수 있다는 장점은 프로토타입 기반 언어가 가지고 있지.

<br/>



#### 자바스크립트의 함수 객체 프로토타입을 활용한 효율적 프로그래밍

: 자바스크립트에서는 봤듯이 var me = new Person("sungbin"); 이런식으로 new 키워드를 이용해 새로운 객체를 만들 수 있어. 하지만 만약 저 Person이라는 함수 객체 안에 setName이나 getName과 같은 함수가 들어있다 생각을 해봐. 그러면 새로운 객체를 만들어 낼 때 마다 해당 함수들을 위한 공간도 만들어져야하고 그럼 매우 비효율적이자나!!
-> 함수 객체의 프로토타입을 이용하는거지!! 함수 객체의 프로토타입에 setName이나 getName과 같은 함수를 넣는다면 위의 문제는 해결이 되자너!!
```javascript
Function.prototype.method = function(name,func){
    if(!this.prototype[name]){
        this.prototype[name] = func;
    }
}
//위의 형태가 더글라스 크락포드가 제시하는 메서드 정의 방법이래 이를 활용한 예제가 아래야

Function.prototype.method = function(name,func){
    this.prototype[name] = func;
}

function Person(arg) {
    this.name = arg;
}

Person.method("setName", function(value){
    this.name = value;
}):

Person.method("getName", function(){
    return this.name;
});

var me = new Person("me"):
var you = new Person("you");
console.log(me.getName());
console.log(you.getName());
```
해당 코드를 보면 무슨느낌인지 이해가지??

<br/>



#### 상속

: 자바스크립트의 상속의 종류는 크게 2가지로 볼 수 있으

1. 클래스 기반 전통적인 상속 방식을 흉내내는 거
2. 클래스 개념 없이 프로토타입으로 상속 구현

<br/>



#### 프로토타입을 이용한 상속

: 아래의 코드를 이해하면 거의 된거야. 이해했지?
```javascript
function create_object(o){
    function F(){}
    F.prototype = o;
    return new F();
}
```
함수 create_object의 인자로 들어오는 객체를 부모로 하는 새로운 객체를 new 를 통해 만든 후 반환해주고 있어. 이렇게 할 경우 클래스의 개념을 사용하지 않고도 상속을 구현할 수 있게 되는거지

<br/>



#### jQuery의 extend 함수

: 객체의 복사 혹은 여러 객체를 합칠 때 사용해. 이때 주의해야할 것이 있어. 객체의 프로퍼티를 복사하는 과정에서 얕은 복사가 진행이 될 경우 문제가 발생할 수 있는 부분이 있다는 거야. 만약 프로퍼티로 객체를 가지고 있을 경우 얕은 복사 진행시에 문제가 발생해. 알지? 그렇기에 깊은 복사를 하는 것이 일반적이야. extend 함수의 첫번째 인자로 true를 넣어줄 경우 깊은 복사를 진행할 수 있네.

<br/>



#### extend() 함수를 이용한 확장

: 간단하게 얕은 복사를 진행하는 extend 함수를 만들어서 객체를 확장 해 보겠으
```javascript
//create_object 함수 경우 위와 동일하고 불필요한 부분은 생략하겠으
function extend(obj,prop){
    if(!prop){prop = obj; obj = this; }
    for(var i in prop) obj[i] = prop[i];
    return obj;
};

var student = create_object(person);
var added = {
    setAge : function(age){
        this.age=age;
    },
    getAge : function(){
        return this.age;
    }
};

extend(student,added);

student.setAge(25);
```
이런식으로 사용할 수 있게 되는거지!! extend 함수는 사용자에게 유연하게 기능 확장을 할 수 있게 해주는 함수일 뿐만 아니라 상속에서도 자식 클래스를 확장할 때 유용하게 사용된다니까 알아둬~

<br/>



#### 클래스 기반의 상속

: 이것도 결국은 프로토타입 상속과 똑같네. 다만 구현하는 방법과 그에서 오는 구조의 차이긴 한데. 다시 확인하고 싶다면 p185부터 봐봐.

<br/>



#### 캡슐화 

: 캡슐화는 기본적으로 관련된 여러가지 정보를 하나의 틀 안에 담는 것을 이야기해. 그래서 일반적으로 java와 같은 클래스에서는 서로 관련된 멤버 변수와 메서드가 존재하는거지. 여기서 중요한 것은 정보의 공개 여부이고 이를 정보은닉이라고 얘기해. 이전까지 알고있던 언어들에서는 접근제어 지시자를 통해 이를 실현 했지만 자바스크립트는 좀 다르네.
```javascript
var Person = function(arg){
    var name = arg ? arg : "zzoon";
    
    return {
        getName : function(){
            return name;
        },
        setName : function(arg){
            name = arg;
        }
    };
}

var me = Person();
console.log(me.getName());
```
이런식으로 할 경우 name에 대해서는 외부에서 접근할 수 없게되자너!! 대신 메서드를 통해 접근할 수가 있고 이것이 자바스크립트에서 정보 은닉성을 구현하는 방안이야. 

하지만 코드가 위와 동일할 경우 주의점이 발생해져. 접근하는 private 멤버가 객체나 배열이면.... 쟤들은 얕은 복사로 참조만을 반환하기에 사용자가 이후 이를 쉽게 변경할수도 있게 되버려.

그렇기에 객체를 반환하는 경우 신중해야해. 보통은 객체를 반환하지 않고 객체의 주요 정보를 새로운 객체에 담아서 반환하는 방법을 이용하는데 만약 꼭 객체가 반환되어야 하는 경우 깊은 복사로 복사본을 만드는 방법을 사용하는게 좋아

<br/>



### 객체지향 프로그래밍 응용 예제


#### 클래스의 기능을 가진 subClass함수

: 코드와 다이아그램을 보면서 이해해
```javascript
function subClass(obj){
    ...
    
    var parent = this;
    var F = function() {};
    
    var child = function(){};
    
    F.prototype = parent.prototype;
    child.prototype = new F();
    child.prototype.constructor = child;
    child.parent = parent.prototype;
    child.parent_constructor = parent;
    
    .....
    
    return child;
}
```
