# 服务器使用文档

注：命令中带 [] 的部分表示需要依据实际情况修改。

### 需求软件

* Window：[Termius](https://www.termius.com/)、[Cmber](https://cmder.net/) 或 [Git Bash](https://git-scm.com/)
* Linux：Terminator 或终端
* Android：[JuiceSSH](https://download.agekt.com/juicesshpro.apk)

### 权限获取
* 请在你的主机上 [生成 ssh-key](https://git-scm.com/book/zh/v1/%E6%9C%8D%E5%8A%A1%E5%99%A8%E4%B8%8A%E7%9A%84-Git-%E7%94%9F%E6%88%90-SSH-%E5%85%AC%E9%92%A5)，生成后将公钥（位置：`~/.ssh/id_rsa.pub`）的内容发送到 <lmy0217@126.com>，等待管理员确认。

### 登录
* 登录中转服务器
`[你的用户名]@[你的主机名]:~$ ssh -p60001 tribody@agekt.com`


* 从中转服务器登录 GPU 服务器
    - 登录结点2
    `tribody@tzonst-lab:~$ ssh node2`
    - 登录结点3
    `tribody@tzonst-lab:~$ ssh node3`
    - 登录结点4
    `tribody@tzonst-lab:~$ ssh node4`

### 工作目录
* 创建工作目录
`[结点用户名]@[结点名]:~$ mkdir ~/[你的名称]`

* （可选）工作目录结构：
```
  ~
  ├── [你的名称]
  |    ├── datasets 存放不同数据集
  |    |    ├── dataset1
  |    |    ├── dataset2
  |    |    └── ...
  |    ├── venv     虚拟环境
  |    ├── works    存放不同代码
  |    |    ├── project1
  |    |    ├── project2
  |    |    └── ...
  |    └── ...
  └── ...
```

### 虚拟环境
* 在工作目录创建虚拟环境
`[[结点用户名]@[结点名]: [你的名称]]# virtualenv -p /usr/bin/python3 venv`
* 启用虚拟环境
`[[结点用户名]@[结点名]: [你的名称]]# source venv/bin/activate`
* 停用虚拟环境
`(venv) [[结点用户名]@[结点名]: [你的名称]]# deactivate`

### 文件夹传输
大数据量传输方案：
* 使用 [奶牛快传](https://cowtransfer.com/)
* 先上传到百度云，再在服务器上使用 [BaiduPCS](https://github.com/iikira/BaiduPCS-Go) 下载

小数据量传输方案：
* 使用 [WinSCP](https://winscp.net)
* 使用 `scp` 命令
	* 上传文件夹 project1
	 - 上传到中转服务器
	 `[你的用户名]@[你的主机名]:~$ scp -P60001 -r project1 tribody@agekt.com:/home/tribody/temp`
	 - 从中转服务器上传到 GPU 服务器
	 `tribody@tzonst-lab:~$ scp -r /home/tribody/temp/project1 [结点用户名]@[结点名]:/home/[结点用户名]/[你的名称]/works`
	* 下载文件夹 project1
	 - 从 GPU 服务器下载到中转服务器
	 `tribody@tzonst-lab:~$ scp -r [结点用户名]@[结点名]:/home/[结点用户名]/[你的名称]/works/project1 /home/tribody/temp`
	 - 从中转服务器下载
	 `[你的用户名]@[你的主机名]:~$ scp -P60001 -r tribody@agekt.com:/home/tribody/temp/project1 .`

### 运行程序
* 查看 GPU 状态
使用 `nvidia-smi` 或 `gpustat` 查看 GPU 占用率及显存消耗
* 选择未使用或使用量较少的 GPU 运行程序（GPUID = 0,1）
`(venv) [[结点用户名]@[结点名]: [你的project]]# CUDA_VISIBLE_DEVICES=[GPUID] python3 [你的程序] &`
* 后台运行
 - 加上 `&` 使得程序在后台运行
 - 使用 `jobs` 查看正在执行的任务，记下任务ID
 - `disown -h %[任务ID]`

### 查看动态状态
* GPU
`watch -n1 --color gpustat -cpu --color`
* 日志文件
`watch -n1 --color tail -n[需要显示的行数(文件尾)] [保存的日志文件路径]`

##### 更新日志
* 修订于2018年11月3日
* 2018年11月10日：修改代理域名，增加 Android 软件
* 2018年12月14日：修改服务器用户名为 tonnn
* 2019年3月19日：修改 node2 用户名为 web，修改中转服务器为 tribody
* 2019年7月2日：移除登录 GPU 服务器的用户名，增加注解，增加权限获取，增加数据传输方案，修改工作目录结构显示