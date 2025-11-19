# RAID 和 RAID-Z

RAID-Z 是 ZFS 文件系统独有的软件 RAID 的实现方式，
可以避免 RAID5 中的 write-hole 问题，
提供更高的数据可靠性。

## 一些 RAID 级别

**RAID0** 又叫 Striping，RAID0 将数据分布在多个磁盘上，提高读写速度，
但不提供数据安全性。

**RAID1** 在磁盘间复制数据，确保所有数据都包含本地备份。
RAID1 空间利用率很低，通常和其他 RAID 结合使用，或者用于 boot drive。

**RAID5** 使用奇偶校验，可以在磁盘故障时重建数据。RAID5 与 RAID0 一样使用 Striping，
对 stripe 上的数据块进行 XOR 运算，存储一个 parity block。通过 parity block，
可以重建丢失的数据块。

**RAID6** 在 RAID5 的基础上使用两个校验块，可以重建两个块。

## RAID-Z

RAID-Z 类似 RAID5/6 的实现，但使用动态 strip 宽度。RAID-Z 消除了 RAID5/6 的一些问题。

### Read-Modify-Write Penalty

传统 RAID5/6 在写入 小于一个完整 stripe 的数据 时，会遭遇 RMW（Read-Modify-Write）惩罚。
因为 parity 块是根据整条 stripe 计算的，如果只修改一个 strip，
则 RAID 必须读取旧数据和旧 parity 才能计算新 parity:

1. Read：读旧数据块
2. Read：读旧 parity 块
3. Modify：根据 P_new = P_old XOR D_old XOR D_new 计算新的 parity
4. Write：写新数据块
5. Write：写新 parity 块

RAID-Z 的 dynamic stripe width 解决了 RMW Penalty，每个块是自身的 RAID strip，
每次写入都是 full-stripe write。

### Write-Hole 

传统 RAID5/6 写入时，parity 更新需要读取旧数据 → 计算新 parity → 再写入。
如果中途断电或部分写入失败，会出现数据与 parity 不一致，称为 **write-hole**。
RAID-Z 通过 ZFS 的 Copy-on-Write 机制避免了 write-hole 问题，确保:

- 数据永远写到新位置  
- parity 和数据同时写入  
- 写入完成后再更新指针

### Silent Data Corruption

RAID5/6 可以通过奇偶校验恢复发生故障的磁盘块，但不能发现 Silent Data Corruption。

RAID-Z (ZFS) 可以检测和纠正静默数据损坏。读取 RAID-Z 块时，ZFS 会将其与校验和进行比较。
如果不匹配：

- 读取奇偶校验位
- 确定哪个磁盘返回了错误数据
- 修复损坏的数据，返回正确数据

