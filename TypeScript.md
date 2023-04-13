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

3. 其他文件使用三斜杠引用

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

### 泛型

Omit 和 Pick

```typescript

```

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

1. declear:该关键字用于声明全局变量

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

2. keyof(取对象类型的 key 类型)

   ```typescript
   interface Person {
     name: string;
     age: number;
   }
   type keyPer = keyof Person; //'name' | 'age'
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

### extends 和 implements

1. extends

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

     `type继承可以使用交叉类型`

   - 可以在泛型内使用

     ```typescript
     type testRange1<T extends testRange> = T;
     ```

2. implements

   ​ 用于定义类的类型多个使用逗号分隔

   ```typescript
   class stu extends second implements Student, Person
   ```

3. 高阶类型方法

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
```

- ==类型推导 infer==

  ```typescript
  type inferType<T extends string[]> = T extends [infer First, ...infer Rest]
    ? First
    : never;
  type tempInferType = inferType<["1", "2456"]>; //'1'
  ```
