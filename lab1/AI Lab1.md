# AI Lab1 Report

​											  							   徐海阳 PB20000326

## 实验环境

机器设备情况：RTX3090 x 1

所用语言：Python

库：anaconda python == 3.9

​		torch, numpy, sklearn



## 数据预处理及读取

### MLP

读取.csv文件并存入.npy文件

<img src="pics/loaddata.png" style="zoom:40%;" />

定义Dataset

<img src="pics/dataset.png" style="zoom:50%;" />

### SVM

尝试了在整个数据集上进行训练，速度极慢。于是将数据集随机选出1/100作为新的数据集。也就是trainset 7k样本，validset 7k样本，testset 1k样本。记为"small_{}"feat/label,{}=train, validation, test

取1/100的代码：

<img src="pics/SVMsmalldata.png" style="zoom:40%;" />



##  训练过程

### 主要函数

#### MLP

1. init dataset和model

<img src="pics/init.png" style="zoom:50%;" />

2. train

<img src="pics/train.png" style="zoom:50%;" />

3. eval

<img src="pics/eval.png" style="zoom:50%;" />

#### SVM

<img src="pics/SVM.png" style="zoom:40%;" />



### 训练及调参过程

***60分内容均有完成***

#### MLP2

1. 第一次实验超参数：

    batch_size: 1024, learning_rate: 0.50, max_epoch: 5, activator: sigmoid, layer_list: [285, 16, 2]

2. 第一次实验结果：

    <img src="pics/MLP21.png" style="zoom:40%;" />

3. 结果分析及超参数选择：

    train和eval都逐渐收敛
    但F1 score和accuracy都存在一定抖动，同时F1 score和accuracy无法到达很高的数值
    因此存在问题：无法收敛到最优的点
    针对这种情况，采取以下超参数调整办法：

    1. 适当***降低lr***，如0.5->0.4
    2. ***增大某些层神经元数量***，让网络学到更多、更细致的特征，利于提高F1 score

4. 第二次实验超参数：

    batch_size: 1024, learning_rate: 0.40, max_epoch: 5, activator: sigmoid, layer_list: [285, 32, 2]

5. 第二次实验结果：

    <img src="pics/MLP22.png" style="zoom:40%;" />
    
    稳定性和F1 score都有一定提升
    
    选择第二次实验的模型在test集上进行测试

#### MLP3

1. 第一次实验超参数：

    batch_size: 1024, learning_rate: 0.50, max_epoch: 5, activator: sigmoid, layer_list: [285, 64, 8, 2]

2. 第一次实验结果：

    <img src="pics/MLP31.png" style="zoom:40%;" />

3. 结果分析及超参数选择：

    train和eval都逐渐收敛
    但F1 score和accuracy都存在一定抖动
    因此存在问题：无法收敛到最优的点
    针对这种情况，采取以下超参数调整办法：

    1. 适当***降低lr***，如0.5->0.3
    2. 适当***减小batchszie***，防止bsz大、lr小从而导致收敛到局部最优
    3. ***增大某些层神经元数量***，让网络学到更多、更细致的特征，利于提高F1 score

4. 第二次实验超参数：

    batch_size: 512, learning_rate: 0.30, max_epoch: 5, activator: sigmoid, layer_list: [285, 128, 16, 2]

5. 第二次实验结果：

    <img src="pics/MLP32.png" style="zoom:40%;" />
    
    F1 score最大值超过了第一次试验，但稳定性没有提升。
    
    选择第二次实验的模型在test集上进行测试。

#### MLP4

1. 第一次实验超参数：

    batch_size: 1024, learning_rate: 0.50, max_epoch: 5, activator: sigmoid, layer_list: [285, 128, 64, 8, 2]

2. 第一次实验结果：

    <img src="pics/MLP41.png" style="zoom:40%;" />

3. 结果分析及超参数选择：

    F1 score始终有较大的抖动，甚至经常出现0

    同时accuary也存在抖动。直到epoch4之后，F1 score和accuracy才上到一个较高的数值
    因此存在问题有：

    1. lr太大，导致前期一直没有学习到什么，后期也存在一定抖动
    2.  2.存在几次F1 score为0的情况，说明存在较严重的过拟合现象

    针对这种情况，采取以下超参数调整办法：

    1. 适当***降低lr***，如0.5->0.3 
    2. 适当***增大batchszie***，避免出现学到某个batch的局部特征，从而导致F1骤降的情况发生 
    3. ***将激活函数换为relu***,降低过拟合现象 
    4. ***减小某些层神经元数量***，目的是让网络学到更精炼、更高层的特征，提高泛化性，避免学到某些层特征时导致F1骤降的情况发生

4. 第二次实验超参数：

    batch_size: 512, learning_rate: 0.30, max_epoch: 5, activator: sigmoid, layer_list: [285, 128, 16, 2]

5. 第二次实验结果：

    <img src="pics/MLP42.png" style="zoom:50%;" />
    
    稳定性提升明显。F1 score没有明显提升，与上一次实验持平。
    
    选择第二次实验的模型在test集上进行测试

#### SVM

##### kernal = rbf

<img src="pics/svmtrain.png" style="zoom:40%;" />

可以看到，软间隔c=1时，train和epoch的F1 score较为接近；调为1000，过拟合；调为500，仍过拟合；调为100，效果略有上升；调为50，持平；调为150，过拟合

选择c=100作为候选

##### kernal = linear

<img src="pics/svmlinear.png" style="zoom:40%;" />

软间隔c=1时，train和epoch的F1 score较为接近；调为100，持平

F1 score低于“kernal = rbf， c = 100”的模型

##### kernal = poly

<img src="pics/svmpoly.png" style="zoom:40%;" />

一阵狂调，皆失败...

最终选择“kernal = rbf， c = 100”的模型在test集上进行测试



### 测试及分析

***36分内容均有完成***

#### 拟合情况及F1 score， AUC， ROC曲线

##### MLP2

F1 score及AUC：

<img src="pics/MLP2t.png" style="zoom:50%;" />

分析：由F1_score和train_F1_score较为接近，且与验证集结果持平，可以判断拟合良好

​			符合验证集预期。

ROC曲线：

​			注意到这里的ROC曲线并不是阶梯状上升的（如PPT上所示）。可能是该数据集的数据特征决定的。

<img src="pics/ROC_curve_0.7558238514083215.png" style="zoom:33%;" />



##### MLP3

F1 score及AUC：

<img src="pics/MLP3t.png" style="zoom:50%;" />

分析：由F1_score和train_F1_score较为接近，且与验证集结果持平，可以判断拟合良好

​			符合验证集预期。

ROC曲线：

​			注意到这里的ROC曲线并不是阶梯状上升的（如PPT上所示）。可能是该数据集的数据特征决定的。

<img src="pics/ROC_curve_0.7574916280973238.png" style="zoom:33%;" />

##### MLP4

F1 score及AUC：

<img src="pics/MLP4t.png" style="zoom:50%;" />

分析：由F1_score和train_F1_score较为接近，且与验证集结果持平，可以判断拟合良好

​			符合验证集预期。

ROC曲线：

​			注意到这里的ROC曲线并不是阶梯状上升的（如PPT上所示）。可能是该数据集的数据特征决定的。

<img src="pics/ROC_curve_0.7547711034758281.png" style="zoom:33%;" />

##### SVM

F1 score及AUC：

<img src="pics/SVMt.png" style="zoom:50%;" />

分析：有一定过拟合现象（train_F1_score比F1_score高了5个点）

​			大体上符合验证集预期

ROC曲线：

​			注意到这里的ROC曲线与上三张图相似。

​			***F1_score，train_F1_score，AUC，ROC曲线都与另三个在全数据集上进行训练的结果差不多，因此可以认为1/100随机采样后的特征仍服从原始数据集的分布，即数据预处理在该实验要求下对实验结果产生的影响可以忽略***

<img src="pics/ROC_curve_0.7433755760368663.png" style="zoom:33%;" />

## 实验结论

熟悉了Pytorch和sklearn库

学会了分析各种评价指标（本实验主要是F1 score）来进行超参数的选择与调整













