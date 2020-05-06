# 1. tmux快捷键
>`ctrl+b ?`            显示快捷键帮助
>
>`ctrl+b 空格键`       采用下一个内置布局，这个很有意思，在多屏时，用这个就会将多有屏幕竖着展示
>
>`ctrl+b !`            把当前窗口变为新窗口
>
>`ctrl+b  "`           模向分隔窗口
>
>`ctrl+b %`           纵向分隔窗口
>
>`ctrl+b :`           进入命令行模式；此时可输入支持的命令，例如 kill-server 关闭所有tmux会话
>
>`ctrl+b ,`           重命名当前窗口，便于识别
>
>`ctrl+b .`           修改当前窗口编号，相当于重新排序
>
>`ctrl+b ~`           列出提示信息缓存；其中包含了之前tmux返回的各种提示信息
>
>`ctrl+b q`            显示分隔窗口的编号
>
>`ctrl+b o`            跳到下一个分隔窗口。多屏之间的切换
>
>`ctrl+b 上下键`      上一个及下一个分隔窗口
>
>`ctrl+b ctrl + 方向键`    以1个单元格为单位移动边缘以调整当前面板大小
>
>`ctrl+b alt + 方向键`    以5个单元格为单位移动边缘以调整当前面板大小
>
>`ctrl+b ctrl + z`   挂起当前会话
>
>`ctrl+b &`           确认后退出当前tmux
>
>`ctrl+b [`           复制模式，即将当前屏幕移到上一个的位置上，其他所有窗口都向前移动一个。
>
> `ctrl+b ]`           进入粘贴模式，粘贴之前复制的内容，按q/Esc退出
> 
> `ctrl+b {`           向前置换当前面板
>
> `ctrl+b }`           向后置换当前面板
>
>`ctrl+b c`           创建新窗口
>
>`ctrl+b n`           选择下一个窗口
>
>`ctrl+b l`           最后使用的窗口
>
>`ctrl+b p`           选择前一个窗口
>
>`ctrl+b w`           以菜单方式显示及选择窗口
>
>`ctrl+b s`           以菜单方式显示和选择会话。这个常用到，可以选择进入哪个tmux
>
>`ctrl+b t`           显示时钟。然后按enter键后就会恢复到shell终端状态
>
>`ctrl+b d`           脱离当前会话；这样可以暂时返回Shell界面，输入tmux attach能够重新进入之前的会话
>
>`ctrl+b f`            在所有窗口中查找关键词，便于窗口多了切换
>
>`ctrl+b z`            最大化当前所在面板
>
> `ctrl+b D`           选择要脱离的会话；在同时开启了多个会话时使用
# 2. 基本命令
## 2.1. 信息查询
- `tmux list-keys` 列出所有可以的快捷键和其运行的 tmux 命令
- `tmux list-commands` 列出所有的 tmux 命令及其参数
- `tmux info` 流出所有的 session, window, pane, 运行的进程号，等。
## 2.2. 窗口控制
### 2.2.1. Session会话
- `tmux new -s session_name` 创建一个叫做 session_name 的 tmux session
- `tmux attach -t session_name` 重新开启叫做 session_name 的 tmux session
- `tmux switch -t session_name` 转换到叫做 session_name 的 tmux session
- `tmux list-sessions / tmux ls` 列出现有的所有 session
- `tmux detach` 离开当前开启的 session
- `tmux kill-server` 关闭所有 session
### 2.2.2. window窗口
- `tmux new-window` 创建一个新的 window
- `tmux list-windows`
- `tmux select-window -t :0-9` 根据索引转到该 window
- `tmux rename-window` 重命名当前 window
### 2.2.3. pane面板
- `tmux split-window` 将 window 垂直划分为两个 pane
- `tmux split-window -h` 将 window 水平划分为两个 pane
- `tmux swap-pane -[UDLR]` 在指定的方向交换 pane
- `tmux select-pane -[UDLR]` 在指定的方向选择下一个 pane
