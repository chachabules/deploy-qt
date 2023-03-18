## 项目编译
1. 将完整的项目在linux下用Qt creater编译运行release版本
2. 将生成的release文件夹下的执行文件名（一般与项目名或者target名相同）放入到英文文件夹内（以execute文件夹为例）
## 创建脚本
创建两个空的脚本--pack.sh和可执行文件名.sh
#pack.sh
```
#!/bin/sh  
exe="programdeploy" #你需要发布的程序名称
des="pathtocreatedir" #创建文件夹的位置
deplist=$(ldd $exe | awk  '{if (match($3,"/")){ printf("%s "),$3 } }')  
cp $deplist $des
```
#可执行文件名.sh
```
#!/bin/sh  
appname=`basename $0 | sed s,\.sh$,,`  
dirname=`dirname $0`  
tmp="${dirname#?}"  
if [ "${dirname%$tmp}" != "/" ]; then  
dirname=$PWD/$dirname  
fi  
LD_LIBRARY_PATH=$dirname  
export LD_LIBRARY_PATH  
$dirname/$appname "$@"
```
## 运行脚本
pack.sh脚本的作用为将**需要链接的动态链接库**复制到打包文件夹中

在shell中将当前文件夹更改为打包文件夹，输入命令`sudo chmod 777 pack.sh`提升权限，然后输入`./pack.sh`命令将需要链接的.so文件复制到打包文件夹

可执行文件名.sh脚本的作用有二：将本文件夹中的动态链接文件添加系统环境变量中；运行文件夹中的可执行程序

输入命令`sudo chmod 777 可执行文件名.sh`提升脚本权限
输入命令`./可执行文件名.sh`运行相应的程序

## 关于Qt程序的开机自启动
Qt程序的开机自启动分为**有界面程序的开机自启动设置**和**无界面程序的开机自启动设置**

###有界面程序的开机自启动设置
利用linux的.desktop文件实现开机启动
####操作步骤
1. 打开`/etc/xdg/autostart`目录
    ```
    cd /etc/xdg/autostart
    ```
2. 建立test.desktop文件
    ```
    touch test.desktop
    ```
3. 编写文件并保存
   ```
   sudo vim test.desktop
   ```
添加如下代码
```
[Desktop Entry]
Encoding=UTF-8
Type=Application
Name=appname
Exec=pathtoexecuteapp
```

建议上述添加的执行路径为上面所设置的可执行程序脚本，以免缺少相应的动态链接

### 无界面程序的开机自启动设置
由于银河麒麟自带rc.local服务，开机自启动只需在/etc/rc.local中添加所执行程序的路径（或执行程序脚本的路径）即可
