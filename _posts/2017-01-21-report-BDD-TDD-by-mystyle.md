---
layout: post
title: "내맘대로 정리해보는 BDD, TDD"
date: "2017-01-21"
---

저번주 [telegram bot 만들기](/2017/01/13/review-make-telegram-bot)를 하고 이 프로젝트를 통해 많은 것들을 시도해보려 했습니다.

그 중에 이번주는 TDD를 진행하기로 했습니다만.. 

갑작스런 취업으로(게으름에 대한 변명은 아닙니다! ... 아니라구요!!ㅠ) 공부를 얼마 진행하지 못하고 글을 작성합니다. (ㅡㅡ)

## TDD란?

TDD(Test Driven Development)는 Test를 통과하는 코드를 작성하자라는 모토로 개발을 진행하는 소프트웨어 개발 방법 중 하나입니다. 간단히 설명하면 구현해야할 기능 Test를 통과하는 코드를 간단하게 작성한 후, 그 코드를 표준에 맞는 좋은 코드로 변환(리펙토링)을 하는 방법입니다.

프로세스를 요약해본다면 아래와 같습니다.

1. 기능 디자인 설계
2. (설계에 충실한) Test코드를 작성.
3. (간신히 돌아가는) 코드 작성
4. 코드 리펙토링

즉, 설계에 맞는 Test 소스를 만든 후 Test를 통과하는 코드를 작성합니다.

## BDD란?

TDD라는 걸출한 방법론이 있음에도 속속들이 나타나는 xDD들이 나타났는데 그중 하나인 BDD(Behaviour-Driven Development)는 Test를 행동이라는 말로만 바꾼 것입니다. 명세를 테스트 양식을 코드 기준이 아닌 소프트웨어 명세(어플리케이션 사용자의 행동)을 기준으로 테스트를 하는 방법입니다. 제가 이해한 기준으로는 UI양식에 따라서 테스트를 진행하는 것 입니다. 

[BDD 참조](http://blog.jaigurudevaom.net/319)

[TDD와 BDD의 차이점](http://blog.aliencube.org/ko/2014/04/02/differences-between-bdd-and-tdd/)
## 그럼 어떻게 BDD를 진행해볼까?

가장 좋은 하지만 조금 위험한 방법인 "예제를 이용한 설명"을 진행해볼까 합니다. 

가장 만만한 TODO 앱을 BDD기법으로 개발해봅시다.

### 1. TODO앱으로 할 수있는 것들을 나열해봅시다.

* TODO를 등록 할 수 있다.
* 등록된 TODO를 볼 수 있다.
* TODO를 진행완료로 바꿀 수 있다.
* 등록된 TODO를 지울 수 있다.

### 2. 1번에서 크게 나누었던 동작을 세분화 시켜봅시다

* TODO를 등록 할 수 있다.
    * 할일 내용을 입력할 수 있다.
    * 기한을 정할 수 있다.
* 등록된 TODO를 볼 수 있다.
    * 목록으로 볼 수 있다.
    * 상세정보를 볼 수 있다.
* TODO를 진행완료로 바꿀 수 있다.
    * 진행 한 할일을 진행완료로 바꿀 수 있다.
    * 진행완료였던 할 일을 다시 진행 중으로 바꿀 수 있다.
* 등록된 TODO를 지울 수 있다.
    * 진행 중인 할 일을 지울 수 있다.
    * 진행완료된 할 일을 지울 수 있다.

### 3. 그럼 2번을 BDD식 Test case로 작성해 봅시다.( Mocha - bdd 기준입니다.)

    // app.spac.js
    describe("TODO APP", function(){
        describe("TODO를 등록 할 수 있다.", function(){
            it("할일 내용을 입력할 수 있다.", function(){
                
            })
            it("기한을 정할 수 있다.", function(){
            
            })
        })
        describe("등록된 TODO를 볼 수 있다.", function(){
            it("목록으로 볼 수 있다.", function(){
            
            })
            it("상세정보를 볼 수 있다.", function(){
            
            })
        })
        describe("TODO를 진행완료로 바꿀 수 있다.", function(){
            it("진행 한 할일을 진행완료로 바꿀 수 있다.", function(){
            
            })
            it("진행완료였던 할 일을 다시 진행 중으로 바꿀 수 있다.", function(){
            
            })
        })
        describe("등록된 TODO를 지울 수 있다.", function(){
            it("진행 중인 할 일을 지울 수 있다.", function(){
            
            })
            it("진행완료된 할 일을 지울 수 있다.", function(){
            
            })
        })
    })

### 4. Test case에 필요한 것을을 채워 넣습니다.

    // app.js
    var TODOSTORE = [];
    var todo = function(_title, _description, _deadline){
        return {
            title: _title,
            description: _description,
            deadline: _deadline,
            inProgress: true
        }
    }

    // app.spac.js
    describe("TODO APP", function(){
        describe("TODO를 등록 할 수 있다.", function(){
            it("할일 내용을 입력할 수 있다.", function(){
                assert.equal(TODOSTORE.length, 0);
                TODOSTORE.push(todo("할 일","음..?"));
                assert.equal(TODOSTORE.length, 1);                
            })
            it("기한을 정할 수 있다.", function(){
                TODOSTORE.push(todo("할 일2","음음...","2017-01-28"));
                assert.equal(TODOSTORE[TODOSTORE.length-1].deadline, "2017-01-28");                
            })
        })
        describe("등록된 TODO를 볼 수 있다.", function(){
            it("목록으로 볼 수 있다.", function(){
                ...
            })
            it("상세정보를 볼 수 있다.", function(){
                ...
            })
        })
        describe("TODO를 진행완료로 바꿀 수 있다.", function(){
            it("진행 한 할일을 진행완료로 바꿀 수 있다.", function(){
                ...
            })
            it("진행완료였던 할 일을 다시 진행 중으로 바꿀 수 있다.", function(){
                ...
            })
        })
        describe("등록된 TODO를 지울 수 있다.", function(){
            it("진행 중인 할 일을 지울 수 있다.", function(){
                ...
            })
            it("진행완료된 할 일을 지울 수 있다.", function(){
                ...
            })
        })
    })

### 5. 이제 리펙토링을 진행합니다. 

코드는 생략하겠습니다. 일단 todo 함수의 경우에는 module화 시켜서 변수 및 메서드를 정의 하고 객체화 시키는 것도 하나의 방법이라고 할수 있습니다. (여기에 담기에는 홈페이지 공간이 부족(..)하니.. 쿨럭) 또한 저장소를 Array가 아닌 다른 방법으로(DB라던지 파일이라던지 등등) 조건에 맞도록 변경하면 됩니다.


## 정리

간단한게 살펴본 TDD, BDD였습니다. 지금까지 대략적인 방법과 "이렇게 하면 다 된다!" 라는 공식만 주구장창 봐왔던 저에겐 실습책 한권가 실습은 큰 도움이 되었습니다.

아마 이 글은 게으름이 나아갈 때 쯤 보강이 되지 않을 가 생각합니다. 너무 적은 정보를 서술해놓을 것 도 그렇고 예제로 설명되어있으나 예제에 대한 설명이 없기 떄문에 정보라기보다는 일기에 가까운 글이 되었기 때문입니다.

이 글을 보고 계신분중에 "TDD, BDD 이런거 고수들이나 하는 거 아닌가?" 이런 생각하시는분들 이런 허술 한 글 보다는 한번 해보시는 것을 추천합니다. 어렵지 않습니다. 이런 저도 이해 하고 실행 할 뿐만 아니라 글도 쓰지 않았습니까? 이상입니다. 끗!
