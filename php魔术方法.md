魔术方法。就是设置当以某种方式使用类或对象时，类和对象的行为。

- __construct() 当创建这个类的对象时调用。子类没有定义会调用父类的，子类有可以用parent::__construct()调用父类的。 用于初始化对象。
- __destruct() 当这个类的对象的所有引用都被删除/对象被显示销毁/脚本关闭/exit()退出时调用。子类没有定义会调用父类的，子类有可以用parent::__destruct()调用父类的。
- __call() 当调用对象的一个不可访问的方法时调用。比如private方法/没有的方法。参数是方法名和方法参数__call(string $name , array $arguments)
- __callStatic() 当调用类的一个不可访问的静态方法时调用。比如private方法/没有的方法。参数是方法名和方法参数__callStatic(string $name , array $arguments) 。5.3.0起。
- __get() 当读取对象的不可访问的属性时调用。比如private属性/没有的属性。可以返回值。参数是属性名__get(string $name)
- __set() 当给对象的不可访问的属性赋值时调用。比如private属性/没有的属性。参数是属性名__set(string $name, mixed $value)
- __isset() 当isset()或empty()对象的不可访问的属性时调用。返回bool。参数是属性名__isset(string $name)
- __unset() 当unset()对象的不可访问的属性时调用。参数是属性名__unset(string $name)
- __toString() 当把对象当作字符串使用时调用。如echo/print/printf。返回一个字符串。
- __invoke() 当把对象当作函数使用时调用。如$obj($str);
- __clone() 当用clone来复制对象时调用。用于自己完成深度复制，比如有属性是对象就clone一下，有属性是引用就重新赋值。
- __sleep() 当serialize()这个类的对象时调用。用于返回一个想被序列化的属性的数组，如果什么也不返回就序列化null。
- __wakeup() 当unserialize()这个类的对象时调用。用于初始化操作，比如建立数据库链接。反序列化可以看作new了一个对象。
- __set_state() 当用var_export()打印类的对象时调用。参数是数组形式的对象属性__set_state(array $properties) 。用于返回一个用来打印的对象。
- __debugInfo() 当用var_dump()打印类的对象时调用。用于返回一个用来打印的数组。5.6.0起。
