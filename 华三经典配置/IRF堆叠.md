# 一级标题实验一：盒式设备IRF堆叠实验
## 一、实验组网
## 二、实验要求
   实现SWA与SWB之间的IRF堆叠
## 三、实验配置
1.SWA 保留缺省编号1，不需要进行配置；将SWB的成员编号修改为2。
	<SWB> system-view
	[SWB] irf member 1 renumber 2
	Warning: Renumbering the switch number may result in configuration change or loss. Continue?[Y/N]:y
	改完设备编号之后，重启设备 
2.在SWA上创建设备的IRF端口1/2，与物理端口T-GigabitEthernet1/0/50 绑定，并保存配置
	[SWA] interface T-gigabitethernet 1/0/50
	[SWA-Ten-GigabitEthernet1/0/50] shutdown
	[SWA] irf-port 1/2
	[SWA-irf-port1/2] port group interface T-gigabitethernet 1/0/50
	[SWA-irf-port1/2] quit
	[SWA] interface T-gigabitethernet 1/0/50
	[SWA-Ten-GigabitEthernet1/0/50] undo shutdown
	[SWA-Ten-GigabitEthernet1/0/50] save
3.在SWB上创建设备的IRF端口2/1，绑定物理端口GigabitEthernet2/0/50，并保存配置
	[SWB] interface T-gigabitethernet 2/0/50
	[SWB-Ten-GigabitEthernet2/0/50] shutdown
	[SWB] irf-port 2/1
	[SWB-irf-port2/1] port group interface T-gigabitethernet 2/0/50
	[SWB-irf-port2/1] quit
	[SWB] interface T-gigabitethernet 2/0/50
	[SWB-Ten-GigabitEthernet2/0/50] undo shutdown
	[SWB-Ten-GigabitEthernet2/0/50] save
激活SWA和SWB的IRF端口配置：
	[SWA-Ten-GigabitEthernet1/0/50] quit
	[SWA] irf-port-configuration active
	[SWB-Ten-GigabitEthernet2/0/50] quit
	[SWB] irf-port-configuration active
两台设备间将会进行Master竞选，竞选失败的一方将自动重启，重启完成后，IRF形成，系统名称统一为SWA,即为主设备的sysname.
四、实验检测
	1.IRF堆叠成功，SWB的系统名称变为SWA，与主设备SWA统一。
	2.测试PCA或PCB与192.168.10.10通信。由于SWA与SWB形成IRF堆叠，管理地址为主设备即SWA的管理地址192.168.10.10 。
	如果ping192.168.10.20将无法通信。（形成堆叠后地址被覆盖）
	
	 
五、注意事项与总结
1. S3600V2-EI系列交换机和S3600V2-SI系列交换机只能与本系列内的设备建立IRF，这两个系列的设备之间无法建立IRF；
2. 修改成员编号后要进行断电重启生效。
3. IRF激活后会进行IRF竞选。选举规则：1、当前Master优于非Master 2、成员优先级大的优先 3、系统运行时间长的优先 4、成员MAC小的优先。
	 
	 
	 
 
实验二：IRF结合BFD MAD检测
一、实验组网
	
 
二、实验要求
	实现SWA和SWB之间的IRF堆叠，并且使用BFD MAD检测使IRF之间一旦分裂能立即恢复业务。
三、实验配置
1.SWA 保留缺省编号1，不需要进行配置；将SWB的成员编号修改为2。
	<SWB> system-view
	[SWB] irf member 1 renumber 2
	Warning: Renumbering the switch number may result in configuration change or loss. Continue?[Y/N]:y
	改完设备编号之后，重启设备 
2.在SWA上创建设备的IRF端口1/2，与物理端口T-GigabitEthernet1/0/50 绑定，并保存配置
	[SWA] interface T 1/0/50
	[SWA-Ten-GigabitEthernet1/0/50] shutdown
	[SWA] irf-port 1/2
	[SWA-irf-port1/2] port group t 1/0/50
	[SWA-irf-port1/2] quit
	[SWA] interface T 1/0/50
	[SWA-Ten-GigabitEthernet1/0/50] undo shutdown
	[SWA-Ten-GigabitEthernet1/0/50] save
3.在SWB上创建设备的IRF端口2/1，绑定物理端口GigabitEthernet2/0/50，并保存配置
	[SWB] interface t 2/0/50
	[SWB-Ten-GigabitEthernet2/0/50] shutdown
	[SWB] irf-port 2/1
	[SWB-irf-port2/1] port group interface t 2/0/50
	[SWB-irf-port2/1] quit
	[SWB] interface t 2/0/50
	[SWB-Ten-GigabitEthernet2/0/50] undo shutdown
	[SWB-Ten-GigabitEthernet2/0/50] save
4.激活SWA和SWB的IRF端口配置：
	[SWA-Ten-GigabitEthernet1/0/50] quit
	[SWA] irf-port-configuration active
	[SWB-Ten-GigabitEthernet2/0/50] quit
	[SWB] irf-port-configuration active
两台设备间将会进行Master竞选，竞选失败的一方将自动重启，重启完成后，IRF形成，系统名称统一为SWA,即为主设备的sysname.
5.配置BFD MAD检测
	创建VLAN 2，并将SW A 上的端口Gi1/0/1 和SW B 上的端口Gi2/0/1 加入VLAN 2中
	[SWA] vlan 2
	[SWA-vlan2] port Gi1/0/1 Gi 2/0/1
	[SWA-vlan2] quit
	创建VLAN2接口，并配置MAD IP 地址
	[SWA] interface vlan-interface 2
	[SWA-Vlan-interface2] mad bfd enable
	[SWA-Vlan-interface2] mad ip address 192.168.2.1 24 member 1
	[SWA-Vlan-interface2] mad ip address 192.168.2.2 24 member 2
	[SWA-Vlan-interface2] quit
	因为BFD MAD和生成树功能互斥，所以在Gi1/0/1 和Gi2/0/1 上关闭生成树协议
	[SWA] interface Gi 1/0/1
	[SWA-Ethernet1/0/1] undo stp enable
	[SWA-Ethernet1/0/1] quit
	[SWA] interface Git 2/0/1
	[SWA-Ethernet2/0/1] undo stp enabl
 
四、实验检测
1、首先查看IRF堆叠情况，使用命令display irf 可以看到堆叠形成。

 
2、测试连通性，PC3与PC4都能ping通192.168.10.10
3、断开IRF线缆，观察IRF状态，可以看到仅剩一台设备。

1、 观察SWB，由于监测机制，SWB的所有端口处于DOWN状态。
 

 
五、注意事项与总结
1、BFD检测需要单独占用接口,并且被占用的端口不能走业务。BFD主设备地址生效，备设备地址不生效，BFD会话DOWN。当IRF2堆叠分裂时，两个地址同时生效。
2、IRF系统通过日志提示用户修复IRF互联链路，链路修复后，冲突的设备重新启动，恢复IRF系统，被Down掉的端口将重新恢复业务转发；
3、BFD MAD和STP、VPN功能互斥。
4、BFD会话可以穿越二层网络。
	 
 
