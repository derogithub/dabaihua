**应用**
- 不要使用 unset($_SESSION) 来复位超级变量 $_SESSION，这样会导致无法继续在 $_SESSION 中注册会话变量。
- 不可以将引用保存到session，因为无法将一个引用恢复到另外一个变量。
- 同1个sessionid并发请求时，会因为session文件的锁而不能读取session数据。
    1. 解决方案1：用完session后尽快调用session_write_close()来释放锁。
    2. 解决方案2：使用支持并发操作的会话保存管理器来替代文件会话保存管理器。

**流程**
- session流程
	1. 当开始一个会话时（设置了自动开启或调用session_start()），PHP 会尝试从请求中查找会话 ID （通常通过会话 cookie）
	2. 如果请求中不包含会话 ID 信息，PHP 就会创建一个新的会话。
	3. 会话开始之后，PHP 就会将会话中的数据设置到 $_SESSION 变量中。
	4. 当PHP停止或调用session_write_close()关闭会话的时候，它会自动读取 $_SESSION 中的内容，并将其进行序列化， 然后发送给会话保存管理器来进行保存。
- 自定义会话管理器
	1. 会话开始的时候，PHP 会调用 open 管理器
	2. 然后再调用 read 回调函数来读取内容，该回调函数返回已经经过编码的字符串。 然后 PHP 会将这个字符串解码，并且产生一个数组对象，然后保存至 $_SESSION 超级全局变量。
	3. 当 PHP 关闭的时候（或者调用了 session_write_close() 之后）， PHP 会对 $_SESSION 中的数据进行编码， 然后和会话 ID 一起传送给 write 回调函数。
	4. 之后，PHP 内部将调用 close 回调函数。
	5. 销毁会话时，PHP 会调用 destroy 回调函数。
    
	根据会话生命周期时间的设置，PHP 会不时地调用 gc 回调函数。

**功能**
- 指定session处理器。session.save_handler
- 指定session文件路径。session.save_path，默认/tmp
- 指定session文件目录层级。用于session文件过多时分目录存放。每层64个目录，需要用ext/session/mod_files.sh先创建好目录。
- 指定cookie中sessionid的名字。session.name，默认为 PHPSESSID
- 自动开启。session.auto_start
- 指定session序列化方式。session.serialize_handler，默认php，5.5.4 起可以用php_serialize。
- 设置gc的概率。默认每请求1/100的概率启动gc。session.gc_probability/session.gc_divisor
- 设置session过期时间。过期后可以被gc清除，但不是立即清除。session.gc_maxlifetime
- 设置是否用cookie保存sessionid。session.use_cookies，默认1。
- 指定存sessionid的cookie的过期时间/路径/域名。session.cookie_lifetime，默认0（浏览器关闭后过期）。session.cookie_path，默认/。session.cookie_domain。
- 通过设置header中的Expires/Cache-Control/Last-Modified，来告诉浏览器/代理服务器如何缓存页面。session_cache_limiter，设置缓冲模式。session.cache_expire，设置缓存时间。
- 允许在url参数/form表单中传sessionid。session.use_trans_sid，默认0（关闭）。session.trans_sid_tags（7.1.0起）或url_rewriter.tags（7.1.0前），哪些html标签的url要传。trans_sid_hosts，哪些域名的url要传。
- session文件加锁。基于文件的session存储， 在会话开始的时候都会给会话数据文件加锁， 直到PHP脚本执行完毕或者显式调用session_write_close()（别名session_commit）来保存会话数据。 在此期间，其他脚本不可以访问同一个会话数据文件。

**性能**
- 用户并发请求时，基于文件的session被锁。可以在开启session的时候指定只读，session_start(['read_and_close'=>1])。可以尽早释放锁，调用session_write_close()（别名session_commit）。

**问题**
- $_SESSION的索引不能用数字也不能包含特殊字符(| and !)。解决方案：session序列化用php_serialize。session.serialize_handler

**安全**
- 服务器上的其他用户有可能通过session目录的文件列表破解会话。解决方案：修改session目录的权限。
- 检查referer，如果referer不包含session.referer_check设置的值，把sessionid标记为无效。
- 为防止暴力碰撞sessionid，
	* 7.1.0起可以设置更长更复杂的sessionid。session.sid_length，默认32。session.sid_bits_per_character，每个字符存多少bit。
	* php5开始可以设置哈希算法。session.hash_function，默认0（0 md5，1 sha1），5.3.0起支持hash扩展可用的哈希算法。
	* session.hash_bits_per_character，允许用户定义将二进制散列数据转换为可读的格式时每个字符存放多少个比特。
	* 设置sessionid生成时用到的随机数，可以使sessionid更随机，从而更安全。session.entropy_file指定文件，5.4.0起默认用/dev/random或/dev/urandom。session.entropy_length指定读取的字节数，默认0（禁用）。
	* 7.1.0起可以用session_create_id([ string $prefix ])生成无碰撞的sessionid（会做碰撞检测，具体见官方文档）。同时可以给sessionid设置1个前缀如用户id，可以通过检查前缀判断是否和用户匹配。
- 为防止攻击者随意发送sessionid，不接受服务器没生成过的sessionid。开启session.use_strict_mode后，收到没生成过的sessionid，就生成一个发给浏览器。
	- 情形1：攻击者可以通过邮件给受害者发送一个包含会话 ID 的链接： http://example.com/page.php?PHPSESSID=123456789。 如果启用了 session.use_trans_sid 配置项， 那么受害者将会使用攻击者所提供的会话 ID 开始一个新的会话。 如果启用了 session.use_strict_mode 选项，就可以降低风险。
- 为防止有关通过URL传递sessionid的攻击，指定只能用cookie存sessionid。session.use_only_cookies，5.3.0起默认1。
	- 隐患1：如果在 URL 中包含了会话 ID， 并且访问了外部的站点， 那么你的会话 ID 可能在外部站点的访问日志中被记录（referrer 请求头）。
- 攻击者获得服务器生成的sessionid后，用js注入等手段让用户用这个sessionid，这时候攻击者知道用户用了那个sessionid。
	- 如果你已经启用了 session.use_strict_mode 配置项， 同时使用基于时间戳的会话管理， 并且通过设置 session_regenerate_id() 配置项 来重新生成会话 ID， 那么，攻击者生成的会话 ID 就可以被删除掉了。 当发生对过期会话访问的时候， 你应该保存活跃会话的所有数据， 以备后续分析使用。 然后让用户退出当前的会话，并且重新登录。 防止攻击者继续使用“偷”来的会话。
- 如果必须启用session.use_trans_sid。可以通过session_regenerate_id([ bool $delete_old_session = false ])在每次请求都生成1个新的sessionid。可以选择是否立即删除原sessionid的数据。
	- 立即删除旧session出现问题1：网络不稳定或用户并发请求时，旧session被删除，但用户没有接到新sessionid。可能导致会话丢失或会话数据不一致。解决方案：不立即删除旧session。在旧session中自己存1个时间戳，自己设定一个可接受的过期时间。在这个时间范围内，如果接收到旧sessionid，仍可以使用数据如登录状态。也可以在旧session中存新sessionid，时间范围内把sessionid设置为新sessionid。如果超出时间范围，就手动删掉旧session中的隐私数据。具体见 http://php.net/manual/zh/function.session-regenerate-id.php
	- 不立即删除旧session出现问题2：如果sessionid被攻击者得到，会不安全。
- 为防止网络监听获取sessionid，可设置仅通过https传存sessionid的cookie，由浏览器来控制。Chrome52/Firefox52起，仅通过https可以设置secure的cookie。session.cookie_secure
- 为防止XSS攻击，设置存session的cookie只能被http协议访问，js不能获取。浏览器会控制访问权限。session.cookie_httponly
- 拒绝服务攻击
	- 情形1：攻击者设置了不可删除的 cookie
	- 情形2：攻击者并发请求，基于文件的session一直处于锁定状态
- 即使用HTTPS协议，CRIME 和 BEAST 漏洞可以使得攻击者读取到你的数据。
- 即使用HTTPS协议，HTTPS MITM（中间人攻击），可以窃取 HTTPS 协议下的通信数据。
- 基于session自动登录有风险。
	- 减少风险方法1：自己实现自动登录机制，而不是仅仅依据sessionid。
	- 减少风险方法2：用于自动登录的数据是一次性的。


