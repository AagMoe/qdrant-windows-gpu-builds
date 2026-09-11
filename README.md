# qdrant-windows-gpu-builds

---

[Qdrant](https://github.com/qdrant/qdrant) 官方目前仅为 Linux 平台提供了支持 GPU 的 Docker 镜像，并未提供 Windows 平台下支持 GPU 的可执行文件（.exe）。

本项目通过源码编译，提供 master 分支的 Windows 静态构建版本。

---

### 使用说明

1. 下载对应架构的 ZIP 压缩包并解压。
2. 打开 CMD 或 PowerShell，进入解压目录。
3. 启用 GPU 索引构建（任选一种方式）：

#### 方式 A：通过环境变量启用
```cmd
set QDRANT__GPU__INDEXING=1
qdrant.exe
```

#### 方式 B：通过配置文件指定 GPU（推荐）
在同级目录下创建 `config/config.yaml`，内容如下：
```yaml
gpu:
  # 启用 GPU 索引
  indexing: true

  # 在建立索引时，强制对 `f32` 值使用半精度（FP16）。
  # `f16` 转换仅在 GPU 内存中进行，且不会影响底层的存储类型。
  force_half_precision: false

  # 所使用的 GPU Vulkan "groups"
  # 换句话说，即 GPU 可以并行索引多少个点（points）。
  # 最佳值可能取决于 GPU 型号。
  # 该值与物理 warp（线程束）的数量成正比，但不一定相等。
  # 除非你清楚己在做什么，否则请勿更改此值。
  # 默认值：512
  groups_count: 512

  # 按硬件名称过滤 GPU 设备。不区分大小写。
  # 用于匹配 GPU 设备名称的子字符串列表，以逗号分隔。
  # 如果有多张 GPU，可按名称过滤指定的 GPU，如 "Tesla" 或 "RTX"
  # 默认为空，默认情况下接受所有设备。
  device_filter: ""

  # 允许并行运行的索引进程数量。
  # 默认值：1
  parallel_indexes: 1

  # 允许使用集成显卡（核显）。
  # 默认值：false
  allow_integrated: false
```
然后直接运行 qdrant.exe

启动后检查日志，若看到 `Initialized GPU device: "..."` 即代表 GPU 加速已成功启用

---

### 说明与免责声明
* **开源协议**：本项目基于 Qdrant 的 [Apache-2.0 License](https://github.com/qdrant/qdrant/blob/master/LICENSE) 编译并分发。
* **计算机制**：Qdrant 的 GPU 加速基于跨平台的 Vulkan API，主要用于大幅加速 HNSW 向量索引图的构建过程（Indexing）。
