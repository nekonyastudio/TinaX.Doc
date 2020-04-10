# 在Lua中实现类

TinaX中的Class扩展基于使用MIT协议开源的第三方库[https://github.com/kikito/middleclass](https://github.com/kikito/middleclass)实现，您可以在该项目的[wiki](https://github.com/kikito/middleclass/wiki)中查看详细的使用说明。要注意的是：该项目中类的构造函数为`initialize`, 而在TinaX中class的构造函数是`ctor`

``` lua 
local Animal = class("Animal") -- class('class name')

function Animal:ctor(name) --构造方法
    self.name = name;
end

function Animal:greet()
    print("my name is " .. self.name);
end

------------------------------------------------------------------------

local Meow = class("Meow", Animal) -- 继承自Animal类的喵类

function Meow:ctor(name, subName) --子类的构造方法的参数可与父类不一致
    Animal.ctor(self, name); --调用父类方法，显式传递self而不使用冒号
    self.subName = subName;
end

function Meow:greet() --重载父类方法
    print("都系达，喵类");
end

-------------------------------------------------------------------------

local myMeow = Meow:new("meow", "⊙﹏⊙∥"); --实例化类
myMeow:greet(); --调用方法
```