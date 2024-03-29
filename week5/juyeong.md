# 12. 멀티 스레드

1. 동시성 관리
2. 그리고 코세라 강의
   : 둘 중 하나 정리해서 첨부하기

## 1.멀티스레드 개념
- 하나의 프로세스가 두 가지 이상의 작업을 처리할 수 있게 하는 것
- 스레드는 하나의 코드 흐름
- 멀티프로세스 != 멀티스레드
  - 멀티프로세스 : 애플리케이션 단위의 멀티태스킹
  - 멀티스레드 : 애플리케이션 내부에서의 멀티태스킹
- 멀티프로세스들은 운영체제에서 할당받은 자신의 메모리를 가지고 실행하기 때문에 서로 독립적이다. 즉, 하나의 프로세스에서 오류가 발생해도 다른 프로세스에게 영향을 미치지 않는다. 
- 반면, 멀티 스레드로 동작하는 메신저가 있다고 가정하자. 메신저의 경우 파일을 전송하는 스레드에서 예외가 발생하면 메신저 프로세스 자체가 종료되기 때문에 **예외 처리를 신중하게** 해야한다.
- 메인스레드
  - 메인스레드는 필요에 따라 작업 스레드들을 만든다. 즉, 멀테 스레드를 생성한다.
  - main() : 메인스레드 실행
  - 멀티스레드 어플리케이션에서, 실행중인 스레드가 하나라도 있다면 프로세스는 종료되지 않는다.
## 2.작업 스레드 생성과 실행
- 멀티 스레드로 실행하는 애플리케이션을 실행하려면, 
  - 1. 몇개의 작업을 병렬로 실행할지 결정
  - 2. 작업별로 스레드 생성
    - 메인 작업 이외에 추가적인 병렬 작업 수만큼 스레드 생성
    - 자바에서는 작업 스레드도 객체로 생성되기 때문에 클래스가 필요하다.
      - 1) java.lang.Thread 클래스를 객체화해서 생성
        - Runnable을 매개값으로 갖는 생성자 호출
        - Runnable : 작업 스레드가 실행할 수 있는 코드를 가지고 있는 객체, 인터페이스 타입. -> 구현 객체를 만들어 대입해야 한다.
        - Runnable에는 run() 메소드 하나가 정의되어 있는데, 구현 클래스는 run()을 재정의해서 작업 스레드가 실행할 코드를 작성한다.
        -   ```
               Thread thread = new Thread(new Runnable() {
                  public void run() {
                     //스레드가 실행할 코드
                  }
               }
            ```
        - Runnable 인터페이스는 run() 메소드 하나만 정의되어 있기 때문에 함수적 인터페이스이다. 따라서 람다식을 매개값으로 사용할 수도 있다.
      - 작업 스레드는 생성즉시 실행되는 것이 아니라, thread.start(); 메소드를 호출해야 실행된다.
        - start() 메소드가 호출되면, 작업 스레드는 매개값으로 받은 runnable의 run 메소드를 실행하여 작업을 처리한다. 
      - 2) Thread를 상속해서 하위 클래스 만들어 생성
   
- 작업 스레드가 실행할 작업을 Runnable로 만들지 않고, Thread의 하위 클래스로 작업 스레드를 정의하면서 작업 내용을 포함시킬 수도 있다. 이게 무슨 말이냐면, 
- Thread 클래스를 상속한 후 run 메소드를 재정의(overriding)해서 스레드가 실행할 코드를 작성하면 된다. 
1) ```
      public class Worker Thread extends Thread {
         @Override
         public void run() {
            // 스레드가 실행할 코드
         }
      }
   ```

2) ```
      public class Worker Thread extends Thread {
         @Override
         public void run() {
            // 스레드가 실행할 코드
         }
      }
   ```
- 스레드의 이름
  - 디버깅할 때 어떤 스레드가 어떤 작업을 하는지 조사할 목적으로 가끔 사용된다.
  - 메인 스레드는 "main" 이라는 이름을 가지고 있고, 우리가 직접 생성한 스레드는 자동적으로 "Thread-n" 이라는 이름으로 설정된다. n은 스레드의 번호를 말한다.
  - 만약 다른 이름으로 설정하고 싶으면 Thread.setName();을 사용하면 된다.

## 3.스레드 우선순위
- 멀티스레드는 동시성(Concurrency), 병렬성(Parallelism)으로 실행되기 때문에 이 용어들에 대해 정확히 이해하는 것이 필요하다.
- 동시성 : 하나의 코어에서 멀티 스레드가 번갈아가며 실행하는 성질
- 병렬성 : 멀티코어에서 개별 스레드를 동시에 실행하는 성질
- 스레드의 개수가 코어의 수보다 많을 경우, 스레드를 어떤 순서에 의해 동시성으로 실행할 것인가를 결정해야하는데, 이것을 **스레드 스케쥴링** 이라고 한다.
- 스케줄링에 의해 스레드들은 아주 짧은 시간에 번갈아가면서 그들의 run() 메소드를 조금씩 실행한다.
- 자바의 스레드 스케쥴링
  - 1) 우선순위 방식(Priority)
    - 우선순위가 높은 스레드가 실행상태를 더 많이 가지도록 스케줄링
    - 스레드 객체에 우선 순위 번호를 부여할 수 있어서, 개발자가 코드로 제어 가능
      - 1~10까지 부여되는데, 1이 가장 낮음
      - 우선순위 변경하고 싶으면 thread.setPriority();
      - 아니면 가독성을 위해 상수 사용해도 됨! thread.setPriority(Thread.MAX_PRIORITY)
      - 최소한 5개 이상의 스레드가 실행되어야 우선순위의 영향을 받는다.
  - 2) 순환 할당(Round Robin) 
    - 시간 할당량(Time Slice)을 정해서 하나의 스레드를 정해진 시간만큼 실행하고 다시 다른 스레드를 실행하는 방식
    - JVM에 의해서 정해지므로, 코드로 제어할 수 없다.


## 4.동기화 메소드와 동기화 블록
- 멀티스레드 프로그램에서 스레드들이 객체를 공유해서 작업해야 하는 경우가 있다. 이 때, 스레드 A를 사용하던 객체가 스레드 B에 의해 상태가 변경될 수 있기 때문에, 스레드 A가 의도했던 것과는 다른 결과가 나올 수 있다.
- 이를 방지하려면, 즉 스레드가 사용하는 객체를 다른 스레드가 변경할 수 없도록 하려면 **스레드 작업이 끝날 때까지 객체에 잠금**을 걸어서 다른 스레드가 사용할 수 없도록 해야한다.
- 멀티 스레드 환경에서 하나의 스레드만 실행할 수 있는 코드영역을 임계영역(Critical section)이라고 한다.
- 자바는 임계영역 지정을 위해 동기화(synchronized)메소드와 동기화 블록을 제공한다. 스레드가 객체 내부의 동기화 메소드 또는 블록에 들어가면, 즉시 객체에 잠금을 걸어 다른 스레드가 임계영역코드를 실행하지 못하도록 한다.
  - 만드는 방법은 , synchronized 키워드를 붙이면 된다. 싱크로나이즈드 키워드는 인스턴스와 정적 메소드 어디든 붙일 수 있다.
  - 스레드가 동기화 메소드를 실행 종료하면 잠금이 풀린다.
  - 메소드 전체가 아니라, 일부내용만 임계 영역으로 만들고 싶다면 다음과 같이 동기화 블록을 만들면 된다.
      ```
         public void method() {
            // 여러 스레드 실행 가능 영역
            ...
            synchronized(공유객체) {
               임계영역 // 단 하나의 스레드만 실행
            }

            // 여러 스레드 실행 가능 여역
         }

         ```
## 5.스레드 상태
- 스레드 객체를 생성하고, start() 메소드를 호출하면 곧바로 스레드가 실행되는 것처럼 보이지만 사실은 실행 대기 상태가 된다. 
  - **실행대기 상태** : 아직 스케줄링 되지 않아서 실행을 기다리고 있는 상태
    - 실행 대기 상태에 있는 스레드 중에서 스레드 스케줄링으로 선택된 스레드가 CPU를 점유하고 run() 메소드를 실행한다. (Running 상태)
    - 실행 대기 상태와 실행상태를 번갈아가면서 자신의 run() 메소드를 조금씩 실행한다.
  - **종료 상태** : run() 메소드가 종료. 
  - **일시정지 상태** : 스레드가 실행할 수 없는 상태. **WAITING, TIMED_WAITING, BLOCKED** 상태가 있다. "일시정지 -> 실행대기 -> 실행" 순으로 진행된다.
  - 이러한 스레드 상태는 자바의 .getState() 메소드를 통해 리턴된다.
    - 상수로 리턴됨. 위의 웨이팅, 타임웨이팅 .. 외에도 뉴,러너블 등이 있다.


## 6.스레드 싱태 제어
- 실행중인 스레드의 형태를 변경하는 것을 스레드 상태 제어라고 한다.
- 잘 사용하면 약이 되지만, 잘못 사용하면 치명적 버그가 되므로 사용하는 법을 잘 숙지해야한다.
- 제어를 제대로 하기 위해서, 상태 변화를 가져오는 메소드들을 알아야한다.

1. 주어진 시간동안 일시 정지 sleep()
   - 실행 중인 스레드를 일정 시간 멈추게 하고 싶다면 Thread 클래스의 정적 메소드인 sleep() 사용
   - 메소드를 호출한 스레드는 주어진 시간 동안 일시 정지 상태 -> 이후 다시 실행 대기 상태로 돌아감
2. 다른 스레드에게 실행 양보 yield()
   - 스레드가 처리하는 작업은 반복적인 실행을 위해 for/while을 포함하는 경우가 있는데, 이런 반복문이 무의미한 반복을 하는 경우가 있다. 계속 도는거지
   - yield() 메소드를 호출한 스레드는 실행 대기 상태로 돌아가고 동일한 우선순위 or 높은 우선순위를 갖는 스레드가 실행 기회를 가질 수 있도록 한다.
3. 다른 스레드의 종료를 기다림 join()
   - 다른 스레드가 종료될 때까지 기다렸다가 실행해야하는 경우가 발생한다면? 
   - 예를 들어 계산작업을 하는 스레드가 있으면, 계산결과를 받아 실행해야하는 스레드가 있을 것이다.
   - 이 경우 ThreadA가 ThreadB 의 join() 메소드를 호출하면 A는 B가 종료할 때까지 일시 정지 상태가 된다. 계산하는 애가 작업을 마칠 때까지 일시 정지 상태에 있다가 최종 계산된 결과값을 산출, 종료하면 값을 받아 출력한다. 
   - Thread.join();
4. 스레드의 안전한 종료 - stop플래그 / interrupt()
   - 스레드는 자신의 run() 메소드가 모두 실행되면 자동으로 종료되지만, 실행중인 스레드를 바로 종료시켜야할 때가 있다.
   - 예를들어 비디오 일시정지
   - Thread는 stop()을 통해 스레드를 즉시 종료시켰지만 비정상적인 종료로 스레드 자원이 불안정하게 남겨지면 deprecated 되었고, stop 플래그 or interrupt() 메소드를 권장한다.
     - 1. stop 플래그 - while(!stop)
     - 2. interrupt() - 예외를 발생시킨다. 이를 통해 run() 메소드를 정상적으로 종료시킨다. (InterruptedException)
       - 인터럽트 메소드가 실행되면 인터럽티드익셉션이 바로 발생하는 것이 아니라 스레드가 미래에 **일시정지 상태가 되어야 예외가 발생**한다. 
       - 일시정지 여부를 확인하려면 : isInterrupted()


## 7.데몬 스레드
- 주 스레드의 작업을 돕는 보조적인 역할을 수행하는 스레드
- 주 스레드가 종료되면 데몬 스레드는 강제로 종료 
- 데몬스레드와 주스레드 큰 차이 없음
- 주 스레드가 종료되면 같이 종료된다.
- 스레드를 데몬으로 만들고 싶다면 : setDemon(true) 호출
- 실행중인 스레드가 데몬스레드인지 확인 : isDaemon() 리턴값 확인 

## 8.스레드 그룹
- 스레드 그룹은 관련된 스레드를 묶어서 관리할 목적으로 이용됨.
- 스레드는 반드시 하나의 스레드 그룹에 포함되는데, 명시적으로 스레드 그룹에 안넣으면 기본적으로 자기를 만든 스레드와 같은 스레드에 속한다. 
- 보통 main 스레드가 다른 스레드 생성하므로 여기에 많이 속함

1. 스레드 그룹 이름 얻기
   - getThreadGroup();
   - getAllStackTraces(); : map 타입의 객체 리턴

2. 스레드 그룹 생성
   - 부모 스레드 안정하면 현재 스레드는 부모 스레드 하위로 들어감
   - ThreadGroup tg = new ThreadGroup(String name);

3. 스레드 그룹의 일괄 interrupt()
   - 스레드 그룹에 들어가면 뭐가 좋나요 -> 일괄 interrupt rksmdgka.
   - 10개 스레드를 종료시키기 위해 하나씩 인터럽트 거는 것보다 10개 한번에 거는 것이 편함


## 9.스레드풀
- 병렬 작업 처리가 많아지면 스레드 개수 증가 -> CPU : 스레드도 만들고 스케쥴링도 해야하고 메모리 사용도 많아짐
- 작업의 폭증으로 인한 스레드 폭증을 막으려면 스레드 풀을 사용해야 한다.
- 사용되는 **스레드를 제한된 개수만큼 정해놓고 작업큐에 들어오는 작업을 하나씩 맡아 처리한다.**
- 자바에서 스레드풀 사용을 위해 제공하는 것
  - 1. Executors 클래스
  - 2. ExecutorService 인터페이스 

[동작과정]
  1. 어플이 스레드풀에 작업처리 요청
  2. 스레드풀: 작업큐에 업무들 말아넣고 각 스레드는 큐에서 하나씩 가져와서 열심히 일함
      - 스레드1: 일할게
      - 스레드2: 일할게
      - 스레드3: 일할게..
  3. 작업 결과를 전달함

1. 스레드풀 생성 및 종료
   1. 생성 : newCachedThreadPool(), newFixedThreadPool()
   2. 종료 : shutdowu(), shutdownNow()


2. 작업 생성과 처리 요청
   1. 생성 : Runnable(리턴값 X), Callable(리턴값 O) 
   2. 처리 요청 : execute(), submit()


3. 블로킹 방식의 작업 완료 통보
   - submit() 메소드는 매개값으로 준 러너블, 콜러블 작업을 스레드풀의 작업 큐에 저장하고 즉시 **Future 객체를 리턴**한다.
   - 퓨처 객체: 작업이 완료될 때까지 기다렸다가 (지연 == 블로킹 되었다가) 최종 결과를 얻는데 사용된다.
   - 그래서 퓨쳐 객체를 pending completion (지연 완료) 객체라고도 한다. 
   - Future.get() 메소드를 호출하면 스레드가 작업을 완료할때까지 블로킹되었다가 작업이 완료되면 처리 결과를 리턴한다.
   - 작업을 처리하는 스레드가 작업 완료하기 전까지 get() 메소드가 블로킹되므로 다른 코드를 실행할 수 없다. 

   - (3-1) 리턴값이 없는 작업 완료 통보
     - 리턴값이 없는 작업일 경우, Runnable 객체로 생성하면 된다. 
  
   - (3-2) 리턴값이 있는 작업 완료 통보
     - 이 경우는 Callable 객체로 생성하기. 이 때 제네릭 타입 T는 call() 메소드가 리턴하는 타입이 되도록 하기
   
   - (3-3) 작업 처리 결과를 외부 객체에 저장
     - 스레드가 작업한 결과를 외부 객체에 저장해야할 경우.
     - 스레드 1의 결과 + 스레드 2의 결과 -> result 결과 취합 -> 결과 사용
     - 이런 작업을 위해서 ExecutorService의 submit(Runnable tast, V result) 메소드를 사용할 수 있다. (여기서 V가 result 타입) 
   
   - (3-4) 작업 완료 순으로 통보
     - 작업 요청 순서대로 처리가 완료되는 것은 아니다. 양도 다르고, 스케쥴링에 따라서도 처리 순서는 달라진다. 
     - 스레드 풀에서 작업 처리가 완료된 것만 통보받으려면? **CompletionService** 사용하기
     - poll(), take(), submit() 


4. 콜백 방식의 작업 완료 통보
   - 어플리케이션이 스레드에게 작업 처리를 요청한 후, 스레드가 작업을 요청하면 **특정 메소드를 자동 실행**하는 방법.
   - **자동 실행되는 메소드를 콜백 메소드라 한다.**
   - 작업 처리를 요청한 후 결과를 기다릴 필요 없이 다른 기능을 수행할 수 있다. (블로킹 방식은 작업 처리를 요청하고 작업이 완료될 때까지 블로킹되는 것과 비교됨)
