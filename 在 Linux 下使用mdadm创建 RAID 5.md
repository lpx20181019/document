[在 Linux 下使用mdadm创建 RAID 5](https://www.cnblogs.com/tcicy/p/8462271.html)

**安装mdadm,验证硬盘**

1.  lsb_release -a 操作系统版本
2.  ifconfig | grep inet

**检查三个磁盘是否存在Raid信息**

mdadm -E /dev/sd[b-d]

sudo mdadm --examine /dev/sdb /dev/sdc /dev/sdd



**第二步,创建分区**

fdisk /dev/sdb

fdisk /dev/sdc

fdisk /dev/sdd

创建分区后检查3个磁盘信息

mdadm --examine /dev/sdb /dev/sdc /dev/sdd



**第三步,创建MD设备md0**

mdadm --create /dev/md0 --level=5 --raid-devices=3 /dev/sdb1 /dev/sdc1 /dev/sdd1

mdadm -C /dev/md0 -l=5 -n=3 /dev/sd[b-d]1 # 或

`mdadm -C /dev/md0 -l5 -n3 /dev/sd[b-d]1 `

cat /proc/mdstat        #创建后,检查raid创建情况



如果你想监视当前的创建过程，你可以使用`watch`命令，将 `cat /proc/mdstat` 传递给它，它会在屏幕上显示且每隔1秒刷新一次。

watch -n1 cat /proc/mdstat



验证Raid 设备

mdadm -E /dev/sd[b-d]1

### 第4步：为 md0 创建文件系统

mkfs.ext4 /dev/md0

 现在，在`/mnt`下创建目录 raid5，然后挂载文件系统到 /mnt/raid5/ 下，并检查挂载点下的文件，你会看到 lost+found 目录。

1. `# mkdir /mnt/raid5`
2. `# mount /dev/md0 /mnt/raid5/`
3. `# ls -l /mnt/raid5/`

在挂载点 /mnt/raid5 下创建几个文件，并在其中一个文件中添加一些内容然后去验证。

1. `# touch /mnt/raid5/raid5_tecmint_{1..5}`
2. `# ls -l /mnt/raid5/`
3. `# echo "tecmint raid setups" > /mnt/raid5/raid5_tecmint_1`
4. `# cat /mnt/raid5/raid5_tecmint_1`
5. `# cat /proc/mdstat`

![1573024261074](C:\Users\27209\AppData\Roaming\Typora\typora-user-images\1573024261074.png)

![1573024287377](C:\Users\27209\AppData\Roaming\Typora\typora-user-images\1573024287377.png)