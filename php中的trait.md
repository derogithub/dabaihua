trait（特性）。为了代码复用。是一种组合的方式，比继承更灵活。相当于把trait中的代码拷贝了过来，除了抽象方法是需要实现的。
#### 概念/约定/设计
- 同名方法优先级 当前类的->trait的->继承的
- 类中属性和trait中属性同名时，除非访问控制和初始值相同，否则都会报fatal error。访问控制和初始值相同，7.0之前报E_STRICT。
#### 应用
- 定义
    - 定义。triat中可以有属性和方法。trait TraitName {}
    - 可以定义trait中方法和属性的访问控制（private/protected/public），在类中调用trait后，所有方法和属性都会引入进来。（相当于把trait中的代码拷贝了过来。）
    - 在triat中定义抽象方法。调用trait的类必须实现。这里就不能相当于把trait中的代码拷贝了过来了。abstract public function funcName();
    - 可以定义静态方法和属性。
- 调用
    - 在类中/trait中 use TraitName;
    - 多个 use TraitName1, TraitName2;
    - 修改trait中方法的访问控制（private/protected/public） 。use TraitName { funcName as protected; }
    - 给trait中方法加别名。原名还可以用。 use TraitName { funcName as aliasName; }
    - 给trait中方法加别名，并且指定别名的访问控制。原名的访问控制不受影响。use TraitName { funcName as private aliasName; }
    - 多个trait中有重名方法，仅使用其中1个。指定1个替代另一个：use TraitName1, TraitName2 { TraitName1::funcName insteadof TraitName2; }
    - 多个trait中有重名方法，两个都要使用。指定1个替代另一个，给另一个指定别名：use TraitName1, TraitName2 { TraitName1::funcName insteadof TraitName2; TraitName2::funcName as aliasName; }
