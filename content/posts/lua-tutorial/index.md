+++
title = 'Lua 从入门到放弃'
summary = 'Lua 快速入门'
keywords = ['Lua', '教程']
date = '2024-06-04'
tags = ['工程实践', 'Lua']
isCJKLanguage = true
draft = true
+++

## 数据类型
Lua 有八种基础数据类型：`nil`、`boolean`、`number`、`string`、`userdata`、`function`、`thread`和`table`。用`type`函数可以打印数据类型：
```lua
print(type("Hello world"))  --> string
print(type(10.4 * 3))       --> number
print(type(print))          --> function
print(type(type))           --> function
print(type(true))           --> boolean
print(type(nil))            --> nil
print(type(type(X)))        --> string
```
### 空值
类型名：`nil`。
- 空值只有一个值`nil`
- 通常用于代表一个变量没有有效的值
- 全局变量赋初值前是`nil`
- 全局变量赋`nil`可以导致其被删除

### 布尔
类型名：`boolean`。

布尔有两个合法值：`true`、`false`。
- `false`和`nil`被视作`false`
- 其余所有值被视作`true`，包括数字`0`和空字符串（警觉）

### 数字
类型名：`number`。

代表实数（双精度浮点数）。Lua 无整数类型。表示法实例：
```
4     0.4     4.57e-3     0.3e12     5e+20
```

### 字符串
类型名：`string`。可以存储二进制数据，中间有`0`值也没关系。

字符串是不可变类型。如要修改，必须创建新的字符串。
```lua
a = "one string"
b = string.gsub(a, "one", "another")  -- change string parts
print(a)       --> one string
print(b)       --> another string
```
使用单引号或双引号表示，同一程序应只用一种，保持风格一致。
```lua
a = "a line"
b = 'another line'
```
支持类似 C 语言的转义符。
| 转义符 | 意义 |
|:-----------:|:------:|
| \a | bell |
| \b | back space |
| \f | form feed |
| \n | newline |
| \r | carriage return |
| \t | horizontal tab |
| \v | vertical tab |
| \\\ | backslash |
| \\" | double quote |
| \\' | single quote |

多行字符串可以用`[[`和`]]`包括起来：
```lua
page = [[
     <html>
     <head>
     <title>An HTML Page</title>
     </head>
     <body>
      <a href="http://www.lua.org">Lua</a>
     </body>
     </html>
     ]]
write(page)
```
为避免代码中本身就有`[[`和`]]`，可以在两个方括号之间加不限数量的`=`，比如`[===[`和`]===]`，但`=`数量必须匹配。

数字和字符串可以自动转换。字符转数字：
```lua
print("10" + 1)         --> 11
print("10 + 1")         --> 10 + 1
print("-5.3e-10" * "2")   --> -1.06e-09
print("hello" + 1)      -- ERROR (cannot convert "hello")
```
数字转字符：
```lua
print(10 .. 20)         --> 1020
```

### 表
类型名：`table`。

表本质上是关联数组，其他编程语言也叫映射（map）或字典（dictionary）。
- 表的索引可以是除了`nil`以外的所有值
- 表没有固定的大小，可以动态添加元素
- 表是 Lua 最主要（也是唯一）的数据构造方式，用它可以实现普通数组、符号表、集合、队列等很多机制
- 表既不是变量，也不是值，它是对象
- 表传递的是引用（或指针），对象传递的都是引用

表的构造方式：
```lua
a = {}
a["x"] = 10
b = a           -- 'b' refers to the same table as 'a'
print(b["x"])   --> 10
b["x"] = 20
print(a["x"])   --> 20
a = nil         -- only ’b’ still refers to the table
b = nil         -- no references left to the table
```
类似全局变量，表的成员如果没有初始化，值是`nil`；重新赋值`nil`后，成员会被删除。
```lua
a = {}          -- empty table
-- create 1000 new entries
for i = 1, 1000 do a[i] = i * 2 end
print(a[9])     --> 18
a["x"] = 10
print(a["x"])   --> 10
print(a["y"])   --> nil
```
`a.name`和`a["name"]`是等价的：
```lua
a.x = 10    -- same as a["x"] = 10
print(a.x)  -- same as print(a["x"])
print(a.y)  -- same as print(a["y"])
```
注意`a.x`和`a["x"]`等价，但和`a[x]`不等价！
```lua
a = {}
x = "y"
a[x] = 10           -- put 10 in field "y"
print(a[x]) --> 10  -- value of field "y"
print(a.x)  --> nil -- value of field "x" (undefined)
print(a.y)  --> 10  -- value of field "y"
```
使用整数作为索引，就是传统的数组：
```lua
-- read 10 lines storing them in a table
a = {}
for i = 1, 10 do
    a[i] = io.read()
end
```
理论上，索引是任何数字都可以，但 Lua 的索引一般从 1 开始。并且有一些工具会以来这种表示。用`#`可以返回数组的最后一个索引（也是大小）：
```lua
-- print the lines
for i = 1, #a do
  print(a[i])
end
```
更多的用法：
```lua
print(a[#a])    -- prints the last value of list 'a'
a[#a] = nil     -- removes this last value
a[#a+1] = v     -- appends 'v' to the end of the list
```
对于有「洞」的数组来说，不要去求大小：
```lua
a = {}
a[10000] = 1
```
未初始化的成员是`nil`，而`#`计算大小是看`nil`值。对于有「洞」的数组，可以用`table.maxn`来获取最大的正数索引值：
```lua
a = {}
a[10000] = 1
print(table.maxn(a))          --> 10000
```
注意，数字`0`、字符串`"0"`，作为索引，都是不同的值，也就是代表了表中不同的元素，忽略这点，很容易引入难以觉察的 bug：
```lua
i = 10; j = "10"; k = "+10"
a = {}
a[i] = "one value"
a[j] = "another value"
a[k] = "yet another value"
print(a[j])            --> another value
print(a[k])            --> yet another value
print(a[tonumber(j)])  --> one value
print(a[tonumber(k)])  --> one value
```

### 函数
类型名：`function`。

函数是 Lua 的内置数据类型，可以赋给变量。

### 用户数据和线程
后续再讨论。

## 表达式
### 算术运算
双目运算符：`+`（加）、`-`（减）、`*`（乘）、`/`（除）、`^`（指数）、`%`（模）

单目运算符：`-`（取反）

运算符都支持实数，`x ^ 0.5`是求`x`的平方根。

### 关系运算
`<`、`>`、`<=`、`>=`、`==`、`~=`

关系运算符返回`true`或`false`。`==`测试相等，`~=`测试不等。相同类型的数据才能比较，不同类型的数据不相等。`nil`只跟自己相等。对于`table`、`userdata`、`function`，比较的是引用而不是值：
```lua
a = {}; a.x = 1; a.y = 0
b = {}; b.x = 1; b.y = 0
c = a
print(a == c)   --> true
print(a ~= b)   --> true
```
`<`、`>`、`<=`、`>=`只能用于数字和字符串。

### 逻辑运算
`and`、`or`、`not`。求值时，`false`和`nil`被视作`false`，其他被视作`true`。
- `and`：如果第一个参数为`false`，则返回第一个参数，否则返回第二个参数。
- `or`：如果第一个参数为`true`，则返回第一个参数，否则返回第二个参数。
```lua
print(4 and 5)          --> 5
print(nil and 13)       --> nil
print(false and 13)     --> false
print(4 or 5)           --> 4
print(false or 5)       --> 5
```

`and`和`or`都采用「短路求值（short-cut evaluation）」的方式，即——非必要不求值。
```lua
x = x or v
--> equals to
if not x then x = v end
```

```lua
max = (x > y) and x or y    --> equals a ? b : c in C
```

### 连接
`..`用于连接字符串，如果操作数是数字，会转成字符串。
```lua
print("Hello " .. "World")  --> Hello World
print(0 .. 1)               --> 01
```
字符串是不可变的，所以`..`会产生新的字符串。
```lua
a = "Hello"
print(a .. " World")   --> Hello World
print(a)               --> Hello
```

### 优先级
运算符优先级如下：
```
^
not # -(单目) * / %
+ -
..
< > <= >= ~= == and
or
```
每行代表一种优先级，从上到下优先级递减。双目运算符中，`^`和`..`为「右结合」，其余为「左结合」。

### 表构造器
最简单的是空构造`{}`。

列表构造：
```lua
days = {"Sunday", "Monday", "Tuesday", "Wednesday",
    "Thursday", "Friday", "Saturday"}
```
再次注意索引是从 1 开始的。
```lua
print(days[4]) --> Wednesday
```
记录表构造：
```lua
a = {x=10, y=20}
--> equals to
a = {}; a.x=10; a.y=20
```
事后添加成员：
```lua
w = {x=0, y=0, label="console"}
x = {math.sin(0), math.sin(1), math.sin(2)}
w[1] = "another field"      -- add key 1 to table ’w’
x.f = w                     -- add key "f" to table ’x’
print(w["x"])               --> 0
print(w[1])                 --> another field
print(x.f[1])               --> another field
w.x = nil                   -- remove field "x"
```
列表和记录表可以混用：
```lua
polyline = {color = "blue", thickness = 2, npoints = 4,
              {x = 0,   y = 0},
              {x = -10, y = 0},
              {x = -10, y = 1},
              {x = 0,   y = 1}
}
```
上面初始化后：
```lua
print(polyline[2].x)    --> -10
print(polyline[4].y)    --> 1
```
还有一种更通用的方式，把索引放在`[]`中：
```lua
opnames = {["+"] = "add", ["-"] = "sub",
        ["*"] = "mul", ["/"] = "div"}
i = 20; s = "-"
a = {[i+0] = s, [i+1] = s..s, [i+2] = s..s..s}
print(opnames[s])    --> sub
print(a[22])         --> ---
```
最后一个成员后面可以加`,`：
```lua
a = {[1]="red", [2]="green", [3]="blue",}
```
在构造器中也可以使用`;`分隔成员：
```lua
{x = 10, y = 45; "one", "two", "three"}
```
可以用于隔离不同类型的数据。

## 语句
### 赋值
基本的赋值：
```lua
a = "hello" .. "world"
t.n = t.n + 1
```
可以同时给多个变量赋值：
```lua
a, b = 10, 2 * x
```
在这种情况下，Lua 会先做完所有的求值，再进行赋值：
```lua
x, y = y, x                -- swap ’x’ for ’y’
a[i], a[j] = a[j], a[i]    -- swap ’a[i]’ for ’a[j]’
```
如果变量数更多，多余的变量赋`nil`。如果值的数量更多，多余的值会被丢弃。
```lua
a, b, c = 0, 1
print(a, b, c)          --> 0 1 nil
a, b = a + 1, b + 1, b + 2    -- value of b+2 is ignored
print(a, b)             --> 1 2
a, b, c = 0
print(a, b, c)          --> 0 nil nil
```
把多个变量同时初始化为 0，必须这么写：
```lua
a, b, c = 0, 0, 0
print(a, b, c)         --> 0   0   0
```
多变量同时赋值，并不能提升性能。但有时候会需要这种写法，比如交换变量的值，或者接收多个函数返回值。

### 局部变量
局部变量使用`local`。
```lua
j = 10         -- global variable
local i = 1    -- local variable
```
局部变量的作用域限定在控制块、函数或文件中。
```lua
x = 10
local i = 1         -- local to the chunk
while i <= x do
  local x = i * 2   -- local to the while body
  print(x)          --> 2, 4, 6, 8, ...
  i = i + 1
end

if i > 20 then
  local x         -- local to the "then" body
  x = 20
  print(x + 2)    -- (would print 22 if test succeeded)
else
  print(x)        --> 10  (the global one)
end
print(x)          --> 10  (the global one)
```
可以用`do-end`来限定作用域：
```lua
do
  local a2 = 2 * a
  local d = (b^2 - 4 * a * c)^(1 / 2)
  x1 = (-b + d) / a2
  x2 = (-b - d) / a2
end          -- scope of ’a2’ and ’d’ ends here
print(x1, x2)
```

### 控制结构
#### if-then-else
```lua
if a < 0 then a = 0 end

if a < b then return a else return b end

if line > MAXLINES then
  showpage()
  line = 0
end
```
如果要嵌套，使用`elseif`：
```lua
if op == "+" then
  r = a + b
elseif op == "-" then
  r = a - b
elseif op == "*" then
  r = a * b
elseif op == "/" then
  r = a / b
else
  error("invalid operation")
end
```

#### while
```lua
local i = 1
while a[i] do
  print(a[i])
  i = i + 1
end
```

#### repeat
```lua
-- print the first non-empty input line
repeat
  line = os.read()
until line ~= ""
print(line)
```
**在循环体中声明的局部变量，在循环条件中也是有效的，这个大多数编程语言都不一样。**
```lua
local sqr = x / 2
repeat
  sqr = (sqr + x/sqr) / 2
  local error = math.abs(sqr^2 - x)
until error < x/10000         -- ’error’ still visible here
```

#### for（数字）
针对数字的`for`语句形式：
```lua
for var = exp1, exp2, exp3 do
  <something> 
end
```
`exp1`和`exp2`是起点和终点，`exp3`是步长，步长可省略，如省略，则默认是 1。
```lua
for i = 1, f(x) do print(i) end
for i = 10, 1, -1 do print(i) end
```
如果不想要上限，可以用`math.huge`：
```lua
for i = 1, math.huge do
  if (0.3 * i^3 - 20 * i^2 - 500 >= 0) then
    print(i)
    break
  end
end
```
有两点需要注意：
- `exp1`、`exp2`、`exp3`三个表达式，只会在循环开始前求值一次
- 循环变量的作用域只在循环中有效，不要在循环后使用它
```lua
for i = 1, 10 do print(i) end
max = i      -- probably wrong! ’i’ here is global
```
如果一定要记录循环变量，则需要借助其他变量：
```lua
-- find a value in a list
local found = nil
for i = 1, #a do
  if a[i] < 0 then
    found = i
    break
  end
end
print(found)
```

#### for（泛型）
泛型版本的`for`用来遍历迭代器返回的值。
```lua
-- print all values of array ’a’
for i, v in ipairs(a) do print(v) end
```
`ipairs`是 Lua 基础库提供的一个迭代器，可以遍历数组。每次`i`得到的是索引值，`v`得到的是跟索引关联的内容。
```lua
-- print all keys of table ’t’
for k in pairs(t) do print(k) end
```
Lua 标准库提供了一些迭代器：`io.lines`（迭代文件的每一行）、`pairs`（迭代表所有名值对）、`ipairs`（迭代数组所有索引和值）、`string.gmatch`（迭代字符串的每个词），等等。自己也可以实现迭代器，后面详述。

#### break 和 return
`break`和`return`用于跳出控制块。`break`用于结束循环，`return`用于返回函数。两者都只能出现在代码块的最后，如果有时候处于调试目的，需要在函数一开头就返回，可以把`return`放在`do-end`里面。
```lua
function foo ()
  return --<< SYNTAX ERROR
  -- 'return' is the last statement in the next block
  do return end -- OK
  <other statements>
end
```

## 函数
定义：
```lua
function add (a)
  local sum = 0
    for i, v in ipairs(a) do
      sum = sum + v
    end
  return sum
end
```
包括函数名、参数列表和函数体。参数类似局部变量。调用函数的参数个数可以跟定义的不一样，规则类似多个变量一起赋值。
```lua
function f(a, b) return a or b end

f(3)        -- a=3, b=nil
f(3, 4)     -- a=3, b=4
f(3, 4, 5)  -- a=3, b=4   (5 is discarded)
```
有时候参数需要默认值：
```lua
function incCount (n)
  n = n or 1
  count = count + n
end
```

### 多返回值
子字符串的起始和结束位置：
```lua
s, e = string.find("hello Lua users", "Lua")
print(s, e)   --> 7      9
```
同时返回最大数的值和索引：
```lua
function maximum (a)
  local mi = 1          -- index of the maximum value
  local m = a[mi]       -- maximum value
  for i, val in ipairs(a) do
    if val > m then
      mi = i; m = val
    end
  end
  return m, mi
end
print(maximum({8,10,23,12,5}))  --> 23 3
```
返回值规则：
- 调用函数作为语句，则丢弃所有返回值
- 在表达式中调用函数，只保留第一个返回值
- 在一个表达式的列表中调用函数，并且此函数是最后一个或唯一的元素，则返回所有返回值。列表的场景：
  - 多变量赋值
  - 函数调用的参数
  - 表构造
  - 返回值列表

考虑以下函数定义：
```lua
function foo0 () end                    -- returns no results
function foo1 () return "a" end         -- returns 1 result
function foo2 () return "a","b" end     -- returns 2 results
```
函数返回值的典型场景：
```lua
x, y = foo2()           -- x="a", y="b"
x = foo2()              -- x="a", "b" is discarded
x, y, z = 10, foo2()    -- x=10, y="a", z="b"

x, y = foo0()        -- x=nil, y=nil
x, y = foo1()        -- x="a", y=nil
x, y, z = foo2()      -- x="a", y="b", z=nil

x, y = foo2(), 20      -- x="a", y=20
x, y = foo0(), 20, 30  -- x=nil, y=20, 30 is discarded
```
函数返回值作为另一个函数的入参时：
```lua
print(foo0())           -->
print(foo1())           --> a
print(foo2())           --> ab
print(foo2(), 1)        --> a1
print(foo2() .. "x")    --> ax
```
最后一行，`foo2`出现在表达式中，所以只有一个返回值。

作为构造参数：
```lua
t = {foo0()}    -- t = {}  (an empty table)
t = {foo1()}    -- t = {"a"}
t = {foo2()}    -- t = {"a", "b"}
```
如果列表多于一个元素，还是返回一个值：
```lua
t = {foo0(), foo2(), 4} -- t[1] = nil, t[2] = "a", t[3] = 4
```
作为函数调用作为另一个函数的返回值，返回所有值：
```lua
function foo (i)
  if i == 0 then return foo0()
  elseif i == 1 then return foo1()
  elseif i == 2 then return foo2()
  end
end
print(foo(1))   --> a
print(foo(2))   --> a b
print(foo(0))   -- (no results)
print(foo(3))   -- (no results)
```
如果想强制返回一个值，可以在函数调用外面加`()`：
```lua
print((foo0()))     --> nil
print((foo1()))     --> a
print((foo2()))     --> a
```
`unpack`是一个特殊的返回多个值的函数，它返回一个数组的各个元素，从索引 1 开始：
```lua
print(unpack{10, 20, 30})    --> 10   20   30
a, b = unpack{10, 20, 30}    -- a=10, b=20, 30 is discarded
```
用`unpack`可以构造更「通用」的函数调用，不必明确提供参数：
```lua
f = string.find
a = {"hello", "ll"}
f(unpack(a))    --> 3, 4
```
### 不定参数
```lua
function add (...)
  local s = 0
  for i, v in ipairs{...} do
    s = s + v 
  end
  return s
end
print(add(3, 4, 10, 25, 12))    --> 54
```
可变参数列表称为`varargs`，使用`...`表示，访问这些参数也用`...`。确定的参数可以视作不定参数的特殊情况：
```lua
function foo (a, b, c)
-->
function foo (...)
  local a, b, c = ...
```
恒等函数：
```lua
function id (...) return ... end
```
在函数前添加日志：
```lua
function foo1 (...)
  print("calling foo:", ...)
  return foo(...)
end
```
结合字符串格式化`string.format`和写文件接口`io.write`，把格式化字符串写入文件：
```lua
function fwrite (fmt, ...)
  return io.write(string.format(fmt, ...))
end
```
这里，在`...`之前出现了一个额外的参数`fmt`。只有后面的参数才会进入`varargs`：
```lua
fwrite()                -- fmt = nil, no varargs
fwrite("a")             -- fmt = "a", no varargs
fwrite("%d%d", 4, 5)    -- fmt = "%d%d", varargs = 4 and 5
```
如果我们需要迭代`varargs`，可以像前面的`add`一样做。但是有时候`varargs`里面包含了有效的`nil`，这时候就需要使用`select`。
```lua
for i=1, select('#', ...) do
  local arg = select(i, ...) -- get i-th parameter
  <loop body>
end
```
`select("#",...)`返回参数的准确个数，包括`nil`。

### 参数命名
Lua 基于**顺序**来传递参数，第一个实参传递给第一个形参，第二个实参传递给第二个形参，以此类推。有时候传递参数记不住顺序，需要指定名字。以下代码是错误的：
```lua
-- invalid code
rename(old = "temp.lua", new = "temp1.lua")
```
Lua 没有这种语法。但我们可以把参数打包到**表**里面：
```lua
rename{old = "temp.lua", new = "temp1.lua"}
```
可以这样来引用参数：
```lua
function rename (arg)
  return os.rename(arg.old, arg.new)
end
```
当函数需要很多参数，并且大多数参数是可选的时候，这种传递方法会非常简洁灵活。比如说，创建一个 GUI 程序的窗口，通常需要很多参数：
```lua
w = Window{ x = 0, y = 0, width = 300, height = 200,
            title = "Lua", background = "blue",
            border = true
          }
```
函数实现的例子：
```lua
function Window (options)
  -- check mandatory options
  if type(options.title) ~= "string" then
    error("no title")
  elseif type(options.width) ~= "number" then
    error("no width")
  elseif type(options.height) ~= "number" then
    error("no height")
  end
  -- everything else is optional
  _Window(options.title,
          options.x or 0,     -- default value
          options.y or 0,     -- default value
          options.width, options.height,
          options.background or "white",   -- default
          options.border      -- default is false (nil)
        )
end
```
## 深入理解函数
在 Lua 中，函数是「一级类型」，这意味着 Lua 像对待数字、字符串一样对待函数，函数可以保存到变量和表中，可以作为参数传递，可以作为返回值返回。Lua 同时具备了静态作用域，这以为着函数可以访问外层作用域的变量，并且支持函数式编程。

以下例子说明了函数名，只是个名字而已：
```lua
a = {p = print}
a.p("Hello World")  --> Hello World
print = math.sin    -- 'print' now refers to the sine function
a.p(print(1))       --> 0.841470
sin = a.p           -- 'sin' now refers to the print function
sin(10, 20)         --> 10      20
```
`print`只是一个保存了打印函数的变量名而已。通常我们会这样定义函数：
```lua
function foo (x)  return 2 * x  end
```
但实际上，这只是下面这种方式的「语法糖」而已：
```lua
foo = function (x)  return 2 * x  end
```
从第二种定义方式看，就很容易理解函数也只不过是个变量。`function(x) body`是个构造器，返回的结果叫做「匿名函数」。

`table.sort`用于对表进行排序，它接受一个函数作为参数判断元素的排序规则：
```lua
network = {
  {name = "grauna",  IP = "210.26.30.34"},
  {name = "arraial", IP = "210.26.30.23"},
  {name = "lua",     IP = "210.26.23.12"},
  {name = "derain",  IP = "210.26.23.20"},
}

table.sort(network, function (a,b) return (a.name > b.name) end)
```
更复杂的例子，估算函数的导数：
```lua
function derivative (f, delta)
  delta = delta or 1e-4
  return function (x)
           return (f(x + delta) - f(x))/delta
         end
end

c = derivative(math.sin)
print(math.cos(10), c(10))
-->   -0.83907152907645   -0.83904432662041
```

### 闭包
当一个函数在另一个函数的内部声明的时候，前者能访问后者的所有局部变量。假设我们要实现一个函数，根据成绩等第给学生排序：
```lua
names = {"Peter", "Paul", "Mary"}
grades = {Mary = 10, Paul = 7, Peter = 8}
table.sort(names, function (n1, n2)
  return grades[n1] > grades[n2]        -- compare the grades
end)
```
如果我们要实现一个函数来完成这项工作：
```lua
function sortbygrade (names, grades)
  table.sort(names, function (n1, n2)
    return grades[n1] > grades[n2]      -- compare the grades
  end)
end
```
这里微妙的是传给`table.sort`的匿名函数可以访问`grades`，而`grades`既非全局，也非局部变量（对匿名函数来说）。再看一个例子：
```lua
function newCounter ()
  local i = 0
  return function ()    -- anonymous function
           i = i + 1
           return i
         end
end

c1 = newCounter()
print(c1())  --> 1
print(c1())  --> 2
i = i + 1
```
在这个例子中，匿名函数引用了一个非局部变量`i`，并且，使用`i`的时候，它已经出了作用域了。Lua 通过「闭包」来正确处理这种场景。「闭包」是指一个函数加上其需要访问的所有非局部变量。
```lua
c2 = newCounter()
print(c2())  --> 1
print(c1())  --> 3
print(c2())  --> 2
```
`c1`和`c2`是同一函数的不同闭包，它们各自拥有一份自己的`i`。通过闭包来制作一个安全沙箱的例子：
```lua
do
  local oldOpen = io.open
  local access_OK = function (filename, mode)
    <check access>
  end
  io.open = function (filename, mode)
    if access_OK(filename, mode) then
      return oldOpen(filename, mode)
    else
      return nil, "access denied"
    end
  end
end
```

### 非全局函数
函数不仅可以赋给全局变量，也可以赋给表和局部变量。之前已经看过一些把函数赋给表成员的例子，多数 Lua 的库都是这么实现的：
```lua
Lib = {}
Lib.foo = function (x,y) return x + y end
Lib.goo = function (x,y) return x - y end
```
也可以用构造器：
```lua
Lib = {
  foo = function (x,y) return x + y end,
  goo = function (x,y) return x - y end
}
```
Lua 还提供了另外一种语法：
```lua
Lib = {}
function Lib.foo (x,y) return x + y end
function Lib.goo (x,y) return x - y end
```
函数赋给局部变量时，被限定在一个作用域内，这对于定义「包」很有用。
```lua
local f = function (<params>)
  <body>
end
local g = function (<params>)
  <some code>
  f() -- 'f' is visible here <some code>
end
```
对于局部函数，可以用语法糖来简化：
```lua
local function f (<params>)
  <body>
end
```
考虑以下递归函数的定义：
```lua
local fact = function (n)
  if n == 0 then return 1
  else return n * fact(n - 1)   -- buggy
  end
end
```
这种定义是无法正常工作的。当编译`fact(n - 1)`的时候，局部的`fact`尚未定义，所以它用的是全局的`fact`。所以你必须这样定义：
```lua
local fact
fact = function (n)
  if n == 0 then return 1
  else return n * fact(n - 1)
  end
end
```
这样就知道需要调用的是局部的`fact`。不用担心`fact`的值，因为到调用的时候，它已经得到了正确的值。上面的语法糖版本能正确处理这种情况：
```lua
local function foo (<params>) <body> end
```
实际被展开为：
```lua
local foo
foo = function (<params>) <body> end
```
但是，它无法处理间接递归的情况，间接递归需要这么定义：
```lua
local f, g    -- 'forward' declarations
function g () <some code> f()
end
function f () <some code> g()
end
```

### 尾调用
如果一个函数的最后以调用另一个函数结束，叫做「尾调用」。比如：
```lua
function f (x)  return g(x)  end
```
尾调用不会占用额外的栈空间，所以嵌套使用尾调用的数量不用受限。以下函数不管`n`有多大，都不会有栈溢出。
```lua
function foo (n)
  if n > 0 then return foo(n - 1) end
end
```
尾调用的标准是，调用这调用完后，不需要做其他任何事情。以下函数不满足尾调用标准：
```lua
function f (x)  g(x)  end
```
因为`f`调用完`g`后，还需要丢弃`g`可能的返回值。以下函数也不满足：
```lua
return g(x) + 1   -- must do the addition
return x or g(x)  -- must adjust to 1 result
return (g(x))     -- must adjust to 1 result
```
在 Lua 中，只有形如`return func(args)`的调用才是尾调用。

## 迭代器
### 迭代器和闭包
在 Lua 中，迭代器其实就是函数：每次我们调用这个函数，返回的是集合中的「下一个」元素。

每种迭代器都需要维护一些内部状态，这样在连续调用的情况下，它才能知道当前的位置，以及下一步如何继续。闭包可以起到这个作用。以下迭代器返回列表的每个值：
```lua
function values (t)
  local i = 0
  return function ()  i = i + 1; return t[i]  end
end
```
它跟`ipairs`略有不同，它不返回元素的索引，只返回值。在这个例子中，`values`起到了「工厂」的作用。每调用一次这个工厂，它就创建一个新的闭包（就是迭代器），这个闭包维护自己那一份`t`和`i`。每次调用这个迭代器，就会返回`t`里面的下一个元素。在最后一个元素之后，迭代器会返回`nil`，意味着迭代结束。在`while`中使用这个迭代器：
```lua
t = {10, 20, 30}
iter = values(t)           -- creates the iterator
while true do
  local element = iter()   -- calls the iterator
  if element == nil then break end
  print(element)
end
```
在`for`里面使用会更方便：
```lua
t = {10, 20, 30}
for element in values(t) do
  print(element)
end
```
`for`会自己维护好迭代器的状态：
- 内部保存迭代器的函数，就不再需要`iter`变量
- 每次迭代调用一次迭代器
- 遇到`nil`自动终止

迭代器是写起来难，但用起来简单。下面是一个更复杂的例子：
```lua
function allwords ()
  local line = io.read()  -- current line
  local pos = 1           -- current position in the line
  return function ()      -- iterator function


-- repeat while there are lines
while line do
  local s, e = string.find(line, "%w+", pos)
  if s then           -- found a word?
  pos = e + 1       -- next position is after this word
  return string.sub(line, s, e)     -- return the word
else
  line = io.read()  -- word not found; try next line
pos = 1 end
end
    return nil
  end
end
```

### 泛型 for 的迭代语义
泛型`for`的完整语法：
```lua
for <var-list> in <exp-list>
  do <body>
end
```
- `var-list`：一个或多个变量，逗号分隔
- `exp-list`：一个或多个表达式，逗号分隔
`expplist`一般情况下只有一个，生成迭代器的工厂函数。例如：
```lua
for k, v in pairs(t) do print(k, v) end
```
这里，`var-list`就是`k, v`，`exp-list`就是`pairs(t)`。有时`var-list`也只有一个，例如：
```lua
for line in io.lines() do
  io.write(line, "\n")
end
```
我们把第一个变量叫做*控制变量*。它的值不能是`nil`，一旦成为`nil`，循环就结束了。

`for`首先要做的是，对`in`后面的表达式求值，求值会产生三个值：迭代器函数、不变状态（invariant state）、控制变量的初值。只有最后一个（或唯一的）表达式能返回多个值，返回值的个数调整为 3，丢弃多余的，或补`nil`。

完成初始化后，`for`不断调用迭代器函数，并把不变状态和控制变量作为参数传过去。然后再把迭代器返回的值赋给变量列表。如果第一个返回值为`nil`，循环终止。否则就执行循环体部分，并再次调用迭代器，如此循环。下面的循环：
```lua
for var_1, ..., var_n in <explist> do <block> end
```
等同于：
```lua
do
  local _f, _s, _var = <explist>
  while true do
    local var_1, ... , var_n = _f(_s, _var)
    _var = var_1
    if _var == nil then break end
    <block>
  end
end
```

### 无状态迭代器
无状态迭代器，就是不保存内部状态的迭代器，它只使用不变状态（invariant state）、控制变量两个值来获取下一个元素。如下代码：
```lua
a = {"one", "two", "three"}
for i, v in ipairs(a) do
  print(i, v)
end
```
这里的「不变状态」是表对象本身，它在整个迭代过程中是不变的。控制变量保存的是当前的索引值。迭代器的实现如下：
```lua
local function iter (a, i)
  i = i + 1
  local v = a[i]
  if v then
    return i, v
  end
end

function ipairs (a)
  return iter, a, 0
end
```
当`for`循环调用`ipairs(a)`时，会返回三个值：`iter`函数作为迭代器，`a`作为不变状态，`0`作为控制变量的初始值。然后，程序调用`iter(a, 0)`，它会返回`1, a[1]`（除非`a[1]`是`nil`）。第二次迭代，它调用`iter(a, 1)`，结果为`2, a[2]`。如此往复，直到遇到`nil`。

`pairs`函数也是类似的：
```lua
function pairs (t)
  return next, t, nil
end
```
每次迭代调用`next(t, k)`，`k`是表`t`的键值，返回的是表中的下一个键值，以及对应的值。`next(t, nil)`返回第一对结果。当没有更多元素时，`next`返回`nil`。有些人倾向于直接使用`next`：
```lua
for k, v in next, t do
  <loop body>
end
```
因为`for`循环返回三个值，所以上面的循环返回的是`next, t, nil`，跟调用`pairs(t)`的效果一样。

遍历链表也是一个无状态迭代器的例子：
```lua
local function getnext (list, node)
  return not node and list or node.next
end
function traverse (list)  return getnext, list, nil  end
```
这里的技巧是把链表的主节点用作不变状态，而当前节点用作控制变量，第一次当前节点是`nil`，所以会使用`list`作为当前节点。用法：
```lua
list = nil
for line in io.lines() do
  list = {val = line, next = list}
end
for node in traverse(list) do
  print(node.val)
end
```