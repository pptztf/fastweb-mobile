20140115
日志入库：oracle日志主要用来出报表，mongodb日志主要用来查询详单和统计

1.web是集群，nginx日志分布在不同的服务器上；
2.建立共享目录，在多台服务器之间共享；
3.建立指定用户deploy的定时任务,凌晨4点同步日志【日志时间是每天凌晨3天生成一个文件】
4.建立指定用户oracle的定时任务,凌晨5点导入日志到数据库[oracle and mongodb]
5.配置nginx日志json格式
6.配置nginx日志分卷
7.日志查询
	oracle使用plsql
	mongodb使用rockmongo

加强服务器的安全防护
1.服务器分层架构web/app/db；
2.web在非军事区，需要加强安全防护；
3.使用iptable软件防火墙进行防火，开放必要的端口；【首先要调整ssh-server,禁止root用户登陆、空口令用户登陆、更改端口等】
4.参照"使用iptables加固服务器.sh"，手动一步一步执行。


20140114
solr全文检索工具，下载即可使用：
1.实现中文分词，可自定义词库；提供手动测试例子；
2.实现oracle导入配置示例；
-----------------------------------------------------------------------------------------
快速部署mongodb的分布式集群
1.首先安装moggodb；
2.将当前脚本复制到/home/mymongo/目录下；.bash_profile文件内容添加到当前用户配置文件；
3.机器集群的mymongo用户可以无密码登陆；
4.start-mn.sh启动mongodb分布式集群
5.访问集群 mongo 192.168.0.4 -port 30000 
6.系统的优化和分片机制的激活步骤详见:config-step.txt
7.mongodb数据统计 MapReduce的使用详见：mongodb-统计分析脚本.txt

20130706
动态构造提示信息框
<script type="text/html" id="p-template">
	<div data-role="popup" id="popupBasic1">
		<h1>{{title}}</h1>
		<p>{{msg}}</p>
	</div>  
</script>

<div  id="p-view">
</div>

$(document).on('pageshow','[data-role=page]', function(){
	//动态构造提示框；创建提示框；显示提示框
	template=$('#p-template');
	data={msg:"这是我定制的提示信息",title:"提示信息!"};
	pupopview= Mustache.to_html(template.html(),data).replace(/^\s*/mg, '');
	$('#p-view').empty().append(pupopview).trigger('create');//.popup('refresh');
	$('#popupBasic1').popup();
	$('#popupBasic1').popup('open');
});

JS动态页面加载页面跳转不能刷新
	 加入元素 rel= external  actives
     
    

20130621
    增加
    加入新的html5前端框架，AppFramework(原jqMobi,css进行了升级)。相对于jquerymobile是轻量级的，纯html5 and css3 生成的。
    使用mustache模板引擎动态数据加载。
    左侧菜单组件高度调整(css)。
    表单元素组件。
	面板元素切换。


目标：前端基于组件的快速开发，平台基于引擎的逻辑开发，基于分享的RestApi。

=移动互联快速开发平台=
==设计文档==

==rest-api==
spring rest+spring data+mongodb
  #.采用Mongodb为底层数据库：数据设计随需而变；
  #.采用Mongodb集群，支撑大数据量，大并发实时查询，便于扩展；
  #.采用SpringMongodb简化开发，简单得令人发指；
  #.采用SpringRest提供JSON的输出，支持各种转换；
  #.提供程序整合、兼容中文、跨域JSONP的支持；
  #.进行了大数据量的压力测试，参数的最优配置；
  #.各种最佳实践。

==client==

===安卓===
PhoneGap
===IOS===
PhoneGap
===HTML5===
{{{快速开发的前端架构，专业化分工、快速开发、沉淀复用开发的View。}}}

  #.适配主流的安卓和IOS浏览器；
  #.JQueryMobile组件构造界面；
  #.使用模板引擎Mustache转换JSON数据，动态组合JQueryMobile组件；
  #.使用Dreamweaver的模板和库进行HTML5代码的复用；
  #.引入常用的图表展现工具；


==最佳实践(todo)==
  #.Mongodb的优化配置，Mongodb的分片集群的安装；
  #.Nginx and Keepalived 负责均衡；
  #.Tomcat优化配置，Tomcat集群共享Session MSM(memcached)配置；
  #.fastweb-mobile日志采集分析展现；
  #.Spring aop asyc jmx 等使用示例；
 
==集群==
Tomcat优化配置，Tomcat集群共享Session MSM(memcached)配置；
  #.启动Tomcat MSM的集成环境；
	先手动启动mem-clu1.bat mem-clu2.bat mem-clu3.bat 
  #.启动集群
	手动启动tomcat1.bat tomcat2.bat
  #.测试集群Session,Session一致，集群环境OK
	http://localhost:8180/rest-api/examples/jsp/sessions/my.jsp
	http://localhost:8280/rest-api/examples/jsp/sessions/my.jsp
  #.启动nginx.bat,测试返回session与tomcat的session一致
	http://localhost/rest-api/examples/jsp/sessions/my.jsp
  #.自己笔记本，6G内存，压力测试，并发3000无碍
  #.扩充集群：
	拷贝tomcat-2到tomcat-3,更改server.xml端口不冲突；
	增加nginx配置
	upstream backend {
	ip_hash;
	server 127.0.0.1:8180 weight=10;
	server 127.0.0.1:8280 weight=10;
	server 127.0.0.1:8380 weight=10;
}

==日志采集到mongodb==
1.设置日志格式
log_format  json  '{'
                    '"remote_addr": "$remote_addr",'
                    '"remote_user": "$remote_user",'
                    '"time_local": "$time_local",'
                    '"request": "$request",'
                    '"status": $status,'
                    '"body_bytes_sent": $body_bytes_sent,'
                    '"http_referer": "$http_referer",'
                    '"http_user_agent": "$http_user_agent",'
                    '"http_x_forwarded_for": "$http_x_forwarded_for"'
                  '}';

2.设置输出文件名称
access_log logs/access.log.json json;

3.导入到mongodb
more log2mongodb.sh 
for i in `find /var/log/nginx -name "access.log*.gz"`
do
#echo $i |awk -F= '{print $1}'
filename=`echo $i|awk -F= '{print $1}'`
mkdir -p /var/log/nginx/done-2mongodb/
gunzip $filename   
lname=${filename%.*}
echo $lname
echo 'todo import 2 mongodb '
mongoimport -d admin -c weblog -port 40000  $lname
mv  $lname  /var/log/nginx/done-2mongodb/
echo 'done $lname'
done

#定时调度
#添加执行权限
#chmod +x log2mongodb.sh 
#每天0点01分启动。
#01 00 * * * /路径/log2mongodb.sh 

==一键启动开发环境，一键清理开发环境==
  #.windows 环境下 php + nginx( 已经移除，详见mysmarty项目 )
  


==HTML5项目配置==
  #.访问路径：http://localhost/
  #.资源压缩CSS JS HTML JSON

==一键启动开发环境，一键清理开发环境==
  #.一键启动开发环境，start.bat
  #.一键清理开发环境，stop.bat


==项目地址==
https://code.google.com/p/fastweb-mobile/ 