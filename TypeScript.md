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

