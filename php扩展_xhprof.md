英文文档: http://web.archive.org/web/20110514095512/http://mirror.facebook.net/facebook/xhprof/doc.html

XHPGUI: https://github.com/preinheimer/xhprof

简介
- XHProf 是一个轻量级的分层性能测量分析器。 在数据收集阶段，它跟踪调用次数与测量数据，展示程序动态调用的弧线图。 它在报告、后期处理阶段计算了独占的性能度量，例如运行经过的时间、CPU 计算时间和内存开销。 函数性能报告可以由调用者和被调用者终止。 在数据搜集阶段 XHProf 通过调用图的循环来检测递归函数，通过赋予唯一的深度名称来避免递归调用的循环。
- XHProf 包含了一个基于 HTML 的简单用户界面(由 PHP 写成)。 基于浏览器的用户界面使得浏览、分享性能数据结果更加简单方便。 同时也支持查看调用图。
- XHProf 的报告对理解代码执行结构常常很有帮助。 比如此分层报告可用于确定在哪个调用链里调用了某个函数。
- XHProf 对两次运行进行比较（又名 "diff" 报告），或者多次运行数据的合计。 对比、合并报告，很像针对单次运行的“平式视图”性能报告，就像“分层式视图”的性能报告。

php.ini
```
    extension=xhprof.so
    xhprof.output_dir=/web/xhprof/output    储存 XHProf 运行数据的默认目录，用于接口 iXHProfRuns(即 XHProfRuns_Default 类)。
```

常量
- XHPROF_FLAGS_NO_BUILTINS    使得跳过所有内置（内部）函数。
- XHPROF_FLAGS_CPU    使输出的性能数据中添加 CPU 数据。
- XHPROF_FLAGS_MEMORY    使输出的性能数据中添加内存数据。

函数
- void xhprof_enable([ int $flags = 0 [, array $options ]] )    开始统计.$flags标记需要统计的额外信息(见xhprof常量);$options可设置忽略统计的函数:array('ignored_functions' => array('trim','substr'))
- array xhprof_disable( void )    停止统计.返回统计结果.
- xhprof_sample_enable()    轻量级统计开始.xhprof_enable() 的更轻量的版本，以采样模式开始性能分析。 抽样的间隔为 0.1 秒，样本记录了完整的函数调用堆栈。 主要使用的情况是以较低的性能开销来进行性能监控和诊断。
- xhprof_sample_disable()    停止采样模式的 xhprof 分析器。

参数详解：
名词：
- Function Name 函数名
- Calls 调用次数
- Calls% 调用百分比
- Incl. Wall Time (microsec) 调用的包括子函数所有花费时间 以微秒算(一百万分之一秒)
- IWall% 调用的包括子函数所有花费时间的百分比
- Excl. Wall Time (microsec) 函数执行本身花费的时间，不包括子树执行时间,以微秒算(一百万分之一秒)
- EWall% 函数执行本身花费的时间的百分比，不包括子树执行时间
- Incl. CPU(microsecs) 调用的包括子函数所有花费的cpu时间。减Incl. Wall Time即为等待cpu的时间
- 减Excl. Wall Time即为等待cpu的时间
- ICpu% Incl. CPU(microsecs)的百分比
- Excl. CPU(microsec) 函数执行本身花费的cpu时间，不包括子树执行时间,以微秒算(一百万分之一秒)。
- ECPU% Excl. CPU(microsec)的百分比
- Incl.MemUse(bytes) 包括子函数执行使用的内存。
- IMemUse% Incl.MemUse(bytes)的百分比
- Excl.MemUse(bytes) 函数执行本身内存,以字节算
- EMemUse% Excl.MemUse(bytes)的百分比
- Incl.PeakMemUse(bytes) Incl.MemUse的峰值
- IPeakMemUse% Incl.PeakMemUse(bytes) 的峰值百分比
- Excl.PeakMemUse(bytes) Excl.MemUse的峰值
- EPeakMemUse% EMemUse% 峰值百分比

使用范例
``` php
<?php
xhprof_enable(XHPROF_FLAGS_CPU + XHPROF_FLAGS_MEMORY);

for ($i = 0; $i <= 1000; $i++) {
    $a = $i * $i;
}

$xhprof_data = xhprof_disable();

$XHPROF_ROOT = "/tools/xhprof/";
include_once $XHPROF_ROOT . "/xhprof_lib/utils/xhprof_lib.php";
include_once $XHPROF_ROOT . "/xhprof_lib/utils/xhprof_runs.php";

$xhprof_runs = new XHProfRuns_Default();
$run_id = $xhprof_runs->save_run($xhprof_data, "xhprof_testing");

echo "http://localhost/xhprof/xhprof_html/index.php?run={$run_id}&source=xhprof_testing\n";

?>
```

