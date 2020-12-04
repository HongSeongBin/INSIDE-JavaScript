## Chapter08 jQuery 소스 코드 분석
<br/>

#### jQuery 함수 객체

: jQuery 함수 정의를 보면 앞에서 배웠던 것 처럼 new 연산자를 통해 jQuery 객체를 생성하는 부분이 있어.
  
  그리고 일반적으로 개발자들이 jQuery를 호출할 때 $ 를 많이 사용하는데 이것이 가능한 이유는 jQuery 함수를 $변수에 매핑시켰기 때문이지. ( var $ = jQuery; )
  
  그럼 여기서 보자. jQuery() 역시 함수 생성자로 동작하는 것이 가능하기에 다음과 같은 작업이 가능해지고 jQuery 소스코드에 포함되어있는 부분이기도 해.
  
  jQuery() 생성자 함수를 호출할 경우 생성된 jQuery 객체는 Prototype 링크로 자신의 프로토타입인 jQuery.prototype 객체에 접근 가능해져. 그렇기에 모든 jQuery 객체 인스턴스는 자신의 프로토타입인 jQuery.prototype 객체의 프로퍼티를 공유할 수 있게 되는거지!
   
  jQuery 소스코드에는 이런 부분이 있어. jQuery.fn = fQuery.prototype = { .....}  해당 부분을 보면 jQuery() 함수 정의 후 jQuery.prototype 디폴트 객체를 객체 리터럴 형식의 다른 객체로 변경하고 이 변경된 jQuery의 프로토타입을 jQuery.fn 프로퍼티가 참조하게해. -> 이후에 생성된 jQuery 객체 인스턴스는 변경된 프로토타입 객체에 정의된 다양한 메서드를 프로토타입 체이닝으로 사용할 수 있게되는거지!
  
  <br/>
  
  
  
  #### 객체확장 - extend()메서드
  
  : extend 메서드는 앞에서 해서 알자나? extend(obj,prop) 일 경우 prop의 프로퍼티를 obj 객체에 복사시켜주는 것이구 prop이 없다면 this (메서드를 호출한 객체에 바인딩)에 obj 의 property를 복사해주는 거자나. 이를 이용해서 jQuery 객체를 확장하는거지. jQuery.extend() 호출을 통해서 jQuery 함수 객체를, jQuery.fn.extend() 메서드 호출로 jQuery.prototype 객체의 기능을 확장시킬 수 있는 거쥐!
  
  <br/>
  
  
  
  #### jQuery 소스코드의 기본 구성 요소
  
  * jQuery 함수 객체
  * jQuery.prototye 객체
  * jQuery 객체 인스턴스
  
![jquery](https://user-images.githubusercontent.com/31160622/101112341-6a95fd00-3620-11eb-8698-b0a165b318e2.png)

 이런 구조야!! jQuery 객체는 프로토타입 체이닝을 통해 프로토타입 메서드를 호출하는 것이 가능해지지!
 
 jQuery 함수의 가장 기본적인 역할은 new 연산자로 jQuery 객체를 생성하는 것이고 이렇게 생성된 jQuery 객체는 프로토타입 체이닝으로 jQuery.prototype 객체에 포함된 프로토타입 메서드를 호출할 수 있게 되는거야. 또한, jQuery 함수 객체 자신이 메서드를 포함하고 있는 것도 있어. 이러한 jQuery 함수 객체의 메서드는 각각 생성된 jQuery 인스턴스 객체에 특화되지 않고 범용적으로 사용되는 jQuery 코어 메서드로 구성되.
 
 <br/>
 
 
 
 #### jQuery의 id 셀렉터 동작
 
 : jQuery의 가장 기본적인 기능은 HTML 문서에서 원하는 DOM 객체를 선택한 후 , 해당 객체의 속성 변경이나 효과 이벤트를 처리하는거 잖아.
 
 ```HTML
 
  <div id="myDiv">Hello<div>
  
  <script>alert($("#myDiv").text())</script>
  
  ```
  
  이런식으로 말야. 그럼이게 jQuery 코드에서 어떻게 처리되는건지 한번 보자.
  
  아까 앞에서 설명했듯이 $는 jQuery() 함수를 참조하니까 $("#myDiv")는 jQuery("#myDiv") 함수 호출을 의미해! (내부 돌아가는 것을 다시 확인하고 싶다면 p237)
  
  <br/>
  
  
  
  #### 정규표현식
  
  : 정규표현식은 문자열에 나타나는 특정 문자 조합과 대응시키기 위해 사요오디는 패턴이래. 그리고 자바스크릡트에서는 이 정규표현식 또한 객체야. 정규표현식은 슬래시(/)로 감싸서 만들 수 있고 다음과 같은 종류들이 있어.
  
  1. ^ : 문자열 시작을 나타내
  2. [^<]* : 문자 클래스([])에 안에 포함된 ^는 지정한 문자를 제외하라는 의미야. 즉, [^<]의 의미는 < 문자를 제외한 모든 문자를 의미하는거지. 클래스 뒤에는 수량자*가 붙었고, 이것은 문자 클래스에 해당하는 글자가 0번 이상 나타나는 것을 의미해. 즉 정리하자면 [^<]*의 의미는 빈 무자열이나 <문자를 제외한 문자나 문자열을 의미한다는거야.
 
