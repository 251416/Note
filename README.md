# Git
## 将本地仓库推送到远端
```bash
# 1. 确认当前仓库的远程地址,会看到类似 origin 指向你当初克隆的源地址
git remote -v

# 2. 添加新的远程地址（或修改 origin）
# 方法一：添加一个新的 remote（保留 origin）
git remote add new-origin https://lq-a550.local:53000/lq/src.git

# 二：直接修改 origin 地址
git remote set-url origin https://lq-a550.local:53000/lq/src.git

# 3. 然后推送：
git push new-origin --all	# 推送所有分支
git push new-origin --tags	# 推送所有标签（如果需要）
```

## 全局配置（适用于当前电脑的所有仓库）
```bash
git config --global user.name "Your Name"
git config --global user.email "You Email@example.com"
```

## 配置 Git 信任该证书
```bash
git config --global http.sslVerify false # 临时解决（仅当前会话）
```

# 计算机网络
## Windows共享网络端口转发
```powershell
# 初始操作
# 1. 端口转发/反向代理
netsh interface portproxy add v4tov4 listenport=53000 listenaddress=0.0.0.0 connectport=53000 connectaddress=192.168.137.192
# 2. 放行 53000 端口的入站规则
New-NetFirewallRule -DisplayName "Allow 53000" -Direction Inbound -Protocol TCP -LocalPort 53000 -Action Allow -Profile Any

# 后续更改
# 1. 查看当前规则，确认要删除的目标（关键步骤）
netsh interface portproxy show v4tov4
# 2. 删除旧的规则（必须精确匹配原有的 listenaddress 和 listenport）
netsh interface portproxy delete v4tov4 listenaddress=0.0.0.0 listenport=53000
# 3. 添加指向新IP的规则（connectaddress换成新的IP）
netsh interface portproxy add v4tov4 listenaddress=0.0.0.0 listenport=53000 connectaddress=192.168.137.xxx connectport=53000
# 4. 再次查看，确认新规则已生效
netsh interface portproxy show v4tov4
```

## Linux添加静态域名解析
```bash
# 1. 打开终端并编辑 hosts 文件
sudo nano /etc/hosts

# 2. 添加解析记录：在文件的末尾，按照 IP地址 域名 的格式添加一行
192.168.137.1   pc-202501221000
```

# Linux基础
## 查看cpu频率
```bash
# 方式1 命令行
watch -n 1 "grep 'cpu MHz' /proc/cpuinfo"

# 方式2 安装图形化软件
sudo apt update
sudo apt install snapd
sudo snap install jscpumonitor
sudo snap connect jscpumonitor:hardware-observe     # 连接硬件权限（重要）：为了能正常读取 CPU 温度、功耗和频率信息
```

## 安装 xRDP 远程桌面服务
```bash
# 更新软件源并安装
sudo apt update
sudo apt install xrdp -y

# 启动服务并设置开机自启
sudo systemctl enable xrdp
sudo systemctl start xrdp

# 将用户加入 ssl-cert 组：这一步是为了让 xRDP 能正常读取 SSL 证书，避免连接出错
sudo adduser xrdp ssl-cert

# 配置防火墙 开放 3389 端口
sudo ufw allow 3389/tcp
```

## 安装 Edge浏览器
```bash
sudo apt install curl
# 1. 导入GPG密钥
curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
sudo install -o root -g root -m 644 microsoft.gpg /etc/apt/trusted.gpg.d/
sudo sh -c 'echo "deb [arch=amd64] https://packages.microsoft.com/repos/edge stable main" > /etc/apt/sources.list.d/microsoft-edge.list'
sudo rm microsoft.gpg

# 2. 更新APT包列表并安装
sudo apt update
sudo apt install microsoft-edge-stable -y
```

## 安装 Visual Studio Code
```bash
# 1. 安装依赖工具
sudo apt update
sudo apt install -y wget gpg

# 2. 导入微软 GPG 密钥
wget -qO- https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor | sudo tee /usr/share/keyrings/packages.microsoft.gpg > /dev/null

# 3. 添加 VS Code 官方仓库
echo "deb [arch=amd64 signed-by=/usr/share/keyrings/packages.microsoft.gpg] https://packages.microsoft.com/repos/code stable main" | sudo tee /etc/apt/sources.list.d/vscode.list

# 4. 更新软件包列表
sudo apt update

# 5. 安装 VS Code
sudo apt install code -y
```

## 安装并启动 GParted 图形化工具
```bash
sudo apt update
sudo apt install gparted -y
sudo gparted
```

## 安装 open-vm-tools
```bash
sudo apt update
sudo apt install open-vm-tools open-vm-tools-desktop -y
sudo reboot
```
