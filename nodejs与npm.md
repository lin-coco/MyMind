# nodejs与npm安装

去官网下载：[Node.js — Download Node.js®](https://nodejs.org/en/download)

```bash
# Download and install nvm:
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.40.2/install.sh | bash

# in lieu of restarting the shell
\. "$HOME/.nvm/nvm.sh"

# Download and install Node.js:
nvm install 22

# Verify the Node.js version:
node -v # Should print "v22.14.0".
nvm current # Should print "v22.14.0".

# Verify npm version:
npm -v # Should print "10.9.2".

```

nvm用来管理多个nodejs版本号，来支持快速切换

1. 查看可用版本：nvm ls-remote

2. 安装指定版本（以 16.20.2 为例）：nvm install 16.20.2

3. 切换 Node 版本：nvm use 16.20.2

4. 设置默认版本：nvm alias default 16.20.2

5. 验证安装：node -v  # 查看Node版本，npm -v   # 查看npm版本

# 配置 npm 华为源

华为提供了国内镜像源，加速依赖下载。

1. 全局配置华为源：
   
   ```bash
   npm config set registry https://mirrors.huaweicloud.com/repository/npm/
   npm cache clean -f
   ```

2. 验证配置：npm config get registry

# npm基本使用

1. 安装项目依赖：npm install 
