# 함수형 프로그래밍 개요

## 함수형 프로그래밍

> 순수함수를 만들어 부수효과를 없애고 모듈화 수준을 높여 조합성을 강조하는 프로그래밍 패러다임

### 순수함수

- 들어온 인자가 같으면 항상 동일한 결과를 리턴하는 함수
- 외부의 값에 영향을 끼치지 않는 함수
- 리턴값 외에는 외부와 소통하는 것이 없는 함수
- 오류를 줄이고 안정성을 높임
- 평가시점에 영향을 받지 않음

### 모듈화 수준이 높다

- 생산성을 높임
- 기획 변경에 따른 대응이 용이

### 예제

```jsx
/* 순수함수 */

function add(a, b) {
  return a + b;
}

console.log(add(10, 2)); // 12
console.log(add(10, 2)); // 12

/* 순수하지 않은 함수(1) */

let c = 10;

function add2(a, b) {
  return a + b + c;
}

console.log(add2(10, 2)); // 22
c = 20;
console.log(add2(10, 2)); // 32

/* 순수하지 않은 함수(2) */

let c = 10;

function add3(a, b) {
  c = b;
  return a + b;
}

console.log(c); // 10
console.log(add3(20, 30)); // 50
console.log(c); // 30
console.log(add2(20, 30)); // 50

/* 순수하지 않은 함수(3) */

let obj1 = { val: 10 };

function add4(obj, b) {
  obj.val += b;
}

console.log(obj1.val); // 10
add4(obj1, 20);
console.log(obj1.val); // 30

/* 다시 순수 함수 */
let obj1 = { val: 10 };

function add5(obj, b) {
  return { val: obj.val + b };
}

console.log(obj1.val); // 10
let obj2 = add5(obj1, 20);
console.log(obj1.val); // 10
console.log(obj2.val); // 30
```

## 일급함수, add_maker, 함수로 함수 실행하기

```jsx
function add(a, b) {
  return a + b;
}

/* 일급 함수 : 함수를 값(변수,인자,반환값)으로 다룰 수 있음 */
let f1 = function (a) {
  return a * a;
};

let f2 = add;

function f3(f) {
  return f();
}

console.log(
  f3(function () {
    return 10;
  })
); // 10
console.log(
  f3(function () {
    return 20;
  })
); // 20

/* add_maker */

function add_maker(a) {
  // 클로저 : a 값을 외부에서 가져다 쓸 수 있음
  return function (b) {
    return a + b;
  };
}

let add10 = add_maker(10);
console.log(add10(20)); // 30

let add5 = add_maker(5);
let add15 = add_maker(15);

console.log(add5(10)); // 15
console.log(add15(10)); // 25

/* 순수함수 조합 */

function f4(f1, f2, f3) {
  return f3(f1() + f2());
}

console.log(
  f4(
    function () {
      return 2;
    },
    function () {
      return 1;
    },
    function (a) {
      return a * a;
    }
  )
);
```

# 함수형으로 전환하기

## 회원 목록, map, filter, each

- 함수형 프로그래밍에서 중복을 제거하거나 추상화할 때 함수를 사용
- 고차 함수 : 함수를 인자로 받아 실행하거나 함수를 리턴하는 함수

```jsx
const users = [
  { id: 1, name: "ID", age: 36 },
  { id: 2, name: "BJ", age: 32 },
  { id: 3, name: "JM", age: 32 },
  { id: 4, name: "PJ", age: 27 },
  { id: 5, name: "HA", age: 25 },
  { id: 6, name: "JE", age: 26 },
  { id: 7, name: "JI", age: 31 },
  { id: 8, name: "MP", age: 23 },
];

// 1. 명령형 코드
// 1. 30세 이상인 users를 거른다
const answer = [];
for (let i = 0; i < users.length; i += 1) {
  if (users[i].age >= 30) {
    answer.push(users[i]);
  }
}
// 2. 30세 이상인 users의 names를 수집한다
const anwser = [];
for (let i = 0; i < users.length; i += 1) {
  if (users[i].age >= 30) {
    answer.push(users[i].name);
  }
}
// 3. 30세 미만인 users를 거른다
const answer = [];
for (let i = 0; i < users.length; i += 1) {
  if (users[i].age < 30) {
    answer.push(users[i]);
  }
}
// 4. 30세 미만인 users의 ages를 수집한다
const answer = [];
for (let i = 0; i < users.length; i += 1) {
  if (users[i].age < 30) {
    answer.push(users[i].age);
  }
}

// 2. _filter, _map으로 리팩토링 : 조건 필터링을 함수로 위임
function _filter(arr, predi) {
  const newArr = [];
  _each(arr, (val) => {
    if (predi(val)) {
      newArr.push(val);
    }
  });
  return newArr;
}

function _map(arr, mapper) {
  const newArr = [];
  _each(arr, (val) => {
    newArr.push(mapper(val));
  });
  return newArr;
}

// 3. _each 만들기
function _each(arr, iter) {
  for (let i = 0; i < arr.length; i += 1) {
    iter(arr[i], i);
  }
}
```

- Array의 메서드 : 객체지향프로그래밍, 해당 클래스의 인스턴스만 사용가능
- array like 에서 Array 메서드 사용 불가 , but 커스텀 `_filter` , `_map`, `_each` 에서는 작동

```jsx
_map(document.querySelectorAll("*", function(node) {
  return node.nodeName;
}); // 작동이 된다!
```

## 커링, curry, curryr

- 커링 : 함수와 인자를 다루는 기법, 함수에 인자를 하나씩 적용하다가 필요한 인자를 다 넘겼으면 함수 본체를 실행

```jsx
// 3. 커링
	// 1. _curry, _curryr
function _curry(fn){
  return function(a , b){
    return arguments.length === 2 ? fn(a,b) : function(b){ return fn(a,b); };
  }
}

let add = _curry((a,b)=>(a+b));
let add10 = add(10);
console.log(add10(5)); // 15
console.log(add(5)(3)); // 8

function _curryr(fn){
  return function(a, b){
    return arguments.length === 2 ? fn(a,b) : function(b){ return fn(b,a); };
}

	// 2. _get 만들어 좀 더 간단하게 하기 : object에 있는 값을 안전하게 참조하는 함수
  const _get = _curryr((obj, key){
  return obj && obj[key];
});

console.log(_get(user,'name'));
console.log(_get('name')(user));

const getName = _get('name');
console.log(getName(user));

_map(_filter(users,function(user){ return user.age >= 30;}), _get('name'));
_map(_filter(users,function(user){ return user.age < 30;}), _get('age'));
```

## reduce

```jsx
const slice = Array.prototype.slice;
function _rest(list, num) {
  return slice.call(list, num || 1);
}

// 4. _reduce 만들기
function _reduce(list, iter, memo) {
  if (arguments.length === 2) {
    memo = list[0];
    list = _rest(list);
  }
  _each(list, function (val) {
    memo = iter(memo, val);
  });
  return memo;
}
```

## 파이프라인, \_go, \_pipe, 화살표 함수

```jsx
// 5. 파이프라인 만들기
	// 1. _pipe : 인자로 들어온 함수들을 연속적으로 실행하는 함수를 리턴
    //            reduce 특화 함수
    //            함수가 담긴 배열이라는 인자를 연속적으로 적용한 최종결과로 축약
function _pipe(){
    const fns = arguments;
  	return function(arg){
      return _reduce(fns,function(arg,fn){
        return fn(arg);
      },arg);
}

const f1 = _pipe(
  function(a){ return a+1;}, // 1 + 1
  function(b){ return a*2;}, // 2 * 2
);

console.log(f1(1));

 	// 2. _go : 즉시 실행되는 _pipe 함수

function _go(arg){
    const fns = _rest(arguments);
	return _pipe.apply(null,fns)(arg);
}

_go(
  1,
  function(a){ return a+1;},
  function(b){ return a*2;},
  console.log
);

	// 3. users에 _go 적용
console.log(
  _map(
    _filter(users,function(user){ return user.age >= 30; }),
    _get('name')));

_go(
  users,
  function(users){
    return _filter(users,function(user){ return user.age >= 30; });
  },
  function(users){
    return _map(users,_get('name');
  },
  console.log
);

_go(
  users,
  _curryr(_filter)(function(user){ return user.age >= 30; }),
  _curryr(_map)(_get('name')),
  console.log
);

const _filterCurryr = _curryr(_filter);
const _mapCurryr = _curryr(_map);

_go(
  users,
  _filterCurryr((user)=> (user.age >= 30)),
  _mapCurryr(_get('name')),
  console.log
);
```

## 다형성 높이기, \_keys, 에러

```jsx
// 6. _each의 외부 다형성 높이기
// 1. _each에 null 넣어도 에러 안나게
// try catch, typeof 체크하여 에러를 내는 대신 코드가 그대로 진행되도록 하는 방식 => lodash, backbone, sequelize 등이 쓰는 방식
const _length = _get("length");

function _each(list, iter) {
  for (let i = 0, len = _length(list); i < len; i += 1) {
    iter(list[i]);
  }
}
// 2. _keys 만들기
function _keys(obj) {
  return _is_object(obj) ? Object.keys(obj) : [];
}

// 3. _keys에서도 _is_object인지 검사하여 null 에러 안 나게
function _is_object(obj) {
  return typeof obj === "object" && !!obj;
}

// 4. _each 외부 다형성 높이기
function _each(list, iter) {
  const keys = _keys(list);
  for (let i = 0; i < keys.length; i += 1) {
    iter(list[keys[i]]);
  }
}

_each(
  {
    13: "ID",
    19: "HD",
    29: "YD",
  },
  console.log
);
```

# 컬렉션 중심 프로그래밍

컬렉션(배열과 같이 요소들을 순회할 수 있는 데이터 형식)을 잘 다루는 함수 세트들을 구성하는 방식으로 프로그래밍

### 컬렉션 중심 프로그래밍의 4가지 유형과 함수

**굵은 글씨** 는 대표함수 (추상화 레벨이 높음), 얇은 글씨는 특화함수 (대표함수로 만들어짐)

1. 수집하기 - **map**, values, pluck 등

2. 거르기 - **filter**, reject, compact, without 등

3. 찾아내기 - **find**, some, every 등

4. 접기 - **reduce**, min, max, group_by, count_by

## 수집하기 - map, values, pluck

```jsx
const users = [
  { id: 10, name: "ID", age: 36 },
  { id: 20, name: "BJ", age: 32 },
  { id: 30, name: "JM", age: 32 },
  { id: 40, name: "PJ", age: 27 },
  { id: 50, name: "HA", age: 25 },
  { id: 60, name: "JE", age: 26 },
  { id: 70, name: "JI", age: 31 },
  { id: 80, name: "MP", age: 23 },
  { id: 90, name: "FP", age: 13 },
];

// 컬렉션 중심 프로그래밍의 유형별 함수 만들기

// 1. 수집하기 - map
//	1. values

function _identity(val) {
  return val;
}

function _values(data) {
  return _map(data, _identity);
}

// curryr로 확장시킨 _map함수
const _values = _map(_identity);
_values(users[0]);

//	2. pluck - 특정 key에 해당하는 값들을 수집
//           서버, 데이터베이스 등 where, in과 함께 많이 사용

function _pluck(data, key) {
  return _map(_get(key))(data);
}

_pluck(users, "age"); // [33,22,11,...]
```

## 거르기 - reject, compact

```jsx
// 2. 거르기 - filter
//	1. reject - true로 평가되는 값들을 제외

function _negate(func){
  return function(val){
    return !func(val);
  }
}

function reject(data,predi){
  return _filter(data,(val)=>!predi(val));
}

function reject(data,predi){
  return _filter(data,_negate(predi));
// 	2. compact - truthy한 값들만 남김

const _compact = _filter(_identity);

console.log(
  _compact([1,2,0,false,null,{}])
);
```

## 찾아내기 - find, find_index, some, every

```jsx
// 3. 찾아내기 - find

//	1. find 만들기 - 처음으로 true인 값을 반환
const _find = _curryr((list, predi) => {
  const keys = _keys(list);
  for (let i = 0, len = keys.length; i < len; i += 1) {
    const val = list[keys[i]];
    if (predi(val)) return val;
  }
});

_go(
  users,
  _find((user) => user.id === 50),
  _get("name"),
  console.log
);

//	2. find_index

const _find_index = curryr((list, predi) => {
  const keys = _keys(list);
  for (let i = 0, len = keys.length; i < len; i += 1) {
    if (predi(list[keys[i]])) return i;
  }
  return -1;
});
// 	3. some - 조건 하나라도 만족하면 true

function some(list, predi) {
  return _find_index(list, predi || _identity) !== -1;
}

//	4. every - 조건 모두가 만족하면 true

function every(list, predi) {
  return _find_index(list, _negate(predi || _identity)) === -1;
}
```

## 접기 - reduce,min_by, max_by

```jsx
// 4. 접기 - reduce
//         for문 대체하는 사고X
//         reduce의 두 번째 인자 작성시 오직 acc,val만 알고 있다는 가정 하에 함수 작성하기
//	1. min, max, min_by, max_by

function _min(list){
	return _reduce(list,(min,val) =>(min > val ? val : min));
}

function _max(list){
	return _reduce(list,(max,val) =>(max < val ? val : max));
}

_min([1,2,4,10,5,-4]); // -4
_max([1,2,4,10,5,-4]); // 10

const _min_by = _curryr((list,predi){
	return _reduce(list, (min,val) => (predi(min) > predi(val) ? val : min));
});

const _max_by = _curryr((list, _predi){
	return _reduce(list,(max,val) => (predi(max) < predi(val) ? val : max));
});

_min_by([1,2,4,10,5,-4], Math.abs); // 1
_max_by([1,2,4,-10,5,-4], Math.abs); // -10

_max_by(users,_get('age'));

_go(users,
    _filter(user=> user.age >= 30),
    _min_by(users,_get('age')),
    _get('name')
 );

//	2. group_by, push

function _push(obj,key, val){
  obj[key] = obj[key] || [];
  obj[key].push(val);
  return obj;
}

function _group_by(list,iter){
  return _reduce(list, (grouped,val)=>{
    const key = iter(val);
  	grouped[key] = grouped[key] || [];
    grouped[key].push(val);
    return grouped;
  }, {});
}

const _group_by = _curryr((list,iter){
  return _reduce(list, (grouped,val)=> _push(grouped, iter(val), val), {});
});

_group_by(users,_get('age'));

const groupedUser = {
	36 : [ { id: 10, name:'ID', age: 36}],
  	32 : [ { id: 20, name:'BJ', age: 32}, { id: 30, name:'JM', age:32}],
  :
}

_go(users,
  _group_by(function(user){
    return user.age - user.age % 10; // 10대 20대 ...
  }),
  console.log
);

_go(users,
  _group_by(function(user){
  	return user.name[0],
  console.log
})

function _head = function(list){
  return list[0];
}

_go(users,
   _group_by(_pipe(_get('name'), _head),
   console.log
 );

//	3. count_by, inc

function _inc(count, key){
  count[key] = count[key] ? count[key] + 1 : 1;
  return count;
}

const _count_by = _curryr((list, iter){
  return _reduce(list,(count, val) =>{
    const key = iter(val);
    count[key] = count[key] || 0;
    count[key] += 1;
    return count;
  }, {});
});

function _count_by(list, iter){
  return _reduce(list,(count, val) => _inc(count,iter(val)), {});
}

function _each(list,iter){
  const keys = _keys(list);
  for(let i = 0; i < keys.length; i += 1){
    iter(list[keys[i]], keys[i]);
  }
}

function _map(list, mapper){
  const new_list = [];
  _each(list,(val,key)=> {
    new_list.push(mapper(val,key));
  });
  return new_list;
}

const _pairs = _map((val, key) => [key, val]);

console.log(_pairs(users[0]); // [['id', 10], ['name','ID'],['age',36]]

_go(users,
  _reject((user) => user.age < 20),
  _count_by((user) => user.age - user.age % 10),
  _map(count,key) => <li>`${key}대는 ${count}명 입니다`<li>),
  (list)=> '<ul>' + list.join('') + '</ul>',
  document.write.bind(document)
);

const printCountByAge= _pipe(
  _count_by((user) => user.age - user.age % 10),
  _map(count,key) => <li>`${key}대는 ${count}명 입니다`<li>),
  (list)=> '<ul>' + list.join('') + '</ul>',
  document.write.bind(document)
);

printCountOver20 = _pipe(
  _reject((user) => user.age < 20),
  printCountByAge
);

printCountOver20(users);

_go(users,_reject(user => user.age < 20), printCountByAge);
_go(users,_filter(user => user.age > 10), printCountByAge);
```

컬렉션에 대한 함수를 만들 때 유형을 분석, 유형 간 조합으로 함수 작성 => 문제를 보다 잘 정리된 패턴으로 분석할 수 있고 문제가 쉬워져서 프로그래밍이 좀 더 쉽게 이뤄지고 i++ 과 같은 변화나 for문이 없고 모든 함수들이 다형성이 높으면서도 안정성이 이미 확보된 함수들을 조립하면서 프로그래밍을 하기 때문에 내가 만든 로직이 잘 돌아갈 것이라는 확신이 좀 더 빨리 들 수 있다. 또한 테스트도 쉽다. 함수 하나만 테스트 혹은 보조 함수 하나만 테스트가 가능하다.

# 자바스크립트에서의 지연 평가

## 지연 평가

```jsx
// 지연 평가를 시작시키고 유지시키는(이어 가는) 함수
// 1. map
// 2. filter, reject

// 끝을 내는 함수
// 1. take
// 2. some, every, find

let mi = 0;
let fi = 0;

_go(
  _.range(100),
  _.map(function (val) {
    ++mi;
    return val * val;
  }),
  _.filter(function (val) {
    ++fi;
    return val % 2;
  }),
  _.some((val) => val > 100),
  console.log
);

console.log(mi, fi); // 100 100

// 한 개의 값을 map, filter, take 함수에 넣어 값 측정 과정 반복해서 5개 모이면 종료
_.go(
  _.range(100),
  L.map(function (val) {
    ++mi;
    return val * val;
  }),
  L.filter(function (val) {
    ++fi;
    return val % 2;
  }),
  L.some((val) => val > 100),
  console.log
);

console.log(mi, fi); // 12 12
```

## 요약

1. 함수를 되도록 **작게** 만들기
2. **다형성** 높은 함수 만들기 with 보조 함수
3. **상태**를 변경하지 않거나 **정확히** 다루어 **부수 효과를 최소화** 하기
   - 부수효과는 있을 수 밖에 없음. 결국엔 브라우저에 반영해야하기 때문.
   - 부수효과는 최종 결과/목적
   - 부수효과 가기 전에는 상태를 변경하지 않는 식으로 프로그래밍
4. 동일한 인자를 받으면 항상 동일한 결과를 리턴하는 순수 함수 만들기
5. 복잡한 객체 하나를 인자로 사용하기보다 되도록 일반적인 값 여러 개를 인자로 사용하기
6. 큰 로직을 **고차 함수**로 만들고 세부 로직을 **보조함수**로 완성하기
7. 어느 곳에서든 **바로 혹은 미뤄서 실행**할 수 있도록 메서드가 아닌 일반함수이자 순수함수로 선언하기
8. 모델이나 컬렉션 등의 커스텀 객체보다는 **기본 객체** 이용하기
9. 로직의 흐름을 최대한 **단방향으로 흐르게** 하기

# 실전 코드

## users, posts, comments

```jsx
const users = [
  { id: 101, name: 'ID' },
  { id: 102, name: 'BJ' },
  { id: 103, name: 'PJ' },
  { id: 104, name: 'HA' },
  { id: 105, name: 'JE' },
  { id: 106, name: 'JI' }
];

const posts = [
  { id: 201, body: '내용1', user_id: 101 },
  { id: 202, body: '내용2', user_id: 102 },
  { id: 203, body: '내용3', user_id: 103 },
  { id: 204, body: '내용4', user_id: 102 },
  { id: 205, body: '내용5', user_id: 101 },
];

const comments = [
  { id: 301, body: '댓글1', user_id: 105, post_id: 201 },
  { id: 302, body: '댓글2', user_id: 104, post_id: 201 },
  { id: 303, body: '댓글3', user_id: 104, post_id: 202 },
  { id: 304, body: '댓글4', user_id: 105, post_id: 203 },
  { id: 305, body: '댓글5', user_id: 106, post_id: 203 },
  { id: 306, body: '댓글6', user_id: 106, post_id: 204 },
  { id: 307, body: '댓글7', user_id: 102, post_id: 205 },
  { id: 308, body: '댓글8', user_id: 103, post_id: 204 },
  { id: 309, body: '댓글9', user_id: 103, post_id: 202 },
  { id: 310, body: '댓글10', user_id: 105, post_id: 201 }
];

// 1. 특정인의 posts의 모든 comments 찾기

_.go(
  _.filter(posts, (post)=>post.user_id === 101),
  function(posts){
    return _.filter(comments, function(comment){
      return _.find(posts,(post)=>post.id === comment.post_id);
    });
  }
);

_.go(
  _.where(posts, { user_id : 101}),
  _.pluck('id'),
  function(post_ids){
    return _.filter(comments, function(comment){
      return _.contains(posts_ids,comment.post_id);
    });
  },
  console.log
);

// 2. 특정인의 posts에 comments를 단 친구의 이름들 뽑기

_.go(
  _.where(posts,{ user_id : 101 }),
  _.pluck('id'),
  function(post_ids){
    return _.filter(comments, function(comment){
      return _.contains(posts_ids,comment.post_id);
    });
  },
  _.map(function(comment){
    return _.find(users,function(user){
      return user.id === comment.user_id;
    }).name;
  }),
  _.uniq
  console.log
);

// 1, 2 리팩토링
function posts_by(attr){
  return _.where(posts,attr);
}

const comments_by_posts = _.pipe(
  _pluck('id'),
  function(post_ids){
    return _.filter(comments, function(comment){
      return _.contains(posts_ids,comment.post_id);
    });
  }
);

_.go({ user_id : 101 },
  posts_by,
  _.pluck('id'),
  comments_by_posts,
  console.log
);

_.go({ user_id : 101 },
  posts_by,
  _.pluck('id'),
  comments_by_posts,
  _.map(function(comment){
    return _.find(users,function(user){
      return user.id === comment.user_id;
    }).name;
  }),
  _.uniq
  console.log
);

// 3. 특정인의 posts에 comments를 단 친구들 카운트 정보
_.go({ user_id : 101 },
  posts_by,
  _.pluck('id'),
  comments_by_posts,
  _.map(function(comment){
    return _.find(users,function(user){
      return user.id === comment.user_id;
    }).name;
  }),
  _.count_by,
  console.log
);

// 1,2,3 리팩토링

const comments_to_user_names = _.map(function(comment){
    return _.find(users,function(user){
      return user.id === comment.user_id;
    }).name;
  });

const f1 = _.pipe(posts_by, comments_by_posts);
const f2 = _.pipe(
  f1,
  comments_to_user_names,
  _.uniq
);

const f3 = _.pipe(
  f1,
  comments_to_user_names,
  _.count_by
);

// 4. 특정인이 comment를 단 posts 가져오기
_.go(
  _.where(comments,{ user_id : 105 }),
  _.pluck('post_id'),
  _.uniq,
  function(post_ids){
    return _.filter(posts,function(post){
      return _.contains(post_ids, post.id);
    });
  },
  console.log
);
```

## 효율 높이기

```jsx
// 5. users + posts + comments (index_by, group_by로 효율 높이기)

const users2 = _.index_by(users, 'id'); // { id : { id :101, name:'ID' }}

const comments2 = _.go(
  comments,
  _.map(function(comment){
  	return _.extend({
  		user: users2[comment.user_id]
  	},comment});
  },
  _.group_by('post_id')
); // { 201 : [{ body : "댓글1", id: 301, post_id: 201  , user }, ... ]}

const posts2 = _.map(posts, function(post){
  	return _.extend({
    	comment: comments2[post.id],
  		user: users2[post.user_id]
  	},post);
});

const posts2 = _.group_by(posts2,'user_id');

const user3 = _.map(user2, function(user){
  return _.extend({
    posts: posts2[user.id]
  },user);
});

// 5.1. 특정인의 posts의 모든 comments 가져오기
_.go(user.posts,
 _.pluck('comments'),
 _.flatten,
 console.log
);

console.log(_.deep_pluck(users3[0],'posts.comments'));
// 5.2. 특정인의 posts에 comments를 단 친구의 이름들 뽑기
_.go(user.posts,
 _.pluck('comments'),
 _.flatten,
 _.pluck('user'),
 _.pluck('name'),
 _.uniq
 console.log
);

_.go(users3[0],
 _.deep_pluck('posts.comments.user.name'),
 _.uniq,
 console.log
);

// 5.3. 특정인의 posts에 comments를 단 친구들 카운트 정보
_.go(user.posts,
 _.pluck('comments'),
 _.flatten,
 _.pluck('user'),
 _.pluck('name'),
 _.count_by
 console.log
);

_.go(users3[0],
 _.deep_pluck('posts.comments.user.name'),
 _.count_by,
 console.log
);

// 5.4 특정인이 comment를 단 posts 가져오기
_.filter(posts2, function(post){
  return _.find(post.comments, function(comment){
    return comment.user_id === 105;
  });
});
```

## 장바구니

```jsx
const products = [
  {
    is_selected: true, // <--- 장바구니에서 체크 박스 선택
    name: "반팔티",
    price: 10000, // <--- 기본 가격
    sizes: [
      // <---- 장바구니에 담은 동일 상품의 사이즈 별 수량과 가격
      { name: "L", quantity: 4, price: 0 },
      { name: "XL", quantity: 2, price: 0 },
      { name: "2XL", quantity: 3, price: 2000 }, // <-- 옵션의 추가 가격
    ],
  },
  {
    is_selected: true,
    name: "후드티",
    price: 21000,
    sizes: [
      { name: "L", quantity: 2, price: -1000 },
      { name: "2XL", quantity: 4, price: 2000 },
    ],
  },
  {
    is_selected: false,
    name: "맨투맨",
    price: 16000,
    sizes: [{ name: "L", quantity: 10, price: 0 }],
  },
];

// 1. 모든 수량

const total_quantity = _.reduce(function (tq, product) {
  return _.reduce(product.sizes, (tq, size) => tq + size.quantity, tq);
}, 0);

_.go(products, total_quantity, console.log);

// 2. 선택된 총 수량

_.go(products, _filter(_.get("is_selected")), total_quantity, console.log);

// 3. 모든 가격

const total_price = _.reduce(function (tp, product) {
  return _.reduce(
    product.sizes,
    (tp, size) => tp + size.quantity * (product.price + size.price),
    tp
  );
});

_.go(products, total_price, console.log);

// 4. 선택된 총 가격

_.go(products, _filter(_.get("is_selected")), total_price, console.log);
```

# 비동기

```jsx
function square(a) {
  return new Promise(function (resolve) {
    setTimeout(function () {
      resolve(a * a);
    }, 500);
  });
}

square(10).then(square).then(square).then(square).then(console.log);

_.go(square(10), square, square, square, console.log);

const list = [2, 3, 4];
new Promise(function (resolve) {
  (function recur(res) {
    if (list.length === res.length) return resolve(res);
    square(list[res.length]).then(function (val) {
      res.push(val);
      recur(res);
    });
  })([]);
}).then(console.log);

_.go(list, _.map(square), console.log);
```
