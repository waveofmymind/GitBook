# GC

## GC는 무엇이고, 왜 사용해야 할까?

1. 메모리 누수 방지: 프로그램이 동적으로 메모리를 할당하는데, 사용하지 않는 메모리를 해제하지 않으면 메모리 누수가 발생한다. GC는 이러한 것을 방지해준다.
2. 사용하지 않는 메모리를 프로그램이 다시 사용할 수 있게 해주기 때문에 재사용성에 용이하다.
3. GC 프로세스 자체가 CPU 및 메모리를 사용하기 때문에, GC가 발생할 때 프로그램의 성능이 저하 될 수 있다.
4. 발생 시점을 예측하기 어렵다.

## GC 튜닝을 해야하는 이유

1. STW 시간을 최소화해야하기 때문이다.
2. 힙 메모리를 최적화해서 GC 발생을 줄여야한다.
3. GC가 자주 발생하는 경우 프로그램의 성능이 저하한다.

## G1GC부터는 GC튜닝에 크게 손이 가진 않는데, G1GC는 어떻게 만들었길래 개발자가 튜닝을 이전보다 덜 해도 되는걸까요?

1. G1GC는 리전으로 메모리를 나눈다. 즉, GC의 Eden, Survivor, Old 영역을 동적으로 할당할 수 있다.
2. 가장 많은 가비지를 포함하는 영역부터 GC를 수행한다.
3. G1GC는 병렬 처리를 활용하여 Young Generation의 가비지를 처리하고, 동시 처리를 활용하여 Old Generation의 가비지를 처리합니다. 이를 통해 멀티 코어 프로세서의 장점을 최대한 활용할 수 있습니다.
4. G1GC는 여러 내부 휴리스틱을 사용하여 힙의 영역 크기, 일시 중지 시간, GC 간격 등을 자동으로 조절합니다.&#x20;
