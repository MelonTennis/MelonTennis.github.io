---
layout: post
title: Text Classification Notes
subtitle: 我到底在工作个啥。。。
date: 2018-11-17
categories: Note
tags: [ML]
catalog: true
---

## Text Classification Task Notes

大概一个月之前一直在做文档分类的任务，经过了一番瞎train和读论文，觉得还是总结一下。由于某种原因，我有5000+条文本和GPU~~并删掉了一些废话~~。

### Baseline

随便搞个TFIDF塞进RandomForestClassifier里。因为label imbalance所以试图将class_weight设成balanced，然后用GroupKFold来cross validate一下。嗯，imbalance一点也没有改善嘛。根据[这个关于Imbalance的博客](https://machinelearningmastery.com/tactics-to-combat-imbalanced-classes-in-your-machine-learning-dataset/)里总结的，试一下过采样(imblearn.over_sampling.SMOTE)和欠采样(imblearn.under_sampling.ALLKNN). 微弱的提升，不过RF看起来对imbalance data比较敏感，或许MLP会好一些。[有一种说法](https://stats.stackexchange.com/questions/283760/is-cross-validation-unnecessary-for-random-forest)是[random forest](https://www.stat.berkeley.edu/~breiman/RandomForests/cc_home.htm)不需要进行corss valid，因为它不怎么会overfit。不过这会我的train data差异性不明，希望能够观察一下是否会造成结果差异。

### LSTM 

~~为什么在实现之前没有好好看论文！！写代码一时爽，RFC火葬场QAQ 我又为什么不好好先看[ML Yearning](http://www.mlyearning.org/)！！调参数使我失去了信念。难为前辈code review了。论文读到最后都差不多，实验起来经常一场空。~~

[LSTM](http://colah.github.io/posts/2015-08-Understanding-LSTMs/)做文本分类的话，可以在时间上提取文本的特征，因此不会丢失文本顺序信息。根据[这篇博客](https://medium.com/@sabber/classifying-yelp-review-comments-using-cnn-lstm-and-visualize-word-embeddings-part-2-ca137a42a97d),  单层LSTM的文本分类效果应该是可以接受的。不过听(研究NLP的)朋友说一般会使用两层LSTM网络，但是不知道理由？看起来是第一层LSTM提取word层面上的特征，高层LSTM提取sequence层面的特征，[相关论文](static.googleusercontent.com/media/research.google.com/en//pubs/archive/43905.pdf)。

实际训练中会发现对于文本的预处理对于performance的负向作用，然而之前好像没遇到过预处理使效果变差的情况所以应该是和data有关。道理上讲随着模型复杂度的提升和input data feature的增加，bias会降低而variance会增加。相应的调整一下dropout，regularization，early stop，learning rate decay。失去了pretrain embedding的LSTM就像我的生活一地鸡毛 = ) ~~不知道是不是对于所有的data，embedding层设0.1左右Dropout，Dense层前设0.5左右Dropout会好些？也不知道在character层面建模会不会好一些，可能会更慢？~~

#### References

~~为了表示自己没有瞎拍脑袋随便用了3层LSTM训练而强行引用的~~[Training and Analyzing Deep RNN](static.googleusercontent.com/media/research.google.com/en//pubs/archive/43905.pdf)利用character-based deep RNN来捕获character结构上long-term和short-term的信息，在prediction和classification的结果都很好。~~我应该像这里面一样一层一层向上加来分次训练的，可我没有。~~  

[LSTM Architectures for Large Scale Acoustic Modeling](static.googleusercontent.com/media/research.google.com/en//pubs/archive/43905.pdf)，利用2层带映射层的LSTM(LSTMP)在acoustic data上建模，在speech recognition问题上超过了当年的state-of-art效果。LSTM可以看作是[unroll in time](https://machinelearningmastery.com/rnn-unrolling/)的前馈神经网络，其中每一层共享相同的参数。对于单层LSTM，每个时间的feature通过一层非线性层处理对输出贡献，对于多层LSTM，每个time step的input通过多层LSTM在时间和空间上传播。深层的LSTM结构使模型在input sequence上的记忆增加，同时也会使模型更易于overfit。文章中用5层LSTM达到了最佳效果，超过7层LSTM则难以收敛。~~巧了我也是，而且过于time consuming。~~ 另外对于多层LSTM的output，我用the last time step output，根据[这篇博客](http://deeplearning.net/tutorial/lstm.html)也可以用mean pooling，试了一下效果不是很好。

实现下[Semi-supervised Sequence Learning](https://arxiv.org/pdf/1511.01432.pdf)试试~~反正也失去了好多labeled data~~。根据[1997年LSTM奠基论文](https://www.bioinf.jku.at/publications/older/2604.pdf)，LSTM可以用在文本分类问题上，想要使随机初始化的LSTM网络达到比较好的效果需要仔细的调参工作。这篇论文提出如果在labeled data数量不足但是有unlabeled data的情况下，利用recurrent language model(LM-LSTM)或者sequence auto-encoders(SA-LSTM)训练unlabeled data，将pretrain得到的weights作为LSTM的初始weight。因为网络可以记住输入序列的特征，所以会获得效果上的提升。~~所以是将所有data都看作unlabeled  data然后pretrain，虽然加不加那些labeled data其实差别不是很大？发现这篇相似的[Unsupervised Pretraining for Sequence to Sequence Learning](https://www.aclweb.org/anthology/D17-1039)里也没有对train data进行区别。~~对于SA-LSTM，将[CTC](https://papers.nips.cc/paper/5346-sequence-to-sequence-learning-with-neural-networks.pdf)模型的output替换成input，encoder和decoder网络拥有相同的weights，并作为分类网络的初始weights。 对于LM-LSTM，利用RNN进行pretrain，相当于去掉CTC的encoder部分，这篇也是在word order上面训练的。

[Supervised & Semi-Supervised Text Categorization using LSTM for Region Embeddings](https://arxiv.org/pdf/1602.02373.pdf) ~~试了下[镜像CNN算法](http://riejohnson.com/paper/cnn-semi-nips15.pdf)然而没有performance提升，对LSTM失去信念就没有实现。~~ 利用unlabeled data训练embedding层，来自同一语料库的embedding比不相关词语构成的embedding有更好的效果。这一篇也是在word order上进行predict，基于[Semi-supervised Sequence Learning](https://arxiv.org/pdf/1511.01432.pdf)和[他们之前在word order上用CNN进行文本分类的工作](https://arxiv.org/pdf/1412.1058.pdf)。~~疯狂文本分类，所以如果我把他们这一票论文都试一下最少也能有一个work =)~~这篇的网络结构是用CNN/LSTM生成word vector embedding然后pooling。比起CNN，LSTM无需固定的region size, 双向LSTM的结果优于单向LSTM。最好的结果来自concatenate分别通过one hot CNN和one hot LSTM训练unlabel data得到的region embedding，再通过pooling层chop掉一些不会对performance贡献的信息，然后接一个Dense layer分类。

[HAN for Document Classification](https://www.cs.cmu.edu/~hovy/papers/16HLT-hierarchical-attention-networks.pdf), 没实现，值得一试？~~之前DL学过[Attention is All You Need](https://arxiv.org/pdf/1706.03762.pdf)，但是失忆，通过[这篇解读](https://zhuanlan.zhihu.com/p/34781297)恢复一下记忆。顺便复习一下[Batch Normalize](https://zhuanlan.zhihu.com/p/24810318)和[Layer Normalize](https://datascience.stackexchange.com/questions/12956/paper-whats-the-difference-between-layer-normalization-recurrent-batch-normal)。~~这篇提出的层次化attention网络基于bidirectional [GRU](http://www.wildml.com/2015/10/recurrent-neural-network-tutorial-part-4-implementing-a-grulstm-rnn-with-python-and-theano/)，因为GRU在效果上与LSTM相似却更易于计算。Network具有与document相似的结构，即character-word-sentence的层次结构，所以 加入两层attention机制, 分别作用于word和sentence层面，也就是word sequence encoder, word-level attention layer, sentence encoder, sentence-level attention layer。得到document的向量表示后用Softmax dense layer层分类。~~想知道怎么做的~~可视化显示模型能够选择具有信息性的word和sentence，attention不仅能够带来更好的performance，也可以帮助分析关于哪些句子和单词提供更多信息。结果显示performance的提升是独立于data size的，也比之前的各种CNN和LSTM效果更好。[这个知乎](https://zhuanlan.zhihu.com/p/26892711)就解读的很好。

~~背对着过道然后屏幕上都是些有的没的也太羞耻了orz。Customer service也就算了，瞎写也在上面我还是不做人了。虽然毫无工作的情况下也实在没什么别的事情可以做orz~~

### CNN

~~看[论文](https://www.aclweb.org/anthology/D14-1181)了，也看了[大佬的博客](http://www.wildml.com/2015/12/implementing-a-cnn-for-text-classification-in-tensorflow/)。瞎train的结果好像还能看。会不会到头来还是一场空呢 = )~~ 基本上单层CNN再word order层面上做分类就可以了，各种regularization的微妙提升可以忽略不计，稍微截短一下文本长，不做pre-process.  ~~(所以[其他人](https://medium.com/@sabber/classifying-yelp-review-comments-using-cnn-lstm-and-visualize-word-embeddings-part-2-ca137a42a97d)在text上做pre-process到底是对performance有提升还是下降？~~

#### References

[CNN for Sentence Classification](https://www.aclweb.org/anthology/D14-1181), 2014年state-of-art的论文，在character层面做文本分类。调整参数参照这篇[CNN句子分类的分析](https://arxiv.org/abs/1510.03820)就很全面了。基本可以看作是和2D图像分类相似的结构，input sentences通过pad和truncate处理为长度相同的sentence matrix ~~(post居然比pre的performance好很多)~~，不同大小的filter在sentence matrix上移动进行卷积操作，通过concatenate生成新的feature map。具体而言，embedding layer接不同size的Conv layer, Max pooling, Flatten layer，concatenate得到feature map作为input输入Softmax层，L2 regularization防止overfit。初始化可以random initialize, pre-trained word2vec, embedding参数可以static或者fine-tuned。论文中最好的结果来自于multi-channel non-static CNN, dropout等regularization techs对performance有3%左右的提升。 ~~我不是，佛系了，不管怎么说像素级模仿[Denny大佬的博客](http://www.wildml.com/2015/12/implementing-a-cnn-for-text-classification-in-tensorflow/)实现应该问题不大吧~~

[Effective Use of Word order for Text Categorization with CNN](https://arxiv.org/abs/1412.1058)和[Character-level CNN for Text Classification](https://papers.nips.cc/paper/5782-character-level-convolutional-networks-for-text-classification.pdf)，~~因为based on word order又好实现又快一些所以选择了它。~~ 在1D的word order上进行CNN文本分类比起将文本表示为词袋模型可以保留句子的序列结构，每个卷积层的单元可以看作是文章的一部分句子。同样concatenate多个卷积层的结果生成feature map。Character-level CNN不需要了解语法和语义结构，因此对于各种语言都比较合适。~~看看[相关博客](https://medium.com/@surmenok/character-level-convolutional-networks-for-text-classification-d582c0c36ace)，觉得和[CNN Sentence classification](https://github.com/dennybritz/cnn-text-classification-tf)的结构没有本质差异。~~  这篇文章的对比LSTM算法使用mean pooling，~~为何我用mean pooling就很差~~。

[Semi-supervised CNN for Text Categorization via Region Embedding](http://riejohnson.com/paper/cnn-semi-nips15.pdf), 毫无performance提升，或许是data相关原因。与[Supervised & Semi-Supervised Text Categorization using LSTM for Region Embeddings](https://arxiv.org/pdf/1602.02373.pdf) 相似算法，利用unlabeled data训练embedding层，在word order level上进行predict。~~又是毫无建树的一天 =)~~

### CLSTM

~~CLSTM, RCNN,  不管怎么样合在一起试试，万一work呢？(笑~~ 

来自[A C-LSTM Neural Network for Text Classification](https://arxiv.org/abs/1511.08630)，因为觉得很有趣就试一试。performance在CNN和LSTM之间，~~反正主要还是靠data~~。原文在character level上进行predict，我实现在word order上面，理论应该可以work。因为想要同时具有能够捕获n-gram feature(CNN)和sentence dependency(LSTM)而先将word2vec embedding通过卷积层形成window feature再按照时间序列顺序经过LSTM层，最后经过dense layer进行分类。这里为了不失去序列信息在卷积层后不进行pooling操作，多层LSTM的last time step output作为feature输入dense layer。 





~~看了一票关于神经网络的文本分类论文，果然还是数据最重要了。网络结构基本上不出大错也都能差不多work，参数调节不会有太大的差别。根据[ML Yearning](http://www.mlyearning.org/)的方式通过改变网络结构和regularization调节bias和variance，得到0.75-0.85的[AUC](https://en.wikipedia.org/wiki/Receiver_operating_characteristic#Area_under_the_curve)不会很难, feature选的好也能0.8左右的F1。所以今天也依旧是一行代码未进和很菜啊的一天呢:)~~