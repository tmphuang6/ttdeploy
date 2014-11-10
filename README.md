ttdeploy
========

记录teamtalk在部署过程中遇到的问题，仅供大家参考

这里只是 TTAutoDeploy 的补充, 所以请安装前仔细阅读TTAutoDeploy，尤其是红色标注的“强烈建议”, TTAutoDeploy中说明过的这里不再重复

由于缺少Android, Mac, Vs编译环境，Client只使用了 winclient

为了简化问题，全程采用root 操作

# 操作系统

Centos 6.x 

5.x 经过测试中间会出许多问题，建议用6.x顺利很多

自动部署脚本会检查依赖项，为了简化问题，使用了虚拟机，安装了干净的系统，脚本会自动安装所需的 nginx, redis, mysql, php, jdk
, 还需要 maven, git， 使用方法请自行google

# 准备代码

````
git clone https://github.com/mogutt/TTAutoDeploy

git clone https://github.com/lanbeilyj/TTServer

git cloen https://github.com/mogutt/TTPHPServer
````

WinClient：
	群共享里的 TeamTalk-winclient绿色版.zip
	
	注：用官网下载的是无法连到自己的Server的

TTServer 的 cpp部分已经有编译后的程序，不需要自己编译， java部分要用maven编译一下

jdk 需要用到 jdk-7u71-linux-x64.rpm，下载地址自行解决

下面是用到的命令

````
cd TTServer/java
sh packageproduct.sh

cd ..
cp -r java ttjavaserverPack
tar -czvf ttjavaserverPack.tar.gz ttjavaserverPack

cd ..
cp -r TTServer/ttjavaserverPack.tar.gz TTAutoDeploy/TT/im_db_proxy

cp -r TTPHPServer im
zip -r im.zip im
cp -r im.zip TTAutoDeploy/TT/im_web

cd TTServer/cpp
./build.sh version 1.0

cd ../..
cp TTServer/cpp/im-server-1.0.tar.gz TTAutoDeploy/TT/im_server

cp jdk-7u71-linux-x64.rpm TTAutoDeploy/TT/jdk

````

编辑 TTAutoDeploy/TT/jdk/setup.sh

	修改第9行为 ： 

	JDK=jdk-7u71-linux-x64


编辑 TTAutoDeploy/TT/percona/setup.sh

	将 56-5.6.20-rel68.0.el6.x86_64 全部替换为 56-5.6.21-rel70.0.el6.x86_64

编辑 TTAutoDeploy/TT/im_db_proxy/setup.sh

	第26行改为 sh startup.sh $LISTEN_PORT


修改数据库记录：

	update IMUsers set id=10000 where id=10035;

	update IMUsers set pwd=md5('123456') ;

# 一键部署

有了上面的准备工作，才能做到一键部署

sh setup.sh check

sh setup.sh install

如果中间没报错，大功告成！