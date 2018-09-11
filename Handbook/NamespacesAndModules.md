# Namespaces and Modules
* [docs](https://www.typescriptlang.org/docs/handbook/namespaces-and-modules.html)

## namespace 사용
* namespace도 결국 object
* --outfile로 여러 파일을 연결
* 큰 규모의 프로젝트에서는 주의(전역 변수느낌의 문제)

## module 사용
* namespace와 마찬가지로 code와 declaration이 섞여있음
* 그럼 namespace와의 차이는?
* module은 dependency를 declare
* 또한, module loader(CommonJS, Require.js 등)에 의존
* 주로 대규모 프로젝트에 유용
* 코드 재사용 유리, isolation, bundling을 위한 tool을 제공 
* node에선 module이 기본 단위처럼 이용
* ES6부터 기본 요소

## namespace와 module 사용시 주의해야 할 점
### /// <reference>-ing a module
* import문을 사용하는 대신 위의 reference tag를 쓰는 경우가 종종 있음
* 차이가 뭘까?
* 컴파일러는 .ts, .tsx, .d.ts 파일을 찾으려 할 것. 못찾으면 .d.ts 내의 ambient module을 찾음.

myModules.d.ts
```typescript
// module이 아닌 .d.ts 파일 or .ts 파일
declare module "SomeModule" {
  export function fn(): string;
}
```

myOtherModule.ts
```typescript
/// <reference path="myModules.d.ts" />
import * as m from "SomeModule";
```

* reference tag가 ambient module을 찾아줌
* typescript 내에서 node.d.ts가 주로 이런식으로 쓰임

### 불필요한 namespace

shape.ts
```typescript
export namespace Shapes {
  export class Triangle { /* ... */ }
  export class Square { /* ... */ }
}
```

* Shapes로 또 감쌀 필요성? 없다.

shapeConsumer.ts
```typescript
import * as shapes from "./shapes";
let t = new shapes.Shapes.Triangle(); // shapes.Shapes?
```

* 사용할 때 이름을 정하기 때문에 굳이 wrap해놓을 필요가 없다.
* namespace는 어떤 단위들을 grouping 하고, 서로 충돌하는 것을 막고자 사용
* 파일 자체가 이미 logically grouping 되어있는데 굳이 또 한 layer를 추가하는 건 불필요하다.
* 따라서, 아래처럼 쓰는 것이 바람직

shape.ts
```typescript
export class Triangle { /* ... */ }
export class Square { /* ... */ }
```

shapeConsumer.ts
```typescript
import * as shapes from "./shapes";
let t = new shapes.Triangle();
```

## trade-offs of module
* ts도 파일하나에 모듈하나
* 따라서, 여러 파일을 하나의 모듈로 묶을 수 없는 module system(commonjs, umd)도 있다.
* 하지만 ts 1.8 버전 이후부터는 outFile keyword로 위 시스템에서도 하나로 묶는게 가능하다.