+++
title = "Lazarus Raspberry Pi LED Test"
author = ["Hillfolk"]
date = 2019-02-26
lastmod = 2019-02-26T16:38:50+09:00
tags = ["delphi", "pascal"]
categories = ["posts"]
weight = 100
draft = true
+++

## Lazarus Raspberry Pi LED Test {#lazarus-raspberry-pi-led-test}

라자루스에서 GPIO를 사용해보았다. 우선 지정된 핀 코드만 잘 맞으면 아주 쉽습니다. 저의 경우는 라즈베리파이에서 GPIO를 처음 써본거라 많이 삽질 했습니다.
우선 라즈베리파이에서 Lazarus 를 설치하고 LED를 켜는 부분까지 설명해 보도록 하겠습니다.


### 라자루스 설치 {#라자루스-설치}

우선 라자루스를 설치하는 법은 간단합니다.  라즈베리 파이에서도 팩키지 설치가 쉽기 때문에 apt-get 을 사용해서 쉽게 설치 할수 있습니다. 하지만 시간이 조금 걸리는 편입니다.
설치 방법은 [Lazarus wiki] 를 참조하면 아주 쉽습니다.

```nil
sudo apt-get update
sudo apt-get upgrade
sudo apt-get install fpc
sudo apt-get install lazarus
```


#### Source Code {#source-code}

핀 지정

```nil
const
   PIN_24:PChar = '24';
   PIN_ON:PChar = '0';
   PIN_OFF:PChar = '1';
   OUT_DIRECTION:PChar = 'out';
```

LED 를 연결할 핀을 지정해줍니다. 저의 경우는 24번으로 지정 했습니다. 0을 입력하면 LED가 켜지고  1이 입력되면 꺼집니다. 일반적으로 반대라고 생각했는데 해보니까 아니라서 약간 햇갈렸습니다. 주의 하세요. ^^

```nil

 try
 // 사용할 핀 지정
fileDesc := FpOpen('/sys/class/gpio/export',O_WRONLY);
gReturnCode := FpWrite(fileDesc,PIN_24[0],2);
Log(IntToStr(gReturnCode));
finally
  gReturnCode:=FpClose(fileDesc);
  Log(IntToStr(gReturnCode));
end;

try
//사용할 핀에 용도 지정
fileDesc := FpOpen('/sys/class/gpio/gpio24/direction',O_WRONLY);
gReturnCode := FpWrite(fileDesc,OUT_DIRECTION[0],3);
Log(IntToStr(gReturnCode));
finally
  gReturnCode:=FpClose(fileDesc);
  Log(IntToStr(gReturnCode));
end
```

위 코드는 사용할 GPIO 를 지정하고 해당 핀을 어떤 용도로 사용할지 지정합니다. 우리는 LED를 켜는 행동을 하기 때문에 out 으로 지정합니다.  전체 코드는 Github를 참고해 주세요.
[Github](https://github.com/hillfolk/lazarus_led_test)


### GPIO 연결 {#gpio-연결}

GPIO 는 라즈베리파이와 외부 장치들을 연결해주는 인터페이스 장치입니다. 주로 ON OFF 명령을 통해서 제어 하고 센서의 데이터를 받거나 외부 인터페이스의 데이터를 송수신 할때도 사용합니다.

저는 24번 핀을 사용했기 때문에 아래 6번핀을 24번 핀에 연결 했다고 생각하시면 됩니다.
코드 상에는 24번으로 되어 있습니다.
1R은 1옴의 저항을 나타내고 LED를 연결하실대는 + 와 - 에 주의해서 연결 하셔야 합니다. 거꾸로 연결할경우  LED에 불이 들어오지 않으니 주의 해주세요.

<a id="orgee098ff"></a>
{{<figure src="https://www.raspberrypi.org/documentation/usage/gpio-plus-and-raspi2/images/physical-pin-numbers.png">}}

<a id="org6d9ce38"></a>
{{<figure src="http://wiki.freepascal.org/images/0/00/rpi_testcircuit_1d.png">}}

모든 연결을 완료하고 프로그램을 구동 시키면 아래 동영상과 같이 작동하게 됩니다.

<a id="org97b4a65"></a>
<https://youtu.be/6PjXEOo6I9k>

[Lazarus wiki](http://wiki.freepascal.org/Lazarus_on_Raspberry_Pi)
[Github](https://github.com/hillfolk/lazarus_led_test)