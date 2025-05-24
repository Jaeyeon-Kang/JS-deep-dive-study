## 37.1 Set
- Set 객체는 중복되지 않는 유일한 값들의 집합이다.
- Set 객체는 배열과 유사하지만 다음과 같은 차이가 있다.

  |구분|배열|Set 객체|
  |---|---|---|
  |동일한 값을 중복하여 포함할 수 있다.|O|X|
  |요소 순서에 의미가 있다.|O|X|
  |인덱스로 요소에 접근할 수 있다.|O|X|
- Set은 수학적 집합을 구현하기 위한 자료구조로, 교집합, 차집합, 여집합 등을 구현할 수 있다.

### 37.1.1 Set 객체의 생성
- Set 객체는 Set 생성자 함수로 생성하며, 인수를 전달하지 않으면 빈 Set 객체가 생성된다.
  ```
  const set = new Set();
  console.log(set); // Set(0) {}
  ```
- Set 생성자 함수는 이터러블을 인수로 전달받아 Set 객체를 생성한다. 이터러블의 중복된 값은 Set 객체에 요소로 저장되지 않는다.
  ```
  const set1 = new Set([1, 2, 3, 3]);
  console.log(set1); // Set(3) {1, 2, 3}

  const set2 = new Set('hello');
  console.log(set2); // Set(4) {'h', 'e', 'l', 'o'}
  ```
- 중복을 허용하지 않는 Set 객체의 특성을 활용하여 배열에서 중복된 요소를 제거할 수 있다.
  ```
  // 배열의 중복 요소 제거
  const uniq = array => array.filter((v, i, self) => self.indexOf(v) === i);
  console.log(uniq([2, 1, 2, 3, 4, 3, 4])); // [2, 1, 3, 4]
  
  // Set을 사용한 배열의 중복 요소 제거
  const uniq = array => [...new Set(array)];
  console.log(uniq([2, 1, 2, 3, 4, 3, 4])); // [2, 1, 3, 4]
  ```

### 37.1.2 요소 개수 확인
- Set 객체의 요소 개수를 확인할 때는 Set.prototype.size 프로퍼티를 사용한다.
  ```
  const { size } = new Set([1, 2, 3, 3]);
  console.log(size); // 3
  ```

### 37.1.3 요소 추가
- Set 객체에 요소를 추가할 때는 Set.prototype.add 메서드를 사용한다.
  ```
  const set = new Set();
  console.log(set); // Set(0) {}

  set.add(1);
  console.log(set); // Set(1) {1}
  ```
- add 메서드는 새로운 요소가 추가된 Set 객체를 반환하기 때문에 add 메서드를 연속적으로 호출할 수 있다.
  ```
  const set = new Set();

  set.add(1).add(2);
  console.log(set); // Set(2) {1, 2}
  ```
- Set 객체에 중복된 요소의 추가는 허용되지 않는다.
  ```
  const set = new Set();

  set.add(1).add(2).add(2);
  console.log(set); // Set(2) {1, 2}
  ```
- Set 객체는 자바스크립트의 모든 값을 요소로 저장할 수 있다.
  ```
  const set = new Set();

  set
    .add(1)
    .add('a')
    .add(true)
    .add(undefined)
    .add(null)
    .add({})
    .add([])
    .add(() => {});

  console.log(set); // Set(8) {1, 'a', true, undefined, null, {}, [], () => {}}
  ```

### 37.1.4 요소 존재 여부 확인
- Set 객체에 특정 요소가 존재하는지 확인하려면 Set.prototype.has 메서드를 사용한다.
  ```
  const set = new Set([1, 2, 3]);

  console.log(set.has(2)); // true
  console.log(set.has(4)); // false
  ```

### 37.1.5 요소 삭제
- Set 객체의 특정 요소를 삭제하려면 Set.prototype.delete 메서드를 사용한다.
- delete 메서드에는 인덱스가 아니라 삭제하려는 요소값을 인수로 전달해야 한다.
  ```
  const set = new Set([1, 2, 3, 4]);

  set.delete(2);
  console.log(set); // Set(3) {1, 3, 4}

  set.delete(0); // 존재하지 않는 요소를 삭제하면 무시된다.
  console.log(set); // Set(3) {1, 3, 4}
  
  // delete는 삭제 성공 여부를 나타내는 불리언 값을 반환하기 때문에 연속적으로 호출 할 수 없다.
  set.delete(3).delete(4); // TypeError: set.delete(...).delete is not a function
  ```

### 37.1.6 요소 일괄 삭제
- Set 객체의 모든 요소를 삭제하려면 Set.prototype.clear 메서드를 사용한다.
  ```
  const set = new Set([1, 2, 3]);

  set.clear();
  console.log(set); // Set(0) {}
  ```

### 37.1.7 요소 순회
- Set 객체의 요소를 순회하려면 Set.prototype.forEach 메서드를 사용한다.
- 콜백 함수는 다음과 같이 3개의 인수를 전달받는다.
  - 첫 번째 인수: 현재 순회 중인 요소값
  - 두 번째 인수: 현재 순회 중인 요소값
  - 세 번째 인수: 현재 순회 중인 Set 객체 자체
- 첫 번째 인수와 두 번째 인수가 같은 이유는 Array.prototype.forEach 메서드와 인터페이스를 동일하기 위함이며 다른 의미는 없다.
- Array.prototype.forEach 메서드의 콜백 함수는 두 번째 인수로 인덱스를 전달받지만, Set 객체는 순서에 의미가 없다.
  ```
  const set = new Set([1, 2, 3]);

  set.forEach((v, v2, set) => console.log(v, v2, set));
  /*
  1 1 Set(3) {1, 2, 3}
  2 2 Set(3) {1, 2, 3}
  3 3 Set(3) {1, 2, 3}
  */
  ```
- Set 객체는 이터러블이므로 for...of문으로 순회할 수 있으며, 스프레드 문법과 배열 디스트럭처링의 대상이 될 수도 있다.
  ```
  const set = new Set([1, 2, 3]);

  // for...of 문
  for (const value of set) {
    console.log(value);
  }

  // 스프레드 문법
  console.log([...set]);

  // 배열 디스트럭처링의 대상
  const [a, ...rest] = set;
  console.log(a, rest); // 1, [2, 3]
  ```

### 37.1.8 집합 연산
- Set 객체는 수학적 집합을 구현하기 위한 자료구조이므로 교집합, 합집합, 차집합 등을 구현할 수 있다.

#### 교집합
- A ∩ B
  ```
  // 방법 1
  Set.prototype.intersection = function (set) {
    const result = new Set();

    for (const value of set) {
      if(this.has(value)) result.add(value);
    }

    return result;
  };
  
  // 방법 2
  Set.prototype.intersection = function (set) {
    return new Set([...this].filter(v => set.has(v)));
  }
  
  const setA = new Set([1, 2, 3, 4]);
  const setB = new Set([2, 4]);

  console.log(setA.intersection(setB)); // Set(2) {2, 4}
  console.log(setB.intersection(setA)); // Set(2) {2, 4}
  ```

#### 합집합
- A ∪ B
  ```
  // 방법 1
  Set.prototype.union = function (set) {
    const result = new Set(this);

    for (const value of set) {
      result.add(value);
    }

    return result;
  };
  
  // 방법 2
  Set.prototype.union = function (set) {
    return new Set([...this, ...set]);
  };
  
  const setA = new Set([1, 2, 3, 4]);
  const setB = new Set([2, 4]);

  console.log(setA.union(setB)); // Set(4) {1, 2, 3, 4}
  console.log(setB.union(setA)); // Set(4) {2, 4, 1, 3}
  ```

#### 차집합
- A - B
  ```
  // 방법 1
  Set.prototype.difference = function (set) {
    const result = new Set(this);

    for (const value of set) {
      result.delete(value);
    }

    return result;
  }
  
  // 방법 2
  Set.prototype.difference = function (set) {
    return new Set([...this].filter(v => !set.has(v)));
  }
  
  const setA = new Set([1, 2, 3, 4]);
  const setB = new Set([2, 4]);

  console.log(setA.difference(setB)); // Set(2) {1, 3}
  console.log(setB.difference(setA)); // Set(0) {}
  ```

#### 부분 집합과 상위 집합
- A ⊆ B
- A는 B의 부분 집합, B는 A의 상위 집합
  ```
  // 방법 1
  Set.prototype.isSuperset = function (subset) {
    for (const value of subset) {
      if(!this.has(value)) return false;
    }

    return true;
  }
  
  // 방법 2
  Set.prototype.isSuperset = function (subset) {
    const supersetArr = [...this];
    return [...subset].every(v => supersetArr.includes(v));
  }; 
  
  const setA = new Set([1, 2, 3, 4]);
  const setB = new Set([2, 4]);

  // A가 B의 상위집합인지
  console.log(setA.isSuperset(setB)); // true
  // B가 A의 상위집합인지
  console.log(setB.isSuperset(setA)); // false
  ```

## 37.2 Map
- Map 객체는 키와 값의 쌍으로 이루어진 컬렉션이다.
- Map 객체는 객체와 유사하지만 다음과 같은 차이가 있다.
  |구분|객체|Map 객체|
  |---|---|---|
  |키로 사용할 수 있는 값|문자열 또는 심벌 값|객체를 포함한 모든 값|
  |이터러블|X|O|
  |요소 개수 확인|Object.keys(obj).length|map.size|

### 37.2.1 Map 객체의 생성
- Map 객체는 Map 생성자 함수로 생성한다.
  ```
  const map = new Map();
  console.log(map); // Map(0) {}
  ```
- Map 생성자 함수는 이터러블을 인수로 전달받아 Map 객체를 생성한다. 이때 인수로 전달되는 이터러블은 키와 값의 쌍으로 이루어진 요소로 구성되어야 한다.
  ```
  const map1 = new Map([['key1', 'value1'], ['key2', 'value2']]);
  console.log(map1); // Map(2) {'key1' => 'value1', 'key2' => 'value2'}

  const map2 = new Map([1, 2]); // Uncaught TypeError: Iterator value 1 is not an entry object
  ```
- 중복된 키를 갖는 요소가 존재하면 값이 덮어써지기 때문에 Map 객체에는 중복된 키를 갖는 요소가 존재할 수 없다.
  ```
  const map = new Map([['key1', 'value1'], ['key1', 'value2']]);
  console.log(map); // Map(1) {'key1' => 'value2'}
  ```

### 37.2.2 요소 개수 확인
- Map.prototype.size 프로퍼티를 사용하여 요소 개수를 확인할 수 있다.
  ```
  const { size } = new Map([['key1', 'value1'], ['key2', 'value2']]);
  console.log(size); // 2
  ```

### 37.2.3 요소 추가
- Map 객체에 요소를 추가할 때는 Map.prototype.set 메서드를 사용한다.
  ```
  const map = new Map();
  console.log(map); // Map(0) {}

  map.set('key1', 'value1');
  console.log(map); // Map(1) {'key1' => 'value1'}
  
  // set 메서드를 연속적으로 호출할 수도 있다.
  map
    .set('key2', 'value2')
    .set('key3', 'value3');
   
  console.log(map); // Map(3) {'key1' => 'value1', 'key2' => 'value2', 'key3' => 'value3'}
  ```
- Map 객체는 키 타입에 제한이 없기 때문에 객체를 포함한 모든 값을 키로 사용할 수 있다.
  ```
  const map = new Map();

  const lee = { name: 'Lee' };
  const kim = { name: 'Kim' };

  map
    .set(lee, 'developer')
    .set(kim, 'designer');

  console.log(map);
  // Map(2) { {name: "Lee"} => "developer", {name: "Kim"} => "designer" }
  ```

### 37.2.4 요소 취득
- Map 객체에서 특정 요소를 취득하려면 Map.prototype.get 메서드를 사용한다.
- get 메서드의 인수로 키를 전달하면 값을 반환한다. 존재하지 않으면 undefined 반환
  ```
  const map = new Map();

  const lee = { name: 'Lee' };
  const kim = { name: 'Kim' };

  map
    .set(lee, 'developer')
    .set(kim, 'designer');

  console.log(map.get(lee));   // developer
  console.log(map.get('key')); // undefined
  ```

### 37.2.5 요소 존재 여부 확인
- Map 객체에 특정 요소가 존재하는지 확인하려면 Map.prototype.has 메서드를 사용하며, 불리언 값을 반환한다.
  ```
  const lee = { name: 'Lee' };
  const kim = { name: 'Kim' };

  const map = new Map([[lee, 'developer'], [kim, 'designer']]);

  console.log(map.has(lee)); // true
  console.log(map.has('key')); // false
  ```

### 37.2.6 요소 삭제
- Map 객체의 요소를 삭제하려면 Map.prototype.delete 메서드를 사용한다.
- 존재하지 않는 키로 Map 객체의 요소를 삭제하려 하면 에러 없이 무시된다.
  ```
  const lee = { name: 'Lee' };
  const kim = { name: 'Kim' };

  const map = new Map([[lee, 'developer'], [kim, 'designer']]);

  map.delete(kim);
  console.log(map); // Map(1) { {name: "Lee"} => "developer" }
  ```
- delete 메서드는 삭제 성공 여부를 나타내는 불리언 값을 반환하기 때문에 set 메서드와 달리 연속적으로 호출할 수 없다.
  ```
  const lee = { name: 'Lee' };
  const kim = { name: 'Kim' };

  const map = new Map([[lee, 'developer'], [kim, 'designer']]); // TypeError: map.delete(...).delete is not a function

  map.delete(lee).delete(kim);
  ```

### 37.2.7 요소 일괄 삭제
- Map 객체의 요소를 일괄 삭제하려면 Map.prototype.clear 메서드를 사용한다.
  ```
  const lee = { name: 'Lee' };
  const kim = { name: 'Kim' };

  const map = new Map([[lee, 'developer'], [kim, 'designer']]);

  map.clear();
  console.log(map); // Map(0) {}
  ```

### 37.2.8 요소 순회
- Map 객체의 요소를 순회하려면 Map.prototype.forEach 메서드를 사용한다.
- forEach 메서드의 콜백 함수는 다음과 같이 3개의 인수를 전달받는다.
  - 첫 번째 인수: 현재 순회 중인 요소값
  - 두 번째 인수: 현재 순회 중인 요소키
  - 세 번째 인수: 현재 순회 중인 Map 객체 자체
  ```
  const lee = { name: 'Lee' };
  const kim = { name: 'Kim' };

  const map = new Map([[lee, 'developer'], [kim, 'designer']]);

  map.forEach((v, k, map) => console.log(v, k, map));
  /*
  developer {name: "Lee"} Map(2) {
    {name: "Lee"} => "developer",
    {name: "Kim"} => "designer"
  }
  designer {name: "Kim"} Map(2) {
    {name: "Lee"} => "developer",
    {name: "Kim"} => "designer"
  }
  */
  ```
- Map 객체는 이터러블이기 때문에 for...of 문으로 순회할 수 있으며, 스프레드 문법과 디스트럭처링 할당의 대상이 될 수도 있다.
  ```
  const lee = { name: 'Lee' };
  const kim = { name: 'Kim' };

  const map = new Map([[lee, 'developer'], [kim, 'designer']]);

  // for...of문
  for (const entry of map) {
    console.log(entry); // [{name: "Lee"}, "developer"] [{name: "Kim"}, "designer"]
  }
  
  // 스프레드 문법
  console.log([...map]); 
  // [[{name: "Lee"}, "developer"], [{name: "Kim"}, "designer"]]
  
  // 배열 디스트럭처링
  const [a, b] = map;
  console.log(a, b); // [{name: "Lee"}, "developer"] [{name: "Kim"}, "designer"]
  ```
- Map 객체는 이터러블이면서 동시에 이터레이터(next 메서드로 순환할 수 있는 객체)인 객체를 반환하는 메서드를 제공한다.
  ```
  const lee = { name: 'Lee' };
  const kim = { name: 'Kim' };

  const map = new Map([[lee, 'developer'], [kim, 'designer']]);

  for (const key of map.keys()) {
    console.log(key); // {name: "Lee"} {name: "Kim"}
  }
  
  for (const value of map.values()) {
    console.log(value); // developer designer
  }
  
  for (const entry of map.entries()) {
    console.log(entry); // [{name: "Lee"}, "developer"] [{name: "Kim"}, "designer"]
  }
  ```
