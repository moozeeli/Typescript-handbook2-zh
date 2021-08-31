TypeScript 与 JavaScript 有着不同寻常的关系。TypeScript 提供了 JavaScript 的所有功能，以及在这些功能之上的附加层：TypeScript 的类型系统。

例如，JavaScript 提供了诸如` string` 和`number` 之类的语言原语, 但它不会检查你是否始终分配了这些。TypeScript可以做到。

这意味着你现有的 JavaScript 代码也是 TypeScript 代码。 TypeScript 的主要好处是它可以突出显示代码中的意外行为，从而降低出现错误的机会。

本教程简要概述了 TypeScript，重点介绍了它的类型系统。

# 类型推断（Types by Inference）

TypeScript 了解 JavaScript 语言，并且会在许多情况下为你生成类型。 例如，在创建变量并将其分配给特定值时，TypeScript 将使用该值作为其类型。

```typescript
let helloWorld = "Hello World";
			^
		 let helloWorld: string
```

通过了解 JavaScript 的工作原理，TypeScript 可以构建一个接受 JavaScript 代码但具有类型的类型系统。 这提供了一个类型系统，而无需添加额外的字符来使代码中的类型显式。 这就是在上面的实例中 TypeScript 如何知道` helloWorld` 是`string`的方式。

你可能已经在 Visual Studio Code 中编写了 JavaScript，并且拥有编辑器自动完成功能。 Visual Studio Code 在底层使用 TypeScript 来更轻松地使用 JavaScript。

# 类型定义（ Defining Types）

你可以在 JavaScript 中使用多种设计模式。 但是，某些设计模式很难自动推断类型（例如，使用动态编程的模式）。 为了涵盖这些情况，TypeScript 支持 JavaScript 语言的扩展，它为你提供了位置以告诉 TypeScript 类型应该是什么。

例如，要创建一个包含 `name: string` 和 `id: number` 的推断类型的对象，您可以编写：

```typescript
const user = {
  name: "Hayes",
  id: 0,
};
```

您可以使用接口声明显式描述此对象的形状：

```typescript
interface User {
  name: string;
  id: number;
}
```

然后，您可以在变量声明之后使用类似`: TypeName`的语法来声明符合 `interface` 的形状的 JavaScript 对象：

```typescript
const user: User = {
  name: "Hayes",
  id: 0,
};
```

如果你提供的对象与你提供的接口不匹配，TypeScript 会警告你：

```typescript
interface User {
  name: string;
  id: number;
}
 
const user: User = {
  username: "Hayes",
  
//  Type '{ username: string; id: number; }' is not assignable to type 'User'.
//  Object literal may only specify known properties, and 'username' does not exist in type 'User'.
  
  id: 0,
};
```

由于 JavaScript 支持类和面向对象编程，因此 TypeScript 也支持。 您可以对类使用接口声明：

```typescript
interface User {
  name: string;
  id: number;
}
 
class UserAccount {
  name: string;
  id: number;
 
  constructor(name: string, id: number) {
    this.name = name;
    this.id = id;
  }
}
 
const user: User = new UserAccount("Murphy", 1);
```

您可以使用接口来注释函数的参数和返回值：

```typescript
function getAdminUser(): User {
  //...
}
 
function deleteUser(user: User) {
  // ...
}
```

JavaScript 中已经有一小组可用的原始类型：`boolean`, `bigint`, `null`, `number`, `string`, `symbol`, and `undefined`, 您可以在 interface 中使用它。 TypeScript 扩展了这个列表，比如 any（允许任何东西）unknown (确保使用此类型的人声明该类型是什么),  never（这种类型不可能发生） 和 void （返回值为undefined或没有返回值的函数）

您将看到构建类型有两种语法：Interfaces 和 Types。 你应该更喜欢 `interface`。 当您需要特定功能时使用`type`。

## 类型组合 （Composing Types）

使用 TypeScript，您可以通过组合简单类型来创建复杂类型。 有两种流行的方法可以做到这一点：使用联合（Unions）和使用泛型（Generics）。

## Unions

使用联合（union），您可以声明一个类型可以是多种类型之一。 例如，您可以将`boolean`类型描述为 `true` 或 `false`：

```typescript
type MyBool = true | false;
```

注意：如果您将鼠标悬停在上面的 `MyBool` 上，您会看到它被归类为`boolean`。 这是结构类型系统的一个属性。 

联合类型(union type)的一个流行用例是描述一个允许`string`或`number`字面值的集合：

```typescript
type WindowStates = "open" | "closed" | "minimized";
type LockStates = "locked" | "unlocked";
type PositiveOddNumbersUnderTen = 1 | 3 | 5 | 7 | 9;
```

联合(Unions)也提供了一种处理不同类型的方法。例如，您可能有一个接受`array`或`string`的函数：

```typescript
function getLength(obj: string | string[]) {
  return obj.length;
}
```

要了解变量的类型，请使用 `typeof`：

| Type 类型 | Predicate 谓词                     |
| :-------- | :--------------------------------- |
| string    | `typeof s === "string"`            |
| number    | `typeof n === "number"`            |
| boolean   | `typeof b === "boolean"`           |
| undefined | `typeof undefined === "undefined"` |
| function  | `typeof f === "function"`          |
| array     | `Array.isArray(a)`                 |

例如，您可以根据传递的是字符串还是数组，使函数返回不同的值：

```typescript
function wrapInArray(obj: string | string[]) {
  if (typeof obj === "string") {
    return [obj];
            
(parameter) obj: string
  }
  return obj;
}
```

# 范型 Generics

泛型为类型提供变量。 一个常见的例子是数组。 没有泛型的数组可以包含任何东西。 带有泛型的数组可以描述数组包含的值。

```typescript
type StringArray = Array<string>;
type NumberArray = Array<number>;
type ObjectWithNameArray = Array<{ name: string }>;
```

您可以声明自己的使用泛型的类型：

```typescript

interface Backpack<Type> {
  add: (obj: Type) => void;
  get: () => Type;
}
// This line is a shortcut to tell TypeScript there is a
// constant called `backpack`, and to not worry about where it came from.
declare const backpack: Backpack<string>;
 
// object is a string, because we declared it above as the variable part of Backpack.
const object = backpack.get();
 
// Since the backpack variable is a string, you can't pass a number to the add function.
backpack.add(23);
>> Argument of type 'number' is not assignable to parameter of type 'string'.

```

# Structural Type System 解构类型系统

TypeScript 的核心原则之一是类型检查侧重于值的形状。 这有时被称为“鸭子类型”或“结构类型”。

在结构类型系统中，如果两个对象具有相同的形状，则认为它们属于同一类型。

```
interface Point {
  x: number;
  y: number;
}
 
function logPoint(p: Point) {
  console.log(`${p.x}, ${p.y}`);
}
 
// logs "12, 26"
const point = { x: 12, y: 26 };
logPoint(point);
```

`point` 变量永远不会被声明为 `Point` 类型。 但是，TypeScript 在类型检查中将`point`的形状与 `Point` 的形状进行比较。 它们具有相同的形状，因此代码通过。

形状匹配只需要匹配对象字段的子集。

```typescript
const point3 = { x: 12, y: 26, z: 89 };
logPoint(point3); // logs "12, 26"
 
const rect = { x: 33, y: 3, width: 30, height: 80 };
logPoint(rect); // logs "33, 3"
 
const color = { hex: "#187ABF" };
logPoint(color);
					^
 	Argument of type '{ hex: string; }' is not assignable to parameter of type 'Point'.
  Type '{ hex: string; }' is missing the following properties from type 'Point': x, y
```

类和对象如何符合形状没有区别：

```typescript
class VirtualPoint {
  x: number;
  y: number;
 
  constructor(x: number, y: number) {
    this.x = x;
    this.y = y;
  }
}
 
const newVPoint = new VirtualPoint(13, 56);
logPoint(newVPoint); // logs "13, 56"

```

如果对象或类具有所有必需的属性，则无论实现细节如何，TypeScript 都会说它们匹配。