## Homestead多box配置
* 重新拉取项目代码到新目录下
```
git clone https://github.com/laravel/homestead.git NewHomestead
```

#### 修改配置&启动
* 修改文件 NewHomestead\scripts\homestead.rb, 原则上应该不用全部改，只要改掉生成的虚拟机名称就行，这里不深究。
```
config.vm.define settings['name'] ||= 'homestead' # 把 'homestead' 修改为 'homestead-new'
```

* 执行init.bat, 并修改 Homestead.yaml => ip ，修改成不与其他虚拟你不想不相同的ip即可

* 执行 vagrant up 启动即可

#### 测试验证
* 通过 Oracle VM VirtualBox 对应虚拟机设置 => 网络(网卡1) => 高级(端口转发) => 查看ssh端口映射 
* ssh连接测试(用户名：vagrant)