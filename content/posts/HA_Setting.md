+++
title = "서버 이중화 시스템 구축 설명 (High Ailability System)"
author = ["Hillfolk"]
date = 2019-01-20
lastmod = 2019-02-26T00:23:30+09:00
tags = ["HA", "Linux", "High_ailability"]
categories = ["posts"]
weight = 100
draft = true
+++

## 환경구성 {#환경구성}

1.  ubuntu 14.04
2.  heartbeat


## 설치 순서 {#설치-순서}


### Heartbeat 설치 {#heartbeat-설치}

host1 과 host2 에 heartbeat를 설치한다.

```nil
$ sudo apt-get install heartbeat
```


### 설정파일 복사 {#설정파일-복사}

설치가 되었으면 _etc/ha.d_ 디렉토리 아래에 설정파일을 추가해야 한다. _usr/share/doc/heartbeat_ 디렉토리에 기본 설정파일이 준비되어 있다.

```nil
# Heartbeat 기본 설정파일 복사하기
$ sudo cp /usr/share/doc/heartbeat/authkeys /etc/ha.d/
$ sudo cp /usr/share/doc/heartbeat/ha.cf.gz /etc/ha.d/
$ sudo cp /usr/share/doc/heartbeat/haresources.gz /etc/ha.d/
$ sudo gunzip /etc/ha.d/ha.cf.gz
$ sudo gunzip /etc/ha.d/haresources.gz
```


### authkeys 권한 변경 {#authkeys-권한-변경}

authkeys 파일의 권한은 반드시 600 이어야 오류가 발생하지 않는다.

```nil
# authkeys 권한 변경
$ sudo chmod 600 /etc/ha.d/authkeys
```


### authkeys 파일 편집 {#authkeys-파일-편집}

authkeys 파일에서 주석을 제거한다.

```nil

$ sudo vi /etc/ha.d/authkeys

auth 1
1 crc
```


### ha.cf 파일 편집 {#ha-dot-cf-파일-편집}

ha.cf 파일에서 아래 설정들의 주석을 제거한다.

```nil

$ sudo vi /etc/ha.d/ha.cf
```

```nil
debugfile /var/log/ha-debug
logfile /var/log/ha-log
logfacility local0
keepalive 2
deadtime 30
initdead 120
udpport 694
bcast eth0
auto_failback on
node host1
node host2
```


### hosts 파일수정 {#hosts-파일수정}

host1, host2 서로가 IP를 알 수 있도록 hosts 파일에 추가한다.

```nil
192.168.1.99 host1
192.168.1.101 host2
```


### haresources 파일 편집 {#haresources-파일-편집}

우리의 목적은 host1 서버에 장애가 발생시 host2가 host1 가상 IP를 이어 받아서 동작하게 만드는 것이다. 아래와 같이 haresources 파일을 편집한다.
host1, host2 둘다 똑같이 host1으로 적는다.

```nil
$ sudo vi /etc/ha.d/haresources
```

```nil
host1 192.168.1.101
```


### Heartbeat 실행 {#heartbeat-실행}

```nil

$ sudo service heartbeat start
Starting High-Availability services: INFO:  Resource is stopped
Done
```


### Heartbeat 동작여부 확인하기 {#heartbeat-동작여부-확인하기}

host1, host2 서버 각각 ifconfig를 입력한다. host1에는 eth0:0 장치가 있지만 host2에는 없다.
이때 host1을 종료시킨 후 host2에서 ifconfig를 입력하면 host2에 eth0:0이 생성되어 있을 것이다.

```nil
$ ifconfig

eth0:0    Link encap:Ethernet  HWaddr 08:00:27:d6:41:62
	  inet addr:192.168.56.10  Bcast:192.168.56.255  Mask:255.255.255.0
	  UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
```