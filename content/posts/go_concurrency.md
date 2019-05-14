+++
title = "Go 동시성 프로그래밍 하기"
author = ["Hillfolk"]
date = 2019-05-03
lastmod = 2019-05-14T21:23:03+09:00
tags = ["go", "concurrency", "channel", "goroutine"]
categories = ["posts"]
weight = 100
draft = true
+++

## Goroutine {#goroutine}

-   고루틴은 함수를 동시에 실행시키는 기능이다.
-   다른 언어의 쓰레드보다 운영체제의 리소스를 적게 사용한다.


## 클로저 {#클로저}

-   클로저를 고루틴으로 실행할 때 반복문에 의해 바뀌는 변수는 반드시 매개변수로 넘겨 준다.


## 멀티코어 활용 {#멀티코어-활용}


## 체널 사용 {#체널-사용}

-   채널은 고루틴끼리 정보를 교환하고 실행의 흐름을 동기화하기 위해 사용한다.
-   채널 생성시 보내기 전용 이나 받기 전용으로 생성 가능한다.

```nil

package main

import "fmt"

func sum(a int, b int, c  chan int) {
  c <- a + b
}

func main() {
    c := make(chan int)

    go sum(1 ,2 ,c)
    n := <-c
    fmt.Println(n)
}

```


## range close 사용하기 {#range-close-사용하기}

-   close 는 채널을 닫을 수 있으며 이미 닫은 채널을 닫게되면 패닉 발생
-   채널을 닫으면 range 루프가 종료 됩니다.
-   채널이 열려 있고 값이 들어 오지 않으면 range 는 실행되지 않고 계속 대기 한다.

```nil

package main

import "fmt"

func main()

    c:= make(chan int)
    go func() {
    for i := 0; i >5 i++{
    c <- i // 채널에 값을 보냄
    }
    close(c)
}()

   for i:= range c {
    fmt.Println(i)
    }
}

```


## Select 사용하기 {#select-사용하기}

-   Go 언어는 여러 채널을 손쉽게 사용할 수 있도록 select 분기문을 지원
-   select문은 지정한 채널의 데이터가 있을 경우 case 수행한다.
-   select문에서는 지정한 체널에 데이터를 보내는 case는 항상 수행 된다.

```nil

for{
  case c1 <- 10: // 매번 체널 c1 에 값을 보냄
  case s:= <-c2: // c2 값이 들어 왔을때는 값을 꺼낸뒤 s에 대입
   }

```


## 동기화 객체 {#동기화-객체}

-   Mutex: 뮤텍스 ,상호 배제, 공유 데이터 보호에 주로 사용
-   RWMutex: 읽기/쓰기 뮤텍스 입니다. 일기와 쓰기 동작을 나누어서 잠금을 걸수 있습니다.
-   Cond: 조건 변수 (condition variable) 입니다. 대기하고 있는 하나이상의 객체를 깨울수 있다.
-   Once: 특정함수를 딱 한번만 실행할때 사용합니다.
-   Pool: 멀티 쓰레드 (고루틴)에서 사용할 수 있는 객체풀 입니다. 자주 사용하는 객체를 풀에 보관하여 사용가능 하다.
-   WaitGroup:고루틴이 모두 끝날 때까지 기다리는 기능
-   Atomic: 원자적 연산이라고도 하며 더 이상 쪼갤 수 없는 연산 의미 고루틴에서 안전하게 값을 연산하는 기능 입니다.