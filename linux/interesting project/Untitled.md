终端整活来一波，基本都是纯终端里跑，`Ctrl+C` 退出：

**视觉整活**
- `sl` — 小火车；`sl -l` 小火车，`sl -F` 飞天。







- `cowsay` / `cowthink` — 奶牛说话/思考。  
  `fortune | cowsay -f tux | lolcat`

~~~zsh
`cowsay` 基本用法：

```bash
cowsay "Hello Linux"
```

输出一头牛说这句话。消息也可以从管道来：

```bash
echo "Hello Linux" | cowsay
```

**常用参数**

```bash
cowsay -l                 # 列出所有可用牛文件
cowsay -f tux "Hello"     # 指定牛，比如 tux
cowsay -f dragon "Rawr"   # 龙
cowsay -f moose "Hi"      # 驼鹿
cowsay -W 20 "很长的消息"  # 设置换行宽度
cowsay -n "不自动换行"     # 禁用自动换行
```

**改变牛的表情**

```bash
cowsay -b "Resistance is futile"  # Borg
cowsay -d "I'm dead"              # 死掉
cowsay -g "Greedy"                # 贪婪
cowsay -p "Paranoia"              # 偏执
cowsay -s "Stoned"                # 嗨了
cowsay -t "Tired"                 # 累了
cowsay -w "Wired"                 # 亢奋
cowsay -y "Youthful"              # 年轻
```

也可以自定义眼睛和舌头：

```bash
cowsay -e "^^" -T "U " "Hello"
```

`-e` 是眼睛，`-T` 是舌头。

**cowthink**

`cowthink` 和 `cowsay` 一样，但气泡是思考：

```bash
cowthink "今天吃什么"
cowthink -f tux "Hmm..."
```

**经典组合**

```bash
fortune | cowsay
fortune | cowsay -f tux | lolcat
cowsay "Hello" | lolcat
figlet "Linux" | cowsay -f tux
```

需要装：

```bash
# Debian/Ubuntu
sudo apt install cowsay fortune-mod lolcat

# Fedora
sudo dnf install cowsay fortune-mod lolcat

# Arch
sudo pacman -S cowsay fortune-mod lolcat
```

**自定义牛**

牛文件一般在：

```bash
cowsay -l
# 第一行会显示路径，例如 /usr/share/cowsay/cows/
```

复制一个改：

```bash
cp /usr/share/cowsay/cows/default.cow ~/my.cow
# 编辑 ~/my.cow
cowsay -f ~/my.cow "Hello"
```

**注意**

`cowsay` 对中文支持不太好，因为按字节算宽度，气泡容易歪。最好用英文，或者短句。想要彩色就接 `lolcat`：

```bash
cowsay "Hello" | lolcat
```
~~~







- `figlet` / `toilet` — ASCII 大字。  
  `figlet Linux | lolcat`





~~~zsh
下一个是 **figlet**，以及它的增强版 **toilet**。都是把普通文字变成 ASCII 大字，终端整活经典。

## figlet

基本用法：

```bash
figlet "Hello Linux"
```

输出：

```text
 _   _      _ _        _     _                  _     
| | | | ___| | | ___  | |   (_)_ __  _   ___  __| |    
| |_| |/ _ \ | |/ _ \ | |   | | '_ \| | | \ \/ /| |    
|  _  |  __/ | | (_) || |___| | | | | |_| |>  < |_|    
|_| |_|\___|_|_|\___/ |_____|_|_| |_|\__,_/_/\_\        
```

**常用参数**

```bash
figlet -f slant "Linux"     # 指定字体 slant
figlet -f big "Hello"       # 指定字体 big
figlet -l                   # 列出所有字体
figlet -c "Centered"        # 居中
figlet -w 80 "Long text"    # 设置宽度
figlet -k "Spacing"         # 收紧字距
figlet -p "Paragraph"       # 段落模式
figlet -t "Terminal"        # 终端宽度
```

字体一般在：

```bash
/usr/share/figlet/
```

也可以从网上找 `.flf` 字体文件，放到 `~/.local/share/figlet/` 或直接 `figlet -f ~/myfont.flf "Hello"`。

**组合玩法**

```bash
figlet "Linux" | lolcat
figlet -f slant "Hello" | cowsay -f tux
fortune | figlet | lolcat
```

## toilet

`toilet` 是 figlet 的增强版，支持颜色、滤镜、更多字体格式。

基本用法：

```bash
toilet "Hello Linux"
```

**常用参数**

```bash
toilet -f mono12 "Hello"          # 指定字体
toilet -f bigmono12 "Linux"       # 更大
toilet -l                         # 列出字体
toilet --filter list              # 列出所有滤镜
toilet -F metal "Metal"           # 金属质感
toilet -F gay "Rainbow"           # 彩虹色
toilet -F border "Boxed"          # 加边框
toilet -F flip "Flipped"          # 翻转
toilet -F 180 "Upside down"       # 倒置
toilet -F crop "Cropped"          # 裁剪
toilet -w 80 "Width"              # 宽度
```

常见滤镜：

```bash
toilet -F metal "Linux"
toilet -F gay "Linux"
toilet -F border "Linux"
toilet -F flip "Linux"
toilet -F 180 "Linux"
```

可以组合多个滤镜：

```bash
toilet -F border -F gay "Linux"
```

**经典组合**

```bash
toilet -f bigmono12 -F gay "Hello" | cowsay -f tux
figlet "Linux" | toilet -F metal
toilet -F border "Terminal Fun" | lolcat
```

## 安装

```bash
# Debian/Ubuntu
sudo apt install figlet toilet

# Fedora
sudo dnf install figlet toilet

# Arch
sudo pacman -S figlet toilet
```

## 注意

`figlet` 和 `toilet` 对中文支持不好，基本只能玩英文和数字。中文会显示成乱码或方框。想要中文艺术字，得用别的工具，比如 `toilet` 配合特定字体也基本不行，中文建议直接搜在线生成器。

下一个想继续看哪个？比如 `cmatrix`、`hollywood`、`pipes.sh`、`cbonsai` 都可以。
~~~











- `boxes` — 给文字加框。  
  `echo hello | boxes -d parchment`
- `cmatrix` / `unimatrix` — 黑客帝国数字雨。



~~~zsh
下一个是 **cmatrix**，终端数字雨，黑客帝国同款。

## cmatrix

基本运行：

```bash
cmatrix
```

默认绿色数字雨，按 `q` 或 `Ctrl+C` 退出。

**常用参数**

```bash
cmatrix -a              # 异步滚动，更乱更自然
cmatrix -b              # 粗体
cmatrix -B              # 所有字符都粗体
cmatrix -c cyan         # 指定颜色：green/red/blue/white/yellow/cyan/magenta/black
cmatrix -u 2            # 更新速度，数字越小越快
cmatrix -s              # 屏保模式，按任意键退出
cmatrix -o              # 老式滚动效果
cmatrix -n              # 关闭粗体
```

常用组合：

```bash
cmatrix -a -b -c cyan
cmatrix -a -B -c red -u 1
cmatrix -s
```

**颜色参数**

`-c` 支持：

```text
green, red, blue, white, yellow, cyan, magenta, black
```

比如蓝色雨：

```bash
cmatrix -c blue
```

红色雨：

```bash
cmatrix -c red
```

**安装**

```bash
# Debian/Ubuntu
sudo apt install cmatrix

# Fedora
sudo dnf install cmatrix

# Arch
sudo pacman -S cmatrix
```

## unimatrix

`cmatrix` 的增强替代品，Python 写的，效果更丰富，参数更多。

安装：

```bash
pip install unimatrix
# 或者
pipx install unimatrix
```

运行：

```bash
unimatrix
```

常用参数：

```bash
unimatrix -a          # 异步
unimatrix -b          # 粗体
unimatrix -c blue     # 颜色
unimatrix -f          # 全屏
unimatrix -l "abc"    # 指定字符集
unimatrix -s 96       # 速度
unimatrix -o          # 老式
```

## 组合玩法

`cmatrix` 本身是动态的，不太适合再接 `lolcat`，但可以配合 `tmux` 分屏，一边数字雨一边干别的：

```bash
tmux
# 然后 Ctrl+b % 分屏，一边跑 cmatrix，一边跑其他
```

也可以当屏保：

```bash
cmatrix -s -a -b -c cyan
```

想更花哨，可以 `cmatrix` 和 `pipes.sh` 换着跑。

下一个想看哪个？`hollywood`、`pipes.sh`、`cbonsai`、`asciiquarium`、`genact` 都可以。
~~~



- `hollywood` — 满屏假黑客窗口，很吃资源。









- `pipes.sh` — 彩色管道屏保。
- `asciiquarium` — 终端水族箱。
- `cbonsai` — 终端里种盆栽。
- `nyancat` — 彩虹猫。
- `tty-clock` — 终端时钟。  
  `tty-clock -c -s -t`
- `cava` — 音频频谱可视化。







- `chafa` / `jp2a` / `cacaview` — 终端里看图。
- `mpv --vo=tct 视频.mp4` — 终端里放视频，效果很糊但好玩。
- `oneko` — 小猫追鼠标，X11 桌面下跑。
- `pokemon-colorscripts -r` — 终端随机宝可梦。

**假装黑客/工作**
- `genact` — 假装编译、下载、被黑。  
  `genact -l` 看模块，`genact -m ansible`
- `no-more-secrets` / `nms` — 电影解密效果。  
  `ls -la | nms`
- `hollywood` — 上面说过，经典假黑客。
- `cmatrix -b` — 数字雨加粗版。

**在线一行命令**
```bash
curl parrot.live
curl ascii.live/nyan
curl ascii.live/forrest
curl ascii.live/donut
curl wttr.in
```
`wttr.in` 是终端天气，其他是 ASCII 动画。

**终端游戏**
- `nsnake` — 贪吃蛇
- `nudoku` — 数独
- `tty-solitaire` — 纸牌
- `moon-buggy` — 月球车
- `pacman4console` — 吃豆人
- `ninvaders` — 太空侵略者
- `bastet` — 会故意给你烂方块的俄罗斯方块
- `nethack-console`、`bsdgames`、`frotz`、`dcss`、`cataclysm-dda-curses`、`dwarf-fortress` — 经典 Roguelike / 文字游戏

**几个好玩的组合**
```bash
fortune | cowsay -f tux | lolcat
figlet "Linux" | lolcat
cmatrix -b
sl -l
hollywood
genact -m ansible
```

Debian/Ubuntu 可以一把梭，能装哪个算哪个：

```bash
sudo apt update
sudo apt install sl cowsay fortune-mod lolcat cmatrix hollywood asciiquarium cbonsai nyancat toilet figlet boxes chafa caca-utils cava btop nsnake nudoku tty-solitaire moon-buggy pacman4console ninvaders bastet nethack-console bsdgames frotz tty-clock
```

`genact`、`no-more-secrets`、`pipes.sh`、`pokemon-colorscripts` 不一定在默认源里，通常要去 GitHub/GitLab 或 `cargo install`。