---
layout: post
title: Semaphore
description: >
    [ Semaphore에 대해 알아보자!]
excerpt_separator: <!--more-->

---

<!--more-->

# Semaphore

### Semaphore S

- lock을 걸고 푸는 과정을 추상화시킴!!!!!!!!!
- 정수값(S)을 가진다.(자원의 갯수)
- 아래 두 가지 과정이 atomic하게 이루어진다.

```c
P(S):
while(S<=0) do no-op;
S--;
```

```c
V(S):
S++;
```

- **P(S)**
  - S가 0보다 작을 때에는 while문을 돌면서 기다린다.(busy-wait발생)
  - 기다리다가 누군가가 자원을 돌려놓아 S가 양수가 되면 S--를 통해 자원을 획득한다.
- **V(S)**
  - 연산을 통해 자원을 다 쓰고 되돌려놓는다.

<br>

### Block & Wakeup(sleep lock)

**Semaphore를** 다음과 같이 정의

```c
typedef struct
{
    int value;			/*실제 세마포어 변수 값*/
    struct process *L;	/*잠들어 있는 프로세스들을 담기 위한 큐*/
}semaphore;
```

- **block**
  - 만약 지금 `semaphore`를 획득할 수 없으면 그 프로세스는 block된다.
  - 커널은 block을 호출한 프로세스를 `suspend`시키고 이 프로세스의 PCB를 semaphore에 대한 `wait queue`에 넣는다.

- **wakeup**
  - 누군가가 `semaphore`를 쓰고 나서 반납하게 되면 block된 프로세스중 하나를 깨워서 wakeup시킨다.
  - wakeup된 프로세스의 PCB를 `ready queue`로 옮긴다.

```c
P(S):
S.value--;
if(S.value < 0)
{
    add this process to S.L;
    block();
}
```

```c
V(S):
S.value++;
if(S.value <= 0)
{
    remove a process P from S.L;
    wakeup(P);
}
```

- **P(S)**

  - 자원의 여분이 있다면 자원을 바로 획득하고, 없다면 block상태로 들어가게 된다.
  - semaphore의 변수값을 먼저 1을 빼준 다음 0보다 작다면 자원의 여분이 없는 상태이기 때문에 semaphore의 queue에 연결시키고 block한다.

- **V(S)**

  - 자원을 다 쓰고나서 반납하고 끝나는게 아니라, 혹시 이 자원을 기다리면서 잠들어 있는 프로세스가 있는지 확인하여 깨워준다.
  - 자원을 다 쓰고나면 semaphore의 변수를 1을 늘려주었는데도 불구하고 그 값이 0 이하라면 누군가가 기다리면서 잠들어있다는 뜻이다. 그래서 queue에서 기다리는 프로세스  하나를 깨워준다.

  

>  busy-wait방식에서의 semaphore의 value는 자원의 갯수를 의미하지만, block&wakeup방식에서의 semaphore의 value는 현재 자원의 여분에 대한 상황을 의미하므로 조금 다르다는 것을 이해해야한다

<br>

### busy-wait vs block/wakeup

- 보통은 CPU점유에 대해 생각했을 때 계속 기다리는 `busy-wait`방식보다는 `block/wakeup`방식이 더 의미있게 이용하는 방식이다.
- 하지만 `block/wakeup`에도 overhead가 발생한다..block에서 wakeup상태로, block에서 ready상태로 바꾸어주는 작업이 필요하기 때문에 ...
- Critical section의 길이가 매우 짧은 경우에는` busy-wait`방식을 사용해도 크게 문제가 되지 않는다.
- Critical section의 길이가 긴경우에는 CPU낭비를 막기 위해서 `block/wakeup`방식이 적당하다.

<br>

### semaphores의 종류

- **counting semaphore**
  - semaphore변수값이 1보다 크게 주어질 수 있는 경우
  - 자원의 갯수가 여러개 있어서 여분이 있으면 가져다가 쓸 수 있는 경우
  - 주로 여분의 자원을 세는데 사용(resource counting)
- **binary semaphore(=mutex)**
  - 0또는 1의 값만 가질 수 있는 semaphore
  - 자원의 갯수가 하나인경우(주로 lock을 걸때)
  - 주로 `mutual exclusion`에서 사용한다.

<br>

### semaphore에서 발생할 수 있는 문제

- **Deadlock**
  - 둘 이상의 프로세스가 서로 상대방에 의해 충족될 수 있는 event를 무한히 기다리는 현상
  - 다음장에서 구체적으로 알아보자!!!!
- **Starvation**
  - **indefinite blocking**
  - 프로세스가 suspend된 이유에 해당하는 세마포어 큐에서 빠져나갈 수 없는 현상