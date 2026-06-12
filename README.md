# Git
将本地仓库推送到远端

```bash
# 1. 确认当前仓库的远程地址,会看到类似 origin 指向你当初克隆的源地址
git remote -v

# 2. 添加新的远程地址（或修改 origin）
# 方法一：添加一个新的 remote（保留 origin）
git remote add new-origin http://lq-a550:3000/lq/src.git

# 二：直接修改 origin 地址
git remote set-url origin http://lq-a550:3000/lq/src.git

# 3. 然后推送：
git push new-origin --all	# 推送所有分支
git push new-origin --tags	# 推送所有标签（如果需要）
```

全局配置（适用于当前电脑的所有仓库）
```bash
git config --global user.name "你的名字"
git config --global user.email "你的邮箱@example.com"
```

配置 Git 信任该证书
```bash
git config --global http.sslVerify false # 临时解决（仅当前会话）
```

# 计算机网络
Windows共享网络端口转发
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

# Linux基础
查看cpu频率
```bash
watch -n 1 "grep 'cpu MHz' /proc/cpuinfo"
```