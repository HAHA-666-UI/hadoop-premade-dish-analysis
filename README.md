# 🍲 基于 Hadoop 生态的海量交易数据离线数仓构建与性能调优

> **项目状态**: ✅ 已完成 | **开发者**: HAHA-666-UI  
> **核心技术**: Java, Hadoop MapReduce, Custom Writable, MySQL, ECharts

## 📖 项目简介

本项目针对单机处理**百万级**预制菜订单数据时出现的**内存溢出 (OOM)** 与**计算高延迟**痛点，设计并实现了一套基于 Hadoop 生态的高可用离线数仓系统。

通过**分布式架构重构**与**底层序列化优化**，成功解决了 Shuffle 阶段的内存瓶颈，实现了从数据采集、清洗、多维分析到可视化展示的全链路闭环，**显著提升了数据处理效率与系统稳定性**。

---

## ⚡ 核心亮点与技术攻关

### 1. 分布式架构重构 (Distributed Architecture)
- **痛点**: 单体应用无法承载海量数据，耦合度高，维护困难。
- **方案**: 摒弃传统单体模式，采用**模块化设计**。将复杂业务拆解为 `Price` (价格分析), `Sales` (销量统计), `Repurchase` (复购率) 等多个独立 MapReduce 单元。
- **成果**: 通过自定义 **Driver 链式调度 (JobChain)** 实现任务自动化编排，代码可维护性显著提升，任务执行更加稳定可控。

### 2. 序列化深度优化 (Serialization Optimization)
- **痛点**: Java 原生序列化开销大，包含大量冗余字段，导致 Network I/O 拥堵。
- **方案**: 自定义实现 **Hadoop Writable 接口**，仅保留核心数值字段（ID/Price/Count），剔除所有对象头与非必要元数据。
- **成果**: **显著降低 Network I/O 传输开销**，Shuffle 阶段的数据传输效率得到明显改善。

### 3. Shuffle 阶段性能调优 (Shuffle Tuning)
- **痛点**: Reduce 端频繁出现 `Java Heap Space OOM`，任务经常失败。
- **方案**: 引入 **Map 端预聚合 (Combiner)** 机制，在本地预先合并中间结果，大幅减少落盘数据量。
- **成果**: **大幅缩减 Shuffle 阶段的数据传输量**，有效规避了内存溢出问题，任务成功率提升至生产可用水平。

### 4. 高效 ETL 与可视化落地 (ETL & Visualization)
- **方案**: 开发高效 ETL 脚本，利用 **JDBC Batch Insert (批量插入)** 技术将 HDFS 清洗结果同步至 MySQL。
- **展示**: 对接 **ECharts / 山海鲸** 可视化大屏，实时展现销售趋势、用户复购率及省份分布热力图。

---

## 🛠️ 技术栈

| 类别 | 技术组件 |
| :--- | :--- |
| **计算引擎** | Hadoop 3.x, MapReduce, YARN |
| **数据存储** | HDFS, MySQL 8.0 |
| **开发语言** | Java 8/11, SQL, Shell |
| **优化技术** | Custom Writable, Combiner, JobChain |
| **可视化工具** | ECharts, DataV (或山海鲸) |
| **操作系统** | Linux (CentOS 7), Windows (Dev) |

---

## 📂 项目结构

```text
hadoop-premade-dish-analysis
├── src
│   ├── main
│   │   ├── java
│   │   │   ├── com.analysis.driver      # 任务调度入口 (JobChain)
│   │   │   ├── com.analysis.mapper      # Map 逻辑 (含 Combiner)
│   │   │   ├── com.analysis.reducer     # Reduce 逻辑
│   │   │   └── com.analysis.bean        # 自定义 Writable 实体类
│   │   └── resources
│   │       └── hive-scripts             # Hive SQL 脚本 (可选)
├── data                                 # 示例数据集 (脱敏)
├── docs                                 # 项目文档与报告
├── target                               # 编译输出 (Jar包)
└── README.md                            # 项目说明