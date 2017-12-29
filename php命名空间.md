### 命名空间
针对类（包括抽象类和traits）/接口/函数/常量
#### 应用
- namespace MyProject\Sub\Level;
- 大括号定义
``` php
namespace MyProject {
  const CONNECT_OK = 1;
  class Connection { /* ... */ }
  function connect() { /* ... */ }
}
```
- 所有代码之前声明
- 多个文件可以共用1个
- 1个文件中可定义多个命名空间
- 在已定义命名空间的文件中，写全局代码。
``` php
namespace { // 全局代码
  session_start();
  $a = MyProject\connect();
  echo MyProject\Connection::start();
}
```
- 当前空间是指代码所在文件的空间，而不是执行时所在的空间。
- 获取当前空间的字符串 \_\_NAMESPACE\_\_
- 调用
  - 调用当前空间下的 foo(); 或namespace\foo();
  - 调用当前子空间下的 sub\foo(); 或 namespace\sub\foo();
  - 调用指定空间下的 \xxx\foo();
  - 调用全局的 \foo();
  - 在动态语言中使用，如$a = '\Sub\ClassName'; $classObj = new $a; 要用完全限定名称（绝对路径），因为自动补全是在编译期间完成的。开头没有\的会自动加上。双引号要转义"\\\\namespacename\\\\classname"
- 别名
  - 设置空间的别名 use My\Full as Another; 或use My\Full，默认别名就是Full
  - 设置类的别名 use My\Full\Classname as Another; 或 use My\Full\Classname，默认别名就是Classname
  - 设置函数方法的别名，5.6起。use function My\Full\functionName as func; 或use function My\Full\functionName，默认别名就是functionName
  - 设置常量的别名，5.6起。use const My\Full\CONSTANT
#### 功能
- 避免类（包括抽象类和traits）/接口/函数/常量命名冲突。
- 可以设置别名。用于提高代码易读性。
  - 实现：
    - 导入命名空间: 编译期间，存1个哈希表：FC(imports)，如FC(imports)["cc"] = "aa\bb\cc\dd"。在使用类、函数和常量时会把名称按"\"分割，然后以第一节为key查找FC(imports)，如果找到了则将FC(imports)中保存的名称与使用时的名称拼接在一起，组成完整的名称。实际上这种机制是把完整的名称切割缩短然后缓存下来，使用时再拼接成完整的名称，也就是内核帮我们组装了名称，对内核而言，最终使用的都是包括完整namespace的名称。
    - 导入类: 导入的名称保存在FC(imports)中，与空间不同的是不会根据""切割后的最后一节检索，而是直接使用类名查找
    - 导入函数: 通过use function导入到FC(imports_function)，补全时先查找FC(imports_function)，如果没有找到则继续按照a的情况处理
    - 导入常量: 通过use const导入到FC(imports_const)，补全时先查找FC(imports_const)，如果没有找到则继续按照a的情况处理
数据结构
``` c
typedef struct _zend_file_context {
  ...
  //用于保存导入的类或命名空间
  HashTable *imports;
  //用于保存导入的函数
  HashTable *imports_function;
  //用于保存导入的常量
  HashTable *imports_const;
} zend_file_context;
```
