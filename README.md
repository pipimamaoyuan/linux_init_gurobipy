# linux_init_gurobipy


# 从零开始在 Linux 服务器系统中安装 gurobipy

## 目录
1. [环境配置](#1-环境配置)
   - [1.1 安装 Miniconda](#11-安装-miniconda)
   - [1.2 创建 Conda 虚拟环境](#12-创建-conda-虚拟环境)
2. [Gurobi 安装与许可证配置](#2-gurobi-安装与许可证配置)
   - [2.1 下载 Gurobi](#21-下载-gurobi)
   - [2.2 申请许可证](#22-申请许可证)
   - [2.3 配置环境变量](#23-配置环境变量)
   - [2.4 激活许可证](#24-激活许可证)
3. [关联 Gurobi 与 Conda 环境](#3-关联-gurobi-与-conda-环境)
4. [验证安装](#4-验证安装)
5. [注意事项](#5-注意事项)
6. [常见问题与解决方法](#6-常见问题与解决方法)
7. [完整验证示例](#7-完整验证示例)
8. [扩展配置（可选）](#8-扩展配置（可选）)
   



## 1. 环境配置

### 1.1 安装 Miniconda
```bash
# 步骤说明
1. 进入目标安装目录
2. 下载安装脚本：
   wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh
3. 运行安装脚本：
   bash Miniconda3-latest-Linux-x86_64.sh
4. 阅读协议并按提示输入 `yes` 接受条款
5. 选择初始化 Miniconda（输入 `yes`）
6. 激活配置：
   source ~/.bashrc
7. 验证安装：
   conda env list  # 应显示 `base` 环境
```

### 1.2 创建 Conda 虚拟环境
```bash
# 创建指定 Python 版本的环境（需与 Gurobi 许可证版本匹配）
conda create --name gur python=3.11
```

---

## 2. Gurobi 安装与许可证配置

### 2.1 下载 Gurobi
1. 访问 [Gurobi 官网](https://www.gurobi.com/)，选择 **Linux 版本**（如 `gurobi12.0.0_linux64.tar.gz`）
2. 将安装包上传至 Linux 服务器并解压

### 2.2 申请许可证
1. 使用学校邮箱在[英文官网](https://www.gurobi.com/)注册账号
2. 完成验证获取许可证密钥（格式：`grbgetkey xx-xx4-xxbf-xx6-xxxxx`）

> **注意**  
> - 兴庆校区校园网不支持网络验证，建议使用创新港网络  
> - 通过中国代理申请有 IP 次数限制，推荐英文官网直接认证

### 2.3 配置环境变量
**方法 1：通过终端配置**
```bash
nano ~/.bashrc
```
在文件末尾添加：
```bash
export GUROBI_HOME=/home/mmy/gurobi1200/linux64
export PATH=${GUROBI_HOME}/bin:$PATH
export LD_LIBRARY_PATH=${GUROBI_HOME}/lib:$LD_LIBRARY_PATH
export GRB_LICENSE_FILE=/home/mmy/gurobi1200/linux64/gurobi.lic
```
保存后激活配置：
```bash
source ~/.bashrc
```

**方法 2：通过 PyCharm 配置**  
1. 远程打开 `.bashrc` 文件并添加上述内容  
2. 保存后执行 `source ~/.bashrc`

### 2.4 激活许可证
```bash
# 进入 Gurobi 的 bin 目录
cd /home/mmy/gurobi1200/linux64/bin
# 执行激活命令
grbgetkey xx-xx4-xxbf-xx6-xxxxx
```

---

## 3. 关联 Gurobi 与 Conda 环境
1. **复制 Gurobi 文件到虚拟环境**  
   - 将 `gurobi1200/linux64/bin` 下的文件复制到 `miniconda/envs/gur/bin`  
   - 遇到重复文件时建议跳过覆盖

2. **安装 gurobipy**  
   ```bash
   # 激活 Conda 环境
   conda activate gur
   # 安装 Python 接口
   pip install gurobipy
   ```

---

## 4. 验证安装
```python
# 在 Python 环境中测试
import gurobipy as gp
print(gp.GRB.VERSION)  # 输出版本号即成功
```

---

## 5. 注意事项
1. **Python 版本一致性**：创建 Conda 环境时需与许可证要求的 Python 版本严格匹配
2. **网络验证限制**：不同校区网络支持情况不同，优先使用支持网络验证的环境
3. **文件冲突处理**：复制 Gurobi 文件时若遇重复，建议保留原文件


## 6. 常见问题与解决方法

### 6.1 许可证不匹配错误
**问题描述**  
安装后运行代码时出现 `License version does not match` 错误。  
**解决方法**  
1. 检查 Conda 环境的 Python 版本是否与许可证申请时指定的版本一致  
2. 重新创建虚拟环境并指定正确版本：
   ```bash
   conda remove --name gur --all
   conda create --name gur python=3.11
   ```
### 6.2 环境变量未生效
**验证方法**  
```bash
echo $GUROBI_HOME  # 应显示路径 /home/mmy/gurobi1200/linux64
echo $PATH         # 路径中应包含 Gurobi 的 bin 目录
```

### 6.3 Gurobipy 导入失败
**可能原因**  
- 文件复制步骤未完全覆盖关键文件  
- 未在 Conda 环境中安装 `gurobipy`  

**排查步骤**  
1. 检查 Conda 环境的 `site-packages` 目录是否存在 `gurobipy`：
   ```bash
   ls ~/miniconda3/envs/gur/lib/python3.11/site-packages/ | grep gurobipy
   ```
2. 若缺失则重新执行：
   ```bash
   conda activate gur
   pip install --force-reinstall gurobipy
   ```
---
## 7. 完整验证示例
在 Python 中运行以下代码验证功能完整性：
```python
import gurobipy as gp

# 创建简单线性规划模型
model = gp.Model("test")
x = model.addVar(name="x")
y = model.addVar(name="y")
model.setObjective(x + y, sense=gp.GRB.MAXIMIZE)
model.addConstr(x + 2*y <= 4, "c1")
model.addConstr(3*x + y <= 6, "c2")
model.optimize()

# 输出结果
print(f"Optimal value: {model.ObjVal}")
print(f"x = {x.X}, y = {y.X}")
```
**预期输出**  
```
Optimal value: 3.0
x = 1.6, y = 1.2
```
---
## 8. 扩展配置（可选）

### 8.1 设置永久许可证路径
若多次遇到许可证检测问题，可将许可证文件手动复制到默认路径：
```bash
mkdir -p ~/gurobi/licenses
cp /home/mmy/gurobi1200/linux64/gurobi.lic ~/gurobi/licenses/
```

### 8.2 使用 Conda 直接安装（不推荐）
```bash
conda activate gur
conda install -c gurobi gurobi  # 可能版本滞后
```
