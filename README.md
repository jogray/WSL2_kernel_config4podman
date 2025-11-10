# 自编译WSL2内核以支持podman集群

podman的默认网络组件netavark需要内核nf_tables支持，WSL2的默认配置未启动，故撰此文记录我的编译过程

## 执行脚本
脚本为简单命令序列，无路径判断逻辑，须在项目根目录下执行如下，同理，脚本较易读，你可以自己复制粘贴按步执行
```
./script/build_kernel
```
编译内核系重载任务，默认全线程启动，如希望低负载后台静默编译，修改脚本`build_kernel_ubuntu`中`make`指令的`-j$(nproc)`字段为你期望的线程数，例如`-j2`
***请保存好所有工作，做好应对系统崩溃的准备***，作者CPU为13900HX，启动32核全负载编译共用时约7-11min

## 配置内核
脚本执行完成后在项目根目录产生内核文件`bzImage`，移入任意Windows目录，而后修改Windows用户根目录下的`.wslconfig`文件，指定内核路径，示例如下：
```
[wsl2]
kernel=D:\\WSL\\bzImage
```
也可直接使用WSL Settings 软件GUI界面配置内核，打开后进入`开发商>自定义内核`直接选择路径即可

## 重启wsl
确定wsl中所有工作已保存后直接`wsl --shutdown`后再启动wsl即可，目前`minikube start --driver=podman`仍有未知错误无法启动，`KIND_EXPERIMENTAL_PROVIDER=podman kind create cluster`可以启动集群

参考：
[issue中找到的解决方法](https://github.com/microsoft/WSL/issues/6044#issuecomment-1165103238)
[WSL2内核编译说明](https://github.com/microsoft/WSL2-Linux-Kernel?tab=readme-ov-file#build-instructions)