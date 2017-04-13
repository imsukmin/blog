>---
layout: post
title: "OS: process sync"
date: "2017-04-13"
---


## 프로세스 동기화를 고민해야 하는 이유

Process가 CPU에서 일정하게 순차적으로 돌아간다면 이 고민은 하지 않아도 될 지 모른다. 하지만 현재 Process는 병렬적 혹은 동시성을 지닌 상태로 돌아가게 되기 때문에 이 문재의 고민은 시작한다.

### 고민을 구체화 시켜줄 예제

연산자 `counter++`와 `counter--`를 생각해 보자. 이 연산자는 counter라고 이름 붙여진 저장공간(변수)에 저장되어 있는 값을 1씩 증가시키거나 감소시키는 연산자다. 이 연산자를 기계어 수준으로 잘라보면 다음과 같은 공정을 가질 수 있다.

````c
    // counter++

    register1 = counter
    register1 = register1 + 1
    counter = register1
````

````c
    // counter--

    register1 = counter
    register1 = register1 - 1
    counter = register1
````

각 한줄이 하나의 task(cpu가 처리하는 단위)라는 가정하에 아래의 코드를 진행한다.

```` C
    counter++;
    counter--;
    printf("%d\n", counter); // 5? 4? 6?
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

각 프로세스 안에는 크리티컬 섹션(critical section)이라는 부분이 있다. 크리티컬 섹션은 해당 프로세스가 다른 프로세스와 함께쓰는(공용) 데이터를 불러오거나 쓰는 부분이다. 여기서 문제가 발생하는데 여러 프로세스가 동시에 같은 공용 데이터를 사용하게 될 떄 위와 같은 문제가 발생하게 되며 이를 방지 하기 위해 공용 데이터를 접근할 때에는 접근을 허가하는 요청을 보내고 요청이 수락 되었을 때 접근 할 수 있도록 하는 규약(protocol)을 정함으로서 크리티컬 섹션 문제를 해결한다.

아래의 그림은 일반적인 프로세스 구조로 각 영역별 역활이 있으며 entry section에서는 critical section을 접근 요청하고 접근 허가가 날때까지 대기하며, exit section의 경우에는 허가받은 권한을 돌려주는 과정을 진행해야한다.

![일반적인 process 구조](/images/2017-04-13-jump-to-os-processSync/criticalSection.png)


### 크리티컬 섹션을 만족하는 조건.

1. Mutual exclusion(상호 배제): 한 프로세스가 크리티컬 섹션을 사용한다면 다른 프로세스는 크리티컬 섹션을 접근 할 수 없다.
2. Progress(진행): 크리티컬 섹션이 사용 중이지 않다면 접근을 원하는 프로세스는 언제든지 크리티컬 섹션에 접근 할 수 있어야 한다.
3. Bounded waiting(한정된 대기): 기다리는 시간은 한정적이여야 하며 무한정 기다려선 안된다.

## 크리티컬 섹션 문제의 해결 조건들을 만족하는 여러 알고리즘

### 피터슨 알고리즘

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

### 하드웨어 동기화
사실 소프트웨어에서의 동기화는 커널의 인터럽트에 의해 모든 규칙이 께어질 수 있다. 그래서 하드웨어 단에서 크리티컬 섹션을 만족시키기 위한 기능을 구현되었다. 대부분 locking을 이용하여 크리티컬 섹션을 보호한다.

locking을 사용한다는 것은 크리티컬 섹션을 접근하기 전에 이 섹션에 대한 접근을 막고 단독적으로 사용한다는 의미이다. 그리고 사용이 종료되면 락을 풀고 다른 프로세스가 사용할 수 있도록 하는것이다.

단일 처리 환경(싱글프로세서)에서 공유 변수가 변경되는 동안에 `인터럽트`를 금지함으로서 동기화 문제를 해결하였다.(하지만 지금은 멀티 프로세스 시대)

지금은 `원자적(atomic) 하드웨어 명령어`를 제공함.
* 원자적(atomic) : 인터럽트 되지 않음

#### test_and_set()

> 한 워드(word)의 내용을 검사하고 변경 명령어 이다.

라고 공룡책에 정리되어있는데 잘 이해가 안되는 것 같다.

test_and_set()을 코드로 표현한다면 다음과 같다.

````c 
// default lock = false

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

크리티컬 섹션에 접근하기 위해 test_and_set을 무한하게 실행시키면서, lock변수가 false가 되는 것을 기다린다. 이미 크리티컬 섹션을 점유하였던 프로세스가 용무를 마치고 `크리티컬 섹션의 사용 권한을 반납(=> lock = false)`하게 되면 기다리고 있던 프로세스 중에 첫번 째로 test_and_set을 실행시킨 프로세스가 무한 루프를 빠저나가면서 `크리티컬 섹션의 사용 권한을 획득(=> lock = true)`하고 크리티컬 섹션을 점유하게된다.

#### compare_and_swap()

> 두 워드(expacted, newValue)의 내용을 원자적으로 교환 

````c
// default lock = 0

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
마찬가지로 크리티컬 섹션에 접근하기 위해 compare_and_swap을 무한하게 실행시키면서, lock변수가 0이 되는 것을 기다린다. 이미 크리티컬 섹션을 점유하였던 프로세스가 용무를 마치고 `크리티컬 섹션의 사용 권한을 반납(=> lock = 0)`하게 되면 기다리고 있던 프로세스 중에 첫번 째로 compare_and_swap을 실행시킨 프로세스가 무한 루프를 빠저나가면서 `크리티컬 섹션의 사용 권한을 획득(=> lock = newValue, 위의 코드에서는 1)`하고 크리티컬 섹션을 점유하게된다.

#### 위의 두 기능에 부족한 것이 있다.

위의 두 기능은 크리티컬 섹션의 접근 허가를 무작위로 하고 있기 때문에 한 프로세스의 **bounded waiting이 지켜지지 않을 수도 있다**. 이을 보완 하기 위해 대기 큐를 만들어서 확인 후 대기 순번에 맞추어서 프로세스를 실행 시키는 방법을 도입한다.

````c
do {
    waiting[i] = true;
    key = true;
    while (waiting[i] && key) 
        key = test_and_set(&lock);

    waiting[i] = false; 

        /* critical section */

    j = (i + 1) % n; // waiting queue안의 i의 다음 프로세스
    while ((j != i) && !waiting[j]) 
        j = (j + 1) % n;
    if (j == i) // 대기 중인 프로세스가 없는 경우 
        lock = false;
    else // 첫번째 대기 중인 프로세스인 경우 
        waiting[j] = false;
    /* remainder section */
} while (true);
````

## mutex

하드웨어를 기반으로 하는 해결책의 경우에는 확실한 방법이 되기도 하지만 동시에 프로그래머가 사용하기에는 복잡하다.(대부분 c low level 코드이거나 어샘블리단의 기능)

그래서 운영체제에서 소프트웨어에서 하드웨어적으로 사용되었던 기능을 사용할 수 있도록 툴을 만들어 제공하고 있다.

가장 간단한 방식의 기능이 **mut**ual **ex**clusion(상호 배제)를 제공하는 mutex이다.

````c
acquire() { // 함수 내용이 원자적으로 실행 됨
    while(!available); // busy wait
    abailable = false;
}

release() { // 함수 내용이 원자적으로 실행 됨
    available = true;
}

do {
    acquire();
        // critical section
    release();
        // remainder section
} while (true)

````

* 락획득(acquire())과 락방출(release())함수로 크리티컬 섹션을 보호
* `acquire()`, `release()` 둘은 원자적으로 실행되어야 함.
* 락 획득을 하기 위한 대기 방법으로 busy wait(spin lock) 사용

## semaphore(세마포어)

> 프로세스들을 동기화 시키는 (multex 락 보다) 좀 더 복잡한 동기화툴

이라고 공룡책에 서술되어 있으나 사실 mutex == semaphore라고 보면 된다.

* 세마포어 `S` 는 정수이다.
* 두 개의 표준 원자적 연산 wait()과 signal()로만 세마포어를 접근 할 수 있다.
* wait()과 signal()는 원자적이다.

````c
wait(S) {
    while (S <= 0); // busy wait
    S--;
}
signal(S) {
    S++;
}
````
S가 0이면 락 상태이다. S값에 초기 값에 따라 동시에 접근 할 수 있는 프로세스의 수가 달라지며 S값이 1인 세마포어를 이진 세마포어라고 하며 mutex와 같은 역활을 한다.

### 세마포어는 어렵다.

> 인간은 누구나 실수를 한다`

그런 가능성이 있기 때문에 코드를 꼼꼼히 봐야하지만, 실수를 하기 때문에 다음과 같은 상황이 발생 할 수도 있다.
````
// synch = 0

// P1
    // missing wait()
    S1;
    signal(synch);

// P2
    wait(synch);
    S2;
    // missing signal()
````

또한 여러 프로세스가 동시에 wait()과 signal()을 호출하여 S(세마포어)를 변경하지 못하도록 wait()과 signal()역시 크리티컬 섹션 문제에 해당이 된다.
그리고 무한 루프를 사용하기 떄문에 CPU가 많이 놀게 되는 경우가 발생하기도 한다.

### busy waiting 이라도...

그래서 무한루프로 대기를 하는대신 권한을 획득하기 전까지는 자신을 waiting queue로 보내는 기능을 추가하여 CPU가 놀지 않도록 하게 하면 된다.
그리고 자기 차래가 되면 waiting queue에서 불러와서 권한을 얻고 진행 하면 된다.

````c
typedef struct {
    int value;
    struct process * list;
} semaphore;

wait(semaphore* S) {
    S->value--;
    if (S->value < 0) {
        add this process to s->list;
        block() // back to ready queue in OS
    }
}
signal(semaphore* S) {
    S->value++;
    if (S->value <= 0) {
        remove a process P from S->list;
        wakeup(P);
    }
}
````

## monitor(모니터)

모니터는 OS를 넘은 언어에서 구현이 되어서 지원이 되는 동기화 구조 도구이다.

대표적으로 java의 `Synchronized` 키워드와 `Monitor` 객체가 있다.

기본적으로 모니터안에 존재하면 크리티컬 섹션 문제를 해결하는 공간이 되며 모니터에 접근할 떄는 한 프로세스만 접근 할 수 있기 때문에 상호 배제도 보장이 된다. 또한 모니터를 기다리는 queue를 만들면 progress와 bounded waiting까지 해결 할 수 있게 된다. (크리티컬 섹션 문제를 해결하기 위해서 전에 했던 알고리즘에 장점만 붙였다고 생각하면 된다.)

대략적으로 이렇게 생각하면 된다.

````java
class monitor extends Synchronized {
    
    int value;

    int getValue(){
        return value;
    }
    int setValue(changedValue) {
        value = changedValue;
    }
}
````


끝