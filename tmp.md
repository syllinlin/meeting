---
marp: true
theme: default
paginate: true
header: 'CityU'


---

<!-- _class: title-slide -->
<!-- _paginate: false -->
<!-- _header: "" -->
<!-- _footer: "" -->


# 2026.02.23-2026.02.27
## TODO：
1. 训练不稳定
  1. 增强不稳定：全局 seed + deterministic
  2. 训练更久 + 选 best checkpoint
2. 增强+dual-view 相当于更难任务，通常要更多 epoch 才会稳定收敛。
- 从 80 → 150（但关键是选最好的，不是最后的）
现在没有 val 集，至少做一个最小划分：
  - 16 train / 4 val（固定随机种子划分）
  - 每个 epoch 在 val 上算一次 loss，保存 best
---
3. 扩大训练数据集
4. centerline不连续问题：
  1. 
训练数据少，测试效果一般，泛化能力较弱，特别是“大断裂情况，和centerline的不连续性”

---
## Aug (数据增强) 思路
通过破坏性增强模拟真实场景中的道路不完整，包括：
1. 随机线段切割：在道路上创建随机方向的缺口
2. 随机块状丢失：移除道路上的块状区域
3. 随机腐蚀：使道路变细或断裂
4. 可选膨胀噪声：增加数据多样性

---
## Dual-View (双视图训练) 思路
同时使用原始干净图像和增强图像进行训练：
1. 数据准备：为每个样本生成 x_clean 和 x_aug 两个视图
2. 双前向传播：对两个视图分别进行前向传播
3. 加权损失：loss = ALPHA * loss_clean + (1-ALPHA) * loss_aug
- ALPHA 越高 → 更关注干净数据的拟合
- ALPHA 越低 → 更关注增强数据的泛化
Aug：提高模型对噪声、遮挡、断裂的鲁棒性
Dual-View：平衡拟合能力与泛化能力，避免过拟合

