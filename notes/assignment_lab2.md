# lab2 作业

## 编程作业

### 重写 sys_get_time 和 sys_task_info

这两个函数失效的原因都是因为传入的是虚拟地址，而需要访问对应的物理地址。

根据当前应用token找到 PageTable，通过虚拟地址VA计算出虚拟页号VPN，通过 PageTable 将虚拟页号转换为物理页号 PPN进而得到对应物理地址 PA，再将虚拟地址中的offset取出来加到 PA上，完成虚拟地址到物理地址的转换。

### mmap 和 munmap 匿名映射

首先根据 start, port 的约束，判断入参是否合法，如果不合法直接返回 -1。另外如果 len 为0，不做任何操作，直接返回0。

然后根据 start 和 len 计算出逻辑段。对段中的每个 VirtPageNum，判断 物理页是否已经被映射，如果是，则报错返回-1。否则说明可以成功分配，调用 `insert_framed_area` 函数将逻辑段加入到内核地址空间中，执行成功后返回0。

munmap类似，先做入参检查，然后计算逻辑段，对每个 VirtPageNum 执行 `munmap`。
