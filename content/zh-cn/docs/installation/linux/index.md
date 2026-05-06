---
title: "Linux安装"
linkTitle: "Linux安装"
weight: 10
date: 2026-01-25
description: >
  在 Linux 上安装 Claude Code
---

## 准备工作

```bash
sudo apt install curl
sudo apt install wget
sudo apt install jq
```

## 安装

最好先开启网络代理，然后执行

```bash
curl -fsSL https://claude.ai/install.sh | bash
```

输出为：

```bash
Setting up Claude Code...
                                                                              
✔ Claude Code successfully installed!                                         
                                                                              
  Version: 2.1.39                                                             
                                                                              
  Location: ~/.local/bin/claude                                               
                                                                              
                                                                              
  Next: Run claude --help to get started                                      
                                                                              
⚠ Setup notes:                                                                
  • Native installation exists but ~/.local/bin is not in your PATH. Run:     
                                                                              
  echo 'export PATH="$HOME/.local/bin:$PATH"' >> ~/.zshrc && source ~/.zshrc  
                                                                              

✅ Installation complete!
```

参照提示，执行：

```bash
echo 'export PATH="$HOME/.local/bin:$PATH"' >> ~/.zshrc && source ~/.zshrc
```

检测安装：

```bash
$ claude --version      
2.1.39 (Claude Code)

$ which claude 
/home/sky/.local/bin/claude
```

## 升级

和安装一样，再次执行安装脚本

```bash
curl -fsSL https://claude.ai/install.sh | bash
```

输出为：

```bash
Setting up Claude Code...

✔ Claude Code successfully installed!        

  Version: 2.1.92

  Location: ~/.local/bin/claude


  Next: Run claude --help to get started

✅ Installation complete!
```

检测升级后的版本：

```bash
$ claude --version      
2.1.92 (Claude Code)

$ which claude 
/home/sky/.local/bin/claude
```

## 安装加速

有时会卡在下载 cluase 二进制安装文件上，我记得我早期安装时下载速度（有用科学上网）非常快，大概在几十秒级别。但最近发现下载经常慢，要二三十分钟，还经常断开连接。

因此考虑手工下载二进制文件，然后修改安装脚本使用预先下载好的二进制安装文件。先把安装脚本下载到本地：

```bash
cd ~/temp
wget https://claude.ai/install.sh
```

打开这个脚本文件：

```bash
vi install.sh
```

找到 `download_file` 函数

```bash
download_file() {
    local url="$1"
    local output="$2"
    
    if [ "$DOWNLOADER" = "curl" ]; then
        if [ -n "$output" ]; then
                echo "$url"                  # 打印文件的下载地址
                echo "$output"               # 打印文件下载后的保存地址
            #curl -fsSL -o "$output" "$url"    # 跳过下载
        else 
            curl -fsSL "$url"
        fi
...
```

再执行安装脚本:

```bash
bash ./install.sh
```

只要打印下面的信息，就可以 ctrl+c 结束脚本运行了：

```bash
https://downloads.claude.ai/claude-code-releases/2.1.129/linux-x64/claude
/home/sky/.claude/downloads/claude-2.1.129-linux-x64
```

之后手工用下载工具下载上面的 claude 文件，然后复制到 /home/sky/.claude/downloads/ 目录下，改名 claude-2.1.129-linux-x64:

```bash
cp ./claude ~/.claude/downloads/claude-2.1.129-linux-x64
```

再次执行安装脚本：

```bash
bash ./install.sh
```

输出为：

```bash
Setting up Claude Code...

Installing Claude Code native build latest... # 这里也会卡住很久，大概两三分钟甚至10分钟，不知道为什么

✔ Claude Code successfully installed!        

  Version: 2.1.129

  Location: ~/.local/bin/claude


  Next: Run claude --help to get started

✅ Installation complete!

```