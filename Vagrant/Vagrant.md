# vagrant命令

**vagrant init**：初始化新的 Vagrant 项目，创建一个新的 Vagrantfile。
**vagrant up**：启动虚拟机并设置配置。
**vagrant halt**：关闭虚拟机。
**vagrant reload**：重新加载虚拟机，应用新的配置。
**vagrant suspend**：暂停虚拟机，保存当前状态以便稍后恢复。
**vagrant resume**：恢复之前暂停的虚拟机状态。
**vagrant ssh**：通过 SSH 连接到正在运行的虚拟机。
**vagrant destroy**：销毁虚拟机，删除所有相关资源。
**vagrant status**：查看虚拟机的状态。
 **vagrant provision**：重新应用配置更改，更新虚拟机。
 **vagrant plugin**：管理 Vagrant 插件，例如安装、卸载和列出插件等。
 **vagrant box**：管理 Vagrant 盒子（Box），例如添加、删除和列出盒子等。
 **vagrant global-status**：查看全局运行中的 Vagrant 环境。

# phpstorm 配置vagrant
* 打开 PhpStorm，并进入 Tools -> Vagrant。
* 点击 + 符号，添加你的项目目录中的 Vagrantfile。
* PhpStorm 将检测到你的 Vagrant 虚拟机，并列出它们。
* 选择你想要连接的虚拟机，并点击 OK。
* 后面就可以通过Phpstorm启动Vagrant

# phpstorm 设置ssh配置
* 打开 PhpStorm，并进入 File> Settings -> SSH Config。
* 在弹出的窗口中，输入远程服务器的连接信息，包括主机名、用户名和密码（或者使用SSH密钥进行认证）以及端口号。确保你输入的信息是正确的，并且你有权限访问远程服务器。
* 默认使用的是 127.0.0.1:2222 用户名：vagrant，并且使用openssh方式连接 
* 点击"Connect"按钮，PHPStorm将尝试连接到远程服务器。
* 一旦连接成功，你将能够在PHPStorm中直接操作远程服务器，包括执行PHP代码、编辑文件等。你可以在SSH会话中打开终端，并在远程服务器上执行PHP脚本，就好像你在本地操作一样。

# phpstorm 设置命令行终端
* 参考示例，新建 .bat 扩展文件
```
@echo off
setlocal

:: 获取当前目录的最后一层路径
for %%I in ("%cd%") do set "lastDir=%%~nxI"

cd /D "D:\DevelopTool\VirtualMachine\homestead"

vagrant ssh -c "cd /home/vagrant/Project/%lastDir% && exec bash"

endlocal
```
* 打开 PhpStorm，并进入 File> Settings -> Terminal。
* 设置 Shell 路径 指向对应 .bat 扩展文件，进入控制台选择终端测试效果