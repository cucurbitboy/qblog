---
title: Typescript
tags:
- ts
password: hello
abstract: 有东西被加密了, 请输入密码查看.
message: 您好, 这里需要密码.
wrong_pass_message: 抱歉, 这个密码看着不太对, 请再试试.
wrong_hash_message: 抱歉, 这个文章不能被校验, 不过您还是能看看解密后的内容.

---
Welcome to

#### 1，ts数据类型
##### 1-1. 基本类型合集

```
// 数字，二、八、十六进制都支持
let decLiteral: number = 6;
let hexLiteral: number = 0xf00d;

// 字符串，单双引都行
let name: string = "bob";
let sentence: string = `Hello, my name is ${ name }.

// 数组，第二种方式是使用数组泛型，Array<元素类型>：
let list: number[] = [1, 2, 3];
let list: Array<number> = [1, 2, 3];

let u: undefined = undefined;
let n: null = null;
```
##### 1-2特殊类型
###### 1-2-1 元组 Tuple(需要以正确的顺序预定义数据类型)
```
const messyArray = [' something', 2, true, undefined, null];
const tuple: [number, string, string] = [24, "Indrek" , "Lasn"]
```
###### 1-2-2 枚举 enum(枚举是指将变量的值一一列举出来，变量只限于列举出来的值的范围内取值。例如：性别，星期)
```
// 默认情况从0开始为元素编号，也可手动为1开始
enum weekday{sun=1,mon=2,tue=3,wed=4,thu=5,fri=6,sat=7};
let aaa: weekday = weekday.sun;

let weekName: string = weekday[2];
console.log(weekName);  // 输出'mon'因为上面代码里它的值是2

enum BOOLEAN { false, true } aa, bb; //定义枚举类型并声明了两个枚举型变量
```
###### 1-2-3 void (在Typescript中，你必须在函数中定义返回类型)
```
function fun1(name:string):string{
    return name;
}
function fun2(name:string):viod{
    console.log('当定义函数返回类型为void是不能使用return'+name)
}
```
###### 1-2-4 any （当引入第三方库或者而不知道数据数据类型时用let aa: any=1）
###### 1-2-5 never 
```
function error(message: string): never {
    throw new Error(message);
}
// 返回never的函数必须存在无法达到的终点
function infiniteLoop(): never {
   while (true) {}
}
//使用 never 避免出现新增了联合类型没有对应的实现，目的就是写出类型绝对安全的代码
type Foo = string | number;
function controlFlowAnalysisWithNever(foo: Foo) {
  if(typeof foo === "string") {
    // 这里 foo 被收窄为 string 类型
  } else if(typeof foo === "number") {
    // 这里 foo 被收窄为 number 类型
  } else {
    // foo 在这里是 never
    const check: never = foo;
  }
}
```
##### 3. 类型断言(可以用来手动指定一个值的类型,表示为一些布尔表达)
```
let someValue: any = "this is a string";

let strLength: number = (<string>someValue).length;
let strLength: number = (someValue as string).length;

//不确定一个联合类型的变量到底是哪个类型的时候，我们只能访问此联合类型的所有类型里共有的属性或方法
function getLength(something: string | number): number {
    if ((<string>something).length) { //需要断言判断才不会出错
        return (<string>something).length;
    } else {
        return something.toString().length;
    }
}
```
##### 泛型：Generics
```
//声明泛型方法有以下两种方式：
function gen_func1<T>(arg: T): T {
    return arg;
}
// 或者
let gen_func2: <T>(arg: T) => T = function (arg) {
    return arg;
}
//调用方式也有两种:
gen_func1<string>('Hello world');
gen_func2('Hello world'); 
// 第二种调用方式可省略类型参数，因为编译器会根据传入参数来自动识别对应的类型。
```
##### 自定义类型：Interface vs Type alias
```
// 相同点：1,都可以用来描述一个对象或函数
interface User {
  name: string
  age: number
}

type User = {
  name: string
  age: number
};

interface SetUser {
  (name: string, age: number): void;
}
type SetUser = (name: string, age: number): void;

// 2,都允许拓展（extends）
// interface extends interface
interface Name { 
  name: string; 
}
interface User extends Name { 
  age: number; 
}
// type extends type
type Name = { 
  name: string; 
}
type User = Name & { age: number  };

// interface extends type
type Name = { 
  name: string; 
}
interface User extends Name { 
  age: number; 
}
// type extends interface
/*
*/

```
```
// 不同点：
//一type可行但interface不行
//1.type 可以声明基本类型别名，联合类型，元组等类型
//2.type 语句中还可以使用 typeof获取实例的 类型进行赋值
let div = document.createElement('div');
type B = typeof div
//3
type StringOrNumber = string | number;  
type Text = string | { text: string };  
type NameLookup = Dictionary<string, Person>;  
type Callback<T> = (data: T) => void;  
type Pair<T> = [T, T];  
type Coordinates = Pair<number>;  
type Tree<T> = T | { left: Tree<T>, right: Tree<T> };


//二 interface可以而 type不行
//1, interface 能够声明合并
    interface User {
      name: string
      age: number
    }
    
    interface User {
      sex: string
    }
    
    /*
    User 接口为 {
      name: string
      age: number
      sex: string 
    }
    */
//2, interface 有可选属性和只读属性
    interface Person {
      name: string;
      age?: number; //属性名后加问号就是可选属性
      readonly gender: number;//只读，只能在对象刚刚创建的时候修改其值
    }
```
##### 实现与继承：implementsvs（用它来明确的强制一个类去符合某种契约）；   extends(继承父类)
```
interface IDeveloper {
   name: string;
   age?: number;
}
// OK
class dev implements IDeveloper {
    name = 'Alex';
    age = 20;
}
// OK
class dev2 implements IDeveloper {
    name = 'Alex';
}
// Error
class dev3 implements IDeveloper {
    name = 'Alex';
    age = '9';
}
//二者可以混用
class A extends B implements C,D,E 
```
##### 声明文件与命名空间：declare 和 namespace
###### namespace(module X {} 相当于现在推荐的写法 namespace X {})
- 在命名空间中默认成员都是私有 private 
- 在命名空间中使用 export 关键字声明公有资源
```
// shims-tsx.d.ts 在全局变量 global中批量命名了数个内部模块。
import Vue, { VNode } from 'vue';

declare global {
  namespace JSX {
    // tslint:disable no-empty-interface
    interface Element extends VNode {}
    // tslint:disable no-empty-interface
    interface ElementClass extends Vue {}
    interface IntrinsicElements {
      [elem: string]: any;
    }
  }
}

// shims-vue.d.ts  告诉 TypeScript *.vue 后缀的文件可以交给 vue 模块来处理
declare module '*.vue' {
  import Vue from 'vue';
  export default Vue;
}
```
##### 访问修饰符：private(当成员被标记为private时，它就不能在声明它的类的外部访问)、public、protected(protected和private类似，但是，protected成员在派生类中可以访问)
![image](https://img-blog.csdn.net/2018040620202941?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0NoYUlUU2ltcGxlTG92ZQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)
```
class Animal {
    protected name: string;
    constructor(theName: string) {
        this.name = theName;
    }
}

class Rhino extends Animal {
    constructor() {
        //super关键字用于访问和调用一个对象的父对象上的函数
        super('aaa');
    }         
   getName() {
       console.log(this.name) //此处的name就是Animal类中的name
   }
} 
console.log(new Rhino().getName()) //aaa
```

##### 可选参数 ( ?: ) ( ?. )和非空断言操作符（!.）
```
function buildName(firstName: string, lastName?: string) {
    return firstName + ' ' + lastName
}

// 错误演示
buildName("firstName", "lastName", "lastName")
// 正确演示
buildName("firstName")
// 正确演示
buildName("firstName", "lastName")

The null hero's name is {{nullHero?.name}}

let s = e!.name;  // 断言e是非空并访问name属性
```

装饰器
1. 类装饰器
2. 属性装饰器
3. 方法装饰器
4. 参数装饰器
