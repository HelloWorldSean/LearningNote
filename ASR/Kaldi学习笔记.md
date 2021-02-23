# Kaldi

## 环境搭建

### 运行环境

PowerPC 64bits little-endian (ppc64le)

- Kaldi is expected to work out of the box in RHEL >= 7 and Ubuntu >= 16.04 with OpenBLAS, ATLAS, or CUDA.
- CUDA drivers for ppc64le can be found at https://developer.nvidia.com/cuda-downloads.
- An [IBM Redbook](https://www.redbooks.ibm.com/abstracts/redp5169.html) is available as a guide to install and configure CUDA.

下载镜像，安装到VMware虚拟机：https://ubuntu.com/#download

注：内存稍微多分配点，我直接分配了8G内存，100G硬盘，8个逻辑核，内存不过够编译的时候会因为OOM，OS将编译进程杀死。

### 源码下载

先安装git工具：sudo apt-get install git

git clone https://github.com/kaldi-asr/kaldi.git

### 源码编译

#### 编译MKL

MKL是英特尔提供的一个数学核心函数库，可以提供经过高度优化和大量线程化处理的数学例程。目前来说该函数库面向的是要求极高的自然、工程金融等领域。MKL是一个商用函数库，但是仅支持英特尔旗下的处理器运行。编译命令如下

##### 工具依赖检查

cd  tools/extras

./check_dependencies.sh

根据提示进行工具的安装，安装之后，再执行一次./check_dependencies.sh，反复执行几次，保证安装完整。

最终效果：

```shell
yuanyingchao@Yuan-Ubuntu:~/Code/kaldi/tools$ extras/check_dependencies.sh
extras/check_dependencies.sh: all OK.
```

##### MKL编译

sudo apt-get install update 

sudo ./install_mkl.sh

#### 编译tools

该tools文件夹下都是kaldi所依赖的包，其中主要包括以下内容：

①OpenFST:是用来构造有限自动机的库，我们知道可以把隐马尔可夫模型看作一个有限状态自动机，这也是编译过程中需要的一个比较重要的包。
②ATLAS：这是一个在c++下的线性代数运算库，在机器学习中是经常需要进行矩阵运算的。
③IRSTLM：这是一个统计语言模型的工具包
④sph2pipe：这个是宾夕法尼亚大学(LDC)开发的一款处理sphere_formatted数字音频文件的软件

根据INSTALL指导进行：

extras/check_dependencies.sh

make

make -j 8（8个线程编译，速度快，有条件的可以调高）

#### 编译src

根据INSTALL指导进行：

./configure --shared
make depend -j 8
make -j 8

### 运行Demo

在kaldi的内部自带了很多测试例子，我们可以选择测试例子yesno。

##### 测试例子yesno

该例子内的训练样本都是使用的.wav格式的音频文件，其中的一个男士在不断的说yes或者no，每个wav文件说了八次，其中的文件名字是由0和1组成的8位数字序列。

cd  egs/yesno/s5   //进入测试的例子文件夹 

./run.sh  				//执行脚本，来运行该例子

```shell
# 打印
Overall, lattice depth (10,50,90-percentile)=(1,1,2) and mean=1.2
steps/diagnostic/analyze_lats.sh: see stats in exp/mono0a/decode_test_yesno/log/analyze_lattice_depth_stats.log
local/score.sh --cmd utils/run.pl data/test_yesno exp/mono0a/graph_tgpr exp/mono0a/decode_test_yesno
local/score.sh: scoring with word insertion penalty=0.0,0.5,1.0
%WER 0.00 [ 0 / 232, 0 ins, 0 del, 0 sub ] exp/mono0a/decode_test_yesno/wer_10_0.0
```

最后显示了%WER 0.00 [ 0 / 232, 0 ins ,0 del ,0 sub ]，表示识别的结果还是非常准确的。这里说明一下WER是什么东西。
WER（Word Error Rate）代表的是字错误率，是衡量语音识别系统的准确程度的标准。计算公式如下

WER=（ins+del+sub）/N
其中各字母的含义如下所示：
ins:	代表被插入的单词的个数
del:	代表被删除的单词的个数
sub:	代表被替换的单词个数
N:		代表总共的单词个数

简单的可以理解为把识别过程中出现的多认的、少认的、认错的单词数加起来，去除以总共测试的单词数。因此这个数是越小越好，越小代表出错的概率越低。

## 代码分析

### yesno脚本代码

这里就以yesno为例进行分析

这个例子只进行了单因素训练



## 一些概念

S5是一种语音识别方法的demo，V1是一种说话人识别的demo

### LM

LM算法，全称为Levenberg-Marquard算法，它可用于解决非线性最小二乘问题，多用于曲线拟合等场合。

LM算法是一种迭代求函数极值的算法，理解该算法首先要明白牛顿法求极值与梯度法求极值，LM算法综合了这两种算法的特点

LM算法的实现并不算难，它的关键是用模型函数 ff 对待估参数向量 pp 在其邻域内做线性近似，忽略掉二阶以上的导数项，从而转化为线性最小二乘问题，它具有收敛速度快等优点。LM算法属于一种“[信赖域法](http://www.codelast.com/?p=7488)”——所谓的信赖域法，此处稍微解释一下：在最优化算法中，都是要求一个函数的极小值，每一步迭代中，都要求目标函数值是下降的，而信赖域法，顾名思义，就是从初始点开始，先假设一个可以信赖的最大位移 ss ，然后在以当前点为中心，以 ss 为半径的区域内，通过寻找目标函数的一个近似函数（二次的）的最优点，来求解得到真正的位移。在得到了位移之后，再计算目标函数值，如果其使目标函数值的下降满足了一定条件，那么就说明这个位移是可靠的，则继续按此规则迭代计算下去；如果其不能使目标函数值的下降满足一定的条件，则应减小信赖域的范围，再重新求解。

事实上，你从所有可以找到的资料里看到的LM算法的说明，都可以找到类似于“如果目标函数值增大，则调整某系数再继续求解；如果目标函数值减小，则调整某系数再继续求解”的迭代过程，这种过程与上面所说的信赖域法是非常相似的，所以说LM算法是一种信赖域法。

### MFCC

MFCC(Mel-frequency cepstral coefficients):梅尔频率倒谱系数。梅尔频率是基于人耳听觉特性提出来的， 它与Hz频率成非线性对应关系。梅尔频率倒谱系数(MFCC)则是利用它们之间的这种关系，计算得到的Hz频谱特征。主要用于语音数据**特征提取和降低运算维度**。例如：对于一帧有512维(采样点)数据，经过MFCC后可以提取出最重要的40维(一般而言)数据同时也达到了将维的目的。

MFCC一般会经过这么几个步骤：预加重，分帧，加窗，快速傅里叶变换(FFT)，梅尔滤波器组，离散余弦变换(DCT)，其中最重要的就是FFT和梅尔滤波器组，这两个进行了主要的将维操作。

### Monophone

训练单音素

### Triphone

三音素训练

### fMLLR

声学特征变换，特征空间最大似然线性回归（Feature-space Maximum Likelihood Linear Regression, FMLLR）

### SAT

说话人自适应训练	(Speaker Adaptive Training）

说话人自适应技术是利用特定说话人数据对**说话人无关(Speaker Independent,SI)**的码本进行改造，其目的是得到说**话人自适应(SPeaker Adapted, SA)**的码本来提升识别性能。我们知道在某个说话人的训练数据足够多的时候，针对当前说话人数据采用传统的训练方法可以得到×**说话人相关(Speaker Dependent, SD)**的码本,由于SD码本很好的反应了当前说话人的特征，因此效果往往更好，但实际中往往缺少足够的数据，因此采用说话人自适应，这样我们只需要很少量的数据就可以得到比较大的性能提升。**其实质是利用自适应数据调整SI码本以符合当前说话人特性**。

由于传统训练方法得到的 SI 码本不可避免地受训练集特性的影响, 在**训练集和自适应数据失配**时这会导致自适应效果变得不明显, **原始码本越具有说话人无关性, 在自适应时就越能迅速地趋近当前说话人的特征**。与自适应相结合的码本训练**对 SI 码本、训练集内每个说话人特性分别建立模型**, 因此可以得到更具说话人无关性的 SI 码本。

### TDNN

时延神经网络：相当于CNN的前身，相当于1dcnn,即一维CNN。它的共享权重被限制在单一的维度上，并且没有池化层，适用于语音和时间序列的信号处理。

TDNN最早是在1989年，由Hinton提出，目的在于解决音素的识别。

### GMM

高斯混合模型：高斯模型就是用高斯[概率密度函数](https://baike.baidu.com/item/概率密度函数/5021996)（[正态分布曲线](https://baike.baidu.com/item/正态分布曲线/12726695)）精确地量化事物，将一个事物分解为若干的基于高斯概率密度函数（正态分布曲线）形成的模型。

GMMs已经在数值逼近、语音识别、图像分类、图像去噪、图像重构、故障诊断、视频分析、邮件过滤、密度估计、目标识别与跟踪等领域取得了良好的效果 。

别称：MOG(Mixture of Gaussian)

### HMM

隐马尔科夫模型：（Hidden Markov Model，以下简称HMM）是比较经典的机器学习模型了，它在语言识别，自然语言处理，模式识别等领域得到广泛的应用。

当然，随着目前深度学习的崛起，尤其是[RNN](https://link.zhihu.com/?target=http%3A//www.cnblogs.com/pinard/p/6509630.html)，[LSTM](https://link.zhihu.com/?target=http%3A//www.cnblogs.com/pinard/p/6519110.html)等神经网络序列模型的火热，HMM的地位有所下降。



## 运行aishell

中文识别的demo，需要下载语料，15G





## Kaldi语音识别步骤

1. 语料准备
2. 构建语法模型（G.fst）
3. 构建发音词典模型（L.fst）
4. 合并发音词典与语法模型（LG.fst）
5. 确定化发音词典与语法模型（det-LG.fst）
6. 构建上下文模型与发音词典模型、语法模型（CLG.fst）
7. 构建HMM模型（H.fst）
8. 合并HMM模型，上下文模型、发音词典模型、语法模型（HCLG.fst）

模型可视化：sudo apt install graphviz

```shell
kaldi/tools/openfst/bin/fstdraw --isymbols=words.txt --osymbols=words.txt G.fst > G.dot
dot -Tjpg G.dot > G.jpg
dot -Tpdf G.dot > G.pdf
模型太大话画不出来，非常吃内存
```

在训练的job并行训练过程中，训练数据的各个子集合是分散到不同的处理器去进行训练，然后每轮迭代后会进行合并。 下面就讲一下训练的过程： 

1.首先是初始化GMM，使用的脚本是/kaldi-trunk/src/gmmbin/gmm-init-mono，输出是0.mdl和tree文件； 

2.compile training graphs,使用的脚本是/kaldi-trunk/source/bin/compile-training-graphs，输入是tree,0.mdl和L.fst,输出是fits.JOB.gz，其是在训练过程中构建graph的过程； 

3.接下来是一个对齐的操作，kaldi-trunk/source/bin/align-equal-compiled； 

4.然后是基于GMM的声学模型进行最大似然估计得过程，脚本为/kaldi-trunk/src/gmmbin/gmm-est； 

5.然后进行迭代循环中进行操作，如果本步骤到了对齐的步骤，则调用脚本kaldi-kaldi/src/gmmbin/gmm-align-compiled； 

6.重新估计GMM，累计状态，用脚本/kaldi-trunk/src/gmmbin/gmm-acc-states-ali；调用新生成的参数(高斯数)重新估计GMM，调用脚本/kaldi-trunk/src/gmmbin/gmm-est；

7.对分散在不同处理器上的结果进行合并，生成.mdl结果，调用脚本gmm-acc-sum； 

8.增加高斯数，如果没有超过设定的迭代次数，则跳转到步骤5重新进行训练 最后生成的.mdl即为声学模型文件 在离线识别阶段，即可以调用utils/mkgraph.sh；来对刚刚生成的声学文件进行构图 之后解码，得到离线测试的识别率。



### 数据准备

需要准备四个文件:

wav.scp：<recording-id>	<extended-filename>（发声ID	全路径的录音文件名字）

text： <utterance-id>	<sentence content>	（发声ID	分词后的句子内容）

utt2spk：<utterance-id>	<speaker-id>	（发声ID	说话人ID）

spk2utt：<speaker-id>	<utterance-id>	（说话人ID	发声ID）



