# TypeScript

[Typescript - 基本类型 | Basic Types - Basic Types - 开发者手册 - 腾讯云开发者社区-腾讯云 (tencent.com)](https://cloud.tencent.com/developer/section/1475969)

## 基础类型

- **any**

   	声明为 any 的变量可以赋予任意类型的值。  变量的值会动态改变时，比如来自用户的输入，任意值类型可以让这些变量跳过编译阶段的类型检查 

- **unknown**

   不确定变量类型。

    unknown和any都是TS中的顶级类型，但主要区别在于：使用any相当于彻底放弃了类型检查，而unknown类型相较于any更加严格，在执行大多数操作之前，**会进行某种形式的检查**。**同时unknown类型的值也不能赋值给any和unknown以外的类型变量**  

   ```tsx
   let foo: any = 123;
   console.log(foo.msg); // 符合TS的语法
   let a_value1: unknown = foo;   // OK
   let a_value2: any = foo;      // OK
   let a_value3: string = foo;   // OK
   
   let bar: unknown = 222; // OK 
   console.log(bar.msg); // Error
   let k_value1: unknown = bar;   // OK
   let K_value2: any = bar;      // OK
   let K_value3: string = bar;   // Error
   ```

- **number**

   双精度 64 位浮点值。它可以用来表示整数和分数。 除了十六进制和十进制外，还支持二进制和八进制文字。

- **string**

- **boolean**

- **数组**

  声明变量为数组。

  ```ts
  // 在元素类型后面加上[]
  let arr: number[] = [1, 2];
  
  // 或者使用数组泛型
  let arr: Array<number> = [1, 2];
  ```

- **元组**

  元组类型用来表示已知元素数量和类型的数组，各元素的类型不必相同，对应位置的类型需要相同。 

  ```ts
  let x: [string, number];
  x = ['Runoob', 1];    // 运行正常
  x = [1, 'Runoob'];    // 报错
  console.log(x[0]);    // 输出 Runoob
  ```

- **enum**

  枚举类型用于定义数值集合。 

  ```ts
  enum Color { Red, Blue, White, Black}
  
  let c : Color = Color.Black
  console.log(c); //2
  console.log(Color[c]); //Black
  ```

  枚举的一个方便功能是，您也可以从数值转到枚举中该值的名称。

  ```ts
  enum Week {MonDay = 1, TuesDay = 2, Wednesday = 3}
  console.log(Week[1]); //MonDay
  ```

- **viod**

   用于标识方法返回值的类型，表示该方法没有返回值。 声明类型的变量`void`是没有用的，因为你只能分配`undefined`或`null`

- **null**

- **undefined**

   用于初始化变量为一个未定义的值。

- **never**

   `never` 类型表示的是那些永不存在的值的类型 ，是所有基础类型子类型， 可以赋值给任意类型。但是没有类型是 never 类型的子类型，即使是 any 类型也不能赋值给 never 类型 

- **类型断言**

​		TypeScript中的类型断言是一种将变量或表达式的类型强制转换为开发者指定的类型的方式。类型断言有两种方式，分别是使用尖括号（<>）语法和as语法	

```tsx
let str: any = "hello";
let len1: number = (<string>str).length;
let len2: number= (str as string).length
```

- **obejct**

   `object`表示非原始类型，也就是除`number`，`string`，`boolean`，`symbol`，`null`或`undefined`之外的类型 

```js
//全局通用的数据类型判断方法
function getType(obj : any){
    let type  = typeof obj;
    if (type !== "object") {    // 先进行typeof判断，如果是基础数据类型，直接返回
   		 return type;
    }
    // 对于typeof返回结果是object的，再进行如下的判断，正则返回结果
    return Object.prototype.toString.call(obj).replace(/^\[object (\S+)\]$/, '$1'); 
}
```

## 变量声明

声明变量的类型及初始值

```
var [变量名] : [类型] = 值;
```

声明变量的类型，但没有初始值，变量值会设置为 undefined

```
var [变量名] : [类型];
```

 声明变量并初始值，但不设置类型，该变量可以是任意类型。但是ts有类型推断

```
var [变量名] = 值;
```

### 类型推断

​	TypeScript 的类型推断是一种自动推断变量类型的能力，它可以在变量声明时根据初始值或上下文环境来确定其类型。

```ts
const myNumber = 123; // 初始值为数字，因此变量的类型为 number
const myString = 'hello'; // 初始值为字符串，因此变量的类型为 string
let a : number = myString //会报错误，不能将string类型的值赋给number类型的
```

## 联合类型

​	 联合类型（Union Types）可以通过管道(|)将变量设置多种类型，赋值时可以根据设置的类型来赋值。 

```ts
var val : string | number 

//联合类型数组
type StrOrNumArray = Array<string | number>

let a : StrOrNumArray = ["123",123]
console.log(a);
```

## 函数

​	定义一个函数

```ts
function add(x: number, y: number) : number {
  return x + y
}
//调用需要传入对应数量的参数
```

- 可选参数：  **可选参数必须跟在必须参数后面。**  如果带默认值的参数出现在必须参数前面，用户必须明确的传入 `undefined`值来获得默认值 

  ```
  //定义一个匿名函数， 拥有一个可选的string类型的参数， 返回类型string
  const getName = (name ?: string) ：string => {
      if( name ) {
          return name
      }else {
          return "12123"
      }
  }
  ```

- 参数默认值： 根据给定的默认值推断参数类型

  ```ts
  const getName = (name = "小明") : string => {
      return name
  }
  console.log(getName(1)); //类型报错
  ```

- 剩余参数：  把所有参数收集到一个变量里 

  ```ts
  function addAll(x:number, y:number, ...Num: number[]) {
    let [a,b,c] = Num
    console.log(a);
  }
  
  addAll(1,2,3,4,5)
  ```
  

### 重载

函数签名：一个带有函数体的同名函数， 并且这个函数的**参数类型**要完全包含**函数签名**的**所有类型**。 就是函数实现有几个类型就有几个函数签名。

```ts
function pickCard(x: {suit: string; card: number; }[]): number;
function pickCard(x: number): {suit: string; card: number; };
function pickCard(x : any): any {
    if (typeof x == "object") {
        let pickedCard = Math.floor(Math.random() * x.length);
        return pickedCard;
    }else if (typeof x == "number") {
        let pickedSuit = Math.floor(x / 13);
        return { suit: suits[pickedSuit], card: x % 13 };
    }
}
//编译阶段会报错，显示overload并没有该类型，所以函数重载会进行类型检测，运行是不会报错的
console.log(pickCard('123')); 
```

## 接口

​	 接口是一种规范的定义，他定义了行为和动作的规范，接口不关心这些类内部状态数据，也不关心这些类里的方法的实现细节，他只规定这批类里，必须提供的某些方法，提供这些方法的类就可以满足实际需求 。

-  类型检查器不会去检查属性的顺序，只要相应的属性存在并且类型也是对的就可以 

```ts
interface obj {
	name: string
	//age ?: number //可选
    //readonly x: number //只读
}

function f(b : obj) {
	console.log(b);
}
let a = {name: "xiao", age: 1}
f(a)
```

### 函数类型

-  使用接口表示函数类型，我们需要给接口定义一个调用签名。 它就像是一个只有参数列表和返回值类型的函数定义。参数列表里的每个参数都需要名字和类型。 
-  函数的参数名不需要与接口里定义的名字相匹配 

```ts
interface SearchFun {
    (source: string, subString: string) : boolean //类似于函数签名
}

let mySearch: SearchFun = (src: string, sub: string) : boolean => {
    let result = src.search(sub);
    return result > -1;
}

console.log(mySearch('asdadhjl', 'a')); //true
```

## 泛型

​	 泛型是指在预先定义函数、接口或者类的时候，不预先指定数据的类型，而是在使用的时候指定。 

```ts
//any类型: 会丢失返回的类型，导致后面可以被number类型的赋值
function f(name : any) {
	return name
}
let str = f("xiao")
str = 123
console.log(str);

function f<T>(name : T) : T {
	return name
}
//let str = f<string>("xiao")
let str = f("xiao")
str = 123 //编译报错number不能被赋值给string
console.log(str);
```

### 泛型接口

```ts
interface user<T,U> {
    name: T
    age: U
}
let xiao : user<string, number> = {
    name: "肖国志",
    age: 28
}
console.log(xiao);
```

### 泛型类



## 类

```ts
class Greeter {
    greeting: string;
    constructor(message: string) {
        this.greeting = message;
    }
    greet() {
        return "Hello, " + this.greeting;
    }
}

let greeter = new Greeter("world");
```

### 继承

- 使用关键字 `extends`继承父类
-  派生类包含了一个构造函数，它*必须*调用`super()`，它会执行基类的构造函数。 而且，在构造函数里访问`this`的属性之前，我们*一定*要调用`super()`。  

```ts
class Animal {
    name: string;
    constructor(theName: string) { this.name = theName; }
    move(distanceInMeters: number = 0) {
        console.log(`${this.name} moved ${distanceInMeters}m.`);
    }
}

class Snake extends Animal {
    constructor(name: string) { super(name); }
    move(distanceInMeters = 5) {
        console.log("Slithering...");
        super.move(distanceInMeters);
    }
}

class Horse extends Animal {
    constructor(name: string) { super(name); }
    move(distanceInMeters = 45) {
        console.log("Galloping...");
        super.move(distanceInMeters);
    }
}
```

### 修饰符

- public

- private

   当成员被标记成`private`时，它就不能在声明它的类的外部访问 。

- protected

  只能在类和其子类进行访问

- readonly

   使用`readonly`关键字将属性设置为只读的。 只读属性必须在声明时或构造函数里被初始化 

### 存取器

```ts
class Animal {
  name: string;
  constructor(theName: string) {
    this.name = theName
  }

  get _name(): string {
    console.log("get");
    return this.name
  }
  set _name(name: string) {
    console.log("1231");
    this.name = name
  }
}

const a = new Animal("Dog")
a._name = "S"
console.log(a._name);
```

### 抽象类



## tsconfig.json

​	在vite.config.ts设置路径别名后，需要在tsconfig.json设置paths,不然会提示找不到模块或相关类型

```json
{
  /* 此属性指明了 ts 在编译时遵循的规则，这个属性也可以不指定，ts 会使用默认的配置进行编译。 */
  "compilerOptions": {
    "target": "ES2020", //指定ECMAScript目标版本
    "useDefineForClassFields": true,
    "module": "ESNext",
    "lib": ["ES2020", "DOM", "DOM.Iterable"], //TS需要引用的库
    "skipLibCheck": true,

    "allowJs": true, //是否允许编译js文件

    /* paths 可以允许你声明 TypeScript 应该如何解析你的 require/import */
    "baseUrl": ".",
    "types": ["vite/client"],
    "paths": {
      "@/*": ["src/*"],
      "#/*": ["types/*"]
    },
    /* Bundler mode */
    "moduleResolution": "bundler",
    "allowImportingTsExtensions": true,
    "resolveJsonModule": true,
    "isolatedModules": true,
    "noEmit": true,
    "jsx": "preserve",

    /* Linting */
    "strict": true,
    "noUnusedLocals": true,
    "noUnusedParameters": true,
    "noFallthroughCasesInSwitch": true
  },
  /* 指定需要编译的文件列表 */
  "include": ["src/**/*.ts", "src/**/*.d.ts", "src/**/*.tsx", "src/**/*.vue"],
  /* 排除的文件 */
  "exclude": [],
  /* 指定工程引用依赖 */
  "references": [{ "path": "./tsconfig.node.json" }]
}
```



