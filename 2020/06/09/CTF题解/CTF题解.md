# WEB #
# *攻防世界* #
## 攻防世界001 baby web ##
将url修改为index.php页面跳转为1.php，对index.php页面抓包获得flag。
![](http://ww1.sinaimg.cn/large/007u7C9Rly1ge3q8zkspmj30c109oglt.jpg)


## 攻防世界002 Training-WWW-Robots ##
由题意知该题目存在robots.txt文件。
![](http://ww1.sinaimg.cn/large/007u7C9Rly1ge3qefjmq4j30ge04zgln.jpg)
发现一个php页面，访问后获得flag.
![](http://ww1.sinaimg.cn/large/007u7C9Rly1ge3qfmqngij30gy03b74c.jpg)


## 攻防世界003 Web python template injection ##
参考[python模板注入](https://www.cnblogs.com/tr1ple/p/9415641.html "python模板注入")


## 攻防世界004 php_rce ##



# *BUUOJ* #
## [HCTF 2018]WarmUp ##
![](http://ww1.sinaimg.cn/large/007u7C9Rgy1gf9opzsedcj307q02g3yd.jpg)

查看源码，发现提升，打开source.php页面
![](http://ww1.sinaimg.cn/large/007u7C9Rgy1gf9osemvjqj30jg0m775b.jpg)

看到题目php代码

![](http://ww1.sinaimg.cn/large/007u7C9Rgy1gf9otp9bbhj30k902qweh.jpg)

打开hint.php页面，发现提示。
回到source.php页面，函数代码中有四个if语句

    第一个if语句对变量进行检验，要求$page为字符串，否则返回false
    第二个if语句判断$page是否存在于$whitelist数组中，存在则返回true
    第三个if语句判断截取后的$page是否存在于$whitelist数组中，截取$page中'?'前部分，存在则返回true
    第四个if语句判断url解码并截取后的$page是否存在于$whitelist中，存在则返回true
    若以上四个if语句均未返回值，则返回false

有三个if语句可以返回true，第二个语句直接判断$page，不可用
第三个语句截取'?'前部分，由于?被后部分被解析为get方式提交的参数，也不可利用
第四个if语句中，先进行url解码再截取，因此我们可以将?经过两次url编码，在服务器端提取参数时解码一次，checkFile函数中解码一次，仍会解码为'?'，仍可通过第四个if语句校验。（'?'两次编码值为'%253f'）,构造url：
![](http://ww1.sinaimg.cn/large/007u7C9Rgy1gf9p1nmo3rj30rv037wej.jpg)
无返回值，由于我们不知道ffffllllaaaagggg文件的具体位置，只能依次增加../，最终在![](http://ww1.sinaimg.cn/large/007u7C9Rgy1gf9p3bfc4bj30x205r750.jpg)
中成功回显flag


## [强网杯 2019]随便注 ##
![](http://ww1.sinaimg.cn/large/007u7C9Rgy1gf9p6owmc0j30x304waah.jpg)
该题考查**堆叠注入**
![](http://ww1.sinaimg.cn/large/007u7C9Rgy1gf9pd5u3nfj31bi0520t3.jpg)
输入1'提交页面不正常

![](http://ww1.sinaimg.cn/large/007u7C9Rgy1gf9peqna3ij30cd0hk750.jpg)

输入1' or 1=1 #，页面变为正常，说明存在注入。

![](http://ww1.sinaimg.cn/large/007u7C9Rgy1gf9pj0d3nzj30hi04s0st.jpg)

输入1' order by 3#，页面出现错误，说明只有两个字段

![](http://ww1.sinaimg.cn/large/007u7C9Rgy1gf9pl8yxfrj30mn04jq2x.jpg)

使用union select查询，发现很多语句被过滤。

![](http://ww1.sinaimg.cn/large/007u7C9Rgy1gf9pso2bowj30kg0mx0tm.jpg)

使用堆叠注入1';show databases; #列出数据库名。

![](http://ww1.sinaimg.cn/large/007u7C9Rgy1gf9pvbzlxlj30hx0gmgma.jpg)

接着使用1';show tables;#查询出表

![](http://ww1.sinaimg.cn/large/007u7C9Rgy1gf9px8yi8cj309g0o6dgo.jpg)

使用1';show columns from words;#查出表words的字段

![](http://ww1.sinaimg.cn/large/007u7C9Rgy1gf9q1mxqi0j30bk0h3q3i.jpg)

然后使用1';show columns from `1919810931114514`;#查出表1919810931114514的字段(如果表名为纯数字，则需要加上`),从而发现flag字段。

因为这里有两张表，会县内容肯定是从word这张表中回显的，那我们怎么才能让它回显flag所在的表呢

内部查询语句类似 : select id, data from word where id =

(这里从上面的对word列的查询可以看到它是有两列，id和data)

然后1919810931114514只有一个flag字段

这时候虽然有强大的正则过滤，但没有过滤alert和rename关键字

这时候我们就可以已下面的骚姿势进行注入：

1.将words表改名为word1或其它任意名字

2.1919810931114514改名为words

3.将新的word表插入一列，列名为id

4.将flag列改名为data

构造payload

1';rename table words to word1;rename table `1919810931114514` to words;alter table words add id int unsigned not Null auto_increment primary key; alert table words change flag data varchar(100);#

![](http://ww1.sinaimg.cn/large/007u7C9Rgy1gf9q65ogotj30io06mq32.jpg)

然后使用1' or 1=1 #查询获得flag.


## [SUCTF 2019]EasySQL ##
同样是考察堆叠注入

![](http://ww1.sinaimg.cn/large/007u7C9Rgy1gf9qe0hjvej30g7046746.jpg)
![](http://ww1.sinaimg.cn/large/007u7C9Rgy1gf9qhhl6mej30eo04q0sn.jpg)
![](http://ww1.sinaimg.cn/large/007u7C9Rgy1gf9qhqxmofj30ev04x747.jpg)

输入1发现页面有回显，输入1-1数值改变，说明是数值型的注入。
![](http://ww1.sinaimg.cn/large/007u7C9Rgy1gf9qkfyws7j31cl044wen.jpg)
![](http://ww1.sinaimg.cn/large/007u7C9Rgy1gf9qkzny0hj30er03mwef.jpg)

依次查询数据库并查询出表，并发现Flag表

![](http://ww1.sinaimg.cn/large/007u7C9Rgy1gf9qnjgiugj30fr032gli.jpg)

但发现from Flag被过滤所以不能直接读取flag

看了别人的题解知道本题的原理

![](http://ww1.sinaimg.cn/large/007u7C9Rgy1gf9qpv9tp0j30cy01kdfn.jpg)

解题思路1：

payload：![](http://ww1.sinaimg.cn/large/007u7C9Rgy1gf9qtrqv9hj301e00y0dl.jpg)

查询语句：![](http://ww1.sinaimg.cn/large/007u7C9Rgy1gf9qt6po3gj307f00xmwx.jpg)

解题思路2：

payload:1;

![](http://ww1.sinaimg.cn/large/007u7C9Rgy1gf9qsq9fjbj30b100uwea.jpg)

解析：

    在oracle 缺省支持 通过 ‘ || ’ 来实现字符串拼接。
    但在mysql 缺省不支持。需要调整mysql 的sql_mode
    模式：pipes_as_concat 来实现oracle 的一些功能。


## [护网杯 2018]easy_tornado ##
本题考查**ssti(服务端模板注入)**
![](http://ww1.sinaimg.cn/large/007u7C9Rgy1gfaa6dsmr9j309h043745.jpg)

打开页面发现3个文件

![](http://ww1.sinaimg.cn/large/007u7C9Rgy1gfaa8gc7ajj307n02h742.jpg)
![](http://ww1.sinaimg.cn/large/007u7C9Rgy1gfaaeo8xoej30x601m0ss.jpg)
![](http://ww1.sinaimg.cn/large/007u7C9Rgy1gfaa8smxbmj304w029we9.jpg)
![](http://ww1.sinaimg.cn/large/007u7C9Rgy1gfaaf34iioj30wh01vglo.jpg)
![](http://ww1.sinaimg.cn/large/007u7C9Rgy1gfaa91n0zhj30b9026glf.jpg)
![](http://ww1.sinaimg.cn/large/007u7C9Rgy1gfaafapucfj30ux01qmx7.jpg)

依次打开三个txt文件得到提示，所以需要知道cookie_secret。

[https://blog.csdn.net/qq78827534/article/details/80792514](https://blog.csdn.net/qq78827534/article/details/80792514 "render函数介绍")

![](http://ww1.sinaimg.cn/large/007u7C9Rgy1gfaai2hq1uj30uu01w0ss.jpg)
![](http://ww1.sinaimg.cn/large/007u7C9Rgy1gfaaiwi6ifj308f036dfm.jpg)
![](http://ww1.sinaimg.cn/large/007u7C9Rgy1gfaaj6kp2cj30jz025mx4.jpg)

只修改文件名不修改文件hash值，报错。

可以看到在报错的url中参数msg值为Error，然后界面就显示Error。
![](http://ww1.sinaimg.cn/large/007u7C9Rgy1gfaaovcufmj30xt0330sy.jpg)
继续修改证实此处存在模板注入
![](http://ww1.sinaimg.cn/large/007u7C9Rgy1gfaaqglp4mj31ap0h9403.jpg)
在这里我们应该可以得到cookie_secret

![](http://ww1.sinaimg.cn/large/007u7C9Rgy1gfaas3fnwdj30b9026glf.jpg)

然后照着hint解出文件得hash值

![](http://ww1.sinaimg.cn/large/007u7C9Rgy1gfaax5makhj314h0hk3zl.jpg)

从而获得flag。


## [极客大挑战 2019]EasySQL ##
![](http://ww1.sinaimg.cn/large/007u7C9Rgy1gfab2fglexj31540jsgzp.jpg)
![](http://ww1.sinaimg.cn/large/007u7C9Rgy1gfab3fdaq7j30cx0bc42b.jpg)

尝试万能密码
![](http://ww1.sinaimg.cn/large/007u7C9Rgy1gfab4aph2oj30pp0d8qa3.jpg)

获得flag.


## [RoarCTF 2019]Easy Calc ##
![](http://ww1.sinaimg.cn/large/007u7C9Rgy1gfab99x02cj315207oglp.jpg)
![](http://ww1.sinaimg.cn/large/007u7C9Rgy1gfab8totonj30ln066aap.jpg)

查看源代码，发现提示所以需要绕过WAF。
![](http://ww1.sinaimg.cn/large/007u7C9Rgy1gfabgnz7w1j30v30bpt9s.jpg)

查看calc.php页面，显示了waf的法则。

进行绕waf，首先了解一下php的解析规则，当php进行解析的时候，如果变量前面有空格，会去掉前面的空格再解析，那么我们就可以利用这个特点绕过waf。

num被限制了，那么'  num'呢，在num前面加了空格。waf就管不着了，因为waf只是限制了num，waf并没有限制'  num'，当php解析的时候，又会把'   num'前面的空格去掉在解析，利用这点来上传非法字符

构造payload来查看目录，用chr转化成ascll码进行绕过
![](http://ww1.sinaimg.cn/large/007u7C9Rgy1gfabuatrf6j31gu0hgtaf.jpg)
发现 string(5) "f1agg" 
![](http://ww1.sinaimg.cn/large/007u7C9Rgy1gfabyjc1ynj31250h9my7.jpg)
查看flag。


## [极客大挑战 2019]Havefun ##
![](http://ww1.sinaimg.cn/large/007u7C9Rgy1gfac0kkj6ej30ek07yjrq.jpg)

查看源代码，发现提示
![](http://ww1.sinaimg.cn/large/007u7C9Rgy1gfac1m4l3kj31820ejgmy.jpg)

构造payload获得flag。


## [HCTF 2018]admin ##
存在以下三种解法：

    flask session 伪造
    unicode欺骗
    条件竞争

![](http://ww1.sinaimg.cn/large/007u7C9Rgy1gfaglrpya9j30mk0h7js1.jpg)
![](http://ww1.sinaimg.cn/large/007u7C9Rgy1gfagmn0wcij30ia09h3yk.jpg)

注册一个test用户，登录。
![](http://ww1.sinaimg.cn/large/007u7C9Rgy1gfagoh0dvwj30ry08udh3.jpg)
在修改密码的源码中发现提示。
![](http://ww1.sinaimg.cn/large/007u7C9Rgy1gfagqd7i8tj30m70avaay.jpg)

下载文件

![](http://ww1.sinaimg.cn/large/007u7C9Rgy1gfaijmd63ej30jw03edfv.jpg)

是一个flask项目，那就直接先奔路由去看一下，打开route.py，看一下index的注册函数代码

![](http://ww1.sinaimg.cn/large/007u7C9Rgy1gfaikmtd1aj30qx09at9f.jpg)
发现index注册函数没做什么处理，直接返回index.html渲染模版，于是我们看一下templates/index.html代码

![](http://ww1.sinaimg.cn/large/007u7C9Rgy1gfaiovsvsej30ql0ndmza.jpg)

![](http://ww1.sinaimg.cn/large/007u7C9Rgy1gfaip70juwj30pc0fyt9w.jpg)

发现真的是要登录成admin才能得到flag。于是继续看向route.py文件，看看login和change password的注册函数处理代码是怎么写的。

**解法一 —— flask session 伪造**

我们可以用python脚本把flask的session解密出来，但是如果想要加密伪造生成我们自己的session的话，还需要知道flask用来签名的SECRET_KEY，在github源码里找找，可以在config.py里发现下面代码

[https://github.com/noraj/flask-session-cookie-manager](https://github.com/noraj/flask-session-cookie-manager "flask_session_manage")
![](http://ww1.sinaimg.cn/large/007u7C9Rgy1gfaixw55qtj30pa05qq39.jpg)
 
![](http://ww1.sinaimg.cn/large/007u7C9Rgy1gfalpci52zj30og04oq36.jpg)
![](http://ww1.sinaimg.cn/large/007u7C9Rgy1gfalpr57d2j30ml05xwjt.jpg)
![](http://ww1.sinaimg.cn/large/007u7C9Rgy1gfalqobraoj30mj05c42c.jpg)

用得到的cookie值替换掉index页面的cookie值，即可成功伪造session，"变成admin"，得到flag

![](http://ww1.sinaimg.cn/large/007u7C9Rgy1gfaltk9yfnj30qb0bkt95.jpg)

**法二 —— Unicode欺骗**

![](http://ww1.sinaimg.cn/large/007u7C9Rgy1gfamywjzioj30d502jglg.jpg)
这里用到了nodeprep.prepare函数，而nodeprep是从twisted模块中导入的from twisted.words.protocols.jabber.xmpp_stringprep import nodeprep，在requirements.txt文件中，发现这里用到的twisted版本是Twisted==10.2.0，而官网最新版本为19.2.0(2019/6/2)，版本差距这么大，估计是存在什么漏洞。

原理就是利用nodeprep.prepare函数会将unicode字符ᴬ转换成A，而A在调用一次nodeprep.prepare函数会把A转换成a。
所以当我们用ᴬdmin注册的话，后台代码调用一次nodeprep.prepare函数，把用户名转换成Admin，我们用ᴬdmin进行登录，可以看到index页面的username变成了Admin，证实了我们的猜想，接下来我们就想办法让服务器再调用一次nodeprep.prepare函数即可。
![](http://ww1.sinaimg.cn/large/007u7C9Rgy1gfan8syszjj30cq09j3yj.jpg)


我们发现在改密码函数代码里，也用到了nodeprep.prepare函数，也就是说，我们在这里改密码的话，先会把username改为admin，从而改掉admin的密码。
![](http://ww1.sinaimg.cn/large/007u7C9Rgy1gfana7tvutj30om0as74g.jpg)
然后用admin和改的密码的登录，即可获取flag。
![](http://ww1.sinaimg.cn/large/007u7C9Rgy1gfanbj04yrj30sz0bpt95.jpg)

**解法三 —— 条件竞争**
[https://www.jianshu.com/p/f92311564ad0](https://www.jianshu.com/p/f92311564ad0 "HCTF2018-admin")


## [极客大挑战 2019]Secret File ##
![](http://ww1.sinaimg.cn/large/007u7C9Rgy1gfanstiw0oj30sy04ggmf.jpg)
查看源码发现提示

![](http://ww1.sinaimg.cn/large/007u7C9Rgy1gfantton39j30lg0cd74g.jpg)
![](http://ww1.sinaimg.cn/large/007u7C9Rgy1gfanujro2lj30ev087dft.jpg)

继续点击，发现什么也没有，根据提示抓包分析。

![](http://ww1.sinaimg.cn/large/007u7C9Rgy1gfanvn1llgj307804va9x.jpg)

发现php页面，打开该页面
![](http://ww1.sinaimg.cn/large/007u7C9Rgy1gfbe5son5aj30uk0aaaap.jpg)
发现是文件包含漏洞。
![](http://ww1.sinaimg.cn/large/007u7C9Rgy1gfbefqitdfj30s7092aa9.jpg)
但是打开flag.php并不能看到flag，所以flag应该是写在php代码里面

传入的文件进行了过滤，但没有过滤filter，所以可以用php://filter来获取文件。
![](http://ww1.sinaimg.cn/large/007u7C9Rgy1gfbebnttfzj31hc096abe.jpg)
获取到flag.php的base64编码，进行解码获得flag。
![](http://ww1.sinaimg.cn/large/007u7C9Rgy1gfbee8u6y0j30m60880tg.jpg)


## [SUCTF 2019]CheckIn ##
![](http://ww1.sinaimg.cn/large/007u7C9Rgy1gfbetawh5jj308g048mx2.jpg)

发现是文件上传漏洞，于是想到上传一句话木马。

![](http://ww1.sinaimg.cn/large/007u7C9Rgy1gfbeulv6d1j307n04pwef.jpg)

上传一句话木马后发现<?被过滤了，所以不能使用<?php,所以使用其他的方法构造一句话木马，如script脚本
![](http://ww1.sinaimg.cn/large/007u7C9Rgy1gfbfoa6kc7j30hg00wa9v.jpg)
![](http://ww1.sinaimg.cn/large/007u7C9Rgy1gfbfp2jc2oj308v059mx5.jpg)

上传之后发现错误提示，所以需要绕过exif_imagetype。
![](http://ww1.sinaimg.cn/large/007u7C9Rgy1gfbfrgfz2nj30hf01m0sm.jpg)

所以在一句话木马前加上GIF89a再上传。
![](http://ww1.sinaimg.cn/large/007u7C9Rgy1gfbfuz0qq3j30np05sgly.jpg)
![](http://ww1.sinaimg.cn/large/007u7C9Rgy1gfbfwrx895j30ae04wq2s.jpg)

然后用蚁剑连接index.php页面，发现不能连接，所以是木马没有执行。

看了题解后发现这题还有一个知识点就是**.user.ini**
[https://wooyun.js.org/drops/user.ini%E6%96%87%E4%BB%B6%E6%9E%84%E6%88%90%E7%9A%84PHP%E5%90%8E%E9%97%A8.html](https://wooyun.js.org/drops/user.ini%E6%96%87%E4%BB%B6%E6%9E%84%E6%88%90%E7%9A%84PHP%E5%90%8E%E9%97%A8.html ".user.ini")

![](http://ww1.sinaimg.cn/large/007u7C9Rgy1gfbg2rycpdj309w01tmwz.jpg)
于是创建一个.user.ini文件并将.user.ini和aaa.jpg文件上传
![](http://ww1.sinaimg.cn/large/007u7C9Rgy1gfbg4unow1j30tl05m3yw.jpg)
![](http://ww1.sinaimg.cn/large/007u7C9Rgy1gfbg63dc06j30ca03zmx5.jpg)

然后蚁剑连接获得flag。


## [极客大挑战 2019]LoveSQL ##
![](http://ww1.sinaimg.cn/large/007u7C9Rgy1gfbgko45ufj30rh0i5woi.jpg)
尝试万能密码
![](http://ww1.sinaimg.cn/large/007u7C9Rgy1gfbgm3qdh3j30tj09kgpl.jpg)
登录成功，说明存在注入点

![](http://ww1.sinaimg.cn/large/007u7C9Rgy1gfbgpg5cd6j30eu03iq32.jpg)
![](http://ww1.sinaimg.cn/large/007u7C9Rgy1gfbgpu8llwj30sy09fgp3.jpg)
说明有3个字段。

![](http://ww1.sinaimg.cn/large/007u7C9Rgy1gfbgr5czwlj30f80a4dj7.jpg)

在用户名方框里没有注入成功，在密码方框里注入成功

在密码框中使用
admin' union select 1,2,group_concat(table_name) from information_schema.tables where table_schema=database() #
列出表名
![](http://ww1.sinaimg.cn/large/007u7C9Rgy1gfbh26o28hj30lb0a0wi4.jpg)

接着使用
admin' union select 1,2,group_concat(column_name) from information_schema.columns where table_name="l0ve1ysq1" #
列出字段

![](http://ww1.sinaimg.cn/large/007u7C9Rgy1gfbh9yytzxj30m109z420.jpg)

最后使用
admin' union select 1,2,group_concat(password) from l0ve1ysq1 #
![](http://ww1.sinaimg.cn/large/007u7C9Rgy1gfbhbpxpejj30wy0d2jxu.jpg)

获得flag。


## [极客大挑战 2019]PHP ##
![](http://ww1.sinaimg.cn/large/007u7C9Rgy1gfbl86nvixj30oj07g3zk.jpg)
根据提示，页面有备份文件

![](http://ww1.sinaimg.cn/large/007u7C9Rgy1gfbl7ooiwbj30aw011weh.jpg)

使用dirsearch扫描，发现备份文件www.zip
![](http://ww1.sinaimg.cn/large/007u7C9Rgy1gfbl9scevgj30l804gwep.jpg)
![](http://ww1.sinaimg.cn/large/007u7C9Rgy1gfblb4esaxj30og0o3gn8.jpg)
![](http://ww1.sinaimg.cn/large/007u7C9Rgy1gfblbvo3qlj30b403x3yh.jpg)

从class.php和index.php可知，该题是反序化漏洞。代码审计可知username应为admin，password应为100才能获得flag。
![](http://ww1.sinaimg.cn/large/007u7C9Rgy1gfblhrdqzsj30j00fl75c.jpg)
![](http://ww1.sinaimg.cn/large/007u7C9Rgy1gfblitplw1j30lg01ya9x.jpg)

将代码反序化

public、protected与private在序列化时的区别和__wakeup()方法绕过参考：
[https://www.cnblogs.com/wangtanzhi/p/12193930.html](https://www.cnblogs.com/wangtanzhi/p/12193930.html "参考链接")

![](http://ww1.sinaimg.cn/large/007u7C9Rgy1gfblqidxzoj314i0hddj5.jpg)
构造payload获得flag，也可以参考上面的参考链接中
![](http://ww1.sinaimg.cn/large/007u7C9Rgy1gfblrwxv7sj30na01q0sn.jpg)

用python传值获得flag


## [极客大挑战 2019]Knife ##
![](http://ww1.sinaimg.cn/large/007u7C9Rgy1gfblwywrjlj30lt0980sw.jpg)

发现是一句话木马，直接用菜刀连接

![](http://ww1.sinaimg.cn/large/007u7C9Rgy1gfblyno71fj30gj08e74n.jpg)
![](http://ww1.sinaimg.cn/large/007u7C9Rgy1gfblzgek3aj30f403kq2y.jpg)

然后获得flag。


## [极客大挑战 2019]Http ##
![](http://ww1.sinaimg.cn/large/007u7C9Rgy1gfbm49rup2j30jw06iglm.jpg)

查看源码，发现提示
![](http://ww1.sinaimg.cn/large/007u7C9Rgy1gfbm5hp0hdj319b09pn3u.jpg)
想到抓包，直接改header头信息即可，我们可以通过使用Referer头来修改
![](http://ww1.sinaimg.cn/large/007u7C9Rgy1gfbm8kwmz8j30yn0ha76b.jpg)
修改之后提示说使用Syclover浏览，于是修改User-Agent
![](http://ww1.sinaimg.cn/large/007u7C9Rgy1gfbma2trtuj30yp0gftan.jpg)
根据提示，需要修改X-Forwarded-For为127.0.0.1
![](http://ww1.sinaimg.cn/large/007u7C9Rgy1gfbmcnwclyj30y70g940e.jpg)
从而获得flag。


## [GKCTF2020]CheckIN ##
![](http://ww1.sinaimg.cn/large/007u7C9Rgy1gfbmr1prhpj30ne0dbdgp.jpg)

审计代码发现Ginkgo可以传参，于是想到可以传一句话木马，审计代码可知要把$this->code进行base64加密。
![](http://ww1.sinaimg.cn/large/007u7C9Rgy1gfbnef4v15j30qo0213yl.jpg)

所以Ginkgo传入加密后的**@eval($_POST[value]);**
![](http://ww1.sinaimg.cn/large/007u7C9Rgy1gfbng00sipj30f60c774p.jpg)

用蚁剑连接
![](http://ww1.sinaimg.cn/large/007u7C9Rgy1gfbnuh85awj30jw076taw.jpg)

直接利用github上的exploit:
[https://github.com/mm0r1/exploits/blob/master/php7-gc-bypass/exploit.php](https://github.com/mm0r1/exploits/blob/master/php7-gc-bypass/exploit.php "exploit")
![](http://ww1.sinaimg.cn/large/007u7C9Rgy1gfbnxf3636j30gd07wmxj.jpg)
改为执行/readflag。
![](http://ww1.sinaimg.cn/large/007u7C9Rgy1gfbnyikohcj30s90fomy3.jpg)
上传到服务器

![](http://ww1.sinaimg.cn/large/007u7C9Rgy1gfbo3njuclj30zr066t9l.jpg)
构造payload：?Ginkgo=include('/tmp/exploit.php');并将include('/tmp/exploit.php');用base64加密，执行获得flag。


## [GKCTF2020]cve版签到 ##
由hint知识**cve-2020-7066，ssrf**漏洞
[https://security-tracker.debian.org/tracker/CVE-2020-7066](https://security-tracker.debian.org/tracker/CVE-2020-7066 "CVE-2020-7066")
![](http://ww1.sinaimg.cn/large/007u7C9Rgy1gfbo8gz2aaj30mi040t8s.jpg)
![](http://ww1.sinaimg.cn/large/007u7C9Rgy1gfbo9eot9fj30rc0hu0th.jpg)
点击View CTFHub发现什么也没有
根据漏洞可知存在\0截断。
[https://blog.csdn.net/weixin_45485719/article/details/106432960?fps=1&locationNum=2](https://blog.csdn.net/weixin_45485719/article/details/106432960?fps=1&locationNum=2 "cve-2020-7066简单解析")
![](http://ww1.sinaimg.cn/large/007u7C9Rgy1gfbofn9rapj309h014q2q.jpg)
![](http://ww1.sinaimg.cn/large/007u7C9Rgy1gfbodclbhzj30u20gygn7.jpg)
根据提示知应该改为127.0.0.123
![](http://ww1.sinaimg.cn/large/007u7C9Rgy1gfboejavs8j30ti0hh75x.jpg)
从而获得flag。 


## [GKCTF2020]老八小超市儿 ##
![](http://ww1.sinaimg.cn/large/007u7C9Rgy1gfbokpmsh8j30ze0bxjya.jpg)
从图中可知是ShopXO漏洞
参考
[http://www.nctry.com/1660.html](http://www.nctry.com/1660.html "ShopXO")
![](http://ww1.sinaimg.cn/large/007u7C9Rgy1gfbonczmoqj30vx0gracr.jpg)
进入后台登录界面，用admin,shopxo登录后台
![](http://ww1.sinaimg.cn/large/007u7C9Rgy1gfbopongy4j30si0l9tdo.jpg)
下载默认主题
![](http://ww1.sinaimg.cn/large/007u7C9Rgy1gfbottukynj30qw06naal.jpg)
将写好的一句话木马文件直接拖入压缩包中(注意：不能先解压然后将文件放进去再压缩，这样文件就会失效)
![](http://ww1.sinaimg.cn/large/007u7C9Rgy1gfbow18efvj30lf0ea0tk.jpg)
然后上传木马文件，用蚁剑连接
地址是：
![](http://ww1.sinaimg.cn/large/007u7C9Rgy1gfbozq44m5j30lu01oaa1.jpg)
在目录下看到
![](http://ww1.sinaimg.cn/large/007u7C9Rgy1gfbp0wk696j30ko02it8o.jpg)
告诉我们flag在/root下，并且flaghint中的时间每隔一分钟会变化
![](http://ww1.sinaimg.cn/large/007u7C9Rgy1gfbp394dfvj30ih03ogln.jpg)
根据auto.sh找到makeflaghint.py
![](http://ww1.sinaimg.cn/large/007u7C9Rgy1gfbp78g5shj30a805dt8x.jpg)

发现有修改权限，且有root的执行权限，能每60秒刷新hint文件。

rb+ 读写打开一个二进制文件，只允许读写数据。
[https://blog.csdn.net/guyue6670/article/details/6681037](https://blog.csdn.net/guyue6670/article/details/6681037 "fopen")
![](http://ww1.sinaimg.cn/large/007u7C9Rgy1gfbpc2izasj30a006ct90.jpg)

于是修改代码能读取flag并写入flag.hint文件中
![](http://ww1.sinaimg.cn/large/007u7C9Rgy1gfbpdoahgmj30bp03wq2z.jpg)

等待60秒查看获得flag。