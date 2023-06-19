### interface 与 type 关键字的异同点

1. interface 可以实现接口合并，type 不行
2. interface 仅限于描述对象类型
3. type 可以定义基本类型别名
4. type 可以声明联合类型、元组类型

```typescript
//interface描述函数类型
interface IType2 {
  (name: string): void;
}
let fun: IType2 = function (name: string) {
  console.log(name);
};
```

5. 继承

```typescript
//interface
interface Person {}
interface Student extends Person {
  school: string;
}
//type
type Student = Person & { school: string };
```

### 命名空间

- 必须在命名空间内export导出、外部成员才能访问到命名空间内部的类型

1. 解决重名问题

2. 同文件使用

   ```typescript
   namespace SomeNameSpaceName {
     export interface ISomeInterfaceName {
       name: string;
       age: number;
     }
     export class SomeClassName {}
   }
   //同文件下使用
   let snsn: SomeNameSpaceName.ISomeInterfaceName = {
     name: "string",
     age: 20,
   };
   ```

3. 其他文件使用三斜杠引用(==三斜线指令==)

   ```typescript
   /// <reference path = "test.ts" />
   let demo: SomeNameSpaceName.ISomeInterfaceName = { name: "", age: 20 };
   ```

4. 命名空间合并

   ```typescript
   namespace Animals {
     export class Zebra {}
   }
   namespace Animals {
     export interface Legged {
       numberOfLegs: number;
     }
     export class Dog {}
   }
   ///等同于
   namespace Animals {
     export interface Legged {
       numberOfLegs: number;
     }
     export class Zebra {}
     export class Dog {}
   }
   ```

### 三斜线指令

1. `/// <reference path = "test.ts" />`

   导入该路径下的所有类型

2. `/// <reference types="node" />`

   表明这个文件使用了`@types/node/index.d.ts`里面声明的名字

### 声明合并

1. 合并接口

```typescript
interface Box {
  height: number;
  width: number;
  e;
}

interface Box {
  scale: number;
}
//此时box含有两次声明的所有类型
let box: Box = { height: 5, width: 6, scale: 10 };
```

### 关键字

1. declear  该关键字用于声明全局变量(一般在声明文件中使用 .d.ts)

```typescript
declare namespace myLib {
  interface MyInterface {
    // ...
  }
  class MyClass {
    // ...
  }
  function myFunction(): void;
}
declare var $: any;
declare var jQuery: any;
```

2. keyof  (取对象类型的 key 类型)

   ```typescript
   interface Person {
     name: string;
     age: number;
   }
   type keyPer = keyof Person; //'name' | 'age'
   ```
3. extends

   - 定义类时为继承自某个父类

     ```typescript
     class stu extends second implements Student, Person
     ```

   - 条件判断

     ```typescript
     //此情况下 T必须包含Student的所有类型
     type temp<T> = T extends Student ? Student : { test: string };
     ```

   - interface 继承

     ```typescript
     interface Person{
     }
     type Person1{
     }
     //1 interface interface继承
     interface Student extends Person {
         school:string
     }
     //2 interface type继承
     interface Student extends Person1 {
         school:string
     }
     ```

     `type继承可以使用交叉类型&`

   - 可以在泛型内使用

     ```typescript
     type testRange1<T extends testRange> = T;
     ```

4. implements

   用于定义类的类型多个使用逗号分隔

   ```typescript
   class stu extends second implements Student, Person
   ```

### 联合类型（ | ）和交叉类型 （ & ）

### 类型保护

1. 类型断言

2. 使用==is==关键字判断避免重复使用类型断言

```typescript
let pet = getSmallPet();
//避免重复类型断言避免出错
if ((<Fish>pet).swim) {
  (<Fish>pet).swim();
} else {
  (<Bird>pet).fly();
}
//使用判断方法一劳永逸
type arrOrStr = Array<any> | string;
let arr1: arrOrStr = [];
//使用is关键字判断类型
function testArr(params: arrOrStr): params is Array<any> {
  return Array.isArray(params);
}

if (testArr(arr1)) {
  //此后不再需要类型断言
  arr1.push();
} else {
}
```

3. 使用 typeof 进行类型断言（js 中的基本数据类型）

4. 使用 instanceof 判断（构造函数，类）

### 高阶类型方法


```typescript
//1. Pick 获取目标类型的某一项
type Pobj = Pick<Iobj1, "gender"> & Pick<Iobj1, "age">;
type PickObj = Pick<obj, "a" | "b">;
//2. Omit 获取目标类型除指定属性的其他所有类型
type OmObj = Omit<Iobj1, "gender">;
//3. Exculde 去除联合类型中的某一项
type strI = "age" | "name" | "gender";
type Eobj1 = Exclude<strI, "age">; //'name'|‘gender’
//4. Extract 与Exculde相反 取联合类型中除选定类型的其他所有类型
type strI = "age" | "name" | "gender";
type Eobj1 = Exclude<strI, "age">; //‘age’
//5. Partial 将传入的属性由非可选变为可选返回
type ptl = Partial<obj>;
//6. Required 将传入的属性由可选变为必选
type RequiredObj = Required<PartialObj>;
//7. Readonly 将所有的属性变为只读返回
type Irdol = Readonly<Iptl>;
//8. Record 示例中 最终该类型拥有两个属性（x、y）,且类型都为obj
type RecordObj = Record<"x" | "y", obj>;
//9. ReturnType 获取函数的返回值类型
//10. Parameters 获取函数的参数类型
type AppendArgument<F extends (...args: any[]) => any, A> = (x: A, ...args: Parameters<F>) => ReturnType<F>;
 - infer方式
type AppendArgument<F extends (...args: any) => any, T> = F extends (
  ...args: infer P
) => infer Return
  ? (x: T, ...args: P) => Return
  : never;

```

- ==类型推导 infer==

  **推导泛型参数**
  
  ```typescript
  type inferType<T extends string[]> = T extends [infer First, ...infer Rest]
    ? First
    : never;
  type tempInferType = inferType<["1", "2456"]>; //'1'
  
  //可以用来推断函数的返回值
  type ReturnType<T> = T extends (...args: any[]) => infer R ? R : any;
  
  type func = () => number;
  type variable = string;
  type funcReturnType = ReturnType<func>; // funcReturnType 类型为 number
  type varReturnType = ReturnType<variable>; // varReturnType 类型为 any
  
  type NaiveFlat<T extends any[]> = T extends (infer P)[] ? (P extends any[] ? NaiveFlat<P> : P) : never;
  
  
  ```
  
- 巧用断言解决问题

  ```typescript
  type ConditionalPick<V, T> = {
    [K in keyof V as V[K] extends T ? K : never]: V[K];
  };
  //提取obj中属性类型为string的属性返回
  type test = ConditionalPick<obj, string>;
  ```

  
### 函数重载

- 当函数参数为联合类型时 可用函数重载解决

```typescript
function f(arg1: string, arg2: string): string;
function f(arg1: number, arg2: number): number;
function f(arg1: string | number, arg2: string | number) {
  if (typeof arg1 == "string" && typeof arg2 == "string") {
    return arg1 + arg2;
  } else if (typeof arg1 == "number" && typeof arg2 == "number") {
    return arg1 + arg2;
  }
}
```

### 关于...T[]

```typescript
type Head2<T extends Array<any>> = T extends [infer H, ...T[number]] ? T[number] : never;
type t6 = Head2<[1,2,3]>//1|2|3
//此处T为传入的数组，也就是[1,2,3]，T[number]可以理解为按照索引取元素内容的类型
//相当于
[1,2,3][0] | [1,2,3][1] | [1,2,3][1] 
//可以得到 1|2|3

type ItemType<T extends Array<any>> = T[number]
```

### 逆变和协变

- 候选类型处于协变位置会被推断为联合类型

  ```typescript
  type Foo<T> = T extends { a: infer U, b: infer U } ? U : never;
  type T10 = Foo<{ a: string, b: string }>;  // string
  type T11 = Foo<{ a: string, b: number }>;  // string | number
  ```

- 候选类型处于逆变位置会被推断为交叉类型

  ```typescript
  type Bar<T> = T extends { a: (x: infer U) => void, b: (x: infer U) => void } ? U : never;
  type T20 = Bar<{ a: (x: string) => void, b: (x: string) => void }>;  // string
  type T21 = Bar<{ a: (x: string) => void, b: (x: number) => void }>;  // string & number
  ```


### .d.ts类型声明文件

- 只能定义类型 

- 使用declare声明全局变量

- 定义模块文件

  ```typescript
  //.d.ts文件定义
  declare module 'myModule' {
    export function doSomething(): void;
  }
  //其他文件引用
  import { doSomething } from 'myModule';
  ```


### 杂项

- 将枚举转换为联合类型

  ```typescript
  keyof typeof HttpMethods 取key
  
  type IGetVals<T> = T extends {[key:string]:infer R } ? R : never;
  IGetVals<typeof HttpMethods>  取val
  
  将某个变量的类型指定为 枚举 则赋值需要使用该枚举赋值
  ```


### 装饰符

- 类装饰器

```typescript
function classDecorator<T extends {new(...args:any[]):{}}>(constructor:T) {
    return class extends constructor {
        newProperty = "new property";
        hello = "override";
    }
}

@classDecorator
class Greeter {
    property = "property";
    hello: string;
    constructor(m: string) {
        this.hello = m;
    }
}

console.log(new Greeter("world"));
//output: hello:"override", property = "property"; newProperty = "new property";
```

- 方法装饰器

```typescript
function enumerable(value: boolean) {
    return function (target: any, propertyKey: string, descriptor: PropertyDescriptor) {
        descriptor.enumerable = value;
    };
}

class Greeter {
    greeting: string;
    constructor(message: string) {
        this.greeting = message;
    }

    @enumerable(false) // 在类中将上面的方法设置为装饰器
    greet() {
        return "Hello, " + this.greeting;
    }
}

```

- 访问器装饰器 get set

  ```typescript
  function configurable(value: boolean) {
      return function (target: any, propertyKey: string, descriptor: PropertyDescriptor) {
          descriptor.configurable = value;
      };
  }
  class Point {
      private _x: number;
      private _y: number;
      constructor(x: number, y: number) {
          this._x = x;
          this._y = y;
      }
  
      @configurable(false)
      get x() { return this._x; }
  
      @configurable(false)
      get y() { return this._y; }
  }
  ```

- 属性装饰器

```typescript
function validate(target: any, key: string, descriptor: PropertyDescriptor) {
  let setter = descriptor.set

  descriptor.set = function (value: any) {
    if (value === undefined || value === null || value === '') {
      throw new Error(`Invalid value for ${key}: ${value}`)
    }

    setter.call(this, value)
  }
}

class Person {
  private _name: string = ''

  @validate
  set name(value: string) {
    this._name = value
  }

  get name() {
    return this._name
  }
}

let p = new Person()
p.name = '' // 报错：Invalid value for name: 
```

- 参数装饰器

  ```typescript
  function log(target: any, key: string, index: number) {
    let originalMethod = target[key]
  
    target[key] = function (...args: any[]) {
      console.log(`Calling ${key} with arguments: ${JSON.stringify(args)}`)
  
      return originalMethod.apply(this, args)
    }
  }
  
  class Person {
    greet(@log name: string) {
      console.log(`Hello, ${name}`)
    }
  }
  
  let p = new Person()
  p.greet('limeng') // 打印日志：Calling greet with arguments: ["limeng"]，Hello, limeng
  
  ```

### 静态成员和实例成员

- 实例成员只能在类被实例化后才能访问
- 静态成员 有static前缀关键字

```typescript
class MyClass {
  static myStaticProperty = 42;
  myInstanceProperty = "hello";

  static myStaticMethod() {
    console.log("I am a static method");
  }

  myInstanceMethod() {
    console.log("I am an instance method");
  }
}
//静态成员可以直接通过类名访问静态成员
console.log(MyClass.myStaticProperty); // 42
MyClass.myStaticMethod(); // "I am a static method"

//实例成员只能在实例化后才能正常访问
const myInstance = new MyClass();
console.log(myInstance.myInstanceProperty); // "hello"
myInstance.myInstanceMethod(); // "I am an instance method"
```

- 执行顺序

  ```typescript
  @classDecorator1
  @classDecorator2
  class Animal {
    constructor(@constructorParamsDecorator options) {
  
    }
  
    @staticPropertyDecorator
    static Name = 'zlx'
  
    @staticFuncDecorator
    static Say(@staticParamsDecorator name: string) {
  
    }
  
    @instancePropertyDecorator
    age = 11
  
    @instanceFuncDecorator
    run(@instanceParamsDecorator time: number) {
    }
  }
  
  // instance property decorator
  // instance params decorator
  // instance func decorator
  // static property decorator
  // static params decorator
  // static func decorator
  // constructor params decorator
  // class decorator2
  // class decorator1
  ```

### 取函数的参数为泛型类型

```typescript
//在参数前加泛型定义 
type IRender = <K extends keyof HTMLElementTagNameMap>(tag: K, options: keyof HTMLElementTagNameMap[K]) => HTMLElementTagNameMap[K];
```

### tsconfig.json

1. `declaration`:是否生成声明文件
2. `declarationDir`:声明文件的文件夹
3. `noEmit`:是否不生成编译文件
4. `include`:编译哪些文件
5. `types`:声明会被包含进来的包如【‘lodash’】
6. `typeroots`:不声明该字段默认引入node_modules下所有的@types的包，声明后引入该目录下的包
7. `baseUrl`:ts文件导出导入时 的基准路径

```json
{
  "compilerOptions": {
    /* Basic Options */
    "target": "es5" /* target用于指定编译之后的版本目标: 'ES3' (default), 'ES5', 'ES2015', 'ES2016', 'ES2017', 'ES2018', 'ES2019' or 'ESNEXT'. */,
    "module": "commonjs" /* 用来指定要使用的模块标准: 'none', 'commonjs', 'amd', 'system', 'umd', 'es2015', or 'ESNext'. */,
    "lib": ["es6", "dom"] /* lib用于指定要包含在编译中的库文件 */,
    "allowJs": true,                       /* allowJs设置的值为true或false，用来指定是否允许编译js文件，默认是false，即不编译js文件 */
    "checkJs": true,                       /* checkJs的值为true或false，用来指定是否检查和报告js文件中的错误，默认是false */
    "jsx": "preserve",                     /* 指定jsx代码用于的开发环境: 'preserve', 'react-native', or 'react'. */
    "declaration": true,                   /* declaration的值为true或false，用来指定是否在编译的时候生成相应的".d.ts"声明文件。如果设为true，编译每个ts文件之后会生成一个js文件和一个声明文件。但是declaration和allowJs不能同时设为true */
    "declarationMap": true,                /* 值为true或false，指定是否为声明文件.d.ts生成map文件 */
    "sourceMap": true,                     /* sourceMap的值为true或false，用来指定编译时是否生成.map文件 */
    "outFile": "./dist/main.js",                       /* outFile用于指定将输出文件合并为一个文件，它的值为一个文件路径名。比如设置为"./dist/main.js"，则输出的文件为一个main.js文件。但是要注意，只有设置module的值为amd和system模块时才支持这个配置 */
    "outDir": "./dist",                        /* outDir用来指定输出文件夹，值为一个文件夹路径字符串，输出的文件都将放置在这个文件夹 */
    "rootDir": "./",                       /* 用来指定编译文件的根目录，编译器会在根目录查找入口文件，如果编译器发现以rootDir的值作为根目录查找入口文件并不会把所有文件加载进去的话会报错，但是不会停止编译 */
    "composite": true,                     /* 是否编译构建引用项目  */
    "removeComments": true,                /* removeComments的值为true或false，用于指定是否将编译后的文件中的注释删掉，设为true的话即删掉注释，默认为false */
    "noEmit": true,                        /* 不生成编译文件，这个一般比较少用 */
    "importHelpers": true,                 /* importHelpers的值为true或false，指定是否引入tslib里的辅助工具函数，默认为false */
    "downlevelIteration": true,            /* 当target为'ES5' or 'ES3'时，为'for-of', spread, and destructuring'中的迭代器提供完全支持 */
    "isolatedModules": true,               /* isolatedModules的值为true或false，指定是否将每个文件作为单独的模块，默认为true，它不可以和declaration同时设定 */
 
    /* Strict Type-Checking Options */
    "strict": true /* strict的值为true或false，用于指定是否启动所有类型检查，如果设为true则会同时开启下面这几个严格类型检查，默认为false */,
    "noImplicitAny": true,                 /* noImplicitAny的值为true或false，如果我们没有为一些值设置明确的类型，编译器会默认认为这个值为any，如果noImplicitAny的值为true的话。则没有明确的类型会报错。默认值为false */
    "strictNullChecks": true,              /* strictNullChecks为true时，null和undefined值不能赋给非这两种类型的值，别的类型也不能赋给他们，除了any类型。还有个例外就是undefined可以赋值给void类型 */
    "strictFunctionTypes": true,           /* strictFunctionTypes的值为true或false，用于指定是否使用函数参数双向协变检查 */
    "strictBindCallApply": true,           /* 设为true后会对bind、call和apply绑定的方法的参数的检测是严格检测的 */
    "strictPropertyInitialization": true,  /* 设为true后会检查类的非undefined属性是否已经在构造函数里初始化，如果要开启这项，需要同时开启strictNullChecks，默认为false */
   "noImplicitThis": true,                /* 当this表达式的值为any类型的时候，生成一个错误 */
    "alwaysStrict": true,                  /* alwaysStrict的值为true或false，指定始终以严格模式检查每个模块，并且在编译之后的js文件中加入"use strict"字符串，用来告诉浏览器该js为严格模式 */
 
    /* Additional Checks */
    "noUnusedLocals": true,                /* 用于检查是否有定义了但是没有使用的变量，对于这一点的检测，使用eslint可以在你书写代码的时候做提示，你可以配合使用。它的默认值为false */
    "noUnusedParameters": true,            /* 用于检查是否有在函数体中没有使用的参数，这个也可以配合eslint来做检查，默认为false */
    "noImplicitReturns": true,             /* 用于检查函数是否有返回值，设为true后，如果函数没有返回值则会提示，默认为false */
    "noFallthroughCasesInSwitch": true,    /* 用于检查switch中是否有case没有使用break跳出switch，默认为false */
 
    /* Module Resolution Options */
    "moduleResolution": "node",            /* 用于选择模块解析策略，有'node'和'classic'两种类型' */
    "baseUrl": "./",                       /* baseUrl用于设置解析非相对模块名称的基本目录，相对模块不会受baseUrl的影响 */
    "paths": {},                           /* 用于设置模块名称到基于baseUrl的路径映射 */
    "rootDirs": [],                        /* rootDirs可以指定一个路径列表，在构建时编译器会将这个路径列表中的路径的内容都放到一个文件夹中 */
    "typeRoots": [],                       /* typeRoots用来指定声明文件或文件夹的路径列表，如果指定了此项，则只有在这里列出的声明文件才会被加载 */
    "types": [],                           /* types用来指定需要包含的模块，只有在这里列出的模块的声明文件才会被加载进来 */
    "allowSyntheticDefaultImports": true,  /* 用来指定允许从没有默认导出的模块中默认导入 */
    "esModuleInterop": true /* 通过为导入内容创建命名空间，实现CommonJS和ES模块之间的互操作性 */,
    "preserveSymlinks": true,              /* 不把符号链接解析为其真实路径，具体可以了解下webpack和nodejs的symlink相关知识 */
 
    /* Source Map Options */
    "sourceRoot": "",                      /* sourceRoot用于指定调试器应该找到TypeScript文件而不是源文件位置，这个值会被写进.map文件里 */
    "mapRoot": "",                         /* mapRoot用于指定调试器找到映射文件而非生成文件的位置，指定map文件的根路径，该选项会影响.map文件中的sources属性 */
    "inlineSourceMap": true,               /* 指定是否将map文件的内容和js文件编译在同一个js文件中，如果设为true，则map的内容会以//# sourceMappingURL=然后拼接base64字符串的形式插入在js文件底部 */
    "inlineSources": true,                 /* 用于指定是否进一步将.ts文件的内容也包含到输入文件中 */
 
    /* Experimental Options */
    "experimentalDecorators": true /* 用于指定是否启用实验性的装饰器特性 */
    "emitDecoratorMetadata": true,         /* 用于指定是否为装饰器提供元数据支持，关于元数据，也是ES6的新标准，可以通过Reflect提供的静态方法获取元数据，如果需要使用Reflect的一些方法，需要引入ES2015.Reflect这个库 */
  }
  "files": [], // files可以配置一个数组列表，里面包含指定文件的相对或绝对路径，编译器在编译的时候只会编译包含在files中列出的文件，如果不指定，则取决于有没有设置include选项，如果没有include选项，则默认会编译根目录以及所有子目录中的文件。这里列出的路径必须是指定文件，而不是某个文件夹，而且不能使用* ? **/ 等通配符
  "include": [],  // include也可以指定要编译的路径列表，但是和files的区别在于，这里的路径可以是文件夹，也可以是文件，可以使用相对和绝对路径，而且可以使用通配符，比如"./src"即表示要编译src文件夹下的所有文件以及子文件夹的文件
  "exclude": [],  // exclude表示要排除的、不编译的文件，它也可以指定一个列表，规则和include一样，可以是文件或文件夹，可以是相对路径或绝对路径，可以使用通配符
  "extends": "",   // extends可以通过指定一个其他的tsconfig.json文件路径，来继承这个配置文件里的配置，继承来的文件的配置会覆盖当前文件定义的配置。TS在3.2版本开始，支持继承一个来自Node.js包的tsconfig.json配置文件
  "compileOnSave": true,  // compileOnSave的值是true或false，如果设为true，在我们编辑了项目中的文件保存的时候，编辑器会根据tsconfig.json中的配置重新生成文件，不过这个要编辑器支持
  "references": [],  // 一个对象数组，指定要引用的项目
}
```

> 如果只想生成声明文件而不想生成js文件可以使用
>
> ```typescript
> tsc --emitDeclarationOnly || tsc -d
> ```
>
> 