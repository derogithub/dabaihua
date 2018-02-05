**PHP的配置。php.ini**

- 配置文件: http://www.php.net/manual/zh/configuration.file.php
- 配置可被设定范围: http://www.php.net/manual/zh/configuration.changes.modes.php
- php.ini 配置选项列表: http://www.php.net/manual/zh/ini.list.php
- 怎样修改配置设定: http://www.php.net/manual/zh/configuration.changes.php
- PHP 配置 open_basedir(说明了优先级问题): http://www.cnblogs.com/ybbqg/archive/2012/05/04/2482479.html

- 配置文件（php.ini）在 PHP 启动时被读取。对于服务器模块版本的 PHP，仅在 web 服务器启动时读取一次。对于 CGI 和 CLI 版本，每次调用都会读取。
- 如果存在 php-SAPI.ini（SAPI 是当前所用的 SAPI 名称，因此实际文件名为 php-cli.ini 或 php-apache.ini 等），则会用它替代 php.ini。SAPI 的名称可以用 php_sapi_name() 来测定。
- 优先级
	- 配置参数优先级：服务器(httpd.conf或php-fpm.conf) > 文件内设置(ini_set) > php.ini
	- httpd.conf里配置优先级：Direcotry里 > VirtualHost里,或.htaccess
- php.ini 的搜索路径如下（按顺序）：
	1. SAPI 模块所指定的位置（Apache 2 中的 PHPIniDir 指令，CGI 和 CLI 中的 -c 命令行选项，NSAPI 中的 php_ini 参数，THTTPD 中的 PHP_INI_PATH 环境变量）。
	2. PHPRC 环境变量。在 PHP 5.2.0 之前，其顺序在以下提及的注册表键值之后。
	3. 自 PHP 5.2.0 起，可以为不同版本的 PHP 指定不同的 php.ini 文件位置。将以下面的顺序检查注册表目录：[HKEY_LOCAL_MACHINE\SOFTWARE\PHP\x.y.z]，[HKEY_LOCAL_MACHINE\SOFTWARE\PHP\x.y] 和 [HKEY_LOCAL_MACHINE\SOFTWARE\PHP\x]，其中的 x，y 和 z 指的是 PHP 主版本号，次版本号和发行批次。如果在其中任何目录下的 IniFilePath 有键值，则第一个值将被用作 php.ini 的位置（仅适用于 windows）。
	4. [HKEY_LOCAL_MACHINE\SOFTWARE\PHP] 内 IniFilePath 的值（Windows 注册表位置）。
	5. 当前工作目录（对于 CLI）。
	6. web 服务器目录（对于 SAPI 模块）或 PHP 所在目录（Windows 下其它情况）。
	7. Windows 目录（C:\windows 或 C:\winnt），或 --with-config-file-path 编译时选项指定的位置。

