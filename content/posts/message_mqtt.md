+++
title = "MQTT Protocol"
author = ["Hillfolk"]
date = 2019-02-25
lastmod = 2019-02-26T17:01:04+09:00
tags = ["Delphi", "Thread"]
categories = ["posts"]
weight = 100
draft = true
+++

## 개념 {#개념}

-   **Message System 개념**
    애플리케이션은 중개자를 통해 목적지로 메시지를 공급한다.
    애플리케이션은 메시지를 소비하기 위해 같은 목적지를 구독한다.

-   **Message**
    개체 간의 교환 하는 데이터

-   Destination
    메시지를 교환 하는데 사용되는 주소

-   Producer
    메시지 공급 개체

-   Consumer
    메시지의 목적지로 부터 소비하는 개체

-   Bloker
    공급자로 부터 메지시를 받아서 소비자 들에게 메시지를 전할하는 개체
    메시지는 참여자들 간의 결합도가 낮다는 것이다. 구성이 유연하다.


## 구독과 발행 {#구독과-발행}

-   메시지 구독 모델
    Point-to-Point : 공급개체가 보낸 메시지가 1명의 소비자개체에게 전달
    Publish/Subscribe : 공급개체가 보낸 메시지를 관심있는 모든 소비자에게 전달

-   Message QoS
    At Most Once(0): 최대 한번 전달: 속도가 빠름 메시지 유실 가능
    At Least Once(1): 최소 한번 전달: Bloker가 게시된 메시지를 소비자에게 최소 한번 전달, 메시지 중복 가능,네트워크 부하 발생 2\*N
    At Exactly Once(2): 정확한 한번 전달: Bloker가 게시된 메시지를 소비자에게 정확하게 한번 전달을 보장, 메시지 유실이나 중복을 발생하지 않음, 속도가 느림 4\*N

-   Message 수신

    -   Message Subscribe : 메시지 구독

    관심 토픽을 구독한다.
    QoS를 지정한다.

    -   Message UnSubscribe: 메시지 구독 해제

    관심 토픽을 해재 한다.


## 구조 {#구조}

-   Message 구조
    -   mid: unsigned short 형 데이터며 메시지의 ID를 가리킨다.
    -   topic: 메시지를 보내는 토픽의 이름, 구독 단위 , 여러개를 구독할수 있음
    -   retained: 메시지를 보관할 것인지 말것인지 결정
    -   payload: 메시지 페이로드는 바이너리 데이터를 담고 있다.

-   Message 수신
    -   **토픽 와일드 카드**

```nil
/ 토픽 레벨 구분자: 토픽 트리 내 레벨을 구분하는 데 사용 되며 토픽 공간에 계층적인 구조를 제공한다.
## 다중 레벨 와일드 카드: 토팩 내 여러 레벨을 일치 시키는 와일드 카드
+ 단일 레벨 와일드 카드: 단일 토픽 레벨만 일치 시키는 와일드 카드
```

-   **와일드 카드 사용**

```nil
/group/dev/tag
/group/+/tag :모든 dev 가보낸 Tag 메시지에 관심 있을때
/group/# : 모든 Group 메시지에 관심 있을때
```

-   **payLoad 객체**

```nil
payloadBytes: ArrayBuffer 형태 데이터
payloadString: String 형태 데이터
```