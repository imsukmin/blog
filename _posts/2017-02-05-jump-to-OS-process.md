---
layout: post
title: "Jump To OS : Process"
date: "2017-02-05"
---


OS는 중요하고 프로그래머라면 누구든지 알고 있어야 하는 것이다. 라는 말을 프로그래밍하시는 분들이나 전산과 계열에서 대학을 졸업하신 분들이라면 한번쯤은 들어보셨겠죠? 그래서 저도 막상 대학에선 잘 이해 하지 못했던 운영체제에 대한 공부를 결심한 한지 약 2년째 되어가는 차에.. 회사 공부 발표를 운영체제로 정해버렸습니다.. ㅠㅠ 이걸 2주만에 공부할 수 있었던건 정말로 신입적응기간이기 때문인것 같은데... 이번에 정리를 잘 해놔서 가치 있는 글을 남겨보려 합니다.(뭐 또 끝내놓으면 사라질 글일 느낌이 없진 않습니다..)


# Process Concept

프로세스는 단순히 실행되고 있는 코드를 뜻하는 것이 아니다. 프로세스는 많은 것을 가지고 있는데 아래에 나열해본다.

![process-in-memory](/images/2017-02-05-jump-to-OS/process-in-memory.PNG)

* Program Counter : 명령어 주소 레지스터라고 불리우며 다음 번에 실행될 명령어의 주소를 가지고 있는 저장소.
* Stack : 임시적인 데이터(function parameters, return addresses, local variables)를 가지는 저장소이며 메모리 끝부터 0번지 방향으로 자료가 쌓인다.
* data section : global variables 저장소.
* heap : process가 실행되는 시간에 동적으로 할당되는 메모리 공간.

Program은 Process가 아니다. Program은 디스크에 저장된 실행코드의 리스트(실행파일)들 같은 **passive entity**이다. 반면 Process는 다음실행해야할 실행코드와 그 실행코드가 엮인 리소스와 같은 **active entity**이다. Program이 process가 되기 위해선 실행파일이 메모리로 불러와져야 한다. 같은 프로그램을 여러 사용자가 동시에 사용하거나 한 사용자가 같은 프로그램을 여러개 실행하여 사용하여도 동일한 process를 사용하는 것이 아닌 각각의 실행된 프로그램의 process를 사용하게 되는 것이다. process는 자신이 아닌 다른 process를 실행할 수 있다.

## Process State

[참고가 된 자료](http://operatingsystems.tistory.com/entry/OS-%ED%94%84%EB%A1%9C%EC%84%B8%EC%8A%A4-%EA%B4%80%EB%A6%ACProcess-Management)

![process-in-memory](/images/2017-02-05-jump-to-OS/diagram-of-process-state.PNG)

* New : Process가 만들어 진다.
* Running : 명령어가 실행되어지고 있다.
* Waiting : 어떠한 이벤트(I/O interrupt 등)로 인해서 기다리게 됨.
* Ready : 프로세서에 할당되길 기다린다.
* Terminated : 프로세스가 종료 되었다.


## PCB (Process Control Block)

OS안에서 각각의 process는 Process Contral Block(PCB)로 표현된다.

![PCB](/images/2017-02-05-jump-to-OS/PCB.PNG)

* Process state : 현재 프로세스의 상태이다( ready, waiting, running 등등)
* Program counter : 이 프로세스가 다음에 실행시켜야 할 명령어의 주소값.
* CPU registers : 프로세스의 다양한 상태값(stack pointer, condition code, index register)이나 interrupt로 인한 waiting 상황 발생시 process의 모든 값을 저장하는 장소.

![CPU-switch](/images/2017-02-05-jump-to-OS/CPU-switch.PNG)

* CPU-Scheduling infomation : cpu가 처리하는 스케쥴링 큐의 포인터나 스케쥴링의 기준이 되는 값을 저장.
* Memory-management information : OS가 관리하는 Memory ststem의 page table이나 segment table, base register, limit register 값이 들어있다.

![base-limit-register](/images/2017-02-05-jump-to-OS/base-limit-register.PNG)

* Accounting infomation : (process가) 사용중인 CPU, 실 사용시간, time limits, account numbers, job or process numbers 등의 정보가 들어있다.
* I/O status infomation : process에 할당된 I/O devices 리스트, 열고 있는 files 등의 정보가 들어있다.

# Process Scheduling

멀티프로그래밍의 목적 중 하나는 CPU가 노는 시간을 줄이는 것이다. 시분할의 목적은 process를 cpu에 자주 할당시켜 각각의 프로그램이 도는동안 상호 호환시키기 위해서다. 이 목적들을 위해 **process Scheduler**는 사용가능한 process를 선택하여 CPU에 실행시킨다.

## Scheduling Queues

process가 시스탬에 속하면 process는 **Job Queue**에 올라간다. process가 memory에 로딩이 되는 순간부터 ready나 Waiting상태일 경우 **Ready Queue**에 올라간다. **Ready Queue**는 보통 linked list로 저장이 되며 첫부분과 끝부분의 포인터를 가지는 구조이고, 각각의 PCB블럭은 각자 자신의 다음 실행해야할 PCB를 가리킨다.

**Job Queue**에는 다른 queue들이 존재한다. process가 CPU에 할당이 될 떄, process가 실행 중 갑작스럽게 종료 되거나, interrupt 당하거나, I/O Event를 기다리기 되기도 한다.

![Job-Queue](/images/2017-02-05-jump-to-OS/Job-Queue.PNG)

새로운 process는 ready queue에 들어간다. 그러고 실행될 때(dispatched) 까지 기다린다. process가 CPU에 처리되는 동안 몇가지 이밴트가 발생할 수 있다.

* I/O Queue에 있던 I/O request가 발생한다.
* 새로운 자식 process가 발생하고 자식 process가 끝나기를 기다려야한다.
* process는 interrupt로 인해 강제적으로 CPU에서 제거 될 수 있다.
* 모든 이벤트가 발생하면 CPU에 있던 process는 Ready Queue로 돌아간다.
* process는 Terminated 될 떄까지 위의 과정을 반복한다.

## Schedulers

OS에서는 스케쥴링을 위해 어떤 방식으로 이루어진 몇몇 큐에 있는 프로세스를 선택해야 하는데 이 일을 수행하는 것은 Scheduler라고 한다.
배치 시스탬은 종종 process가 급작스럽게 많아지게 되면 disk에 process를 저장한다. long-term Scheduler(job Scheduler)는 저런 process들을 실행시키기 위해 memory로 불러오는 역활을 하며, short-term Scheduler(CPU Scheduler)는 실행이 준비가 된 process들 중 하나를 CPU에 할당한다. 두 scheduler의 가장 큰 차이점은 실행 빈도이다. 

short-term Scheduler는 CPU에 할당 시킬 process를 선택한다. 그래서 short-term Scheduler는 빠르게 작동해야한다. 

![long-term Scheduler](http://cfile7.uf.tistory.com/image/123A763350E18EBC2FCF47)
출처 : [빛나유님 블로그](http://operatingsystems.tistory.com/entry/OS-%EC%8A%A4%EC%BC%80%EC%A5%B4%EB%9F%AC-scheduler)


반면에 long-term Scheduler는 disk에 있는 code를 memory로 이전시키는 역활을 하기 떄문에 short-term Scheduler와는 상대적으로 빈도수가 적게 일을 한다. ([참고자료](http://operatingsystems.tistory.com/entry/OS-%EC%8A%A4%EC%BC%80%EC%A5%B4%EB%9F%AC-scheduler))


![short-term Scheduler](http://cfile9.uf.tistory.com/image/2552DD3650E18ECE0AABEC)
출처 : [빛나유님 블로그](http://operatingsystems.tistory.com/entry/OS-%EC%8A%A4%EC%BC%80%EC%A5%B4%EB%9F%AC-scheduler)

대부분의 process들은 I/O bound 이냐 CPU bound 이냐로 나뉜다. I/O bound process의 경우는 처리되는 시간을 I/O처리에 두고, CPU bound process의 경우는 처리되는 시간을 CPU 연산에 둔다. long-term Scheduler는 이 두가지의 process들을 잘 결합해서 스케쥴링을 해야한다.

어떠한 시스탬은 long-term Scheduler를 빼거나 축소 시킨다. 시분할이 적용된 OS들(UNIX, WINDOWS 등)에서는 중간 단계의 스케줄링인 medium-term scheduler를 사용한다. medium-term scheduler의 핵심은 오랫동안 사용되지 않는 process를 메모리에서 뺴서 다른 저장공간(ex. disk, usb 등)에 두고 필요 할 떄 다시 메모리로 불러오는 방식이며 이 구동을 swapping이라고 한다.

![medium-term-scheduling](/images/2017-02-05-jump-to-OS/medium-term-scheduling.PNG)

## context switch

interrupt는 CPU의 처리를 기존 작업에서 kernel routine으로 바꾼다. interrupt가 일어나 기존 작업에 대한 정보(the current **context** of the process running on the CPU)를 저장한다. 그래야만 중단된 지점부터 다시 실행할 수 있기 때문이다. 

CPU가 다른 process로 전환시 현재 process의 상태 정보(PCB)을 저장하고 작업해야할 process의 상태 정보를 불러오는 과정을 **context switch**라고 한다. 

# Operations on Process

대부분의 시스템의 process들은 동시에 실행되며, 동적으로 실행되거나 죽는다.

## Process Creation

process는 다른 process에 의해 생성된다.(그리고 그 프로세스를 child process라고 한다.) 그런 행위들이 반복해서 일어나면 계층이 생기고 그 모양을 도식화 시키면 Tree가 된다.각각의 Process는 PID(프로세스 식별자)가 존재한다.

![tree-of-processes](/images/2017-02-05-jump-to-OS/tree-of-processes.PNG)

init process는 root 프로세스이며 모든 프로세스의 부모가 된다. init process을 통해 다른 프로세스를 만들어내며 사용자의 메인프로세스나 web server, print server, ssh로 접속한 사용자까지 생성해 낸다. linux에서 process 목록을 보고 싶다면 다음 명령어를 사용하면 된다.

    ps -el

보통 child process가 만들어 질 때, 일정한 리소스 (CPU time, memory, files, I/O devices)가 필요하다. 이 리소스는 OS가 주거나 부모 process의 subset 자원으로서 할당 되어(공유하며) 사용할 수도 있다. 새로운 process가 생성되면,

1. 부모와 자식 process가 동시에 실행된다.
2. 부모는 자식이 끝날 떄까지 기다린다.

또한 두가지의 주소 공간을 가질 수 있다.

1. 부모를 복사한다.
2. 새로운 프로그램으로 로드 된다.

이러한 차이를 알기 위해서는 UNIX OS을 조금 알아야한다. UNIX에서는 fork()라는 system call을 이용하여 새로운 process를 만든다. fork()를 이용하면 기존의 PID를 반납하는 부모 process와 PID = 0을 반환하는 자식 process를 생성하여 각자가 할 일을 하며 다음과 같이 사용하면 됩니다.

````
#include <stdio.h>

int main() {
	pid_t pid;    // 프로세스 번호 (Process ID; PID)가 저장될 변수 pid
	
	pid = fork(); // 자식 프로세스를 생성한다. 
  						  // 성공시 0, 실패시 -1, 부모 프로세스이면 양수 (프로세스 ID)를 반환한다.
	if (pid < 0) {
		// pid 가 음수이면 에러가 난 것이다.
		fprintf(stderr, "fork failed!");
		return -1;
	} else if (pid == 0) {
		// pid 가 0 이면 자식 프로세스이다.
		// 자식 프로세스가 해야 할 일
		printf("자식 프로세스 (#%i)", getpid());

	} else {
		// pid 가 양수이면 부모 프로세스이다.
		// 부모 프로세스가 해야 할 일
      	printf("부모 프로세스 (#%i)", getpid());
		wait(NULL);  // 자식 프로세스들이 모두 종료될 때 까지 기다린다.
	}
	return 0;
}
````

fork() 후에 보통은 두 process중 하나는 exec()라는 system call을 사용하여 새로운 프로그램을 해당 process에 로딩한다(exec()를 실행한 process의 메모리는 파괴된다.).([이렇게 생성하는 이유! : 댓글에 shell 프로그램을 예시로 설명함.](https://kldp.org/node/141415))
exec() system call은 바이너리 파일을 메모리로 로딩하고 실행한다. 위의 방법을 사용하면 서로의 process는 통신이 가능하며 다른 일이 가능해진다. 부모 프로세스는 다른 자식 프로세스를 만들거나 wait() system을 이용하여 ready queue로 돌아간 후 자식 프로세스가 끝나기를 기다린다. 

![using-fork](/images/2017-02-05-jump-to-OS/using-fork.PNG)

## Process Termination

process가 종료되는 시점은 process의 마지막 명령을 실행했거나 exit() system call을 사용하는 경우 발생한다. process 가 종료되면 종료 상태 값(보통 int)을 반환하며 OS에서는 모든 리소스를 반환시킨다.

부모 process가 자식 process를 죽이는 경우가 발생하는데 다음과 같다.

* 자식이 허용치 이상의 리소스를 사용하는 경우(죽여야 하는 상황을 감지하기 위해, 부모 process에 자식 리소스를 감시하는 기능을 가져야 한다.)
* 자식 process가 더 이상 필요 없는경우
* 부모 process가 종료 되는 경우.(OS가 자동으로 종료 시킨다. 이런 현상을 cascading termination 이라고 한다.)

자식 process가 종료되면 커널에 리턴값을 넘기고 커널은 부모에게 그 값을 전달한다. 그러나 그 값을 넘기기 전에 부모 프로세스가 불의의 사고(다른 process가 죽임 등등)로 종료된다면 커널은 해당 자식 프로세스를 좀비 프로세스로 만든다.(wait()을 써야하는 이유, UNIX에서는 사실 좀비가 되면 부모가 없어지는게 아니고 init process가 임시로 부모 역활을 하게 된다.)

# interprocess Communication [[참고자료](http://talkingaboutme.tistory.com/551)]

process들은 독립적으로 혹은 서로 의존하며 동시에 실행되고 있다. **process는 독립적이다.** 
어떠한 process도 다른 process의 data를 공유할 수 없다. 만약 process가 서로간의 간섭이 가능하다면 process는 협동이 가능하다. 어떠한 다른 process와 data를 공유하는 process라면 이미 cooperating process 이다.
 프로세스 협력 할 수 있는 환경을 제공하기 위한 몇 가지 이유가 있다.

* Information sharing. 여러명의 사용자가 같은 곳의 정보를 사용하려 하는 경우, 동시에 접근이 가능하도록 해줘야 한다.
* Computation speedup. 어떠한 task를 빠르게 하기위해서는 task를 쪼개서 병렬로 처리하게 된다. 
* Modularity. 모듈러 유행을 따르고 싶어하는 경우이다. process나 threads로 나누어 처리하려한다.
* Convenience. 개인 유저일 경우라도 한번에 많은 task를 처리하고 싶어한다. 예를들면 워드를 작성하고 음악을 들으면서 컴파일하는 경우이다.

cooperating process는 data와 information을 교환을 허락하는 메카니즘인 interprocess communication(IPC)이 필요하다. IPC에는 2가지의 기본 모델이 있다.(shared memory, message passing).
 shared memory 모델에서는, cooperating process가 shared memory를 만든다. 그리고 공유되고 있는 메모리에서 data를 읽고 쓰며 정보를 교환한다.
 message passing 모델에서는 통신이 cooperating process들간의 메시지 교환을 통해 진행된다.

![sharedmemory-messagepassing](/images/2017-02-05-jump-to-OS/sharedmemory-messagepassing.PNG)

## Shared-Memory Ststems

일반적으로, Shared-Memory는 주소공간 안의 process가 생성해놓은 Shared-Memory 위치한다.

### producer-consumer problem

producer process는 정보를 생산하고 consumer process는 정보를 소비한다. 예를 들면, 컴파일러는 어셈블러에게 정보를 소비시켜 어셈블리코드를 생산한다. 그럼 어셈블러는 로더에게 어셈블리코드를 소비시켜 오브젝트 모듈을 만든다. 이 문제는 client-server 페러다임에 비유할 수 있다. 웹 서버가 html 파일을 만들어 client에게 전달하면 client는 브라우저를 통해 html을 소비하여 화면을 구성한다.

producer-consumer problem의 하나의 해결책은 shared-memory를 사용하는 것이다. producer와 consumer를 동시에 돌리기 위해서는 공용으로 사용하는 buffer가 필요하다.
 2가지 종류의 buffer를 사용한다. unbounded buffer로 크기제한이 없는 buffer 이다. consumer는 새로운 아이탬을 기다리겠지만 producer는 항상 새로운 아이탬을 만들어 낼 수 있다. bounded buffer는 크기의 제한을 두는 buffer이다. 이 경우에는 buffer가 비어있다면 consumer가 기다리게 되고, buffer가 가득차면 producer가 기다리게 된다. 

## Message-Passing Ststems

message-passing은 shared-memory없이도 process들의 동작을 동기화 시킬 수 있고, process간에 통신이 가능하도록 하는 메커니즘을 제공한다. 이 환경은 특히 네트워트 안에 다른 컴퓨터의 process와 통신하는 분산 환경에서 유용하다. 
message-passing 기능은 최소 2가지의 기능을 지원한다.

    send(message)                 receive(message)

message의 크기는 고정되거나 가변적으로 보낸다. 만약 message가 고정적이라면, 시스탬 래밸에서의 구현을 간단하게 한다. 하지만 이 재한은 프로그래밍적 작업을 어렵게 한다. 반대로 가변적인 message는 좀더 복잡한 시스탬 래밸의 구현이 필요하나, 프로그래밍 작업이 간단해진다. 
process P와 Q가 있다고 가정하고 그들끼리 통신을 하려면 message를 주고 받을려면 **communication link** 그들 사이에 있어야 한다. **communication link**은 다양한 방법으로 구현할 수 있다. 물리적인 구현을 생각할 필요는 없지만, 논리적인 구현을 해야한다. 논리적 구현을 하는데는 몇가지 방법이 있다.

* 직접 혹은 간접 통신
* 동기 혹은 비동기 통신
* 자동 혹은 명시적 버퍼링

#### Naming

직접 통신하에 통신하려는 각 process는 통신의 수신자 또는 송신자를 명시 적으로 명명해야합니다. 이 경우에 send() receive() 정의를 다음과 같이 합니다.

**symmetry**

* send(P, message) - process P에 message를 보낸다.
* receive(Q, message) — process Q로 부터 온 message를 받는다.

그리고 이 방식의 communication link는 다음과 같은 속성을 따른다.

* 링크는 모든 통신을 원하는 짝이 있는 process끼리 자동으로 열린다. 프로세스들은 자신이 통신하고 싶은 process의 신원값(누구인지 확인하는 값)을 알아야한다.
* 링크는 정확히 2개의 프로세스에 속한다.
* 각각 짝이 된 process들에는 하나의 링크만 존재한다.

**asymmetry**

* send(P, message) — process P에 message를 보낸다.
* receive(id, message) — 어떤 process에서 message를 받는다. id 변수는 통신을 열기 위한 process의 이름을 설정한 것이다.

위의 두방법의 단점은 process정의 결과의 모듈성을 제한한다. 하나의 process의 id를 바꾸면 통신하던 다른 process 역시 id값을 바꿔줘야 한다.

간접 통신에선 message를 mailboxes 혹은 ports라는 곳에서 주고 받는다. mailbox는 추상적으로 개체를 프로세스별로 메시지를 배치하고 메시지를 제거 할 수있는 개체로 볼 수 있습니다. 각 mailbox에는 고유 한 ID가 있습니다. process끼리 통신하기 위해서는 그들끼리 하나의 mailbox를 공유해야 합니다.

* send(A, message) — mailbox A에 message를 보낸다.
* receive(A, message) — mailbox A에서 message를 받는다.

그리고 이 방식의 communication link는 다음과 같은 속성을 따른다.

* 각 맴버 모두 공유 사서함이있는 경우에만 한 쌍의 process간에 링크가 설정됩니다.
* 링크는 두 개 이상의 process와 연관 될 수 있습니다.
* 통신 process의 각 쌍 사이에는 여러 링크가 존재할 수 있으며 각 링크는 하나의 mailbox에 해당합니다.

또한 mailbox에서 수신자를 정하는 규칙과 조건은 다음과 같다.

* 링크가 최대 두 개의 process와 연결되도록 허용합니다.
* 한 번에 하나의 process 만 receive() 작업을 수행 할 수 있습니다.
* 시스템이 메시지를 수신 할 process를 임의로 선택하도록합니다 (두 process가 동시에 접근하더라고 한 process만 수신 가능). 혹은 시스템은 메시지를 수신 할 process를 선택하는 알고리즘을 정의 할 수 있습니다 (예 : 라운드 로빈, 프로세스가 차례로 메시지를 수신하는 경우). 혹은 시스템은 송신자에 대한 수신기를 식별 할 수있다.

mailbox는 process나 OS의 소유가 된다. process가 mailbox의 소유인 경우 mailbox의 소유자(message를 받는사람)와 사용자(message를 주는사람)로 나뉜다. process가 mailbox의 소유을 끝내는경우 mailbox는 없어지고 mailbox가 없어진 것을 다른 process에게 알려야 한다. 

반대로, OS는 자기 소유의 mailbox를 가지게 된다. 그 mailbox는 어떠한 process도 접근할 수 없고 독립적이다. OS다음과 같은 process가 다음의 기능을 가능하게 하도록 제공해야한다.

* 새로운 mailbox를 만들기
* mailbox를 통해 message를 보내고 받기
* mailbox를 지우기

새로운 mailbox를 만든 process는 기본값으로 mailbox에 주인이 된다. 기본적으로 mailbox를 통해서 message를 주고 받지만 system call을 이용하여 사용해야 한다.

#### Synchronization

message passing은 blocking 혹은 nonblocking( synchronous 와 asynchronous 로 알려진)이 된다.

* Blocking send.        수신 process 또는 mailbox에서 message를 수신 할 때까지 송신 process가 차단된다.
* Nonblocking send.     발신 process가 message를 보내고 작업을 다시 시작한다.
* Blocking receive.     수신자를 message를 사용할 수 있을 때 까지 차단한다.
* Nonblocking receive.  수신자는 유효한 message 또는 널 (null)을 가져온다.

send()와 receive()가 모두 블럭일 때, 우리는 둘 사이에 랑데뷰(집결지)를 같는다. 이것이 생산자와 소비자 문제의 해결책이 된다. 생산자는 수신자 혹은 mailbox에 message가 전달 될 때 까지 send() 블락하고 기다립니다. 마찬가지로 소비자도 message를 받을 수 있기 전까지는 receive()를 블락합니다.

#### Buffering

통신이 직접이든 간접이든, message는 임시의 queue에서 통신 process들이 교환한다. 기본적으로 queue라면 3종류로 구현할 수 있다.

* Zero capacity. queue의 최대 길이는 0. 따라서 링크에는 대기중인 메시지가 없고, 이 경우 보낸 사람은받는 사람이 메시지를 받을 때까지 차단해야합니다.
* Bounded capacity. queue의 길이는 n. 따라서 최대 n 개의 메시지가 메시지에 상주 할 수 있다 새 메시지를 보낼 때 queue가 가득 차 있지 않으면 메시지는 queue에 저장된다. (메시지가 복사되거나 메시지 포인터가 유지됨). 송신자는 대기하지 않고 계속 실행할 수 있습니다. 링크가 가득 차면 발신자는 queue에서 사용 가능한 공간이 확보 될 때까지 차단 해야합니다.
* Unbounded capacity. queue의 길이는 잠재적으로 무한. 따라서 어떤 수의 메시지도 기다릴 수 있고, 발신자는 차단하지 않는다.

## example of IPC Systems


### POSIX Shared Memory
````
#include <stdio.h> 
#include <stlib.h> 
#include <string.h> 
#include <fcntl.h> 
#include <sys/shm.h> 
#include <sys/stat.h>

int main()
{
/* the size (in bytes) of shared memory object */
const int SIZE 4096;
/* name of the shared memory object */
const char *name = "OS";
/* strings written to shared memory */ 
const char *message 0 = "Hello";
const char *message 1 = "World!";

/* shared memory file descriptor */ 
int shm_fd;
/* pointer to shared memory obect */ 
void *ptr;

    /* create the shared memory object */
    shm_fd = shm_open(name, O CREAT | O RDRW, 0666);

    /* configure the size of the shared memory object */ 
    ftruncate(shm_fd, SIZE);

    /* memory map the shared memory object */
    ptr = mmap(0, SIZE, PROT WRITE, MAP SHARED, shm_fd, 0);

    /* write to the shared memory object */ 
    sprintf(ptr,"%s",message 0);
    ptr += strlen(message 0); 
    sprintf(ptr,"%s",message 1);
    ptr += strlen(message 1); 
    
    return 0;
}
````

````
#include <stdio.h> 
#include <stlib.h> 
#include <fcntl.h> 
#include <sys/shm.h> 
#include <sys/stat.h>

int main()
{
/* the size (in bytes) of shared memory object */
const int SIZE 4096;
/* name of the shared memory object */
const char *name = "OS";
/* shared memory file descriptor */ 
int shm_fd;
/* pointer to shared memory obect */ 
void *ptr;

    /* open the shared memory object */
    shm_fd = shm_open(name, O RDONLY, 0666);
    /* memory map the shared memory object */
    ptr = mmap(0, SIZE, PROT READ, MAP SHARED, shm fd, 0);
    /* read from the shared memory object */
    printf("%s",(char *)ptr);
    /* remove the shared memory object */ 
    shm_unlink(name);
    
    return 0;
}
````

### Mach message passing

Mach에서는 System Call 조차도 message에 의해 이루어진다. 작업이 만들어지면 두 개의 특수 mailbox 인 Kernel mailbox과 Notify mailbox이 만들어진다. 커널은 Kernel mailbox를 사용하여 작업과 통신하고 이벤트 발생 알림을 Notify port로 보낸다. message 전송에는 시스템 호출이 세 번이면 된다. `msg_send()` 호출은 mailbox로 message를 보낸다. message는 `msg_receive()`를 통해 수신된다. Remote procedure calls (RPC)은 `msg_rpc()`를 통해 실행된다. `msg_rpc()`는 message를 보내고 발신자로부터 정확히 하나의 반환 message를 기다린다. 이런 식으로 RPC는 일반적인 서브 루틴 프로시저 호출을 모델링하지만 시스템간에 작동 할 수 있다. 

### Windows message passing

Windows의 message-passing 기술은 advanced local procedure call (ALPC)라고 불리운다. 같은 컴퓨터 안에 process끼리 통신할 떄 쓰이며, 넓게 쓴다면 RPC와 유사하게 보이지만 windows에 특화 시켰다. 윈도우는 2개의 mailbox를 사용한다: connection ports, communication ports.

Server process는 모든 process가 볼 수있는 Connection port 개체를 게시한다. 클라이언트가 서브 시스템의 서비스를 원하면 서버의 Connection port 객체에 대한 재어권을 열고 해당 포트에 연결 요청을 전송한다. 그런 다음 서버는 채널을 생성하고 클라이언트에 재어권을 반환한다. 채널은 개인 통신 포트 쌍(Client-Server message 용, Server-Client message 용)으로 구성된다. 또한 통신 채널은 클라이언트와 서버가 일반적으로 응답을 기대할 때 요청을 수락 할 수 있도록하는 콜백 메커니즘을 지원한다.

# Communication in Client-Server Systems

## Sockets

소켓은 통신의 끝지점이다. 네트워크를 통해 통신하는 한 쌍의 process는 한 쌍의 소켓 (각 process마다 하나씩)을 사용한다. 소켓은 IP주소와 포트번호로 식별한다. 보통 소켓은 Server-Client 아키텍쳐에 사용된다. 서버는 지정된 포트를 수신하여 들어오는 클라이언트 요청을 대기한다. 요청이 수신되면 서버는 클라이언트 소켓에서 연결을 수락하여 연결을 완료한다.

![socket](https://www.tutorialspoint.com/perl/images/perl_socket.jpg)

소켓 연결의 대표적인 방식이 2가지 있다: Connection-oriented (TCP) sockets, Connectionless (UDP) sockets.

## Remote Procedure Calls(RPC)

RPC는 네트워크 연결이있는 시스템간에 사용하기 위해 프로시저 호출 메커니즘을 추상화하는 방법으로 설계되었다. 많은면에서 IPC 메커니즘과 유사하다.

RPC의 의미는 클라이언트가 프로시저를 로컬에서 호출 할 때와 같이 원격 호스트에서 프로시저를 호출 할 수 있게 한다. RPC 시스템은 클라이언트 측에서 스텁을 제공함으로써 통신이 일어나는 세부 사항을 숨긴다. 일반적으로 각 별도의 원격 프로시저마다 별도의 스텁이 있다. 클라이언트가 원격 프로시저를 호출하면 RPC 시스템은 해당 스텁을 호출하여 원격 프로시저에 제공된 매개 변수를 전달한다. 이 스텁은 서버의 포트를 찾고 매개 변수를 정렬한다. 마샬링은 매개 변수를 네트워크를 통해 전송 될 수있는 형식으로 패키징하는 것을 포함한다. 그런 다음 스텁은 메시지 전달을 사용하여 서버에 메시지를 전송한다. 서버 측의 비슷한 스텁이이 메시지를 수신하고 서버에서 프로시저를 호출한다. 필요한 경우 반환 값은 동일한 기술을 사용하여 클라이언트에 다시 전달된다.

![RPC](http://cfile2.uf.tistory.com/image/195AAD154AC051FD7D2AEF)

## Pipes

파이프는 두 process가 통신 할 수 있도록하는 직점 연결해주는 역할을 한다. 파이프는 초기 UNIX 시스템에서 최초의 IPC 메커니즘 중 하나였다. 이들은 일반적으로 process가 서로 통신 할 수있는 간단한 방법 중 하나를 제공하지만 몇 가지 제한이 있습니다. 파이프를 구현할 때는 다음 네 가지 문제를 고려해야 한다.

1. 파이프가 양방향 통신을 허용합니까? 아니면 통신이 단방향입니까?
2. 양방향 통신이 허용되는 경우 반이중 (데이터는 한 번에 한 방향으로 만 전송할 수 있음) 또는 전이중 (데이터가 동시에 양방향으로 이동할 수 있음)입니까?
3. 의사소통 과정간에 관계 (예 : parent-child)가 있어야합니까?
4. 파이프가 네트워크를 통해 통신 할 수 있습니까? 아니면 통신 프로세스가 동일한 시스템에 있어야합니까?

### Ordinary Pipes

보통 파이프는 두 process가 표준 생산자-소비자 방식으로 통신 할 수 있게한다. 생산자는 파이프의 한쪽 끝(쓰기 끝)에 쓰고 소비자는 반대쪽 끝(읽기 끝)에서 읽는다. 결과적으로 ordinary pipe는 단방향이므로 단방향 통신 만 허용된다. 양방향 통신이 필요한 경우 두 파이프를 사용해야하며 각 파이프는 다른 방향으로 데이터를 보낸다. ordinary pipe는 UNIX 시스템과 Windows 시스템 모두에서 통신 process간에 parent-child 관계가 필요하다. 즉, 이 파이프는 동일한 시스템의 process 간 통신에만 사용할 수 있다.

![Ordinary Pipes](http://cfile25.uf.tistory.com/image/2247644B51E4295B1A4045)

### Named Pipes

named pipe는 훨씬 강력한 통신 도구다. 통신은 양방향 일 수 있으며 parent-child 관계는 필요하지 않다. named pipe가 설정되면 여러 process가 통신을 위해 이 process를 사용할 수 있다. 통신 process가 완료된 후에도 named pipe가 계속 존재한다. UNIX 및 Windows 시스템은 모두 named pipe를 지원하지만 구현 세부 사항은 크게 다르다. 

![Named Pipes](https://wyday.com/blog/images/2010/multi-process-c-sharp-app/mario-pipes-wyupdate.jpg)


참고자료 
* [tutorialspoint - OS](https://www.tutorialspoint.com/operating_system/)
* [빛나유님 블로그](http://operatingsystems.tistory.com/category/Operating%20Systems)
* [IPC 참고자료](http://talkingaboutme.tistory.com/551)
* [Stub 참고자료](http://vascocenter.tistory.com/entry/%EB%B6%84%EC%82%B0%EA%B0%9C%EC%B2%B4-%EC%95%A0%ED%94%8C%EB%A6%AC%EC%BC%80%EC%9D%B4%EC%85%98%EC%97%90%EC%84%9C%EC%9D%98-%EC%8A%A4%ED%85%81stub-%EC%8A%A4%EC%BC%88%EB%A0%88%ED%86%A4Skeleton)
