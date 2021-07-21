# BluetoothSolution
解决Windows重启后，蓝牙驱动总是抽风的问题（自用蓝牙设备是AX200无线网卡带的，走USB端口）。

# 新解决方案
由于使用MS Community的解决方案也无法很好的对该蓝牙问题进行处理，看设备事件应该是USBhub的驱动和蓝牙设备驱动加载顺序不合理导致的问题（偶尔开机未登录时可以自动连上蓝牙外设，随后断开，登陆进入桌面后就显示驱动异常，根据下文中我发现的临时解决方案可解决，不知道是不是首发），故根据临时解决方案快速写了个以系统服务形式，开机自启后判断蓝牙是否正常，不正常则通过Devcon进行设备重新装载后停止服务，正常则直接停止服务。


## 使用步骤：
1. 请保证运行目录无中文存在，确保 `BluetoothSolution.exe` 与子文件夹 `Devcon` 在同一文件夹内
2. 请提前配置 `Devcon` 文件夹内 `device.txt` 内容，device.txt为蓝牙设备的硬件ID(Hardware ID)，硬件ID可到设备管理器找到对应设备-属性-详细信息-硬件ID。例如，作者所用AX200的蓝牙异常时的硬件ID：`USB\VID_8087&PID_0029`，若同为走USB端口的蓝牙设备应也是带有 `USB` 字样的
3. 首次运行请在蓝牙异常状态下进行，以便随时终止服务。在蓝牙异常状态下以管理员权限运行 `服务配置器.exe` ，安装服务，并启动服务进行测试，
4. 若测试无法正确处理请及时停止并卸载服务，尝试在蓝牙驱动异常时获取硬件ID（可能需要手动重启以达到该状态）修改配置后重试
5. 如有别的问题欢迎评论或提问[issues](https://github.com/RealSlakey/BluetoothSolution/issues)

![krdtvkk3.png](https://cdn.jsdelivr.net/gh/RealSlakey/spaceofslakey@latest/usr/uploads/2021/07/1213874442.png)

![krduz5ie.png](https://cdn.jsdelivr.net/gh/RealSlakey/spaceofslakey@latest/usr/uploads/2021/07/3188170316.png)

![krduuzo3.png](https://cdn.jsdelivr.net/gh/RealSlakey/spaceofslakey@latest/usr/uploads/2021/07/3213977368.png)

![krduwt7u.png](https://cdn.jsdelivr.net/gh/RealSlakey/spaceofslakey@latest/usr/uploads/2021/07/2999895347.png)

![krdu0fhh.png](https://cdn.jsdelivr.net/gh/RealSlakey/spaceofslakey@latest/usr/uploads/2021/07/3547185376.png)

![krdu0rc5.png](https://cdn.jsdelivr.net/gh/RealSlakey/spaceofslakey@latest/usr/uploads/2021/07/2309865507.png)

## Download
 [Github项目主页](https://github.com/RealSlakey/BluetoothSolution) 请帮我点亮Star~
 [GitHub Release](https://github.com/RealSlakey/BluetoothSolution/releases) 
以下来自我Blog的原文
# [原文](https://www.slakey.cn/archives/38/)
## 症状
1. 每次重新开机后，蓝牙设备都会报出如题错误。进入设备，从本地重新更新驱动后可以使用，但下次重新开机后继续报错。
2. 尝试卸载设备后重启Windows，Windows安装的设备及驱动是相同版本，并依然出现以上问题。

搜索后发现，大量相似情况存在，甚至我自己的多台Win10设备均出现该问题。起初以为是购买的USB蓝牙适配器问题，当时通过devcon写了个开机自动“更新”驱动的小工具，症状缓解。最近家里的台式机换了带蓝牙（走usb通道）的pcie网卡，又出现了这个问题，才想起来可能是系统问题。
## 暂时解决办法
每次启动系统后，从设备管理中，从本机“可用驱动程序列表”中重新更新驱动后即可使用

## 长久性解决办法（自MS Community）
关闭驱动自动更新功能。
### Windows 10专业版
专业版由于自带"组策略编辑器"，因此操作上更加直观方便一些，具体方法如下：

1. 在Cortana搜索栏输入"组策略"后按回车键进入"组策略编辑器"
2. 找到计算机配置→管理模板→Windows组件→Windows更新，然后在右侧找到"Windows更新不包括驱动程序"
3. 双击打开该项目，在左上方选择"已启用"，点击"确定"。

### Windows 10家庭版
如果使用家庭版，那么则无法通过组策略设置来关闭驱动自动更新，就要用到注册表，具体步骤如下：

1. 在Cortana搜索栏输入regedit后按回车键进入"注册表编辑"
2. 定位到```HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate```
3. 在右侧空白处单击右键，新建"DWORD（32位）值"，命名为```ExcludeWUDriversInQualityUpdate```
4. 双击打开新建的值，修改数值数据为```1```，点击"确定"。
