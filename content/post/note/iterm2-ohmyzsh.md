---
title: 教你如何變身潮潮工程師
description: 
date: 2021-07-16
categories:
    - 學習筆記
tags: 
    - iTerm2
    - zsh
---

> 身為一個專業開發者的一員，一定時常與螢幕前的Command Line為伍，既然都要互相陪伴一輩子，那怎能不來個潮一點的介面呢～

> 接下來重頭戲教你如何在10分鐘內輕鬆打造屬於自己的Command Lime。

<!--more-->
## 重頭戲開始
1. 首先呢先去安裝iTerm2。

由於筆者環境是mac，接下來的操作都會以mac為基礎
```
brew cask install item2
```
2. 打開iTerm2，並照著以下路徑操作。
```
Preferences > Profiles > Terminal > Report Terminal Type
請選擇xterm-256color
否則後續會無法看到漂亮的畫面
```
3. 修改iterm2的顏色
```
Preferences > Profiles > Colors > Color Presets…
iterm2本身內建就有幾個顏色組合，但個人覺得沒有這麼好看
所以我們去這邊下載iTerm2-Color-Schemes
然後將整個repo下載下來，下載位置看個人喜好要放哪都可以
下載完成後，可以看到裡面有很多個資料夾，我們只需要把schemes這個資料夾依照個人喜好選擇想要的配色import即可
```
4. 安裝Powerline font
```
因為我們要使用的theme會有許多特殊字元，為了避免待會出現可怕的問號，所以我們先來下載字型
我是使用nerd-fonts這個字型
brew tap homebrew/cask-fonts
brew install font-hack-nerd-font --cask
安裝完字型後，要記得去iTerm2去更改剛剛的SauceCodePro Nerd Font字型，不然是不會生效的哦
```
5. 安裝zsh
```
brew install zsh
```
## 安裝完zsh，將其設為預設的shell
```
sudo sh -c "echo $(which zsh) >> /etc/shells" 
chsh -s $(which zsh)
```
6. 安裝ohmyzsh
sh -c "$(curl -fsSL https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"
7. 安裝powerlevel9k
git clone https://github.com/bhilburn/powerlevel9k.git ~/.oh-my-zsh/custom/themes/powerlevel9k
8. 設置~/.zshrc
```
# 這是我zshrc設置可作參考，也可依照自己的需求去做設定
ZSH_THEME="powerlevel9k/powerlevel9k"
# 左側
POWERLEVEL9K_LEFT_PROMPT_ELEMENTS=(context dir dir_writable vcs)
# 右側
POWERLEVEL9K_RIGHT_PROMPT_ELEMENTS=(ram load time)
# 如果當前登入角色為xxx，則不會顯示，可減少不必要的資訊顯示
DEFAULT_USER="xxx"
# 這串如果沒有設定的話不會有icon顯示
POWERLEVEL9K_MODE='nerdfont-complete'
# 因為之前很多環境變數都是在bash底下，為了避免後續開發時遇到問題要將環境變數從bash配置過來
source ~/.bash_profile
```
## 完結篇
> 結束了上述得步驟後，你會得到一個非常潮的介面，當然一切都是可以個人化的，之後就靠各位工程師的精神，自行動手設計自己的介面拉～
