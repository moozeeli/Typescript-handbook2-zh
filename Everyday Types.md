

原始类型：string number boolean

## Arrays
两种方式表示：

1. string[]
1. Array<number>

第二种方式是范型
​

## `any`
any 是一个特殊类型，any包容万物。
当一个值的类型是 any, 你可以访问它上面的任何properties。
​

noImplicitAny
（这是一个配置项）
当你不指定类型时，ts没发推断出类型，就会认为这个值的类型为any。
如果不允许一个值被认为是any, 就要修改这个配置。
​

## Type Annotations on Variables
在变量上进行类型注解
​

语法很简单：
```typescript
let myName:string = 'aaa'
```
`:string` 就是注解
​

变量一般不需要做注解，因为ts能根据类型推论得到变量的类型。
ts 官方建议尽量少的使用注解，把工作都交给类型推论
​



## Functions
ts 允许你对函数指定输入类型和输出类型
### Parameter Type Annotations
参数类型类型很简单，与变量注解类似
```typescript
// Parameter type annotation
function greet(name: string) {
  console.log("Hello, " + name.toUpperCase() + "!!");
}
```
默认情况下，函数的参数是无法被准确推论出类型的，所以会认为是any类型。
那，在设置 `noImplicitAny` 为 true 的情况下, 就必须对函数参数类型做注解了
### Return Type Annotations
语法如下：
```typescript
function getFavoriteNumber(): number {
  return 26;
}
```
通常不需要对函数的返回值做注解，因为ts能够基于 return 语句推断出返回值类型。
​

### Anonymous Functions
匿名函数一般是作为回调函数使用的，与普通函数不同的是，ts 能正确识别匿名函数的参数类型
```typescript
// No type annotations here, but TypeScript can spot the bug
const names = ["Alice", "Bob", "Eve"];
 
// Contextual typing for function
names.forEach(function (s) {
  console.log(s.toUppercase());
  							^^^^^^^^^^^^^
//   Property 'toUppercase' does not exist on type 'string'. Did you mean 'toUpperCase'?
});

```
ts 能根据匿名函数的上下文分析出匿名函数的参数类型，官方把这种类型叫做  _contextual typing_  即 上下文类型。
​

## Object Types 
对象类型的写法，看一下官方给的示例：
```typescript
// The parameter's type annotation is an object type
function printCoord(pt: { x: number; y: number }) {
  console.log("The coordinate's x value is " + pt.x);
  console.log("The coordinate's y value is " + pt.y);
}
printCoord({ x: 3, y: 7 });
```
上面的代码中 函数 `printCoord` 的参数 pt 是一个对象类型，表明了pt 是一个具有两个属性x,y 的对象，而且这两个属性都是`number`类型。
对象类型的属性后面可以是 ; 也可以是， 而且最后一个属性的结尾 可以不带符号，像下面的写法都是合法的：
```typescript
{ x: number; y: number; } // 分号结尾
{ x: number, y: number, } // 逗号结尾
{ x: number; y: number }  //  最后属性一个不带分隔符
```
### Optional Properties
对象类型的可选属性，语法很简单：
```typescript
function printName(obj: { first: string; last?: string }) {
  // ...
}
```
## Union Types
```typescript
function printId(id: number | string) {
  console.log("Your ID is: " + id);
}
// OK
printId(101);
// OK
printId("202");
```
## Type Aliases
类型别名有点像js中的变量，就是给类型起个名字。
但是别名与变量不同，别名只是给类型起了个名字，而不会创建一个新的类型。
```typescript
type a = number;
let a:a = 1; 
```
类型别名可以给对象类型命名：
```typescript
type  Point = {
  x: number;
  y: number;
};
```


## Interfaces
interface 是定义命名对象类型的另一个方法。
```typescript
interface Point{
  x: number;
  y: number;
};
```
### Differences Between Type Aliases and Interfaces
Interface 与 别名的区别
interface 可以使用extends 进行扩展
type 可以使用交叉进行扩展
​

关键区别是 interface 可以向同一个对象类型添加属性 ，tyeps 不能添加
具体查看[官网示例](https://www.typescriptlang.org/docs/handbook/2/everyday-types.html#differences-between-type-aliases-and-interfaces)
​

## Type Assertions
两种写法 xx as T, <T>xx;
```typescript
const myCanvas = document.getElementById("main_canvas") as HTMLCanvasElement;
```
```typescript
const myCanvas = <HTMLCanvasElement>document.getElementById("main_canvas");
```
TypeScript only allows type assertions which convert to a _more specific_ or _less specific_ version of a type.
​

类型断言只允许转化成更明确的类型或更宽松的类型，不能转化成完全不同的乐行：
```typescript
const x = 'hello' as number;
									^^^^^^^^^
                   不允许
```
如果必须将一个类型转化成不同的类型，进行两次类型断言，像这样：
```typescript
const a = (expr as any) as T;
```
## Literal Types
字面类型
默认情况下的类型推断，智能推断出这个值是 string /number/boolean /any 等内置类型
​

使用字面类型，可以表明这个值的选项:
```typescript
let x: "hello" = "hello";

x = 'hello' // 正常执行

x = 'world' 
^
  报错
```
上面使用类型注解，表明x 必须是 'hello' 字面类型，一位置 x必须是 字符串'hello'
​

可以使用 联合类型将字面类型结合起来：
```typescript
let letter:'a'|'b'|'c' = 'a'
letter = 'a';
letter = 'b';
letter = 'c';

letter = 'd';
^^^^^^
  报错


```
字面类型也可以和其他非字面类型进行进行组合
```typescript
interface Options {
  width: number;
}
function configure(x: Options | "auto") {
  // ...
}
configure({ width: 100 });
configure("auto");
configure("automatic");
					^^^^^^^^^^^
            错误的值
```
### Literal Inference
字面推断
​

在ts中对象的properties 被认为是会被修改的，所以只推断出内置类型，比如：
```typescript
const obj = { counter: 0 };
if (someCondition) {
  obj.counter = 1;
}
```
obj.counter 有可能被修改，所以TypeScript 认为 obj.counter 是number类型，而不是字面值类型 0
​

在这种情况下， 下面的代码就会报错：[playground](https://www.typescriptlang.org/play?#code/FAMwrgdgxgLglgewgAgBYEMIBMA2BTAJTwEcw8BnGACjACccAuS2uCAcwBoBbPGVBLAwDkAcQCiAFSEAfIQAUA8gGUpASgDewZMgD0O5ADojwAL7BgUJJWS0SyALzJ1yOo2QAiVDBgAHcgz08AA90Lh98A0sudw5kHj4BBg9xCXdkEwBuYAxsfCJSCmpbYgNXWPcU91UsnNxCEjJKKmLS+liW+P4savNeoA)
```typescript

function handleRequest(url:string,method:'GET'|'POST'){
  // ...
}

const req = { url: "https://example.com", method: "GET" };
handleRequest(req.url, "GET");
handleRequest(req.url, req.method);
												^^^^^^^^^
```
这是因为 `handleRequest(req.url, req.method)` 中的 req.method 是 string 类型，而不是更具体的字面值类型。
​

两种方式解决：

1. 使用Type Assertions 修改 method 的类型
   1. `const req = { url:"https://example.com", method:"GET"as"GET" };`
   1. `handleRequest(req.url, req.method as "GET");`

两种写法都可以

2. 使用 as const 将 整个对象转化成 字面值类型，[playground](https://www.typescriptlang.org/play?#code/FAMwrgdgxgLglgewgAgBYEMIBMA2BTAJTwEcw8BnGACjACccAuS2uCAcwBoBbPGVBLAwDkAcQCiAFSEAfIQAUA8gGUpASgDewZMgD0O5ADojwAL7BgUJJWS0SyALzJ1yOo2QAiVDBgAHcgz08AA90Lh98A0sudw5kHj4BBg9xCXdkE2R0cmRLCEoAbmAMbHwiUgpqW2IDV1iqg3j+LFVC8yA)

`const req = { url:"https://example.com", method:"GET" } as const;`
as  const 将 req 对象 断言为一个所有属性都只读的常量对象, [查看](https://www.typescriptlang.org/play?#code/FAMwrgdgxgLglgewgAgBYEMIBMA2BTAJTwEcw8BnGACjACccAuS2uCAcwBoBbPGVBLAwDkAcQCiAFSEAfIQAUA8gGUpASgDewZMgD0O5ADojwAL7BgUJJWS0SyALzJ1yOo2QAiVDBgAHcgz08AA90Lh98A0sudw5kHj4BBg9xCXdkE2R0cmRLCEoAbmAMbHwiUgpqW2IDV1iqg3j+LFVC8yA)
这就让TypeScript知道 methods 的值不会发生变化，所以推断出它的具体值。
​





## `null `and `undefined`
TypeScript 中有两个对于js 的类型 null 和 undefined
这两个类型的行为取决于 配置项：`strictNullChecks`
当配置 strictNullChecks 为 false 的时候，undefined 值和null 值允许赋值给任何类型；
​

当 strictNullChecks 为 true 的时候，不允许 将 undefined 与 null 赋值给任何类型，这时候需要在代码中做判断：
```typescript
function doSomething(x: string | null) {
  if (x === null) {
    // do nothing
  } else {
    console.log("Hello, " + x.toUpperCase());
  }
}
```
上面 的else 逻辑中中， ts 会 根据 [narrowing](https://www.typescriptlang.org/docs/handbook/2/narrowing.html) 机制判断出 x 的值不会是 null。
​

### Non-null Assertion Operator (Postfix`！`）
非空断言 后缀`！`


TypeScript 有一个特殊语法用于从一个类型中移除 `null` 和 `undefined` ，而不需要将判空的检查写在代码逻辑中：
```typescript
function liveDangerously(x?: number | null) {
  // No error
  console.log(x!.toFixed());
}
```
当使用 ！时，你要确保你知道 这个值不会是 null 或 undefined
## Enums
TypeScript 中的枚举值与类型不同，会被编译到js代码中。
具体请查看[枚举](https://www.typescriptlang.org/docs/handbook/enums.html)
​

## Less Common Primitives
不太常见的元语，这里介绍不太常用的原始类型。
bigint
```typescript
// Creating a bigint via the BigInt function
const oneHundred: bigint = BigInt(100);
 
// Creating a BigInt via the literal syntax
const anotherHundred: bigint = 100n;
```
##### symbol
```typescript
const firstName = Symbol("name");
const secondName = Symbol("name");
 
if (firstName === secondName) {
  
This condition will always return 'false' since the types 'typeof firstName' and 'typeof secondName' have no overlap.
  // Can't ever happen
}
```
