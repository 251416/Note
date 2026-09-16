- [Git](#git)
  - [将本地仓库推送到远端](#%E5%B0%86%E6%9C%AC%E5%9C%B0%E4%BB%93%E5%BA%93%E6%8E%A8%E9%80%81%E5%88%B0%E8%BF%9C%E7%AB%AF)
  - [全局配置（适用于当前电脑的所有仓库）](#%E5%85%A8%E5%B1%80%E9%85%8D%E7%BD%AE%E9%80%82%E7%94%A8%E4%BA%8E%E5%BD%93%E5%89%8D%E7%94%B5%E8%84%91%E7%9A%84%E6%89%80%E6%9C%89%E4%BB%93%E5%BA%93)
  - [配置 Git 信任该证书](#%E9%85%8D%E7%BD%AE-git-%E4%BF%A1%E4%BB%BB%E8%AF%A5%E8%AF%81%E4%B9%A6)
- [计算机网络](#%E8%AE%A1%E7%AE%97%E6%9C%BA%E7%BD%91%E7%BB%9C)
  - [Windows共享网络端口转发](#windows%E5%85%B1%E4%BA%AB%E7%BD%91%E7%BB%9C%E7%AB%AF%E5%8F%A3%E8%BD%AC%E5%8F%91)
  - [Windows指定上网卡](#windows%E6%8C%87%E5%AE%9A%E4%B8%8A%E7%BD%91%E5%8D%A1)
  - [Linux添加静态域名解析](#linux%E6%B7%BB%E5%8A%A0%E9%9D%99%E6%80%81%E5%9F%9F%E5%90%8D%E8%A7%A3%E6%9E%90)
- [Windows基础](#windows%E5%9F%BA%E7%A1%80)
  - [计算sha256](#%E8%AE%A1%E7%AE%97sha256)
- [Linux基础](#linux%E5%9F%BA%E7%A1%80)
  - [查看cpu频率](#%E6%9F%A5%E7%9C%8Bcpu%E9%A2%91%E7%8E%87)
  - [安装 xRDP 远程桌面服务](#%E5%AE%89%E8%A3%85-xrdp-%E8%BF%9C%E7%A8%8B%E6%A1%8C%E9%9D%A2%E6%9C%8D%E5%8A%A1)
  - [安装 Edge浏览器](#%E5%AE%89%E8%A3%85-edge%E6%B5%8F%E8%A7%88%E5%99%A8)
  - [安装 Visual Studio Code](#%E5%AE%89%E8%A3%85-visual-studio-code)
  - [安装并启动 GParted 图形化工具](#%E5%AE%89%E8%A3%85%E5%B9%B6%E5%90%AF%E5%8A%A8-gparted-%E5%9B%BE%E5%BD%A2%E5%8C%96%E5%B7%A5%E5%85%B7)
  - [安装 open-vm-tools](#%E5%AE%89%E8%A3%85-open-vm-tools)
  - [查看系统当前所有中断的统计信息](#%E6%9F%A5%E7%9C%8B%E7%B3%BB%E7%BB%9F%E5%BD%93%E5%89%8D%E6%89%80%E6%9C%89%E4%B8%AD%E6%96%AD%E7%9A%84%E7%BB%9F%E8%AE%A1%E4%BF%A1%E6%81%AF)
- [VxWorks](#vxworks)
  - [查看IOAPIC实际映射](#%E6%9F%A5%E7%9C%8Bioapic%E5%AE%9E%E9%99%85%E6%98%A0%E5%B0%84)

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

## Windows端口转发

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

## Windows指定上网卡

指定上网从无线网卡走，核心思路是降低无线网卡的“接口跃点数”，让它比有线网卡优先级更高

**🛠️ 方法一：图形界面调整（最简单）**

1. 按 Win + R，输入`ncpa.cpl`回车，打开网络连接。
2. 右键“WLAN”（无线网卡）→ 属性 → 双击 Internet 协议版本 4 (TCP/IPv4)。
3. 点击 高级 → 取消勾选“自动跃点” → 在“接口跃点数”填一个较小的值（如 1 或 10）。
4. 对有线网卡（以太网）做同样操作，但填入一个较大的值（如 30 或 100），确保它优先级更低

**💡 为什么这样就行了？**

Windows 会根据“跃点数”来决定默认路由走哪条路。你让 WLAN 的数值更小，系统就会优先把上网流量交给它。有线网卡虽然还连着，但因为它优先级低，系统不会主动用它去访问互联网，你依然可以正常访问它所在的局域网设备。

改完后可以用 `route print -4` 检查，看默认路由（目标 `0.0.0.0`）是否指向了你的 WLAN 网关。

## Linux添加静态域名解析

```bash
# 1. 打开终端并编辑 hosts 文件
sudo nano /etc/hosts

# 2. 添加解析记录：在文件的末尾，按照 IP地址 域名 的格式添加一行
192.168.137.1   pc-202501221000
```

# Windows基础

## 计算sha256

```powershell
Get-FileHash "文件完整路径" -Algorithm SHA256
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

## 查看系统当前所有中断的统计信息

```bash
watch -n 0.1 'cat /proc/interrupts | grep -E "CPU|16:"'
```

# VxWorks

## 查看IOAPIC实际映射

```bash
-> vxbIoApicIntrShowAll
IOAPIC_ID    = 0x02000000
IOAPIC_VER   = 0x00170020
IOAPIC_ARB   = 0x00170020
IOAPIC_TBL00 = 0x00000000 00010000
IOAPIC_TBL01 = 0x00000000 000000b4
IOAPIC_TBL02 = 0x00000000 000100ac
IOAPIC_TBL03 = 0x00000000 000000a4
IOAPIC_TBL04 = 0x00000000 0000009c
IOAPIC_TBL05 = 0x00000000 00010094
IOAPIC_TBL06 = 0x00000000 00010000
IOAPIC_TBL07 = 0x00000000 00000084
IOAPIC_TBL08 = 0x00000000 0001007c
IOAPIC_TBL09 = 0x00000000 00010074
IOAPIC_TBL10 = 0x00000000 00010000
IOAPIC_TBL11 = 0x00000000 00010000
IOAPIC_TBL12 = 0x00000000 0001005c
IOAPIC_TBL13 = 0x00000000 00010054
IOAPIC_TBL14 = 0x00000000 0001004c
IOAPIC_TBL15 = 0x00000000 00010044
IOAPIC_TBL16 = 0x00000000 0000a03c
IOAPIC_TBL17 = 0x00000000 0000a039
IOAPIC_TBL18 = 0x00000000 0001a03d
IOAPIC_TBL19 = 0x00000000 0000a03e
IOAPIC_TBL20 = 0x00000000 0001a038
IOAPIC_TBL21 = 0x00000000 0001a03f
IOAPIC_TBL22 = 0x00000000 0001a03a
IOAPIC_TBL23 = 0x00000000 0000a03b
value = 1 = 0x1 = _VX_DATA_ALIGN
```

**分析 IOAPIC TBL16**

低 32 位 0x0000a03c = 二进制 0000 0000 0000 0000 1010 0000 0011 1100

| 位 | 值 | 含义 |
| --- | --- | --- |
| 0-7 | 0x3c (60) | Vector = 60 |
| 8-13 | 0x0 | \- |
| 14 | 1 | Trigger Mode = Level (电平触发) |
| 15 | 1 | Mask = 1 → 中断被屏蔽！ |
| 16-23 | 0x0 | Destination = 0 |
