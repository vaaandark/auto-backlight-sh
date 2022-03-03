# auto-backlight-sh
根据环境光亮度自动调节屏幕亮度的简单脚本

## 准备
- 安装软件包`light`用于屏幕亮度调节。
- 将用户加入`video`组，可以直接下载软件包`polkit`。
- 检查笔记本的光线传感器是否可用，比如检查目录`/sys/bus/iio/devices/iio:device0`下是否有名字包含`illuminance`的文件。对于我的笔记本`Lenovo ThinkBook 14p`， `/sys/bus/acpi/devices/ACPI0008\:00/iio\:device0/in_illuminance_raw`就包含了当前环境光强的尼特值。

## 命令
- `lightup`提高`10%`亮度。
- `lightdown`降低`10%`亮度。
- `set_backlight`根据环境亮度自动调节屏幕亮度，如果上次手动调节在十分钟以内，则不自动调节。

## 使用
- 先将这些脚本放入环境变量。
- 如果是`i3wm`用户，则可以使用快捷键绑定这些脚本 。
``` 
# 开机自动执行脚本
exec --no-startup-id set_backlight
# 删除掉手动调节记录
# 相当于打开自动调节
bindsym $mod+Ctrl+l exec rm /tmp/auto_backlight.log
# 与键盘亮度调节按键绑定
bindsym XF86MonBrightnessUp exec lightup
bindsym XF86MonBrightnessDown exec lightdown
```
- 如果是`i3blocks`用户，则可以利用`i3blocks`间隔执行脚本
```
# 删除掉脚本`set_backlight`如下部分
if [ -f /tmp/auto_backlight_lock ]; then
	exit 1
else
	touch /tmp/auto_backlight_lock
fi

# 修改脚本`set_backlight`最后部分
while sleep 1; do
	auto_set
done
# 改为
auto_set
```
```
# `i3blocks`配置文件加入
[AUTO_BACKLIGHT]
command={set_backlight的路径}
interval=1
```
- 如果想要登录自动启动，可以在`.bashrc`、`.zshrc`等配置文件中加入
```
if [ ! -f /tmp/auto_backlight_lock ]; then
	nohup set_backlight > /dev/null 2>&1 &
fi
```

