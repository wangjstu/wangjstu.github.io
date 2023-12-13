---
title: Apache-http-MPM选择以及配置
date: 2023-08-10 20:12:34
tags:
- Apache
categories:
- Apache
---

## 查看是哪一个模式
- 通过httpd -V   httpd -V | grep MPM查看。
- 如果是prefork则表示为prefork模式。 同理，如果是worker，则为worker模式，如果是event，则为event（其中Apache2.2默认使用prefork，Apache2.4默认使用worker/event）。其中event模式因为解决了worker中的keep-alive场景下，长期被占用线程的资源浪费问题，某些线程因为被keep-alive，空挂在哪等待，中间几乎没有请求过来，甚至等到超时。
- Apache是基于模块化的设计，而Apache 2.0更扩展了模块化设计到Web服务器的最基本功能。服务器装载了一种多道处理模块，负责绑定本机网络端口、接受请求，并调度子进程来处理请求。在用户级，MPM看起来和其它Apache模块非常类似。主要区别是在任意时刻只能有一种MPM被装载到服务器中。


## prefork模式
- prefork模式(线程与进程一一对应，进程数将会很多，一个页面将会调用多个进程)：
	![01-apache-http-prefork.png](/2023/08/10/Apache-HTTP-MPM-Selection-and-Configuration/01-apache-http-prefork.png)
- 效率比较高，但要比worker使用内存更大。
- 这个多路处理模块(MPM)实现了一个非线程型的，预派生的web服务器，工作类似于apache1.3。它适用于没有线程安全库，需要避免线程兼容性问题的系统.它要求将每个请求互相独立的情况下最好的MPM，这样一个请求出现问题就不会影响到其他请求。
- 这个MPM具有很强的自我调节能力，只需要很少的配置指定调整。最重要的是将MaxClients（2.4 MaxRequestWorkers）设置为一个足够大的数值以处理潜在的请求高峰，同时，也不能太大，以致需要使用的内存超过实际物理内存的大小。
- 通常配置：
	```conf
	// Apache 2.2  
	  <IfModule prefork.c>  
		  ServerLimit             1200  
		  StartServers           5  
		  MinSpareServers    5  
		  MaxSpareServers    10  
		  MaxClients             1200  
		  MaxRequestsPerChild     2000  
	  </IfModule>
	  
	  // Apache 2.4  
	  <IfModule  mpm_prefork_module>
			ServerLimit            1200
			StartServers          5
			MinSpareServers          5
			MaxSpareServers          10
			MaxRequestWorkers     1200
			MaxRequestsPerChild    2000
	  </IfModule>
	```
	- StartServers 5     指定服务器在启动时建立的子进程数量，默认为 5  
	- MinSpareServers 5   指定空闲子进程的最小数量，默认为5，假如当前空闲子进程数少于MinSpareServers，那么apache将以最大每秒一个速度产生新的子进程。此参数不宜过大。  **例如：** apache在没有用户访问时候有5个闲置的进程，如果有一个用户访问网站，占用了一条线程。则闲置的进程就只有4个，这个值小于MinSpareServers，所以apache就以第一秒1个进程，第二秒2个进程，第三秒4个进程的速度新建空闲进程，即2的N次方产生个数，最大为32个，直到大于等于MinSpareServers个空闲进程才结束。
	- MaxSpareServers 20 配置空闲子进程的最大数量，默认为20，假如当前有超过MaxSpareServers数量的空闲子进程，那么父进程将杀死多余的子进程。此参数不宜过大。  **例如：** apache在没有用户访问时候有5个闲置的进程，如果有5个用户同时访问网站。则闲置的进程就只有0个，这个值小于 MinSpareServers，所以apache就以第一秒1个进程，第二秒2个进程，第三秒4个进程的速度新建空闲进程。直到大于等于 MinSpareServers个空闲进程才结束。在这个例子中直到第三秒，一共生成1+2+4个进程才能满足大于等于MinSpareServers的要求。后来这5个用户访问完apache，访问结束，关闭浏览器。所以apache就有了5+7个空闲的进程。这时空闲的进程比较多，apache就开始关闭一些进程，直到满足小于MaxSpareServers个空闲进程才结束。如果该值小于MinSpareServers则apache默认将该值设置成MinSpareServers+1。
	- ServerLimit：这个参数是控制apache的进程总数的，那为什么会有两个参数控制apache的进程总数呢？这个参数在apache1的时代是没有的，因为那个时候有个256M内存的服务器就很厉害了。后来apache2的时代到来，服务器的硬件也得到升级。很多服务器都是4G内存，还有很多比4G内存大的服务器出现。apache1的时代只有一个MaxClients参数控制进程总数就够了，而这个参数最大值是256定死了。但是到了apache2的时代必须调整ServerLimit值大于256才能使MaxClient支持大于256的值。
	- MaxClients（2.4 MaxRequestWorkers）：apache最大的进程数。apache1的时代只有一个MaxClients参数控制进程总数就够了，而这个参数最大值是256定死了。但是到了apache2的时代必须调整ServerLimit值大于256才能使MaxClient支持大于256的值。
	- MaxRequestsPerChild，举个例子就明白了：
		apache在没有用户访问的时候有5个空闲进程。当一个用户访问网站，访问完又离开。则apache的第一个进程就处理了一个请求，从新进入闲置状态。再有一个用户访问网站，访问完后离开。则apache的第一个进程就处理了1+1个请求。这样继续访问3998个用户，这个进程就处理了4000个请求，之后就自动关闭这个进程。这个时候apache就只有4个限制的进程，小于MinSpareServers值所以apache从今建立一个空闲进程。至于为什么处理完4000个请求就要关闭这个进程呢？答案之一：为了防止内存的泄露。  
		- 设置为大于0有好处：  
			- 能防止内存泄漏无限进行，从而耗尽内存。  
			- 给进程一个有限寿命，从而有助于当服务器负载减轻的时候减少活动进程的数量。
- 通过 ps -ef | grep httpd | wc -l 可以了解当前系统中apache进程数，  从而可以根据实际情况修改以上参数



## Worker/Event（支持混合的多线程多进程的多路处理模块）
- Worker/Event模式（一个进程拥有多个线程，处理多个请求，一个进程可以处理一个页面）：
	![01-apache-http-work-event.png](/2023/08/10/Apache-HTTP-MPM-Selection-and-Configuration/01-apache-http-work-event.png)
- event模式是worker升级版本
- 使用多个子进程，每个子进程有多个线程。每个线程在某个确定的时间只能维持一个连接。通常来说，一个高流量的HTTP服务器上，worker MPM是个比较好的选择，因为worker MPM的内存使用比Prefork MPM要低得多。但是worker MPM也有缺点，如，一个线程崩溃了，整个进程就会连同其任何线程一起“挂了”，由于线程是共享内存空间，所以一个程式在运行时必须被系统识别为“每个线程是安全的”。
- Worker的工作原理：worker是2.0版中全新的支持多线程和多进程混合模型的MPM。由于使用线程来处理，所以可以处理相对海量的请求，而系统资源的开销要小于基于进程的服务器。但是，worker也使用了多进程，每个进程又生成多个线程，以获得基于进程服务器的稳定性。
- Worker的配置：
	```conf
		// worker
		<IfModule mpm_worker_module>
			StartServers          3
			ThreadsPerChild           64
			ServerLimit            20
			MinSpareThreads         75
			MaxSpareThreads         100
			MaxRequestWorkers     1280
			MaxRequestsPerChild    20000
		</IfModule>
		
		// event
		<IfModule  mpm_event_module>
			StartServers          3
			ServerLimit            20
			MinSpareThreads         75
			MaxSpareThreads         100
			ThreadsPerChild           64
			MaxRequestWorkers     1280
			MaxRequestsPerChild    20000
		</IfModule>
	```
- Worker的工作原理：由主控制进程生成“StartServers”个子进程，每个子进程中包含固定的ThreadsPerChild线程数，各个线程独立地处理请求。同样，为了不在请求到来时再生成线程，MinSpareThreads和MaxSpareThreads设置了最少和最多的空闲线程数；而MaxClients设置了所有子进程中的线程总数。如果现有子进程中的线程总数不能满足负载，控制进程将派生新的子进程。
- Worker/Event  conf指令分析：
	- StartServers  服务器启动时建立的子进程数  
	- MaxClients（2.4 MaxRequestWorkers）  允许同时伺服的最大接入请求数量(最大线程数量)。任何超过MaxClients限制的请求都将进入等候队列。  默认值是“400”，16(ServerLimit)乘以25(ThreadsPerChild)的结果。因此要增加MaxClients的时候，你必须同时增加ServerLimit的值  
	- MaxSpareThreads  设置最大空闲线程数。默认值是“250”。这个MPM将基于整个服务器监视空闲线程数。如果服务器中总的空闲线程数太多，子进程将杀死多余的空闲线程。
	- ThreadsPerChild  每个子进程建立的常驻的执行线程数。默认值是25。子进程在启动时建立这些线程后就不再建立新的线程了。  
	- ThreadLimit 对于ThreadsPerChild的限制。默认ThreadsPerChild将小于64，ThreadLimit的声明类似于ServerLimit的效果。  
	- MaxRequestsPerChild  设置每个子进程在其生存期内允许伺服的最大请求数量。到达MaxRequestsPerChild的限制后，子进程将会结束。如果MaxRequestsPerChild为“0”，子进程将永远不会结束。将MaxRequestsPerChild设置成非零值有两个好处：可以防止(偶然的)内存泄漏无限进行，从而耗尽内存。给进程一个有限寿命，从而有助于当服务器负载减轻的时候减少活动进程的数量。  
	- ServerLimit指令解释：
		- 对于preforkMPM，这个指令设置了MaxClients最大允许配置的数值。  
		- 对于worker/eventMPM，这个指令和ThreadLimit结合使用设置了MaxClients最大允许配置的数值。
		- 对于preforkMPM，只有在你需要将MaxClients设置成高于默认值256的时候才需要使用这个指令。要将此指令的值保持和MaxClients一样。  
		- 对于worker/eventMPM，只有在你需要将MaxClients和ThreadsPerChild设置成需要超过默认值16个子进程的时候才需要使用这个指令。  
		- 不要将该指令的值设置的比MaxClients 和ThreadsPerChild需要的子进程数量高。如果显式声明了ServerLimit，那么它_乘以_ThreadsPerChild的值_必须大于等于_MaxClients，而且MaxClients必须是ThreadsPerChild的整数倍，否则Apache将会自动调节到一个相应值（可能是个非期望值）。  
		- **注意：**  Apache在编译时内部有一个硬限制ServerLimit 20000(对于preforkMPM为ServerLimit 200000)。你不能超越这个限制。
	- Maxclient（2.4 MaxRequestWorkers）指令解释：对于非线程型的MPM(也就是prefork)，MaxClients表示可以用于伺服客户端请求的最大子进程数量，默认值是256。要增大这个值，必须同时增大ServerLimit 。对于线程型或者混合型的MPM(event/worker)，MaxClients表示可以用于伺服客户端请求的最大线程数量。对于混合型的MPM默认值是16(ServerLimit)乘以25(ThreadsPerChild)的结果。因此要将MaxClients增加到超过16个进程才能提供的时候，你必须同时增加ServerLimit的值。



## apache配置部分安装参数
- 配置安装命令：
```shell
	./configure --prefix=/opt/sudytech/apache2  --enable-rewrite --enable-so --enable-headers  --enable-expires --with-mpm=event  --enable-modules=most --enable-deflate
```
- --prefix=/opt/sudytech/apache2表示指定apache的安装路径，默认安装路径为/opt/sudytech/apache2
- --enable-rewrite提供URL规则的重写
- --enable-so激活apache服务的DSO（DynamicShared Objects动态共享目标），即在以后可以以DSO的方式编译安装共享模块，这个模块本身不能以DSO方式编译。
- --enable-headers提供允许对HTTP请求头的控制。
- --enable-expires激活通过配置文件控制HTTP的“Expires:”和“Cache-Control:”头内容，即对网站图片、js、css等内容，提供客户端浏览器缓存的设置。这个是apache调优的一个重要选项之一。
- --with-mpm= event选择apache mpm的模式为event模式。为event模式原理是更多的使用线程来处理请求，所以可以处理更多的并发请求。而系统 资源的开销小玉基于进程的preforkMPM。如果不指定此参数，默认的模式是prefork进程模式。也可以指定 event模式.这个是apache调优的一个重要选项之一。
- --enable-deflate提供对内容的压缩传输编码支持，一般是html、js、css等内容的站点。使用此参数会打打提高传输速度，提升访问者访问的体验。在生产环境中，这是apache调优的一个重要选项之一



## 优化配置 MaxClients
- 配置文件中：  KeepAlive On/Off  指保持连接活跃，类似于mysql永久连接。  如果设置为On，那么来自同一客户端的请求就不需要再一次连接，避免每次请求都要新建一个连接而加重服务器的负担。
- KeepAliveTimeOut 20  如果第二次和第一次请求间隙时间超过了这个值，第一次连接就会中断，再新建第二个连接。一般设置了20秒可以了
- MaxKeepAliveRequests 100  一次连接可以进行的HTTP请求的最大次数。将其值设置为0，将支持一次连接内进行无限次的传输请求。事实上，没有客户程序在一次连接中请求太多的页面，通常达不到这个上限就完成了连接了
- 一个用户一旦连接上，在keeyalive 的存活时间内（KeepAliveTimeout，默认5秒）都不用重新打开连接，如果这个时候连接数到达上线，解决的方法就是加大apache的最大连接数。
- 连接数理论上当然是支持越大越好，但要在服务器的能力范围内，这跟服务器的CPU、内存、带宽等都有关系：
	- 计算单个进程占用内存量：
	    ```shell
		查看当前的连接数可以用：  
		ps aux | grep httpd | wc -l  
		或：  
		pgrep httpd|wc -l  
		计算httpd占用内存的平均数:  
		ps aux|grep -v grep|awk '/httpd/{sum+=$6;n++};END{print sum/n}'
		```
	- 注意，MaxClients默认最大为250，若要超过这个值就要显式设置ServerLimit，且ServerLimit要放在MaxClients之前，值要不小于MaxClients，不然重启httpd时会有提示。  重启httpd后，通过反复执行pgrep httpd|wc -l 来观察连接数。(MaxRequestsPerChild不能设置为0，可能会因内存泄露导致服务器崩溃）
- 更佳最大值计算的公式：  
	- apache_max_process_with_good_perfermance < (total_hardware_memory / apache_memory_per_process ) * 2  
	- apache_max_process = apache_max_process_with_good_perfermance * 1.5



参考：
- [Apache MPM选择以及配置](https://www.sudytech.com/_s80/_t690/2018/0729/c3276a26060/page.psp)
- [Apache HTTPD : MaxRequestsPerChild(formaly MaxClients) and MaxConnectionsPerChild (formaly MaxRequestsPerChild) ](https://cmdref.net/middleware/web/httpd/maxclients.html#apache_22_vs_apache_24maxclients_and_maxrequestworkers)