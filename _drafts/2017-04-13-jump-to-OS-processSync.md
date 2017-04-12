---
layout: post
title: "OS: process sync"
date: "2017-04-13"
---


## 프로세스 동기화를 고민해야 하는 이유

Process가 CPU에서 일정하게 순차적으로 돌아간다면 이 고민은 하지 않아도 될 지 모른다. 하지만 현재 Process는 병렬적 혹은 동시성을 지닌 상태로 돌아가게 되기 때문에 이 문재의 고민은 시작한다.

### 고민을 구체화 시켜줄 예제

연산자 `counter++`와 `counter--`를 생각해 보자. 이 연산자는 저장된 곳(counter)에 저장되어 있는 값을 1씩 증가시키거나 감소시키는 연산자다. 이 연산자를 기계어 수준으로 잘라보면 다음과 같은 공정을 가질 수 있다.

````
    register1 = counter
    register1 = register1 + 1
    counter = register1
````
````
    register1 = counter
    register1 = register1 - 1
    counter = register1
````

각 한줄이 하나의 task(cpu가 처리하는 단위)라는 가정하에 아래의 코드를 진행한다.

````
    counter++;
    counter--;
    printf("%d\n", counter);
````

그리고 위의 코드를 진행하는 스케쥴 표이다.

| Task | Process  |  state  |      task infomation      | processed data |
|------|----------|---------|---------------------------|----------------|
| T0   | producer | execute | register1 = counter       | register1 = 5  |
| T1   | producer | execute | register1 = register1 + 1 | register1 = 6  |
| T2   | consumer | execute | register2 = counter       | register2 = 5  |
| T3   | consumer | execute | register2 = register2 - 1 | register2 = 4  |
| T4   | producer | execute | counter = register1       | counter = 6    |
| T5   | consumer | execute | counter = register2       | counter = 4    |

위와 같은 상황이 100% 저렇게만 진행 되진 않지만 저렇게 진행될 가능성이 있고 이는 코드를 작성한 사람의 의도대로 결과가 나오지 않게 된다. 

> 이처럼 두 개 이상의 프로세스가 공통 자원을 병행적으로(concurrently) 읽거나 쓸 때, 공용 데이터에 대한 접근이 어떤 순서에 따라 이루어졌는지에 따라 그 실행 결과가 달라지는 상황을 `Race Condition`이라고 한다.

## 크리티컬 섹션 문제

각 프로세스 안에는 크리티컬 섹션(critical section)이라는 부분이 있다. 크리티컬 섹션은 해당 프로세스가 다른 프로세스와 함께쓰는(공용) 데이터를 불러오거나 쓰는 부분이다. 여기서 문제가 발생하는데 여러 프로세스가 동시에 같은 공용 데이터를 사용하게 될 떄 위와 같은 문제가 발생하게 되며 이를 방지 하기 위해 공용 데이터를 접근할 때에는 접근을 허가하는 요청을 보내고 요청이 수락 되었을 때 접근 할 수 있도록 하는 것을 크리티컬 섹션 문제라고 한다.

![일반적인 process 구조](/images/2017-04-13-jump-to-os-processSync/criticalSection.png)


### 크리티컬 섹션을 만족하는 조건.

1. Mutual exclusion(상호 배제): 한 프로세스가 크리티컬 섹션을 사용한다면 다른 프로세스는 크리티컬 섹션을 접근 할 수 없다.
2. Progress(진행): 크리티컬 섹션이 사용 중이지 않다면 접근을 원하는 프로세스는 언제든지 크리티컬 섹션에 접근 할 수 있어야 한다.
3. Bounded waiting(한정된 대기): 기다리는 시간은 한정적이여야 하며 무한정 기다려선 안된다.

### 그리고 그 조건들을 만족하는 여러 알고리즘들 

#### 피터슨 알고리즘

소프트웨어로서 해결한 알고리즘, 피터슨 알고리즘이다.

````
    do {

        flag[i] = true;
        turn = j;
        while (flag[j] && turn == j); // Busy Waiting
        
            // critical section
        
        flag[i] = false;
    
            // remainder section 
    
    } while (true);
````
둘다 접근하여고 하는 경우에는 turn 의 값으로 접근할 프로세스가 결정된다. 
그렇지 않은 경우에는 flag값을 통해서 접근할 수 있다. 

하지만 이 알고리즘에는 문제가 없는 것은 아니다. Busy Waiting(=spin lock)의 문제가 발생한다. 계속 CPU와 Memory를 사용하면서 대기하는 상황인 것이다. 쓸데 없이 while 속에서 무한루프를 반속하며 대기하고 있는 것은 비효율 적인 방법이다.

#### test_and_set()과 compare_and_swap() 그리고 하드웨어 동기화

사실 소프트웨어에서의 동기화는 커널의 인터럽트에 의해 모든 규칙이 께어질 수 있다. 그래서 하드웨어 단에서 크리티컬 섹션을 만족시키기 위한 기능을 구현하였으니 그것이 test_and_set()이다.

````
    boolean test_and_set(boolean *target) { 
        boolean rv = *target;
        *target = true; // target == lock

        return rv;
    }

    do {
        while (test_and_set(&lock)); /* do nothing */

            /* critical section */

        lock = false;

            /* remainder section */ 

    } while (true);

````

````
    int compare_and_swap(int *value, int expected, int new value) { 
        int temp = *value;
        if (*value == expected) 
            *value = new value;
        return temp;
    }

    do {
        while (compare_and_swap(&lock, 0, 1) != 0); /* do nothing */
            /* critical sexction */
        lock = 0;
            /* remainder section */ 
    } while (true);
````