## 数据来源

本研究数据来源开源数据集： https://doi.org/10.6084/m9.figshare.2443828

其收集于The United States National Weather Service, Federal Aviation Administration, and Air Force jointly run the Next Generation Radar (NEXRAD) network的雷达扫描数据，并针对候鸟迁徙问题做了特殊处理。其中每个样本点表示一个监测点所观测到的环境变量和候鸟停留密度。

> [!NOTE]
>
> 代码运行需要将数据下载到本地，同时需要目录中包含美国边界的.shp文件等。



### 变量介绍

- Year：数据年份，2016-2020
- skybright_spring/fall：夜空亮度，来自NPP/VIIRS夜间灯光数据
- light_dist_spring/fall: 春季/秋季的人造光源距离
- precip_spring/fall：春季/秋季降水量
- 气候变量：不同月份的平均昼夜温差
- 不同月份增强植被指数（EVI）：反映植被绿度变化
- ...

### 数据预处理

1. 除去重复值，缺失值，处理变量单位等。
2. 划分数据集为训练集、验证集、保留集。

## 建模过程

1. 在美国边界范围内采样250个点，在这些点周围建立200000*200000的采样窗口（以阿尔伯特投影后的地图坐标为单位），最终得到209个有有效数据的窗口。
2. 使用一个采样窗口内的所有数据作为样本，对这209个点分别训练一个xgboost模型，用以预测此范围内的所有观测点的候鸟停留密度。

### 春季和秋季预测与实际测量的迁移中途停留密度对比
Spring

<img width="960" height="720" alt="image" src="https://github.com/user-attachments/assets/7dd3cecd-e181-47ed-a53e-4acf2dc74d4b" />

Fall

<img width="960" height="720" alt="image" src="https://github.com/user-attachments/assets/c6cad61b-d718-4a83-8c03-df5ebb072a42" />

### 候鸟中途停留密度和美国本土热点图

<img width="1009" height="575" alt="image" src="https://github.com/user-attachments/assets/2231aed5-03aa-40cb-b136-e65c10bfa978" />



### 模型具体分析

1. 对209个xgboost模型进行聚类，依据其在不同特征上的weight为特征向量。最终选定76、113、212号模型，分别位于密西西比州，加利福尼亚州，内布拉斯加州。

<img width="1788" height="582" alt="image" src="https://github.com/user-attachments/assets/9148599d-7323-4778-acfc-bc91cc31b959" />


2. 使用shap库对这三个模型进行进一步分析。

<img width="1151" height="592" alt="image" src="https://github.com/user-attachments/assets/278e395c-6d83-40f9-bfeb-13618127bc94" />

<img width="1149" height="581" alt="image" src="https://github.com/user-attachments/assets/251dd6c8-3b40-49f5-8232-eb61bf73f435" />

<img width="1149" height="583" alt="image" src="https://github.com/user-attachments/assets/5c3b0f77-52e4-456c-a4b5-562c553f5d3e" />


## 结论

1. 在三个地区的代表性模型中，鸟类监测雷达对于鸟类迁徙行为均有重要影响，因此应当合理规划雷达安放位置，尽量减少此类干预；
2. 对于经济较为落后的密西西比州，影响候鸟迁徙行为的主要特征为海岸线距离、土地利用模式等自然因素；而对于人口密度大，经济发达的西海岸和中北部来说，夜空亮度和人造光源则成为最重要的影响因素。具体来说，夜间迁徙的鸟类可能会因为夜空亮度过高而滞留；人工光源（如城市灯光、灯塔等）会吸引夜间飞行的候鸟停留，尽管这些地点并不适合觅食与休息。这些光污染与光陷阱导致鸟类在飞行过程中偏离路线，从而影响它们的生存和繁殖。除此之外，人为因素与自然因素有一定的交互效应，导致自然因素的影响在人为因素的干预下可能会被过分放大而同样影响鸟类的正常生存行为。
3. 随着年份增长（从2016到2020），候鸟的停留密度逐年增加。这意味着人工光源正在越来越明显的干扰候鸟的迁徙模式，使得控制人造光、降低夜空亮度成为一个逐渐紧迫的问题。
