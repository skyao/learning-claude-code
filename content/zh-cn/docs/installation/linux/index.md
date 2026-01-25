---
title: "Linux安装"
linkTitle: "Linux安装"
weight: 10
date: 2026-01-25
description: >
  在 Linux 上安装 Claude Code
---

## 安装

最好先开启网络代理，然后执行

```bash
curl -fsSL https://claude.ai/install.sh | bash
```

输出为：

```bash
Setting up Claude Code...
                                                                              
✔ Claude Code successfully installed!                                         
                                                                              
  Version: 2.1.19                                                             
                                                                              
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
2.1.19 (Claude Code)

$ which claude 
/home/sky/.local/bin/claude
```



