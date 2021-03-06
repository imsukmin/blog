---
layout: post
title: "20170319 자바스크립트 중고급 강의 정리"
date: "2017-03-19"
---

## javascirpt Object 생성적 구조

```` javascript
    실행_콘텍스트 = {
      환경_컴포넌트 : {
        렉시컬_환경_컴포넌트: {
          환경_레코드: {},
          외부_렉시컬_환경_참조: {/*[[scope]]*/}
        },
      변수_환경_컴포넌트: {
        환경_레코드: {},
        외부_렉시컬_환경_참조: {/*[[scope]]*/}
      } 
      },
      this_바인딩_컴포넌트:{
        호출된_함수가_속한_오브젝트_프로퍼티들
      }
    }
````

### Execution Context (실행 컨텍스트)

* 함수 코드 => 랙시컬 환경
* 코드 => 글로벌 환경
* eval 코드 => 동적 환경 [설계 적 구조 오류]

실행 콘텍스 초기화 단계에서 
* 변수 이름, 함수 선언문, 함수의 파라미터를
* 렉시컬과 변수 환경 컴포넌트의 환경 레코드에 같이 설정.

### this 바인딩 컴포넌트

* 목적
  * this로 호출한 함수가 속한 오브젝트
  * 접근 메커니즘
    * 호출한 함수가 속한 오브젝트를 참조할 수 있도록
    * this 바인딩 컴포넌트에 호출한 함수가 속한 오브젝트를 설정.
    * Object.prototype.something = this.something

### 스택    

* 실행 컨텍스트의 논리적 구조
* 함수 안의 자바스크립트 코드가 실행되는 곳
  * 함수가 호출되면 스택의 가장위에 실행 콘텍스트에 놓이게 됨.
  * 실행을 진행하면서 스택의 함수들이 빠짐.

### 함수코드

* 처리단계
  1. 준비단계
  2. 함수선언문 초기화
  3. 변수/함수 표현식 초기화
  4. 함수코드 실행
* 글로벌 코드
  * 글로벌 환경
  * 외부 렉시컬 환경 참조 값 없음
  * 오브젝트 환경 레코드에 해석/실행 결과 바인딩 
* 함수 코드
  * 렉시컬 환경
  * 외부 렉시컬 환경 참조 값 있음
  * 선언적 환경 레코드에 해석/실행 결과 바인딩
  * 스코프 있음

### 파라미터 값 매핑

* 파라미터 값 매핑이란?  ==> 호출한 함수에서 넘겨 준 파라미터 값을, 호출받은 함수의 파라미터 순서에 맞추어 값을 설정하는 것.
* 엔진 관점
  * 실행 콘텍스트에 파라미터로 넘겨준 값과, 함수의 [[FormalParameters]]에 저장.

#### 연습
```` javascript
    function sports(one, two, two) { 
      console.log(one + two); // 2번쨰 값으로 바인딩
    };
    sport(11,22,33); // 44
````

### 함수, 변수 바인딩

* 바인딩 순서
  1. 파라미터 이름/값을 선언적 환경 레코드에 설정
  2. 함수 선언문을 function 오브젝트로 생성하여 선언적 환경 레코드에 설정.
  3. **arguments** 오브젝트를 선언적 환경 레코드에 바인딩
  4. 변수와 함수 표현식을 선언적 환경 레코드에 바인딩
* 설정과 바인딩 차이
  * 설정은 프로퍼티가 존재하더라도 값을 대체
    ==> 파라미터 이름과 함수 이름이 같으면 **함수로 대체**

### evel code

* evel code
  * evel() 함수와 파라미터에 작성한 코드
* 작성, 실행 방법
  * 함수의 파라미터에 실행 가능한 코드를 문자열로 작성
  * 문자열을 자바스크립트 코드로 간주하고 해석
```` javascript
    code = "function(){ ... }"
    evel(code);
````

## Function instance

### function instance 생성
* function 구분
  * 빌트인 function object
  * function object : function 키워드로 생성
  * function instance : new 키워드로 생성
* function 오브젝트 == 인스턴스
  * Built-in Function 오브젝트로 다수의 function 오브젝트를 생성
  * new 연산자로 생성하는 인스턴스 == function 연산자로 생성사는 오브젝트(그냥 키워드 구분하기 위해)
    * 아 사실은 아주 조금 차이가 있음
    ==> **new로 만든 function 인스턴스는 prototype를 가지지 않음**
    ==> 그래서 new 키워드로 만든 인스턴스는 new로 새로 만들수 없다.

### 생성자 함수
* 생성자 함수
  * new 연산자와 함께 인스턴스를 생성하는 함수
  * new FunctionName() 에서 FunctionName()이 생성자 함수
  * Object.prototype.**Constructor()**에 위치
* 인스턴스 생성방법
  * new 연산자: 인스턴스 생성 제어
  * 생성자 함수는 인스턴스 생성 및 반환과 인스턴스에 초기값을 설정한다.
* 생성자 함수 형태
  * 첫 문자를 대문자 사용(규약) [new Number(), new String() ...]


### constructor

* constructor **프로퍼티**
  * Function 오브젝트를 생성할 떄 설정
  * prototype 아래 존재함.

### prototype

* 존재 목적
  * 프로퍼티 연결을 통한 프로터피 확장
  * 생성한 각 인스턴스에서 prototype의 프로퍼티 공유
  * 다른 Function 인스턴스를 상속받음
* 프로퍼티 확장
  * prototype이 오프젝트이므로 프로퍼티를 연결하여 확장 가능
* 프로퍼티 공유
  * prototype에 연결된 프로퍼티로 인스턴스 생성
  * 인스턴스에서 prototype에 연결된 프로퍼티 값 공유!
* 인스턴스 상속
  * Function 오브젝트를 인스턴스로 생성하여 prototype 연결
    * 객체지향의 상속 개념
    * prototype-base inheritance라고 함
  * prototype으로 상속을 구현한 형태를 프로토타입 체인(prototype chain) 이라고 함
* 확장 방법
  * Function 오브젝트의 prototype에 프로퍼티 연결
    * prototype = {key:{key:value}} 형태로 확장.
    ==> Object.prototype.property = function(){}
    * value위치에 자바스크립트 지원 데이터 타입을 연결(**지양**)
    * prototype에 null을 설정하면 확장 불가.
```` javascript
    Object.prototype['get'] = function(){};
    Object.prototype.get = function(){};
    Object.prototype[get] = function(){};
    Object.prototype = { key : value }; // 주의! constructor가 삭제됨.
````
* 인스턴스 생성 기준
  * prototype에 연결된 프로퍼티로 인스턴스 생성
  * 원본을 복사해서 사용하기 위해!
* prototype의 프로퍼티 공유
  * 생성한 모든 인스턴스에서 prototype의 프로퍼티 공유
  * 각각 다른 인스턴스에서 같은 결과를 얻을 수 있음.
  * 공유하게 되므로 한곳에서 값을 바구면 모든 인스턴스에 반영
* 인스턴스마다 값을 사용하려면
  * this.getMember(), this.value형태 사용

### this and prototype

* 호출한 함수 앞에 작성한 오브젝트 참조
  * object == this in object scope
* 인스턴스 참조
  * new Sports()로 생성한 인스턴스를 this가 참조

### 프로퍼티 공유 시점
* prototype 프로퍼티의 공유 시점
  * 사용하는 시점(호출되는 시점)에 공유
* 공유 메커니즘
  * prototype에 연결된 프로퍼티로 인스턴스를 생성하지만 인스턴스의 프로퍼티는 원본의 prototype을 참조하는 형식으로 사용된다.

### this
* this ==> keyword
  * this.value || this.property() 와 같이 사용.
* this 참조
  * 글로벌 오브젝트에 this,는 글로벌 오브젝트를 참조
  * this 바인딩 컴포넌트에 글로벌 오브젝트가 설정되기 떄문
* this와 window
  * 최상위에서는 this === window
* strict 모드
  * 함수 앞에 오브젝트를 작성해야 하지만 글로벌 오브젝트의 이름이 없으므로 window.get()과 같이 사용해야함. 


### 오브젝트 반환

### call()과 this

| 구분     | 타입     | 데이터(값)                                                     |
|----------|----------|----------------------------------------------------------------|
| object   | Function | 호출할 함수 이름                                               |
| 파라미터 | Object   | this로 참조할 오브젝트                                         |
|          | Any      | 호출된 함수로 넘겨 줄 파라미터opt. 콤마로 구분, 다수 작성 가능 |
| 반환     | Any      | 호출된 함수에서 반환한 값                                      |

* getValue.call(this, 12, "asdasd")
* getValue.call({ base:20, filter:30 }, 12, "asdasd")

### apply()와 this

| 구분     | 타입     | 데이터(값)                                        |
|----------|----------|---------------------------------------------------|
| object   | Function | 호출할 함수 이름                                  |
| 파라미터 | Object   | this로 참조할 오브젝트                            |
|          | Any      | 호출된 함수로 넘겨 줄 파라미터opt. arguments(opt) |
| 반환     | Any      | 호출된 함수에서 반환한 값                         |

* getValue.apply(this, arguments([12, "asdasd"])
* getValue.apply({ base:20, filter:30 }, arguments([12, "asdasd"])

### bind()와 this
| 구분     | 타입     | 데이터(값)                         |
|----------|----------|------------------------------------|
| object   | Function | 호출할 함수 이름                   |
| 파라미터 | Object   | this로 참조할 오브젝트             |
|          | Any      | 호출된 함수로 넘겨 줄 파라미터opt. |
| 반환     | Function | Function Object                    |

* bind()는 한번에 처리하지 않고 나누어 처리
  1. Function 오브젝트를 생성하여 반한
  2. 생성한 Function 옵젝트를 함수로 호출
* 작성방법
  * 첫 번째 파라미터에 생성한 Function 오브젝트를 호출할 때 this로 참조할 오브젝트 지정
  * 두번 쨰에 bind 메서드에 넘겨 줄 파라미터 작성
  * 생성한 Function 오브젝트를 호출 할 때 에도 파라미터 지정 가능
  * 두 개의 파라미터를 병합하여 사용.

## 객체 지향 프로그래밍

### 객체
* 기본 요소
  * 행위 == method
  * 속성(attribute) == property
* 객체는 독립적으로 존재하며 객체 자체에서 목적 달성

### 클래스
* 객체의 행위와 속성을 선언하는 영역
* text == code
* Object로 생성해야 사용
* ES6 부터 'class' 키워드 사용가능

### 주요 개념
* 추상화 (abstraction)
  * 추상화의 족먹은 최적화
  * 공통 개념을 하나로 정의 하는 것부터 시작
  * 자바스크립트에서는 일반적으로 사용하지 않음
  => 상속을 크게 사용하지 않기 때문
* 캡슐화 (Encapsuilation)
  * 객체의 메서드와 프로퍼티를 외부와 차단(숨김: private)
  * 처리방법을 알려주는 대신 처리 결과값이나 처리된 값을 받을 수 있는 함수를 알려줌 (like API?)
  * 함수 안에 함수를 직접 호출 불가
  * 지역 변수 값은 외부에서 직접 접근 불가
  * source가 오픈되는 것과 exe 파일과의 차이 있음(보안 이슈)
* 다형성 (Polymorphism)
  * 파라미터 수 파이미터 데이터 타입에 따라 메소드 존재
  * **자바스크립트에서 사용되지 않음**
* 상속 (Inheritance)
  * 상속의 목적은 객체의 재사용
  * 목적에 맞는 객체를 내 객체에 포함시키는 것
  * 자바스크립트에선 오브젝트와 오브젝트를 병함.
  * 함수의 prototype에 인스턴스 연결
  * 다중 상속 불가
  * 다수의 인스턴스가 연결된 형태: ptototype chain

* 상속 매커니즘
  * 상속 구현형태 //  검색!
    * 상위든 하위든 본 객체의 하위로 상속하나 부모(상위)일 경우 super, 자식(하위)일 경우 sub으로 붙는다.
* Object.create() // ES5부터 - 궁금하면 검색!

### 프로토타입 체인
* Function 오브젝트의 protortpe에 new 연산자로 생성한 인스턴스를 연결한 형태
* 연속해서 prototype에 인스턴스를 연결할 수 있으므로 계층적으로 계속 연결이 가능하다.
* super, sub 키워드를 이용하여 부모 혹은 자식을 접근할 수 있다. (ES6)

### 검색 우선순위
* this.propertyName 형태로 검색하면
=> 우선 서브 오브젝트에서 검색? 찾으면 종료:못찾으면 상위로 검색

### 프로퍼티 연동 방지
* 오브젝트에 오브젝트를 할당하면 연동됨
* for ... in문을 이용하여 일일이 넣는다.
* 하지만 완벽하지 않음 **제귀함수**를 이용하여 해결

### 제귀함수
* 함수 안에서 자신 함수를 호출하는 형태
* {key: {key: {key: value}}} 형태에 사용
* `[1,2,[3,4,[5,6]]]` 형태에 사용

### 함수 즉시 실행(IIFE?)
* 엔진이 함수 코드를 만나면 자동으로 함수를 실행하는 형태
* 무명 함수, 익명 함수
  * (function(arguments...){ ... })(arguments...);
* 매커니즘 ==> ( 1 + 2 )/3 의 ()식 안을 우선 연산하는 원리

### 클로져
* 자바스크립트의 특정 기능을 지칭
* function 오브젝트를 생성 할 때 [[scope]]에 렉시컬 환경을 설정하고 함수가 호출되어있을 때 [[scope]]의 프로퍼티를 사용하는 메커니즘
* function 오브젝트 생성 과정을 이해하면 클로저는 단지 논리적인 접근

### 클로저와 무명 함수
* 무명 함수의 전제
  * 무명 함수는 이름이 없어 다시 사용하려면 저장 필요
  * 무명 함수 == 최상위에서 저장하기 않기 위해 사용
  * 무명 함수 == 람다 함수
* 클로저 함수 작성
  * 클로저 사용은 스코프의 변수를 사용하려는 의도
  * 변수는 외부에서 직접 접근할 수 없으므로정보 보호 가능
  * 이런 점을 활용하여 무명 함수 안에 클로저 함수를 작성하고  클로저 함수를 반환하여 사용하면 정보 보호 가능
  * (다수의 클로저 함수 사용) 무명 함수 안의 변수를 다수의 클로저 함수에서 공유 가능

