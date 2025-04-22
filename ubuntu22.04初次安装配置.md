# ubuntu22.04初次安装与配置

1. apt软件镜像源配置：华为、阿里源

2. ssh：
   
   ```bash
   sudo apt install ssh vim 
   sudo passwd root 
   su root 
   vim /etc/ssh/sshd_config # 将PermitRootLogin设置为yes systemctl enable ssh systemctl start ssh
   ```

3. git配置与ssh公钥：
   
   ```bash
   apt install git
   git config --global user.name ""
   git config --global user.email ""
   ssh-keygen -t ed25519 -C "邮箱地址"
   #去到github等平台上传pub key
   ```

4. 网络代理：
   
   ```bash
   export all_poxry=
   ```

5. docker安装：华为、阿里源

6. oh-my-zsh：
   
   ```bash
   apt install zsh curl
   sh -c "$(curl -fsSL https://install.ohmyz.sh/)"
   git clone https://github.com/zsh-users/zsh-autosuggestions ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions
   vim .zshrc # 修改主题和插件plugins(git zsh-autosuggestions), ZSH_THEME="af-magic"
   ```
