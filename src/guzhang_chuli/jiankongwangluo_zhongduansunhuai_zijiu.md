# 监控网络管理终端损坏自救

在最初规划网络线路的时候，一般都会留有施工点位图，规划网络较好的公司，一般也会让IT运维登记相关的设备地址表，以便后来查询。

**第一步：** 在已知设备地址表中，找寻有关监控功能的交换机设备，并通过寻线仪定位到故障终端接入的具体接入交换机的端口。

**第二步：** 查看该端口的网络配置

如图找到了当前的端口定义的具体 vlan

![ ](https://cdn.sa.net/2024/12/09/fbQSaEzsdBjeXK5.png)

通过 `dis cu | in vianif100` 这样相关的命令找到其配置的网关

![ ](https://cdn.sa.net/2024/12/09/baj7P1VurAfFDlR.png)

**第三步：** 通过监控设备相关IP登记表，推测未使用的IP地址，并使用交换机进行 ping 验证，确定该地址是否已使用。

![ ](https://cdn.sa.net/2024/12/09/zNg6JVPuiwRTD9L.png)

**btw**

监控设备IP登记表，表头一般遵循这样的规范：

* 序号
* 团队或公司
* 职场（公司所在地址）
* 摄像头名称（按命名规范命名：公司名缩写-公司所在地址-业务。没有相关业务则需另做说明）
* 监控设备的IP地址
* 所属NVR/CVR录像机IP
* 业务类型
* 备注（楼层）