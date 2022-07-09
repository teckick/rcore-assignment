# 在 macOS 用 Vagrant 搭建实验环境

由于个人开发机使用 macOS, 实验中的一些脚本存在不兼容的情况, 为最小化环境因素对实验的影响, 使用 Vagrant 创建 Ubuntu 虚拟机搭建实验环境.

## 安装 Vagrant

在 macOS 下直接使用 [Homebrew](https://brew.sh/) 安装 Vagrant.

```bash
> brew install vagrant
```

vagrant 启动虚拟机还需要虚拟化工具支持, 需要从 [官网](https://www.virtualbox.org/wiki/Downloads) 下载并安装 VirtualBox.

之后就可以创建虚拟机了. 找到一个放置 vagrant 数据的目录, 执行 `vagrant init` 创建并初始化一个 Ubuntu 虚拟机.

```bash
> mkdir oscamp_vagrant
> cd oscamp_vagrant
> vagrant init hashicorp/bionic64
```

成功执行后, 在目录下应该有一个 `Vagrantfile` 文件, 这个是虚拟机的配置文件. 只需要改一个地方, 设置 `config.vm.synced_folder`, 将宿主机的目录映射到虚拟机中. 我是把训练营相关的所有资料, 代码放到一个 workspace 目录下, 然后映射到虚拟机.

```ini
...
config.vm.synced_folder "/Users/eastfisher/workspace", "/home/vagrant/workspace"
...
```

编辑保存之后, 在 `oscamp_vagrant` 目录下执行  `vagrant up` 启动虚拟机:

```bash
> vagrant up
```

应该是可以成功启动的. 然后在该目录下执行 `vagrant ssh` 就能以ssh方式登录虚拟机了.

```bash
> vagrant ssh
```

## 搭建实验环境

按照 [第零章：实验环境配置 - 手动方式进行本地OS开发环境配置](https://learningos.github.io/rust-based-os-comp2022/0setup-devel-env.html#os) 的步骤安装相关软件即可, 没有遇到障碍.

所有相关软件安装完成后, 在 os1 目录下 `make run` , 应该是不会报错的.

我们还使用 vscode 通过 ssh 连接到远程 vagrant 虚拟机, 而不能通过 `vagrant ssh` 命令去连接. 执行以下命令进行配置:

```bash
# vagrant 目录
# 执行这个命令后, 会自动创建一个 private_key, 路径是:
# .vagrant/machines/default/virtualbox/private_key
$ vagrant ssh-config
$ ssh-add ~/.vagrant/machines/default/virtualbox/private_key
# 这一步能连上说明 ssh 配置正确了
$ ssh -p2222 vagrant@127.0.0.1
```

配置完成后, 再按照 [vscode remote ssh 配置说明](https://code.visualstudio.com/docs/remote/ssh) 通过 vscode 连接.

## Tips

整个过程唯一可能遇到的问题是虚拟机网络被墙, 如果你的 macOS 有 proxy, 可以执行 `ifconfig` 命令找到IP, 然后在虚拟机里面配置 proxy 相关的环境变量. 没有的话就只能用国内镜像源了.

```bash
# 宿主机
> ifconfig
    ...
    inet 192.168.0.104 netmask 0xffffff00 broadcast 192.168.0.255
    ...

# 虚拟机
> export http_proxy=http://192.168.0.104:9090 # 9090是proxy的端口号
> export https_proxy=http://192.168.0.104:9090
```

## 参考资料

- [Vagrant Get Started](https://learn.hashicorp.com/collections/vagrant/getting-started)
- [第零章：实验环境配置](https://learningos.github.io/rust-based-os-comp2022/0setup-devel-env.html)
