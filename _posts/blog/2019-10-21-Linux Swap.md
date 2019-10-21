---
layout: post
title: 使用swap分区解决内存不足问题
categories: blog
description: Linux 系统设置
keywords: Linux, swap， 交换分区
---

本文介绍在Linux实例中，如何配置SWAP分区和常见问题处理。在 Linux 系统中的 SWAP（交换分区），类似于Windows系统的虚拟内存。系统会把一部分硬盘空间虚拟成内存使用，将系统内非活动内存换页到SWAP，以提高系统可用内存。

**目录**

* TOC
{:toc}




## SWAP分区介绍
在Linux系统中的SWAP（交换分区），类似于Windows系统的虚拟内存。系统会把一部分硬盘空间虚拟成内存使用，将系统内非活动内存换页到SWAP，以提高系统可用内存。
>> 注：如果您使用普通云盘，不建议使用SWAP分区。如果是高效云盘或SSD云盘，可以根据实际情况使用SWAP分区。详情请参考参文档ECS实例使用须知。 

## SWAP配置介绍
1. 通过远程连接，执行如下命令，创建用于交换分区的文件。
   	dd if=/dev/zero of=/mnt/swap bs=block_size count=number_of_block
 >> 注：block_size、number_of_block大小可以自定义，比如bs=1M count=1024代表设置1G大小SWAP分区。

2. 执行如下命令，设置交换分区文件。
   
   	mkswap /mnt/swap
	
	![mkswap](/images/posts/mkswap.png)

3. 执行如下命令，启用交换分区文件
   
   	swapon /mnt/swap

	![swapon](/images/posts/swapon.png)

	![cat_rc](/images/posts/cat_rc_local.png)
	>> 注：如果在/etc/rc.local中有swapoff -a需要修改为swapon -a

4. 设置开机时自启用SWAP分区，需要修改文件`/etc/fstab`中的SWAP行，添加如下代码。
   
   	/mnt/swap swap swap defaults 0 0

	![fstab](/images/posts/fstab.png)

5. 执行如下命令，临时修改swappiness参数值，此处以空闲内存少于10%时才使用SWAP分区为例。
  >> 提示：在Linux系统中，可以通过查看/proc/sys/vm/swappiness内容的值来确定系统对SWAP分区的使用原则。当 swappiness内容的值为0时，表示最大限度地使用物理内存，物理内存使用完毕后，才会使用SWAP分区。当swappiness内容的值为100时，表示积极地使用SWAP分区，并且把内存中的数据及时地置换到SWAP分区。查看修改前为0，需要在物理内存使用完毕后才会使用SWAP分区。

  	echo 10 >/proc/sys/vm/swappiness

	![echo_10](/images/posts/echo_10.png)  

6. 若需要永久修改此配置，在系统重启之后也生效的话，通过vim命令编辑/etc/sysctl.conf文件，并增加如下内容

   	vm.swappiness = 10

7. 执行如下命令，验证添加成功。

   	sysctl -p

## 关闭 SWAP 分区
当系统出现内存不足时，开启SWAP可能会因频繁换页操作，导致IO性能下降，可以采用如下方法，关闭SWAP分区。

1. 执行如下命令，查询SWAP分区设置。

   	free -m

   系统显示类似如下
   ![free_m_1](/images/posts/free_m.png)	   

2. 执行如下命令，关闭SWAP分区。
   	swapoff [$SWAP_File]

   >> 注：[$SWAP_File]SWAP分区标识。

   系统显示类似如下。
   ![](/images/posts/swapoff.png)

3. 通过vim命令，修改/etc/fstab文件，删除或注释相关配置，取消SWAP的自动挂载，系统显示类似如下。
   ![](/images/posts/swapoff_1.png)

4. 执行如下命令，确认SWAP分区已经关闭。

   	free -m

   系统显示类似如下
   ![](/images/posts/free_m_1.png)	 

5. 执行如下命令，临时修改`swappiness`参数值。此处以空闲内存为**0%**为例。

   	echo 0 >/proc/sys/vm/swappiness     
    
6. 若需要永久修改此配置，在系统重启之后也生效的话，通过**vim**命令编辑`/etc/sysctl.conf`文件，并增加如下内容

   	vm.swappiness = 0

7. 执行如下命令，验证添加成功。	   

   	sysctl -p

## 常见问题处理
### 常见问题描述
使用mkswap创建SWAP时出现类似如下报错信息：

	mkswap: error: swap area needs to be at least 40 KiB

  ![](/images/posts/mkswap_problem.png)	

### 问题原因
指定的SWAP分区文件太小，SWAP分区文件**至少**应该大于**40KB**。

### 解决方法
重新生成更大的文件格式化为SWAP即可。
