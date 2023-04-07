# GC 日志笔记

[TOC]

## 1. GC Log

### 1.1. 开启 GC Log

```shell
# JVM 就会在服务进程发生内存泄露时抓拍下当时的内存状态，也就是我们想要的堆转储文件。默认会在项目根目录下生成*.hprof文件可供分析。
-XX:+HeapDumpOnOutOfMemoryError

# 设置服务发生内存泄漏下当前内存状态生成堆转储hprof文件的位置
-XX:HeapDumpPath=/opt/bak/logs/ztom/projects

# 设置gc日志信息保存文件的位置
-Xloggc:/opt/bak/logs/ztom/projects/gc.log

# 打印full gc的详细信息。
-XX:+PrintGCDetails

# 输出GC的时间戳（以日期的形式，如 2013-05-04T21:53:59.234+0800）
-XX:+PrintGCDateStamps

# java 火焰图，根据高度来找cpu热点的。
-XX:+PreserveFramePointer

# 打印GC前后的详细堆栈信息
-XX:+PrintHeapAtGC

# 查看每次 minor GC 后新的存活周期的阈值
-XX:+PrintTenuringDistribution
```

### 1.2. GC Log实例

```shell
Java HotSpot(TM) 64-Bit Server VM (25.281-b09) for windows-amd64 JRE (1.8.0_281-b09), built on Dec  9 2020 13:54:07 by "" with MS VC++ 15.9 (VS2017)
Memory: 4k page, physical 16451544k(3218808k free), swap 19921952k(5193052k free)
CommandLine flags: -XX:-BytecodeVerificationLocal -XX:-BytecodeVerificationRemote -XX:+HeapDumpOnOutOfMemoryError -XX:HeapDumpPath=/opt/bak/logs/ztom/socket-server -XX:InitialHeapSize=263224704 -XX:+ManagementServer -XX:MaxHeapSize=4211595264 -XX:+PreserveFramePointer -XX:+PrintGC -XX:+PrintGCDateStamps -XX:+PrintGCDetails -XX:+PrintGCTimeStamps -XX:TieredStopAtLevel=1 -XX:+UseCompressedClassPointers -XX:+UseCompressedOops -XX:-UseLargePagesIndividualAllocation -XX:+UseParallelGC 
2021-09-10T11:18:20.401+0800: 0.673: [GC (Allocation Failure) [PSYoungGen: 64512K->8051K(75264K)] 64512K->8067K(247296K), 0.0069900 secs] [Times: user=0.06 sys=0.02, real=0.01 secs] 
2021-09-10T11:18:20.846+0800: 1.115: [GC (Metadata GC Threshold) [PSYoungGen: 61376K->10215K(75264K)] 61392K->10239K(247296K), 0.0100806 secs] [Times: user=0.00 sys=0.00, real=0.01 secs] 
2021-09-10T11:18:20.857+0800: 1.125: [Full GC (Metadata GC Threshold) [PSYoungGen: 10215K->0K(75264K)] [ParOldGen: 24K->9782K(119808K)] 10239K->9782K(195072K), [Metaspace: 20670K->20669K(1067008K)], 0.0428625 secs] [Times: user=0.05 sys=0.02, real=0.04 secs] 
2021-09-10T11:18:21.307+0800: 1.577: [GC (Allocation Failure) [PSYoungGen: 64512K->6657K(75264K)] 74294K->16512K(195072K), 0.0053440 secs] [Times: user=0.00 sys=0.02, real=0.01 secs] 
2021-09-10T11:18:21.666+0800: 1.934: [GC (Allocation Failure) [PSYoungGen: 71169K->9969K(93696K)] 81024K->19832K(213504K), 0.0068206 secs] [Times: user=0.09 sys=0.02, real=0.01 secs] 
```

### 1.2.1  详细日志

```shell
 # 指令 -XX:+PrintGCDetails
 # 日志示例
2021-09-10T14:37:42.651+0800: 1.687: [GC (Allocation Failure) Desired survivor size 11010048 bytes, new threshold 7 (max 15) [PSYoungGen: 64512K->7794K(75264K)] 75113K->18404K(188928K), 0.0041452 secs] [Times: user=0.00 sys=0.00, real=0.00 secs] 
```

### 1.2.2. 时间戳格式

```shell
 # 指令 -XX:+PrintGCDateStamps
 # 日志示例
[PSYoungGen: 64512K->7794K(75264K)] 75113K->18404K(188928K), 0.0041452 secs]
```

### 1.2.3. GC 前后的详细堆栈信息

```shell
 # 指令 -XX:+PrintHeapAtGC
 # 日志示例
{
  Heap after GC invocations=1 (full 0):
  eden space 64512K, 0% used [0x000000076c500000,0x000000076c500000,0x0000000770400000)
  from space 10752K, 81% used [0x0000000770400000,0x0000000770c98e10,0x0000000770e80000)
  to   space 10752K, 0% used [0x0000000770e80000,0x0000000770e80000,0x0000000771900000)
  object space 172032K, 0% used [0x00000006c4e00000,0x00000006c4e04000,0x00000006cf600000)
}
```

### 1.2.3.  查看每次minor GC后新的存活周期的阈值

```shell
 # 指令 -XX:+PrintTenuringDistribution
 # 日志示例
 PSYoungGen      total 75264K, used 8803K [0x000000076c500000, 0x0000000771900000, 0x00000007c0000000)
 ParOldGen       total 172032K, used 16K [0x00000006c4e00000, 0x00000006cf600000, 0x000000076c500000)
 Metaspace       used 15330K, capacity 15856K, committed 16000K, reserved 1062912K
 class space    used 1960K, capacity 2094K, committed 2176K, reserved 1048576K
```
