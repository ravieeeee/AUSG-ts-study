# Iterators and Generators
* [docs](https://www.typescriptlang.org/docs/handbook/iterators-and-generators.html)

## Iterable
* Symbol.iterator를 implement

> In computer programming, an iterator is an object that enables a programmer to traverse a container.

* built-in types(Array, Map, Set, String 등)은 기본적으로 구현되어 있음

## for-of 구문
```typescript
let someArray = [1, "string", false];
for (let entry of someArray) {
  console.log(entry); // 1, string, false
}
```

## for-of vs for-in
```typescript
let list = [4, 5, 6];
for (let i in list) {
  console.log(i); // 0, 1, 2 (key를 반환)
}
for (let i of list) {
  console.log(i); // 4, 5, 6 (value를 반환)
}
```


## 모든 object에서 동작하는 for-in과 Symbol.iterator를 가지는 Collection의 for-of
```typescript
Object.prototype.objCustom = function () {};
Array.prototype.arrCustom = function () {};

var iterable = [3, 5, 7];
iterable.foo = "hello";

for (var key in iterable) {
  console.log(key); // 0, 1, 2, foo, arrCustom, objCustom
}

for (var value of iterable) {
  console.log(value); // 3, 5, 7
}
```

## for-of in ES3 and ES5
* for-of는 ES6 Syntax

```typescript
let numbers = [1, 2, 3];
for (let num of numbers) {
  console.log(num); // 1, 2, 3
}
```

* 컴파일러가 아래로 변환

```typescript
var numbers = [1, 2, 3];
for (var _i = 0; _i < numbers.length; _i++) {
    var num = numbers[_i];
    console.log(num);
}
```