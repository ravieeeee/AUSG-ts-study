# Symbol
* [docs](https://www.typescriptlang.org/docs/handbook/symbols.html)

## what is Symbol?
* ES6(ECMAScript 2015) 문법
* 새로운 primitive type!
* [Symbol에 대한 자세한 이해](https://medium.com/@hyunwoojo/javascript-symbol-%EC%97%90-%EB%8C%80%ED%95%B4%EC%84%9C-6aa5903fb6f1)
* 사용 빈도는..?

## 생성 방법 및 주요 특징
```typescript
let sym1 = Symbol(); // constructor
let sym2 = Symbol("key"); // 심볼의 key값
let sym3 = Symbol("key");
sym2 === sym3 // false, symbols are unique
```

## object의 key가 될 수 있다.
```typescript
let sym = Symbol();
let obj = {
  [sym]: "value"
};
console.log(obj[sym]); // "value"
```

## Well-known Symbols
* 사용자가 정의하는 Symbol말고도, 유명한 Symbol들이 존재
* [Well-known Symbol에 대한 자세한 이해](https://dmitripavlutin.com/detailed-overview-of-well-known-symbols/)