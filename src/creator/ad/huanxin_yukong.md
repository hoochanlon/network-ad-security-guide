# 将域控制器迁移到新买的服务器

达到效果：替换域控，让域用户无感。新增一台虚拟机 new dc 做为域控迁移换新演示。

旧的 DC 降级，新的保持和旧的 DC 计算机名、IP 地址保持一致。

## 旧 DC 降级

做好旧 DC 的 IP 地址记录。

![ ](https://cdn.sa.net/2024/10/07/6DQg59mbRvZYqWu.png)

### 降级失败排错

#### 指定域不存在或无法联系

> 在正常降级期间，Active Directory 域控制器 （DC） 必须联系另一个 DC，以告知正在进行降级。这允许从复制拓扑中删除降级的 DC，以便其合作伙伴不再尝试联系它进行复制。但是，在某些情况下，DC 可能无法再联系域中的另一个 DC。如果无法解决此情况，则可以执行强制降级以降级受影响的 DC，而无需联系另一个 DC。 —— [DELL - 如何将 Active Directory 域控制器强制降级](https://www.dell.com/support/kbdoc/zh-cn/000202630/%E5%A6%82%E4%BD%95-%E5%B0%86-active-directory-%E5%9F%9F-%E6%8E%A7%E5%88%B6%E5%99%A8-%E5%BC%BA%E5%88%B6-%E9%99%8D%E7%BA%A7)

通过上面这段描述，还有可能是另外一个备份域没开机，联系不上所以降级不成功，相关警示以及报错提示图，如下。

> 即便网络连通、ping 通，但也需要等待一段时间，五至十分钟左右再操作。

![ ](https://cdn.sa.net/2024/10/07/FHfonpilLSDtEIP.png)

![ ](https://cdn.sa.net/2024/10/07/q2EjT5wxQz4sB38.png)

![ ](https://cdn.sa.net/2024/10/07/C73jIQRFxJlf6Xg.png)

#### 目录服务没有必需的配置信息，并且不能决定新的浮动单主机操作角色的所有权

这个问题可以创建一个组策略再降级，或者参考 [learn.microsoft|error-run-adprep-rodcprep-command](https://learn.microsoft.com/zh-cn/troubleshoot/windows-server/active-directory/error-run-adprep-rodcprep-command)

![ ](https://cdn.sa.net/2024/10/08/Wo8vIE3xKGpYDCt.png)

### 常规降级

选择 【启用删除角色和功能向导】，然后反向勾选【Active Diretory域服务】

![ ](https://cdn.sa.net/2024/10/07/wliWSY2rTxoIXfv.png)

![ ](https://cdn.sa.net/2024/10/07/EIkQ7oMuHP9LliK.png)

**不用选**【强制】，然后【继续删除】

![ ](https://cdn.sa.net/2024/10/07/RkHW2eOoudnJ9g3.png)

![ ](https://cdn.sa.net/2024/10/07/onS5i4fYNuU18XQ.png)

![ ](https://cdn.sa.net/2024/10/07/Icl1haspLHvoy3W.png)

在【查看选项】处点击【降级】，等待降级完成。

![ ](https://cdn.sa.net/2024/10/08/Od4bizkIBVEoZ1c.png)

![ ](https://cdn.sa.net/2024/10/07/yFZlOH3VGjToex7.png)

降级验证，可以看到 DCSERVER 从主 DC 降级成普通计算机了。

![ ](https://cdn.sa.net/2024/10/07/ohOiNDnzTAvgP8c.png)

## 新主机升级成 DC


### 升级所遇到的问题

域管理员权限和普通用户没有区别，不能改ip等等

![ ](https://cdn.sa.net/2024/10/08/Pb6ajWIBoMAiqfx.png)

【本地策略】-【安全选项】-【用户账户控制：用于内置管理员账户的管理员批准模式】，已启用，重启生效。

![ ](https://cdn.sa.net/2024/10/08/JlRsyICVYpduv73.png)

出现这个问题，使用 ping 也会出现传输失败或无法连接目标主机。

![ ](https://cdn.sa.net/2024/10/09/fbLVSOqRPcFJxKr.png)

![ ](https://cdn.sa.net/2024/10/09/HzevsA5KciT2CU7.png)



### 升级成DC方式

旧 DC 降级完后，在备域里将组里的旧 DC 的计算机删掉。然后将旧 DC 关机。如果保持旧 DC 在线，新主机改成旧 DC 计算机名会报错。

![ ](https://cdn.sa.net/2024/10/08/GLgNd2FtrkW6xZq.png)

新设备改成旧 DC 计算机名、IP地址，并暂时将首选 DNS 调整成备 DC。

![ ](https://cdn.sa.net/2024/10/08/R6QV2YrMqpiUKnm.png)

选择【DNS服务器】、【Active Directory 域服务】，下一步直到安装。

![ ](https://cdn.sa.net/2024/10/08/QoFeD3Xc8ybzf4T.png)

将此服务器提升为域控制器，将域控制器添加到现有域。

![ ](https://cdn.sa.net/2024/10/08/z5mjJILMkhdgKV9.png)

![ ](https://cdn.sa.net/2024/10/08/CKrHNn5SlQ9wVJU.png)




## 参考资料

* [Cory_Hu - 域控制器的降级【卸载】](https://blog.csdn.net/weixin_41982957/article/details/107011670)
* [飘渺的尘埃 - 域学习笔记十三：将域控制器迁移到新买的服务器 ](https://www.cnblogs.com/atomy/p/13741596.html)
* [DELL - 如何将 Active Directory 域控制器强制降级](https://www.dell.com/support/kbdoc/zh-cn/000202630/%E5%A6%82%E4%BD%95-%E5%B0%86-active-directory-%E5%9F%9F-%E6%8E%A7%E5%88%B6%E5%99%A8-%E5%BC%BA%E5%88%B6-%E9%99%8D%E7%BA%A7)
* [learn.microsoft - 降级域控制器和域](https://learn.microsoft.com/zh-cn/windows-server/identity/ad-ds/deploy/demoting-domain-controllers-and-domains--level-200-)
* [subsea - 解决win10（1809）加域后，域管理员无权限设置](https://www.cnblogs.com/subsea/p/14178434.html)
* [有伊说一  - Windows2008R2 AD降级错误解决方案](https://blog.51cto.com/gaowenlong/1706526)
* [learn.microsoft|error-run-adprep-rodcprep-command](https://learn.microsoft.com/zh-cn/troubleshoot/windows-server/active-directory/error-run-adprep-rodcprep-command)
