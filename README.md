# CAN-Bootloader
使用USB2XXX实现的CAN Bootloader功能，实现CAN节点固件远程升级

基于大家的需求，我们重新推出了基于CAN UDS协议的Bootloader/固件升级，程序通用性更强，也更加实用，代码下载链接：[https://gitee.com/toomoss_admin/can_uds_bootloader](https://gitee.com/toomoss_admin/can_uds_bootloader)

# 功能简介<br>
* 利用CAN总线实现对有CAN总线接口的设备进行固件升级；<br>
* 升级采用一键升级方式，傻瓜式操作，方便使用；<br>
* 用户可以自己修改上位机源码和下位机源码，实现固件的加密传输；<br>
* 目前此项目包含了STM32F1，STM32F2，STM32F4系列单片机示例源码，后面会增加其他单片机源码；<br>
* 上位机界面程序目前是用Qt(C++)实现，同时也有C#，Labview及安卓版本代码；<br>

# 使用步骤<br>
1,将USB2XXX（USB2CAN适配器）通过一个CAN收发器模块（如：TJA1050）连接到CAN总线上，同时将要实现升级功能的板子也连接到CAN总线上。<br>
2，找到和你所使用的单片机对应的源码，比如为STM32F103芯片，用keil 5打开bootloader/RVMDK目录下的工程，检查下你的CAN总线引脚配置是否跟我代码里面的一样，若是一样的，则可以直接编译下载，若不一样，则需要更改CAN总线引脚配置部分代码；<br>
3，和bootloader同目录下有个app目录，同样打开app/RVMDK目录下的工程文件，并编译工程，若一切正常的，那么在app/RVMDK/Output目录下应该会生成一个.bin文件，这个就是我们后面用来升级的固件；<br>
4，使用Qt5打开software/CANBootloader-Qt/project目录下的CAN_Bootloader.pro文件，点击“构建”->“运行”即可编译运行此程序（若没有Qt开发环境，可以直接下载我打包好的程序，安装后即可运行，百度网盘下载地址：http://pan.baidu.com/s/1hsFjZMk ,在“软件”->“CANBootloader”目录)。<br>
5，运行CANBootloader上位机软件后，点击“操作”->“扫描节点”，此时软件会弹出节点地址范围设置对话框，设置好扫描的节点返回，点击“确定”之后软件就开始扫描节点，同时将扫描到的节点显示在节点列表里面，选择列表里面的节点，然后再点击界面上的“打开文件”按钮，在弹出的文件浏览对话框中找到第3步编译出来的.bin文件，然后再点击“更新固件”按钮，此时就会开始固件更新，固件更新成功后，节点列表里面的节点固件类型会由原来的“BOOT”变成“APP”，到此固件更新完毕。<br>
6，若当前固件是“APP”的情况下，一样是可以进行固件更新的，只是在更新固件之前程序会有一个固件跳转的操作，具体流程可以参考源码。<br>

# 参考资料<br>
* 此项目所要用到的USB2CAN适配器可以到此购买：https://usb2xxx.taobao.com/<br>
* 上位机软件里面用到的API函数说明文档可以访问：http://www.toomoss.com/help/<br>
* 若在使用过程中有任何不清楚的地方可以QQ咨询我：188298598

# 好消息！！！<br>
很高兴这个项目得到大家的认可，也有很多人将代码拿到自己的工程中使用，基于大家的反馈，也发现了当前版本的一些不足，所以我又花了些时间，重新制定了一套协议，重新实现了CAN Bootloader代码。
目前协议文档已经写好，上位机软件也修改完成，单片机代码目前只实现了STM32F4的代码，后续会逐步完善，等我把这些资料和代码整理下之后再发布出来，若急需的用户可以通过上面的联系方式联系我获取代码和资料。
目前新版本的协议和代码改进点如下：
* 之前数据传输会用到多个ID，而且节点地址也定义在ID中，实际使用中发现这样做非常不合理，因为CAN的ID对于一个CAN网络来说非常宝贵，特别是在汽车CAN网络中，这样传输协议会浪费大量的帧ID，而且很多时候也是不允许这样做的，所以新版本的协议数据传输可以用一个ID实现，最多2个ID（可以自己配置）。
* 新版本的协议在进行多个节点同时更新时，稳定性和可靠性更高，之前的版本无法获取每个节点的更新状态，所以在更新的时候若某个节点更新出错是无法知道的，而新版本的协议弥补了这个不足。
* 之前版本的协议在数据传输出错后没法知道数据是传输出错还是写入芯片出错，新版本的协议弥补了这个不足，若发现数据传输出错，可以对数据进行重新传输。
* 新版本的协议在对数据进行块传输时增加了每一帧的数据包序号，如此可以更加可靠的传输数据，从节点接收到数据后也更容易对数据进行重新组包。
* 新版本协议在制定的时候考虑到了使用其他总线进行更新，比如UART,SPI,IIC,LIN等，所以通用性更强，同样的代码更容易移植到使用其他总线进行更新。
