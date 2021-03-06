[风机叶片开裂故障预警](https://www.datafountain.cn/competitions/302/details), Top3
#  stage1
### 数据预处理
异常数据的处理将为后续提取良好的特征打下良好的基础。<br>
（1）将变量0和变量8同时为0的行删除掉。如果删除后的文件不再有数据，则直接判定为0（无故障）。<br>
原因：我们发现，本来是int型的离散变量，比如机器的16-无功功率控制状态，因为这些异常数据的存在导致均值特征变成了float型的连续变量。这为分类器识别带来的极大的困难。<br>
（2）删除最后三列。<br>
原因：最后三列全部为0，没有意义的特征。<br>

### 提取特征
各变量的均值和方差、各变量进行傅里叶变换后的均值和方差以及为了防止变量左偏、右偏后进行的log操作后的变量的均值和方差。

### F1指标分类
直接使用sklearn的gdbt，采取F1指标进行评价训练，选择最好的F1指标对应的分类器，得到第一阶段的分数F1--0.67053。

# stage2
对预测的值进行分析，我们发现上述分类器以大概率预测为1，只有很少的0出现在预测文件中。在此观察的基础上，我们仔细分析了F1指标,发现只有机器以高置信度判定为0，才会使得F1指标有所提升。因此问题转化为如何训练一个高置信度的0（对0具有高准确率）的分类器。因此后续的选择分类器的评价标准将是accuracy。<br>
### 数据再处理
通过对train data和test data分布的比较，我们发现分布存在明显差异，比如变量65-叶片三变频器温度，65以等于300和不等于300分成两个组。在train中等于300的占比近30%，而在test中仅占比12.4%。又比如变量55-偏航状态值，train中该变量均值等于4占比79%，而test中竟占比97%。基于上述发现，我们对train，test数据进行同分布处理。具体处理请参看代码。<br>
### 数据划分 
在上述基础上，我们又对风机按照53-风机当前状态、54-轮毂当前状态、65-叶片三变频器温度、16-无功功率控制状态分成了36个子部分。因为我们认为不同类型的机器，不同状态的机器所表现出来的故障因素是不同的。<br>
### 数据子part再分类
对上述36个子部分，选择其中机器数最多的两个子部分分别进行了随机森林的分类，分别得到F1--0.67234，F1——0.67835。具体的过程请参考代码实现。<br>

# 赛后总结
* 数据的预处理特别重要，决定了你提取的特征的可区分度。
* 对评价指标的分析，可以让你有不一样的思路。（😏）
* 通过对数据的不断分析和理解（pandas），你可以发现新的思路并提取新的特征。这也是数据挖掘比赛的核心。<br>

我们在本次比赛的创新点主要在于对于train与test的同分布处理、人为的划分子part进行识别以及将指标从F1转化为预测为label-0的accuracy。

# 其他推荐链接
[风机第二名](https://github.com/SY575/DF-Early-warning-of-the-wind-power-system)
