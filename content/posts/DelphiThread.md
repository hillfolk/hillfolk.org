+++
title = "언제나 어려운 이름 Thread(Multithread Programming)"
author = ["Hillfolk"]
date = 2019-02-04
lastmod = 2019-02-23T21:54:13+09:00
tags = ["Delphi", "Thread"]
categories = ["posts"]
weight = 100
draft = true
+++

## Delphi Thread Object {#delphi-thread-object}

델파이 쓰레드 객체는 TThread 객체를 상속 한다. 필요한 실행 코드는  Excute 를 오버라이드하여 구현하며 생성후 Resume 하여 작업을 시작한다.

```nil

 type
  TExThread = class(TThread)
  protected
    procedure Execute; override;
  end;


{ TExThread }

procedure TExThread.Execute;
begin
  inherited;

  while not Terminated do
  begin
    Synchronize(
      procedure()
      begin
	frmHillThread.mConsole.lines.Add('Time:' + TimeToStr(Now));
      end);
    Sleep(1000);
  end;
end;

```


### Thread 기본 사용법 {#thread-기본-사용법}


#### Thread priority {#thread-priority}

tpIdle -> tpLowest -> tpLower -> tpNormal -> tpHigher -> tpHighest -> tpTimpCritical

-   Thread Execute

```nil
 while not Terminated do // 쓰레드가 종료 되면 작업을 수행 하지 않도록 하는 조건
begin
  // 실제 작업이 수행 되는 공간
end;
```


#### Thread Terminate {#thread-terminate}

쓰레드는 생성하는것보다 해제 하는것이 더욱 번거롭다. 쓰레드가 작업중인 경우 쓰레드가 중지 되지 않는 경우가 많고 적절하게 중지하는것이 가장 중요하다.
FreeOnTerminate 옵션은 쓰레드가 해재 되면 자동으로 메모리를 해재 하지만 보통 자동으로 해재하게 되면 문제가 발생하는 경우가 많기 때문에 수동으로 해재해 주는 것이 많습니다.

```nil
HillThread.Terminate; // 쓰레드를 종료하는 명령
HillThread.WaitFor(100); // 쓰레드가 작업을 마치고 종료할때 까지 100ms 대기
HillThread.Free; //쓰레드 메모리 해제 (FreeOnTerminate 옵션을 True 할경우 자동으로 해제)
```


#### Critical Sections {#critical-sections}

Critical Section 은 동시에 2개의 쓰레드가 접근하는 것을 막아줍니다. Acquire 함수는 다른 스레드가 섹션을 실행하지 못하도록 합니다. Release  하지 전까지는 다른 쓰레드에서 접근하지 못합니다. 멀티 쓰레드 프로그래밍에서는 여러개의 쓰레드가 동시에 전역 변수에 접근하는 경우 문제가 발생합니다. Critical Section 을 이용하면 해당 전역 메모리에 접근하는 것을 막아줍니다.

```nil

uses System.SyncObjs.TCriticalSection

LockXY = TCriticalSection.Create;

LockXY.Acquire; { lock out other threads }
try
  Y := sin(X);
finally
  LockXY.Release;
end;
```


#### Synchronize 와 Lock {#synchronize-와-lock}

Synchronize는 주로 쓰레드 안에서 데이터를 디스플레이 할때 사용된다.
Synchronize() 는 현재 쓰레드를 중지 시키고 주 쓰레드를 깨운다.
주 쓰레드에서 동기화 코드를 수행할 경우 무한 루프가 발생할 수 있다.

```nil

 Synchronize(
  procedure
  begin
    Form1.Memo1.Lines.Add(‘Begin Execution’);
  end);

procedure TSortThread.DoVisualSwap;
begin
  with FBox do
  begin
    Canvas.Pen.Color := clBtnFace;
    PaintLine(Canvas, FI, FA);
    PaintLine(Canvas, FJ, FB);
    Canvas.Pen.Color := clRed;
    PaintLine(Canvas, FI, FB);
    PaintLine(Canvas, FJ, FA);
  end;
end;


 Synchronize(DoVisualSwap); // procedure 나 function 를 수행할수 있다.

```


#### Sleep {#sleep}

-   지정된 시간 동안 스레드의 실행을 일시 중단하고 컨트롤을 주 쓰레도에 반환합니다. 오랬동안 처리되는 작업에는 일정 주기의 Sleep을 넣어 줘야 사용자 인터페이스의 프리징 현상을 줄일수 있습니다.


#### Thread Safe Queue {#thread-safe-queue}

TThreadedQueue 객체는 비동기적으로 접근 가능한 Queue로서 복수의 쓰레드에서 접근 가능한 큐 데이터 구조이다.
해당 객체는 여러개의 쓰레드에서 안전하게 접근이 가능하기 때문에  consumer/producer 구조를 안전하게 구현할 수 있다.
TThreadQueue 객체 생성시에는 아래 속성을 지정한다.

QueueDepth  : 큐의 최대 사이즈이다.  데이터의 양이 QueueDepth 에 도달할경우 PushTimeout이 발생한다.

PushTimeout: 밀리센컨드 단위로 지정한 시간동안 데이터를 넣지 못할경우 실패로 판별한다.

PopTimeout: 밀리세컨드 단위로 지정한 시간동안 데이터를 꺼내오지 못할경우 실패로 판별한다.

```nil

LQueueDepth = 100;
LPushTimeout = 1000;
LPopTimeout = 1;

FQueue = TThreadQueue.Create(QueueDepth ,LPushTimeout,LPopTimeout); //생성

FQueue.PushItem(Random(256)); //데이터 넣기

while FQueue.PopItem(Value) = TWaitResult.wrSignaled do //데이터 가져오기
begin
{데이터 처리 }
end;

```


### 참고 {#참고}

생산자/소비자 패턴은 한개의 생산자가 생산한 데이터를 큐에 지속적으로 넣어주고 복수의 소비자가 각각 데이터를 큐에서 가져가는 디자인 패턴을 이야기 합니다.


### Event 에 대한 설명 <번역> {#event-에-대한-설명-번역}

Thread 에서 이벤트 객체에 대한 설명입니다.
TEvent 는 외부 이벤트를 나타냅니다. TEvent 를 사용 하여 이벤트가 발생했거나 상태에 도달 했음을 알립니다 .
다중 스레드 응용 프로그램에서 한 스레드가 이벤트가 발생한 다른 스레드에 신호를 보낼 수 있도록 하려면 TEvent 를 사용합니다.
TEvent 객체 의 핸들은 또한 다른 프로세스와 통신하는 데 사용될 수 있으므로 응용 프로그램이 다른 응용 프로그램과 이벤트의 타이밍을 조정할 수 있습니다.
예를 들어, 다른 프로세스가 정보를 전송할 준비가 될 때까지 대기하려면 TEvent 객체 의 핸들을 사용 하십시오.
단일 스레드 응용 프로그램에서는 TEvent 를 사용 하여 시스템 이벤트와 같은 비동기 이벤트에 응답하는 코드 섹션을 조정합니다.

[원문 참고](<http://docwiki.embarcadero.com/Libraries/Tokyo/en/System.SyncObjs.TEvent>)

TEvent 객체를 인스턴스화하여 이벤트 객체를 나타냅니다. Create를 호출하여 TEvent 개체를 만듭니다.
Create는 새 이벤트 객체를 생성하거나 기존의 명명 된 이벤트 객체에 대한 액세스를 제공 할 수 있습니다.
첫 번째 오버로드 된 생성자는 다음 매개 변수를 설정합니다.

EventAttributes 매개 변수를 설정하여 새 이벤트 객체의 액세스 권한 및 상속 기능을 지정합니다.
EventAttributes를 nil (Delphi) 또는 NULL (C ++)로 설정하여 Create를 호출하면 기본 보안 속성 세트가있는 이벤트 객체가 만들어집니다.

TEvent 개체가 기존 명명 된 이벤트 개체에 액세스하기 위해 만들어지면 이벤트 개체를 만들 때 액세스 권한이 결정되므로 EventAttributes의 bInheritHandle 필드 만 사용됩니다.
ResetEvent 메서드를 호출하여 TEvent 개체의 신호를 끌 수 있는지 여부 또는 대기중인 단일 스레드가 해제 될 때 자동으로 재설정되는지 여부를 지정하려면 ManualReset을 설정합니다.
ManualReset이 true이면 ResetEvent 메서드가 해제 할 때까지 TEvent 신호가 설정 상태를 유지합니다.
ManualReset이 false이면 단일 스레드 만 신호를 기다리고 해당 스레드가 해제 될 때 신호가 자동으로 꺼집니다.
기존의 명명 된 이벤트 객체에 액세스하기 위해 TEvent 객체가 만들어진 경우 ManualReset 매개 변수는 무시됩니다.
매개 변수가 설정된 상태에서 TEvent 객체를 만들어야하는지 또는 꺼져 있는지를 나타내도록 InitialState를 설정합니다.
InitialState가 true이면 신호 세트가있는 TEvent 객체가 만들어집니다. 기존의 명명 된 이벤트 개체에 액세스하기 위해 TEvent 개체가 만들어지면 InitialState 매개 변수가 무시됩니다.
이름을 설정하여 새 이벤트 객체의 이름을 제공하거나 기존의 명명 된 이벤트 객체를 지정합니다.

다른 스레드 나 프로세스가 신호를 기다리기 위해 이벤트 객체에 액세스해야하는 경우 Name을 비워 둘 수 있습니다. 이름은 백 슬래시 문자 (\\)를 포함하지 않고 최대 260 자의 문자열 일 수 있습니다.
Name을 사용하여 기존 이벤트 오브젝트를 지정하는 경우 값은 대소 문자를 구분하여 비교할 때 기존 이벤트의 이름과 일치해야합니다.
이름이 기존의 세마포어, 뮤텍스 또는 파일 매핑 개체의 이름과 일치하면 Handle을 0으로 설정하여 TEvent 개체가 만들어지고 모든 메서드 호출이 실패합니다.
UseCOMWait을 True로 설정하면 스레드가 차단되어 개체를 기다릴 때 STA COM 호출을이 스레드로 되돌릴 수 있습니다.
두 번째 오버로드 된 생성자는 UseCOMWait 매개 변수 만 받아들입니다. 이 생성자는 널 이벤트 속성, 수동 재설정, 초기 신호 없음 및 빈 이름으로 이벤트를 작성합니다.

[원문 참고](<http://docwiki.embarcadero.com/RADStudio/Tokyo/en/Waiting_for_a_Task_to_Be_Completed>)


### Wiat For에 대한 구글 번역 <구글 번역> {#wiat-for에-대한-구글-번역-구글-번역}

때로는 특정 스레드가 실행을 완료하기를 기다리지 않고 스레드가 어떤 작업을 마칠 때까지 기다려야 할 때가 있습니다. 이렇게하려면 이벤트 개체를 사용하십시오. 이벤트 개체 ( System.SyncObjs.TEvent )는 모든 스레드에서 볼 수있는 신호처럼 작동 할 수 있도록 전역 범위로 만들어야합니다.
스레드가 다른 스레드가 의존하는 작업을 완료하면 TEvent.SetEvent를 호출 합니다 . SetEvent 는 신호를 켜기 때문에 검사를 수행하는 다른 스레드는 작업이 완료되었음을 알 수 있습니다.
신호를 끄려면 ResetEvent 메서드를 사용합니다. 예를 들어, 하나의 스레드가 아니라 여러 스레드가 실행을 완료 할 때까지 기다려야하는 상황을 생각해보십시오. 마지막으로 끝나는 스레드를 알지 못하기 때문에 스레드 중 하나의 WaitFor 메서드를 사용할 수 없습니다 .
대신 각 스레드가 완료 될 때 카운터를 증가시키고 이벤트를 설정하여 마지막 스레드 신호를 모두 보냈 음을 알릴 수 있습니다.
다음 코드는 완료해야하는 모든 스레드에 대한 System.Classes.TThread.OnTerminate 이벤트 처리기 의 끝을 보여줍니다 . CounterGuard 는 다중 스레드가 동시에 카운터를 사용하지 못하게하는 전역 적으로 중요한 섹션 개체입니다. 카운터 는 완료된 스레드 수를 계산하는 전역 변수입니다.

```nil

Event1.ResetEvent; { clear the event before launching the threads } //신호를 클리어 합니다.
for i := 1 to Counter do
begin
TaskThread.Create(False); { create and launch task threads }
if Event1.WaitFor(20000) <> wrSignaled then //20000 밀리세컨드 동안 대기 합니다.  0을 사용할경우 대기 없이 검사후 진행 합니다.
raise Exception;
{ now continue with the main thread. All task threads have finished }
end;

```


### 신호의 종류 {#신호의-종류}

-   wrSignaled

이벤트의 신호가 설정되었습니다.

-   wrTimeout

신호가 설정되지 않고 지정된 시간이 경과했습니다.

-   wrAbandoned

시간 제한 기간이 경과하기 전에 이벤트 개체가 파괴되었습니다.

-   wrError

대기 중 오류가 발생했습니다.

[델파이의 TEvent 객체 - 이벤트 시그널링](<https://m.blog.naver.com/PostView.nhn?blogId=futurewave01&logNo=220434272326&proxyReferer=https%3A%2F%2Fwww.google.co.kr%2F>)