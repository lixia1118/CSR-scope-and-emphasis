# CSR 范围一致性与重点差异化计算

本项目用于计算企业社会责任（CSR）的范围一致性（Scope Conformity）和重点差异化（Emphasis Differentiation）指标。这些指标基于企业社会责任报告的披露数据，通过特征向量中心性（Eigenvector Centrality）方法计算得出。

本项目的参考文献如下：

![](https://github.com/lixia1118/CSR-scope-and-emphasis/blob/main/%E5%8F%82%E8%80%83%E6%96%87%E7%8C%AE/AMJ.png)

## 📋 项目简介

本项目实现了基于网络分析方法的CSR指标计算，主要包括：

- **范围一致性（Scope Conformity）**：衡量企业在CSR议题覆盖范围上与行业标准的符合程度
- **重点差异化（Emphasis Differentiation）**：衡量企业在CSR议题重点分配上与行业平均水平的差异程度

计算过程基于特征向量中心性（Eigenvector Centrality）方法，通过构建议题-议题共现网络来确定各议题的相对重要性权重。

## ✨ 功能特性

1. **数据加载与预处理**
   - 加载企业CSR议题披露数据
   - 加载企业行业分类代码数据
   - 数据清洗与合并

2. **行业平均投入计算**
   - 计算各行业-年份-议题的平均披露投入

3. **特征向量中心性计算**
   - 构建双模从属矩阵（Firms × Issues）
   - 转换为单模共现矩阵（Issues × Issues）
   - 计算特征向量中心性作为议题重要性权重

4. **范围一致性计算**
   - 基于滞后一期的特征向量中心性权重
   - 计算企业在各议题上的覆盖范围一致性

5. **重点差异化计算**
   - 计算企业投入比例与行业平均投入比例的差异
   - 基于加权绝对差的方法衡量差异化程度

6. **结果标准化**
   - Z-score标准化处理
   - 输出最终计算结果

## 📁 项目结构

```
CSR scope and emphasis calculate/
├── 计算过程.ipynb                          # 主计算脚本（Jupyter Notebook）
├── final_results.csv                       # 最终计算结果
├── 上市公司社会责任评价指标表/
│   ├── CSRR_EvaluationIndex.xlsx          # CSR披露数据（Excel格式）
│   ├── CSRR_EvaluationIndex[DES][csv].txt # 数据字段说明
│   └── 版权声明.pdf
├── 公司基本情况文件/
│   ├── CG_Co.xlsx                          # 公司基本信息数据
│   ├── CG_Co[DES][xlsx].txt               # 数据字段说明
│   └── 版权声明.pdf
└── 参考文献/
    └── AMJ.png                             # 参考文献图片
```

## 🔧 环境要求

### Python 版本
- Python 3.7+

### 依赖库
```python
pandas          # 数据处理
numpy           # 数值计算
networkx        # 网络分析（用于特征向量中心性计算）
openpyxl        # Excel文件读取
```

### 安装依赖
```bash
pip install pandas numpy networkx openpyxl
```

## 📊 数据要求

### 1. CSR披露数据（CSRR_EvaluationIndex.csv）
必需字段：
- `股票代码` (StockCode): 企业股票代码
- `统计截止日期`: 报告统计截止日期
- `披露内容类型编码` (DisclosureType): CSR议题编码
  - S3301: 股东权益保护
  - S3302: 债权人权益保护
  - S3303: 职工权益保护
  - S3304: 供应商权益保护
  - S3305: 客户及消费者权益保护
  - S3306: 环境和可持续发展
  - S3307: 公共关系和社会公益事业
  - S3308: 社会责任制度建设及改善措施
  - S3309: 安全生产内容

### 2. 公司基本信息数据（CG_Co.xlsx）
必需字段：
- `Stkcd`: 证券代码（对应股票代码）
- `Nnindcd`: 行业代码（2012版证监会行业分类代码）
- `Stktype`: 股票类型（可选）

## 🚀 使用方法

1. **准备数据文件**
   - 将CSR披露数据放置在 `上市公司社会责任评价指标表/` 目录下
   - 将公司基本信息数据放置在 `公司基本情况文件/` 目录下

2. **运行计算脚本**
   - 打开 `计算过程.ipynb` Jupyter Notebook
   - 按顺序执行所有代码单元格

3. **获取结果**
   - 计算结果将保存在 `final_results.csv` 文件中
   - 结果包含以下字段：
     - `StockCode`: 股票代码
     - `Year`: 年份
     - `ScopeConformity`: 范围一致性（原始值）
     - `EmphasisDifferentiation`: 重点差异化（原始值）
     - `ScopeConformity_Z`: 范围一致性（Z-score标准化）
     - `EmphasisDifferentiation_Z`: 重点差异化（Z-score标准化）

## 📈 输出结果说明

### 范围一致性（Scope Conformity）
- **定义**: 衡量企业在CSR议题覆盖范围上与行业标准的符合程度
- **计算公式**: $$\text{Scope Conformity}_{it} = \sum_{k=1}^9 \text{CSR}_{ikt} \cdot \text{CEN}_{k, t-1}$$
- **取值范围**: [0, 1]，值越大表示范围一致性越高

### 重点差异化（Emphasis Differentiation）
- **定义**: 衡量企业在CSR议题重点分配上与行业平均水平的差异程度
- **计算公式**: $$\text{Emphasis Differentiation}_{it} = \sum_{k=1}^K \left( |\text{FE}_{ikt} - \text{AE}_{jkt}| \cdot \mathbf{\text{CEN}_{k, t-1}} \right)$$
- **取值范围**: [0, +∞)，值越大表示差异化程度越高

### 标准化指标
- `ScopeConformity_Z`: 范围一致性的Z-score标准化值（均值为0，标准差为1）
- `EmphasisDifferentiation_Z`: 重点差异化的Z-score标准化值（均值为0，标准差为1）

## 🔍 计算流程

1. **数据加载**: 读取CSR披露数据和公司基本信息
2. **数据合并**: 将披露数据与行业代码进行匹配
3. **披露计数**: 计算每个公司在各年份各议题上的披露次数
4. **行业平均**: 计算各行业-年份-议题的平均披露投入
5. **网络构建**: 构建议题-议题共现网络
6. **中心性计算**: 计算特征向量中心性作为议题重要性权重
7. **指标计算**: 计算范围一致性和重点差异化
8. **标准化**: 对结果进行Z-score标准化
9. **结果输出**: 保存最终计算结果

## ⚠️ 注意事项

1. **数据完整性**: 确保数据文件路径正确，且包含必需的字段
2. **行业代码**: 项目使用2012版证监会行业分类代码（Nnindcd）
3. **缺失值处理**: 缺失行业代码的记录将被自动过滤
4. **收敛问题**: 对于网络分析不收敛的情况，CEN值将被设为0
5. **滞后处理**: 第一年的CEN滞后值（t-1）将被设为0

## 📝 参考文献

本项目基于学术研究论文的方法论实现，具体参考文献请参见 `参考文献/` 目录。



