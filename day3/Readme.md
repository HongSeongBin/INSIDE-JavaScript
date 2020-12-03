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
![example](https://user-images.githubusercontent.com/31160622/100978142-54872e80-3585-11eb-9694-5ea42ee320d9.png)

관계가 어떻게 구성되어있는지 알겠지.

그 뒤에 재귀적으로 생성자 해주는 거나 그런 부분들은 p190 부터 다시보자. 상속이 필요하여 졌을 때 참고하는 게 좋은 거 같음.

<br/>



## Chpater07 함수형 프로그래밍
<br/>

#### 함수형 프로그래밍의 개념

: 함수형 프로그래밍은 함수의 조합으로 작업을 수행하는 것을 의미해. 중요한 건 이 작업이 이루어지는 동안 작업에 필요한 데이터와 상태는 변하지 않아. 변할 수 있는건 오로지 함수뿐이야. 이 함수가 바로 연산의 대상이 된데. 흠... 어렵넹

외부에 영향을 미치지 않는 함수를 순수함수라고하고 외부에 영향을 미치지 않기에 이미 작성된 순수 함수로 다른 작업에 활용해도 문제가 없데.

함수를 또 하나의 값으로 간주해서 함수의 인자 혹은 반환값으로 사용할 수 있는 함수는 고계함수라고 한데.

정리하자면 내부 데이터 및 상태는 그대로 둔 채 제어할 함수를 변경 및 조합함으로써 원하는 결과를 얻어내는 것이 함수형 프로그래밍의 중요한 특성이래. 이 특성으로 인해 높은 모듈화가 가능해지고 이게 함수형 프로그래밍의 가장 큰 장점이래.

생각보다 개념에 대한 설명이 잘 안되있으니 따로 자료를 찾아보자~

<br/>



### 자바스크립트에서의 함수형 프로그래밍
자바스크립트에서도 함수형 프로그래밍이 가능해!! 어케?? -> 1) 일급 객체로서의 함수,  2) 클로저  

<br/>



#### 배열의 각 원소 총합 구하기

: 이건 직접 코드를 봐야 이해가 팍 되니까 아래에 코드 적어둬야지
```javascript
//기존이라면 이렇게 하겠지
function sum(arr){
    var len = arr.length;
    var i=0,sum=0;
    
    for(;i<len ; i++){
        sum+=arr[i];
    }
    return sum;
}
```
배열의 합을 구하고 싶다면 위와같은 함수를 가장 먼저 생각했을거야. 위는 자바스크립트로 명령형 프로그래밍 방식으로 코딩한거지. 그럼 아래에 함수형 프로그래밍 방식으로 구현한다면 어케 달라지는지 봐봐.
```javascript
function reduce(func,arr,memo){
    var len = arr.length, i=0 , accum = memo;
    
    for( ; i<len ; i++){
        accum = func(accum,arr[i]);
    }
    
    return accum;
}

var sum = function(x,y){
    return x+y;
};

var arr = [1,2,3,4];

console.log(reduce(sum,arr,0));
```
이런식으로 할 수 있어! 차이가 눈에 확 보이지?? 만약 배열의 각 원소에 대해 곱하기를 하고싶다면 위의 명령형 같은경우 곱하기를 처리해주는 함수를 덧셈을 구현한 것 처럼 똑같이 하나 새로 만들어줘야 할거야. 하지만 밑에 함수형 프로그래밍 부분을 봐봐. 간단하게 multiply 함수를 정의해서 reduce의 인자로 넣어주기만 하면 되자나!! 코드가 훨씬 간결해지지. 그래서 함수형 프로그래밍을 하면 한단계 높은 모듈화를 이룰 수 있다고 하는거야.

<br/>



#### 팩토리얼

: 이 예제에서는 메모이제이션 부분을 눈여겨 봐두는게 좋을 거 같네. 앞서 연산한 결과를 캐시에 저장하여 사용할 수 있도록 구현해놓은거고 해당 부분이 이 예제에서는 key point 인 거 같어.
```javascript
var fact = function(){
    var cache = {'0' : 1};
    var func = function(n){
        var result = 0;
        
        if(typeof(cache[n]) === 'number'){
            result = cache[n];
        }else{
            result = cache[n] = n*func(n-1);
        }
        
        return result;
    }
    
    return func;
}
```

위 함수를 보면 알 수 있듯이 fact는 cache에 접근할 수 있는 클로저를 반환받네. 그리고 그 클로저는 숨겨지는 cache에 팩토리얼을 연산한 값을 저장하네ㅔ. 그래서 연산을 수행하는 과정에서 캐시에 저장된 값이 있으면 곧바로 그 값을 반환받게 하는구먼


jQuery에서는 jQuery.cache라는 것을 제공해준다니 나중에 한번 봐봐. 그리고 경우에 따라서 Function.prototype에 메모이제이션 해주는 함수를 집어넣어 사용하는 것도 여러 함수를 사용할 때 중복을 피해주는 방법이 되지

<br/>



#### 피보나치 수열

: 이것도 아까 합을 구할 때랑 비슷한 느낌인데 거기에다 메모이제이션 개념을 추가했네. 코드 한번 봐봐
```javascript
var cacher = function(cache,func){
    var calculate = function(n){
        if(typeof(cache[n]) === 'number'){
            result = cache[n];
        }else{
            result = cache[n] = func(calculate,n);
        }
        
    return result;
};

var fact = cacher({'0':1}, function(func,n){
    return n*func(n-1);
});

var fibo = cacher({'0':0, '1':1}, function(func,n){
    return func(n-1)+func(n-2);
});

console.log(fact(10));
console.log(fibo(10));
```
이렇게 해놓으니까 그때그때 끼워맞추기 좋네. 왜 모듈화가 한단계 높다는지는 알겠는데 아직 너무 어색한 느낌이고 눈에 바로 안들어오네... ㅠㅠ

<br/>



### 자바스크립트에서의 함수형 프로그래밍을 활용한 주요 함수
<br/>

#### 함수 적용

: 앞에서 Function.prototype.apply 함수로 함수호출 하는거 했지? 이것도 왜 apply냐 함수형 프로그래밍에서는 특정 데이터를 여러 가지 함수를 적용시키는 방식으로 작업을 수행하는데 이때 함수는 단순히 입력을 넣고 출력을 받는 기능을 수행하는 것 뿐 아니라, 인자 혹은 반환값으로 전달된 함수를 특정 데이터에 적용시키는 개념이야. 그래서 적용한다는 느낌을 따와서 apply라넹

<br/>



#### 커링

: 커링이란 특정함수에서 정의된 인자의 일부를 고정시키고, 나머지를 인자로 받는 새로운 함수를 만드는거야. 기본적으로 함수형 프로그래밍에서 제공되어지는데 자바스크립트에서는 제공되어지지 않으나 만들 수 있어.
```javascript
function calculate(a,b,c){
    return a*b+c;
}

function curry(func){
    var args = Array.prototype.slice.call(arguments,1);
    
    return function(){
        return func.apply(null, args.concat(Array.prototype.slice.call(arguments)));
    }
}

var new_func1 = curry(calculate,1);
console.log(new_func1(2,3));
// 1을 고정시킨 new_func1에 2와 3을 인자로 받아서 수행시켜 결과 5를 받아오게 되는거야
```

<br/>



#### bind

: 함수형 프로그래밍 방식을 사용한 대표적인 함수래. bind 함수 역시 커링과 같이 사용자가 고정시키고자 하는 인자를 bind()함수를 호출할때 인자로 넘겨주고 반환받은 함수를 호출하면서 나머지 가변 인자를 넣어줄 수 있어. 그리고 bind 함수를 사용하면 this 가 가리키는 객체를 사용자가 정의 해 줄 수 있다는 점도 있지. 아래 코드 한번 봐봐아.
```javascript
var print_all = function(args){
    for(var i in this) console.log(i+" : " + this[i]);
    for(var i in arguments) console.log(i+" : "+arguments[i]);
}
var myobj = {name:'sungbin'};

var myfunc = print_all.bind(myobj);
myfunc();
//name : sungbin 만 출력이되지

var myfunc1 = print_all.bind(myobj,'minsoo','who are u');
myfunc1("na~~~");
/* name : sungbin
   0 : minsoo
   1 : who are u
   2 : na~~~
*/
```
이처럼 특정 함수에 원하는 객체를 바인딩시켜 새로운 함수를 사용할 때 bind()함수가 사용되어져!

<br/>



#### 래퍼

: wrapper는 특정 함수를 자신의 함수로 덮어쓰는 것을 말하는거야. 이 wrap 함수의 첫번 째 인자로 원래 함수의 참조를 받을 수 있어서 원래 함수를 실행하고 자신의 로직을 실행하는 게 가능해. wrapper 즉 덮어쓰더라도 사용자는 원래 함수 기능을 잃어버리지 않은 상태로 자신의 로직을 수행할 수 있어야하고 이런 점에서 봤을 때 그래서 첫번째 인자로 원래 함수를 참조할 수 있게 해놨나보다 생각이 드넴. 래퍼는 기존에 제공되는 함수에서 사용자가 원하는 로직을 추가하고 싶을 때 사용할 수 있다네.

<br/>



#### 반복 함수
* each : each()함수는 배열의 각 요소 혹은 객체의 각 프로퍼티를 하나씩 꺼내서 차례대로 특정 함수에 인자로 넣어 실행시켜줘
* map : map()함수는 주로 배열에 많이 이용되고 배열의 각 요소를 꺼내서 사용자 정의 함수를 적용시켜 새로운 값을 얻은 후 새로운 배열에 넣어줘
* reduce : reduce()는 배열의 각 요소를 하나씩 꺼내서 사용자의 함수를 적용시킨 뒤 그 값을 계속해서 누적시키는 함수야
