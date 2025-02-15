원시 타입과 객체 타입의 비교 3가지.
비교1. 원시 값은 변경 불가능한 값이다. <-> 객체(참조) 타입의 값, 즉 객체는 변경 가능한 값이다.
비교2. 원시 값을 변수에 할당하면 변수(확보된 메모리 공간)에는 실제 값이 저장된다. <-> 객체를 변수에 할당하면 변수(확보된 메모리 공간)에는 참조 값이 저장된다.
비교3. 원시 값을 갖는 변수를 다른 변수에 할당하면 원본의 원시 값이 복사되어 전달된다. <-> 객체를 가리키는 변수를 다른 변수에 할당하면 원본의 참조 값이 복사되어 전달된다.

11.1 원시 값
11.1.1 변경 불가능한 값
한번 생성된 원시 값은 읽기 전용 값으로서 변경할 수 없다.
변경 불가능하다는 것은 변수가 아니라 값에 대한 진술이다.
원시 값을 할당한 변수에 새로운 원시 값을 재할당하면 메모리 공간에 저장되어 있는 재할당 이전의 원시 값을 변경하는 것이 아니라 새로운 메모리 공간을 확보하고 재할당한 원시 값을 저장한 후, 변수는 새롭게 재할당한 원시 값을 가리킨다. 이때 변수가 참조하던 메모리 공간의 주소가 바뀐다.
불변성을 갖는 원시 값을 할당한 변수는 재할당 이외에 변수 값을 변경할 수 있는 방법이 없다.

11.1.2 문자열과 불변성
자바스크립트의 문자열은 원시 타입이며 변경 불가능하다.
cf) 유사 배열 객체란 마치 배열처럼 인덱스로 프로퍼티 값에 접근할 수 있고 length 프로퍼티를 갖는 객체를 말한다.
* 문자열은 유사 배열이므로 배열과 유사하게 인덱스를 사용해 각 문자에 접근할 수 있다. 하지만 문자열은 원시 값이므로 변경할 수 없다. 이때 에러가 발생하지 않는다.

11.1.3 값에 의한 전달

11.2 객체
