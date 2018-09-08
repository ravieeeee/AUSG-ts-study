# Modules
* [docs](https://www.typescriptlang.org/docs/handbook/modules.html)
* ES6
* export를 이용하여 전역이 아닌, 자체 scope
* 다른 module을 이용하기 위해서는 import문 이용
* 런타임시 module loader(CommonJS, require.js)가 현재 module 실행 전에 다른 module에 대해 가져오고 실행

## Export
```typescript
export interface StringValidator {
  isAcceptable(s: string): boolean;
}
```

```typescript
export const numberRegexp = /^[0-9]+$/;

export class ZipCodeValidator implements StringValidator {
  isAcceptable(s: string) {
    return s.length === 5 && numberRegexp.test(s);
  }
}
```

* rename
```typescript
class ZipCodeValidator implements StringValidator {
  isAcceptable(s: string) {
    return s.length === 5 && numberRegexp.test(s);
  }
}

export { ZipCodeValidator };
export { ZipCodeValidator as mainValidator };
```

* re-export
```typescript
export class ParseIntBasedZipCodeValidator {
  isAcceptable(s: string) {
    return s.length === 5 && parseInt(s).toString() === s;
  }
}
export { ZipCodeValidator as RegExpBasedZipCodeValidator } from "./ZipCodeValidator";
```

* 한 파일 내 여러 모듈을 한번에 가져올 수 있다.
```typescript
export * from "./StringValidator";
export * from "./LettersOnlyValidator";
export * from "./ZipCodeValidator";
```

## Import
```typescript
import { ZipCodeValidator as ZCV } from "./ZipCodeValidator";
let myValidator = new ZCV();
```

```typescript
import * as validator from "./ZipCodeValidator";
let myValidator = new validator.ZipCodeValidator();
```

* default export : module당 하나
JQuery.d.ts
```typescript
declare let $: JQuery;
export default $;
```

```typescript
import $ from "JQuery";

$("button.continue").html("Next Step...");
```

* 클래스, 함수, 특정 값에 대해서도 default export 가능

## export = 와 import = require()
* 'export ='로 CommonJS와 AMD에서의 export를 호환 가능
* 'export ='된 module은 'import module = require('module')'로 import 해야함

```typescript
let numberRegexp = /^[0-9]+$/;
class ZipCodeValidator {
  isAcceptable(s: string) {
    return s.length === 5 && numberRegexp.test(s);
  }
}
export = ZipCodeValidator;
```

```typescript
import zip = require("./ZipCodeValidator");

let strings = ["Hello", "98052", "101"];

let validator = new zip();

strings.forEach(s => {
  console.log(`"${ s }" - ${ validator.isAcceptable(s) ? "matches" : "does not match" }`);
});
```

* 호환 가능하게끔 쓴 import와 export문은 module loader에 따라 컴파일러가 적절히 바꿔준다.

## dynamic module loading
```typescript
declare function require(moduleName: string): any;

import { ZipCodeValidator as Zip } from "./ZipCodeValidator";

if (needZipValidation) {
  let ZipCodeValidator: typeof Zip = require("./ZipCodeValidator");
  let validator = new ZipCodeValidator();
  if (validator.isAcceptable("...")) { /* ... */ }
}
```

## Ambient Module
* 실제 구현이 아닌 declare로 이루어진 것을 ambient라고 부름
* .d.ts 파일에 정의
* C의 헤더파일(.h)과 비슷한 개념

node.d.ts
```typescript
declare module "url" {
  export interface Url {
    protocol?: string;
    hostname?: string;
    pathname?: string;
  }

  export function parse(urlStr: string, parseQueryString?, slashesDenoteHost?): Url;
}

declare module "path" {
  export function normalize(p: string): string;
  export function join(...paths: any[]): string;
  export var sep: string;
}
```

```typescript
/// <reference path="node.d.ts"/>
import * as URL from "url";
let myUrl = URL.parse("http://www.typescriptlang.org");
```

* shorthand
```typescript
declare module "hot-new-module";
```

```typescript
import x, {y} from "hot-new-module";
x(y);
```
* 아래와 같은 의미

```typescript
export class y { }
const x = (someVar: y) => { /* */ };
export default x;
```

## JS가 아닌 것을 import
* semantic 표현으로 *을 달아준다.

```typescript
declare module "*!text" {
  const content: string;
  export default content;
}

declare module "json!*" {
  const value: any;
  export default value;
}
```

## UMD module
* 여러 module loader에서 사용되거나, global로 사용되는 module

math-lib.d.ts
```typescript
export function isPrime(x: number): boolean;
export as namespace mathLib;
```

```typescript
import { isPrime } from "math-lib";
isPrime(2);
mathLib.isPrime(2); // 오류: 모듈 내부에서 전역 정의를 할 수 없습니다.
```

* script(import나 export가 없는 파일)에서 global 변수로 이용 가능
```typescript
mathLib.isPrime(2);
```

## Module 구조를 어떻게 잘 짤 수 있을까?
### 가능한 top-level로 export
* 중첩이 적도록
* 예를 들어, module 내에서 namespace를 export하는 것 X
* 혹은 export class 내에 static method를 넣는 것 X -> helper 함수를 export 하는 방향으로 해결하자.

### 1개의 함수나 class만 export 하는 경우, export default를 사용하자.
* 원하는 object에 접근하기 위해 추가적으로 점을 찍을 필요도 없어짐

### 여러개를 export할 때, 가장 위에 두기

### 여러개를 import할 때, namespace import 패턴을 사용하자.
```typescript
export class Dog { ... }
export class Cat { ... }
export class Tree { ... }
export class Flower { ... }
```

```typescript
import * as myLargeModule from "./MyLargeModule.ts";
let x = new myLargeModule.Dog();
```

### module 확장을 위한 re-export
* 원본 object를 변형하지 않고, 확장된 기능을 하는 새 object를 export
```typescript
export class Calculator {
  ...
}

export function test(c: Calculator, input: string) {
  ...
}
```

```typescript
import { Calculator } from "./Calculator";

class ProgrammerCalculator extends Calculator {
  ...
}

export { ProgrammerCalculator as Calculator };
// helper 함수도 export
export { test } from "./Calculator";
```