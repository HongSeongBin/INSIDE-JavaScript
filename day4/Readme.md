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
