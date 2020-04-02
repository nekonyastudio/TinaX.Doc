# Lua扩展 - Class

原生的Lua中并没有Class的功能。TinaX的Class实现主要参考云风的Class实现。

<!-- more -->

代码用法：

``` lua
--定义一个宠物类
local pet = class()

--ctor是类的构造函数
function pet:ctor(name)
    self.name = name
end

function pet:getName()
    return self.name
end

function pet:say()
    print("my name is Van~");
end

function pet:printName()
    print("my name is " .. self.name);
end

-------------------------------------------------

--再定义一个类，继承自pet
local cat = class(pet)

--构造函数是一定得有的
function cat:ctor(name)
    --即使被override，子类和父类的构造函数都会被调用
end

--重载了父类的printName方法
function cat:printName()
    print("cat name is " .. self.super.getName(self)) 
    --在调用父类方法时，只能使用self.super.xxx(self)
    --无法使用self.super:xxx();
end

--------------------------------------------------

--使用类

--实例化
local myCat = cat.new("大喵")
myCat:say();
myCat:printName();

```