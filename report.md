# 每日作业报告

> 复制为本日独立仓库根目录的 `report.md`。删除本提示，全部换成你们自己的真实证据。不要让智能体替学生写反思或结论。

## 1. 本日问题

- 里程碑：day-01
- 学生或小组：高川石
- 使用者：博物馆教育团队
- 真实输入：Kaggle Titanic 的 train.csv，891 行、12 列真实乘客记录
- 需要的输出：一个可运行的分类程序：先跑基线、再跑随机森林候选，输出指标和真实错误乘客记录
- 与使用者最相关的错误：假阴性——真实幸存却被预测为未幸存（因为博物馆想展示"哪些人可能幸存"，漏掉幸存者最误导）
- 本日产品边界：这是历史观察数据分析，不是现代救援分配工具，不能证明某个特征"导致"生存

## 2. 真实数据或真实课程输入

- 所有者/发布者：Syed Hamza Ali
- 标题：Titanic-Dataset (train.csv)
- 原始 URL：https://www.kaggle.com/datasets/hesh97/titanicdataset-traincsv
- 许可标签或使用许可：公有领域，CC0
- 下载/取得日期：2026-08-17
- 预期文件与结构：data/raw/train.csv
- 检查命令：python train.py --check-data
- 实际检查结果：REAL DATA CHECK PASSED；rows:891；columns:12；survived_counts: {0:549, 1:342}；missing_age:177；missing_cabin:687；missing_embarked:2
- 已知缺失、偏差或限制：Age 177、Cabin 687、Embarked 2；历史观察数据，不能当现代救援依据

Day 0 请把“真实输入”写成实际课程仓库、Day 1 文件和 `learning-plan.json`，并记录路径检查。

## 3. 可复现运行

```powershell
# 当前目录
cd "C:\Users\LEGION\Desktop\夏令营\ai\ai-summer-camp-2026-main\student-work\day-01-titanic"
# 安装
python -m pip install -r requirements.txt
# 数据检查
python train.py --check-data
# 预期：REAL DATA CHECK PASSED；rows: 891；survived_counts: {0: 549, 1: 342}
# 主程序
python train.py
# 输出到：metrics.json（终端同时打印 JSON）；errors.csv
# 测试
python -m unittest discover -s tests -v
# 预期：Ran 3 tests in 0.027s OK


## 4. 基线与候选

### 简单基线

- 方法：DummyClassifier(strategy="most_frequent")
- 为什么足够简单：不用学习任何规律，只要数出哪个类别最多
- 命令：python train.py
- 结果：accuracy 0.614；recall/precision/F1 都是 0；混淆矩阵 [[137,0],[86,0]]

### 候选方法

- 学生完成的核心改动：把build_candidate里原来的报错，换成preprocessor()+RandomForestClassifier(random_state=SPLIT_SEED)
- 保持不变的数据、划分、指标或参数：同一份 train.csv、test_size=0.25、random_state=42、同一套指标
- 命令：python train.py
- 结果：accuracy 0.744；precision 0.684；recall 0.628；F1 0.655；混淆矩阵 [[112,25],[32,54]]

| 项目 | 基线 | 候选 | 含义 |
| --- | ---: | ---: | --- |
| 主指标 | 0.614 | 0.744 | 候选更高 |
| 重要错误 | 0/86 | 54/86 | 候选找回更多幸存者 |

## 5. 一个真实失败案例

- 样本位置/编号：errors.csv 第 27 行；PassengerId: 432
- 真实结果：1
- 系统输出：0
- 可以观察到什么：女性、三等舱、Age 缺失、SibSp=1、Fare=16.1、S 港登船
- 说明的限制：Age 缺失被中位数填补，模型对信息不完整的乘客更容易出错；检查显示猜错的人里Age缺失占33%，而整体只有约20%
- 不能证明什么：单一样本不能证明因果；不能说"Age 缺失导致误判"，只能说存在联系
- 下一项最小检查：比较 Age 缺失与不缺失乘客的准确率

## 6. 智能体与学生工作边界

- 智能体提出/生成/修改了什么：解释基线、随机森林、混淆矩阵等概念，教我改train.py，帮我排查Python环境
- 学生怎样核对文件、来源、输出、测试和 diff：在终端跑命令，改train.py，算指标，挑失败案例，跑 Age 缺失检查，填报告；
- 学生修改或拒绝了什么建议：选了Thorneycroft这个案例，提出了"比较 Age 缺失准确率"的检查思路；
- 每名成员能独立解释的代码或证据：build_candidate 为什么这样写、基线为什么是 0.614、recall 为什么从 0 变 0.628。

## 7. 结论与限制

在同一批测试乘客上，随机森林各项指标都超过"永远猜未幸存"的基线，因此更有用；recall 只有 0.63，仍漏掉 32 个幸存者，且 Age 缺失的人更容易被猜错；这些发现只描述这份历史数据，不能证明因果，不能用于现代救援决策。

## 8. 提交复核

- [ ] README 从新环境可以开始运行
- [ ] 数据检查、测试和主程序重新运行
- [ ] 报告数字与保存输出一致
- [ ] `presentation.pptx` 在 3 分钟内讲完
- [ ] `submission.json` 路径正确
- [ ] 无密钥、大数据、私人信息、虚拟环境或缓存
- [ ] GitHub 网页复查并邮件发送 URL

