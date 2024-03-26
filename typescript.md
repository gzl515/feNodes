### 基础语法
- tsc命令：将ts文件编译为js文件

### 基础类型
- any
- number
- string
- boolean
- 数组
  - `let arr: number[] = [1, 2];`
- 元组
  - `let arr: [number, string] = [1, '2'];`
- 枚举
  - ```
    enum Color {Red, Green, Blue}
    let c: Color = Color.Blue;
    console.log(c);    // 输出 2
    ```
- void
- null
- undefined
- never

*Null 和 Undefined 是其他任何类型（包括 void）的子类型，可以赋值给其它类型，如数字类型。
此时，赋值后的类型会变成 null 或 undefined。
而在TypeScript中启用严格的空校验（--strictNullChecks）特性，就可以使得null 和 undefined 只能被赋值给 void 或本身对应的类型。
可声明多类型：`let x: number | null | undefined;`*

*never 是其它类型（包括 null 和 undefined）的子类型，代表从不会出现的值。
这意味着声明为 never 类型的变量只能被 never 类型所赋值，在函数中它通常表现为抛出异常或无法执行到终止点（例如无限循环）。*

### 变量声明
- 变量声明
  - `var [变量名] : [类型] = 值;`
- 类型断言
  - ```
    var str = '1' 
    var str2:number = <number> <any> str   //str、str2 是 string 类型
    ```
- 类型推断
- 变量作用域